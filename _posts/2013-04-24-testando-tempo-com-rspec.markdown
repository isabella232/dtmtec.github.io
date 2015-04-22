---
layout: post
title: "Testando Tempo com RSpec"
date: 2013-04-24 13:35
comments: true
author: Luis Vasconcellos
categories:
- rspec
- rails
- ruby
- tdd
---

Suponha que você tenha uma clase **Project** com o método **configure_start_date**, cuja responsabilidade é definir o start_date de um project para o tempo atual, como abaixo:

```ruby
class Project < ActiveRecord::Base
  attr_accessible :start_date

  def configure_start_date
    self.start_date = Time.now
  end
end
```

Seria razoável pensar que o teste abaixo seria capaz de testar esse método...

```ruby
  it "configure the project start date to the current time" do
    project.configure_start_date
    project.start_date.should eq Time.now
  end
```

... Porém, não é o que acontece. O teste acima **falha**. O que acontece é que se passa um pequeno intervalo de tempo entre a chamada do método e o seu teste, e é esse intervalo de tempo que faz ambos os objetos Time serem diferentes, fazendo o teste **falhar**. <!-- more -->

Ao me deparar com esse problema eu encontrei duas soluções:

#### 1) Usando o Within RSpec Matcher ##

O matcher do rspec within pode ser utilizado para desconsiderar um intervalo de tempo qualquer entre dois objetos Time. Nesse caso, estamos dizendo que os dois objetos Time devem ser iguais, salvo uma diferença de 1 segundo entre eles:

```ruby
require 'spec_helper'

describe Project do
  let(:project) { Project.new }

  describe "#configure_start_date" do
    it "configure the project start date to the current time" do
      project.configure_start_date
      project.start_date.should be_within(1).of(project.start_date)
    end
  end
end
```

### 2) Usando stub

Utilizando stub, podemos definir que, toda vez que o método now da classe Time seja chamado, ele **irá retornar sempre o mesmo valor**. Isso é feito dentro do método tap porque queremos que o **let!(:now)** receba o valor do nosso now assim que o código do stub termine de ser interpretado:

```ruby
require 'spec_helper'

describe Project do
  let(:project) { Project.new }

  let!(:now) do
    Time.now.tap do |now|
      Time.stub!(:now).and_return(now)
    end
  end

  describe "#configure_start_date" do
    it "configure the project start date to the current time" do
      project.configure_start_date
      project.start_date.should eq(now)
    end
  end
end
```
