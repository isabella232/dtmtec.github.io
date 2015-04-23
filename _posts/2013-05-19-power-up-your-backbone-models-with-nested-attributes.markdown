---
layout: post
title: "Power up your Backbone models with Nested Attributes"
date: 2013-05-19 13:23
comments: true
author: vicente.mundim
read_time: 5 min
categories:
- JavaScript
- Backbone
- Rails
---

So you've been using Backbone and Rails for some application, and you want your Backbone models to have some relationships, as well as the Rails ones do. Bare Backbone models have no knowledge about relationships, they treat them as embedded objects and this means that you can't listen for changes in an embedded model, or link these embedded models to specific views.

<!-- more -->

There are some [plugins](https://github.com/documentcloud/backbone/wiki/Extensions%2C-Plugins%2C-Resources#relations) that can help you with that, but none of them support Rails nested attributes feature yet. For instance, deleted models in a `1-N` relationship will not be tracked, thus they will not be destroyed in the Rails backend.

Here comes [Backbone.NestedAttributes](https://github.com/dtmconsultoria/backbone-nested-attributes). It provides you with `1-1` and `1-N` relations support, as well as Rails-like nested attributes.

## How can I use it? ##

Okay, so now you have followed the instructions on the [README](https://github.com/dtmconsultoria/backbone-nested-attributes/blob/master/README.md#installation), installing the gem and adding the require in the `application.js`. What now?

Now you declare you models by extending `Backbone.NestedAttributesModel` and defining your relations:

```javascript
var Post = Backbone.NestedAttributesModel.extend({
  relations: [
    {
      type: 'one',
      key: 'author',
      relatedModel: function () { return Person }
    },
    {
      key:  'comments',
      relatedModel: function () { return Comment }
    }
  ]
})

var Comment = Backbone.NestedAttributesModel.extend({})
var Person = Backbone.NestedAttributesModel.extend({})
```

Here we have declared a Post model, that has one author and many comments. When specifying the related model we passed a function, instead of giving the model class. This allow us to declare nested models after we declare our parent model. Also, we didn't have to specify a type for the `1-N` relation because it defaults to `many`. Finally `key` is the attribute which will hold the nested relation.

When instantiating a post we can pass nested attributes to it, and it will create models and collections for you:

```javascript
var post = new Post({
  title: 'My Title',
  author: { name: "Vicente Mundim" },
  comments: [
    {
      body: "Nice writeup!"
    },
    {
      body: "Keep it going!"
    }
  ]
})

post.get('author')   // returns a Person model
post.get('comments') // returns a Backbone.Collection of Comment models
```

The nice thing about this is that once created, the collection of models is never changed, it is only updated. For example:

```javascript
post.set('comments', [{ body: 'Great post' }])
```

This will remove all comments from the collection, and add this new one. It basically use the [Collection#set](http://backbonejs.org/#Collection-set) method with the given parameters. Since none of the existing models had IDs, they were removed. Suppose we had this instead:

```javascript
var post = new Post({
  title: 'My Title',
  author: { name: "Vicente Mundim" },
  comments: [
    {
      id: 123,
      body: "Nice writeup!"
    },
    {
      id: 124,
      body: "Keep it going!"
    }
  ]
})

post.set('comments', [{ body: 'Great post' }, { id: 123, body: 'Really nice writeup' }])
```

Now we have added a comment, updated the one with ID `123` and removed the one with ID `124`. This is really nice when dealing with nested models, since you can just fetch an entire graph of objects from the server and have them update the graph, without ever having to re-bind collections or models unnecessarily.

## Rails to the rescue

The main issue with all the relation plugins for backbone is that they do not integrate well with Rails nested attributes. So when saving models with nested attributes Rails will want you to append `_attributes` to your relations keys, and track removed models. With `Backbone.NestedAttributesModel`:

```javascript
post.save({}, { nested: true })
```

And your server gets:

```javascript
{
  id: 123,
  title: 'My Title',
  author_attributes: { id: 987, name: "Vicente Mundim" },
  comments_attributes: [
    {
      id: 765,
      body: "Nice writeup!"
    },
    {
      id: 766,
      body: "Keep it going!"
    }
  ]
}
```

See how each relation is now given with a `_attributes`. It also keeps track of deleted models for you:

```javascript
var comment = post.get('comments').at(0)
post.get('comments').remove(comment)

post.save({}, { nested: true })
```

Your server will receive this data:

```javascript
{
  id: 123,
  title: 'My Title',
  author_attributes: { id: 987, name: "Vicente Mundim" },
  comments_attributes: [
    {
      id: 765,
      body: "Nice writeup!",
      _destroy: true
    },
    {
      id: 766,
      body: "Keep it going!"
    }
  ]
}
```

The comment with ID `765` will be destroyed in the server. Nice, isn't it?

## Reverting changes

Ok now you have a graph of objects presented to your user, and he has changed some data, deleted some nested models, and added a few ones. Then he sees that he have changed the wrong set of models, and hits the cancel button. Easy, just fetch the data again from the server and you're done, uh? nope. Better to use `Backbone.UndoableModel`:

```javascript
var Post = Backbone.UndoableModel.extend({
  relations: [ // UndoableModel is a NestedAttributesModel, so it can have relations
    {
      type: 'one',
      key: 'author',
      relatedModel: function () { return Person }
    },
    {
      key:  'comments',
      relatedModel: function () { return Comment }
    }
  ]
})

var Comment = Backbone.UndoableModel.extend({})
var Person = Backbone.UndoableModel.extend({})

var post = new Post({
  id: 123,
  title: 'My Title',
  author: { id: 987, name: "Vicente Mundim" },
  comments: [
    {
      id: 765,
      body: "Nice writeup!"
    },
    {
      id: 766,
      body: "Keep it going!"
    }
  ]
})

post.set({ title: 'My new title' })
post.get('author').set({ name: 'Jon Snow' })
post.get('comments').at(0).set({ body: 'Great post!' })

post.undo() // that's it, post is now reverted to its initial attributes, as well as its relations

post.get('title')                      // 'My Title'
post.get('author').get('name')         // 'Vicente Mundim'
post.get('comments').at(0).get('body') // "Nice writeup!"
```

It stores the original attributes, and sets them for you when you call `undo`, restoring not only its own attributes but also the attributes and models of its relations. Removed models will be bring back to life, models that were added will be removed and the ones the were updated will be reverted to their synchronized attributes.

Just add a undo button in your user interface, and make it call the `undo` method in your model. One nice thing about it is that it can be used with the root model but also with some nested model, in which case it will revert only that nested model to its synchronized attributes.

## Conclusion

Having relations in your Backbone models is very handy when it comes to creating rich client interfaces. The ability to undo changes is also really nice, and integrating all of this features with a Rails backend makes it shine even more. `Backbone.NestedAttributesModel` and `Backbone.UndoableModel` gives you all of this with a few lines of code, hope this help you!
