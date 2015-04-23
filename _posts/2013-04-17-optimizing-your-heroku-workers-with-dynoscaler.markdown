---
layout: post
title: "Optimizing your Heroku workers with DynoScaler"
date: 2013-01-21 23:00
comments: true
author: vicente.mundim
read_time: 9 min
categories:
- autoscaling
- heroku
- resque
- ruby
---

If you have an application and need to perform tasks in background then you probably use <a href="https://github.com/defunkt/resque">Resque</a>, <a href="https://github.com/mperham/sidekiq">Sidekiq</a> or <a href="https://github.com/tobi/delayed_job">DelayedJob</a>. And if not, then you should really consider using one of these. Each one of them have advantages and fallbacks, Sidekiq uses threads, Resque spawn processes while DelayedJob will store queues in your database.

<!-- more -->

But for us running applications on Heroku there is always the issue with paying for an extra Dyno only to run a few tasks each day. You may use <a href="http://hirefireapp.com/">Hirefire</a>, to scale workers up and down for you, but it will cost you $10, and it can only monitors your application once in a minute. You wouldn’t want that, would you?

Let me present you <a href="https://github.com/dtmconsultoria/dyno_scaler">DynoScaler</a>. With it you can configure your application so that it scales up workers when there are jobs in the queue, and scales down workers when there are no pending or running jobs. It scales up right after enqueueing a job, and scales down after the last job finishes, so that you never have to wait more than you need to.

## Let’s get it installed ##

First you’ll need to add the gem to your Gemfile:

{% gist 4561342 Gemfile %}

Then run `bundle install`, and your good to go.

## How do we use it? ##

To use it with **Resque**, just include `DynoScaler::Workers::Resque` in your job class:

{% gist 4561342 my_job.rb %}

Now whenever `MyJob` gets queued and there are no workers running **DynoScaler** will scale up a worker to process the job. It will eventually scale more workers depending on the total number of pending jobs in all queues. This can all be configured among other things.

For example you may tell *DynoScaler* to always keep at least one worker running, and to scale at most 3 workers. You can also configure how many workers should be spawned given a pending queue size. In a rails application, you could just add this lines to your `config/application.rb`:

{% gist 4561342 application.rb %}

To enable **DynoScaler** in Heroku just configure these two configs:

{% gist 4561342 heroku-commands.bash %}

**DynoScaler** needs your API key and the name of the application in order to scale workers up and down, and that’s all it needs.

You can get more info by following the <a href="https://github.com/dtmconsultoria/dyno_scaler/blob/master/README.md">README</a>.

## Tuning it even further ##

If you followed the instructions up until now and have give it a try (what? have you not?) with one of your applications in Heroku, you may have notice some slowdown in your actions. What is happening? Well, since we need to talk to Heroku APIs every time we enqueue a job, it will actually perform an HTTP request to Heroku.

 This request may take some time, even though the guys at Heroku have done a great job, you can’t predict how much time it will take for this request to return. If we leave it like that it will block your application until it returns, and slowdown things, specially if you enqueue lots of jobs in a single action.

How can we fix this? It's simple, perform the request to scale up workers asynchronously. But how do we do that? We use <a href="https://github.com/mperham/girl_friday">Girl Friday</a>, which will use <a href="http://en.wikipedia.org/wiki/Actor_model">Actors</a>, which in turn use Threads, to run tasks. Just add it to your Gemfile:

{% gist 4561342 Gemfile-girl-friday %}

And configure **DynoScaler** to be async:

{% gist 4561342 async_application.rb %}

This will spawn a `GirlFriday::WorkQueue` with one thread, in your application’s dyno that will handle the requests to the Heroku API to scale workers up when enqueueing jobs. If you want you can pass a block when configuring it so that you can tune it even further (you could keep the queue in Redis, for instance):

{% gist 4561342 custom_async_application.rb %}

## The Future ##

This gem was mostly inspired by <a href="http://verboselogging.com/2010/07/30/auto-scale-your-resque-workers-on-heroku">this</a> post. It may still have some flaws, but we are successfully using them in production for a while in some applications. For now it only scales workers up and down and supports only **Resque**, but we will probably add support for **Sidekiq** and **DelayedJob**.

You’re more than welcome to help, just fork the repository and send us a pull request!
