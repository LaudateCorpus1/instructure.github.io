---
layout: post
title: "Facebook React vs. Ember"
author: "Eric Berry"
date: 2013-12-17 08:54
comments: true
published: true
categories: 
- EmberJS
- JavaScript
---

<div class="comment">Edited Jan 13, 2014 with minor changes which allow for testing timeouts in Ember.</div>

We engineers at Instructure love [Ember](http://www.emberjs.com). We also love to learn about other frameworks and know which tools are the best for the job at hand. One of the newer frameworks to come out is React.

[React](http://facebook.github.io/react/) is Facebook's new JavaScript library for building user interfaces. I'm excited to see another player in the game of front-end JS frameworks. When we have so much mind share going to a similar problem, we all win.

On the home page <a href="/images/posts/react-website.png">(screenshot)</a>, there are a few examples of how React can be used. I thought it might be fun to show how each of these can be done using Ember.

<!-- more -->

## A Simple Component

React components take data and return content which is to be displayed. Here's their example code:

{% raw %}
```js React
/** @jsx React.DOM */
var HelloMessage = React.createClass({
  render: function() {
    return <div>{'Hello ' + this.props.name}</div>;
  }
});

React.renderComponent(<HelloMessage name="John" />, mountNode);
```
{% endraw %}

Let's build this in Ember. For something as simple as this example, a [Handlebars Helper](http://emberjs.com/guides/templates/writing-helpers/) will do the trick.

{% raw %}
```js Ember
Ember.Handlebars.helper('hello-message', function(name) {
  var escaped = Handlebars.Utils.escapeExpression(name);
  return new Handlebars.SafeString('<div>Hello ' + escaped + '</div>');
});

{{{hello-message "John"}}}
```
{% endraw %}

### Example:

<a class="jsbin-embed" href="http://jsbin.com/ecAJuxE/4/edit?html,js,output">Facebook React vs Ember</a><script src="http://static.jsbin.com/js/embed.js"></script>

## A Stateful Component

The stateful component example on the React page is cool. It tracks the elapsed time on a page (since refresh). Here's their code:

```js React
var Timer = React.createClass({
  getInitialState: function() {
    return {secondsElapsed: 0};
  },
  tick: function() {
    this.setState({secondsElapsed: this.state.secondsElapsed + 1});
  },
  componentDidMount: function() {
    this.interval = setInterval(this.tick, 1000);
  },
  componentWillUnmount: function() {
    clearInterval(this.interval);
  },
  render: function() {
    return React.DOM.div({},
      'Seconds Elapsed: ' + this.state.secondsElapsed
    );
  }
});
```

Since we are maintaining state within the component itself, let's build it using an Ember Component.

```js Ember
App.TimeOnPageComponent = Em.Component.extend({
  secondsViewed: 0,
  
  // Increment the secondsViewed attribute
  tick: function() {
    this.incrementProperty('secondsViewed');
  },
  
  // Initiates timer when element is rendered
  startTimer: function() {
    Ember.run.next(this, function() {
      this.interval = setInterval(this.tick.bind(this), 1000);
    });
  }.on('didInsertElement'),
  
  // Ensure that the timer stops when closed
  clearInterval: function() {
    clearInterval(this.interval);
  }.on('willDestroyElement')
});
```

### Example:
<a class="jsbin-embed" href="http://jsbin.com/OPeVeted/2/embed?js,output">Facebook React vs Ember</a><script src="http://static.jsbin.com/js/embed.js"></script>

## An Application

The application example is a simple todo list which has a TodoList class (for rendering content) and a TodoApp class (maintains the state and renders the layout). Here's the code:

```js React
/** @jsx React.DOM */
var TodoList = React.createClass({
  render: function() {
    var createItem = function(itemText) {
      return <li>{itemText}</li>;
    };
    return <ul>{this.props.items.map(createItem)}</ul>;
  }
});
var TodoApp = React.createClass({
  getInitialState: function() {
    return {items: [], text: ''};
  },
  onChange: function(e) {
    this.setState({text: e.target.value});
  },
  handleSubmit: function(e) {
    e.preventDefault();
    var nextItems = this.state.items.concat([this.state.text]);
    var nextText = '';
    this.setState({items: nextItems, text: nextText});
  },
  render: function() {
    return (
      <div>
        <h3>TODO</h3>
        <TodoList items={this.state.items} />
        <form onSubmit={this.handleSubmit}>
          <input onChange={this.onChange} value={this.state.text} />
          <button>{'Add #' + (this.state.items.length + 1)}</button>
        </form>
      </div>
    );
  }
});
React.renderComponent(<TodoApp />, mountNode);
```

When writing this in Ember, we would use both a controller and a handlebars template. The controller will maintain the state and respond to user interaction and the template will display the form and list.

```js Ember (controller)
App.ApplicationController = Em.ArrayController.extend({
  content: function() {
    return [];
  }.property(),
  
  btnLabel: function() {
    return "Add #" + (this.get('content.length') + 1);
  }.property('content.length'),
  
  actions: {
    handleSubmit: function() {
      this.get('content').pushObject(this.get('text'));
      this.set('text', '');
    }
  }
});
```

{% raw %}
```html Ember (template)
<div>
  <h3>TODO</h3>
  <ul>
  {{#each content}}
    <li>{{this}}</li>
  {{/each}}
  </ul>

  <form {{action "handleSubmit" on="submit"}}>
    {{input value=text}}
    <button type="submit">{{btnLabel}}</button>
  </form>
</div>
```
{% endraw %}

### Example:

<a class="jsbin-embed" href="http://jsbin.com/OkOLEze/4/embed?html,js,output">Facebook React vs Ember</a><script src="http://static.jsbin.com/js/embed.js"></script>

## A Component Using External Plugins

This example on the React website under *A Component Using External Plugins* is a great example of encapsulating functionality.

{% raw %}
```js React
/** @jsx React.DOM */

var converter = new Showdown.converter();

var MarkdownEditor = React.createClass({
  getInitialState: function() {
    return {value: 'Type some *markdown* here!'};
  },
  handleChange: function() {
    this.setState({value: this.refs.textarea.getDOMNode().value});
  },
  render: function() {
    return (
      <div className="MarkdownEditor">
        <h3>Input</h3>
        <textarea
          onChange={this.handleChange}
          ref="textarea"
          defaultValue={this.state.value} />
        <h3>Output</h3>
        <div
          className="content"
          dangerouslySetInnerHTML={{
            __html: converter.makeHtml(this.state.value)
          }}
        />
      </div>
    );
  }
});

React.renderComponent(<MarkdownEditor />, mountNode);
```
{% endraw %}

The caveat to this example is that it assumes that the [showdown](https://github.com/coreyti/showdown) library is already loaded. In our Ember example, note that the library is loaded in the *head*.

```js Ember (controller)
App.ApplicationRoute = Em.Route.extend({
  model: function() {
    return { value: 'Type some *markdown* here!' };
  }
});

Em.Handlebars.registerBoundHelper('markdown', function(str) {
  var converter = new Showdown.converter();
  var html = converter.makeHtml(str || '');
  return new Handlebars.SafeString(html);
});
```

{% raw %}
```html Ember (template)
<div class="container">
  <h3>Input</h3>
  {{textarea value=value class="form-control"}}
  <h3>Output</h3>
  {{markdown value}}
</div>
```
{% endraw %}

I added Twitter Bootstrap to this example for cleanliness.

<a class="jsbin-embed" href="http://jsbin.com/eyOlOFI/7/embed?js,output">Facebook React vs Ember</a><script src="http://static.jsbin.com/js/embed.js"></script>

I haven't spent enough time with React to be able to convert the [Ember examples](http://www.emberjs.com) to it, but I would love to see someone do that for a better comparison.

{% render_partial _authors/ericb.html %}