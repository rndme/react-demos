This is a collection of simple demos of vcc.js.

These demos are purposely written in a simple and clear style. You will find no difficulty in following them to learn this powerful library.

## How to use

First copy the repo into your disk.

```bash
$ git clone git@github.com:rndme/react-demos.git
```

Then play with the source files in the repo's demo(n) directories.

## Index

1. [Render HTML](#demo01-render-jsx-source)
1. [Use JavaScript in HTML](#demo02-use-javascript-in-jsx-source)
1. [Use array in HTML](#demo03-use-array-in-jsx-source)
1. [Define a component](#demo04-define-a-component-source)
1. [this.props.innerHTML](#demo05-thispropschildren-source)
1. PropTypes - NA at this time
1. [Finding a DOM node](#demo07-finding-a-dom-node-source)
1. [this.state](#demo08-thisstate-source)
1. [Form](#demo09-form-source)
1. [Component Lifecycle](#demo10-component-lifecycle-source)
1. [Ajax](#demo11-ajax-source)
1. [Display value from a Promise](#demo12-display-value-from-a-promise-source)

---

## Demo01: Render HTML ([source](https://github.com/rndme/react-demos/blob/master/demo01/index.html)) ([live](http://danml.com/vcc/react-demos/demo01/))

The template syntax in VCC is called HTML, as in regular HTML5. Use ES6 template literals to contain pretty (un-escaped) multi-line HTML in JS files.
If you want backwards compatibility with non-ES6 browsers, use something like babel to transform your source into ES5.

note:  intraHTML is the entry way to the internal VDOM-based partial updater, used to render without defining a component.

```js
intraHTML(
  document.getElementById('example'),
  `<h1>Hello, world!</h1>`
);
```

## Demo02: Use JavaScript in HTML ([source](https://github.com/rndme/react-demos/blob/master/demo02/index.html)) ([live](http://danml.com/vcc/react-demos/demo02/))

You could also use JavaScript in HTML. It takes angle brackets (&lt;) as the beginning of HTML syntax, and money curly brackets (${) as the beginning of JavaScript syntax.

```js
var names = ['Alice', 'Emily', 'Kate'];

intraHTML(
  document.getElementById('example'),
  `<div>
  ${
    names.map(function (name) {
      return `<div>Hello, ${name}!</div>`
    }).join('')
  }
  </div>`
);
```

## Demo03: Use array in HTML ([source](https://github.com/rndme/react-demos/blob/master/demo03/index.html))  ([live](http://danml.com/vcc/react-demos/demo03/))

If a JavaScript variable is array, VCC will NOT implicitly concat all members of the array, so use `join("")` to view arrays:

```js
var arr = [
  `<h1>Hello world!</h1>`,
  `<h2>VCC is awesome</h2>`,
];

intraHTML(
  document.getElementById('example'),
  `<div>${arr.join('')}</div>`
);
```

## Demo04: Define a component ([source](https://github.com/rndme/react-demos/blob/master/demo04/index.html))  ([live](http://danml.com/vcc/react-demos/demo04/))

`VCC()` creates a component class, which implements a render method to return an component instance of the class. You don't need to call `new` on the class in order to get an instance, just use it to define behavior of a custom HTML element.

```javascript
var HelloMessage = VCC({
  displayName: "hellomessage",
  render: function() {
    return `<h1>Hello ${this.props.name}</h1>`
  }
});

```

```html
  <vcc-hellomessage name="John"></vcc-hellomessage>
```



Components could have attributes, and you can use `this.props.[attribute]` to access them, just like `this.props.name` of `<vcc-hellomessage name="John">` is John.



## Demo05: this.props.children ([source](https://github.com/rndme/react-demos/blob/master/demo05/index.html))  ([live](http://danml.com/vcc/react-demos/demo05/))

VCC uses `this.children to access a component's content.

```javascript
var NotesList = VCC({
  displayName: "noteslist",
  render: function() {
    return `
      <ol>
      ${
        [].map.call(this.children, function (child) {
          return `<li>${child.textContent}</li>`;
        }).join('')
      }
      </ol>
    `;
  }
});
```

```html
  <vcc-noteslist>
    <span>hello</span>
    <span>world</span>
  </vcc-noteslist>
```


Please be minded that the value of `this.children` has two possibilities. If the component has no children node, the value is null; If single children or multiple children nodes, an nodelist. You should be careful to handle it.











## Demo07: Finding a DOM node ([source](https://github.com/rndme/react-demos/blob/master/demo07/index.html))  ([live](http://danml.com/vcc/react-demos/demo07/))

Sometimes you need to reference a DOM node in a component. VCC gives you the `ref` attribute to find it using a callback function.

```js
  var MyComponent = VCC({
	displayName: "mycomponent",
  handleClick: function() {
    // Explicitly focus the text input using the raw DOM API.
    if (this.myTextInput != null) {
      this.myTextInput.focus();
    }
  },
  render: function() {
    // The ref attribute is a callback that saves a reference to the
    // component to this.myTextInput when the component is mounted.
    return `
      <div>
        <input type="text" ref="function(ref){this.myTextInput = ref}" />
        <input
          type="button"
          value="Focus the text input"
          on-click="this.handleClick"
        />
      </div>
    `;
  }
});
```

```html
<vcc-mycomponent></vcc-mycomponent>
```

VCC supports a special attribute that you can attach to any component. The ref attribute can be a callback function, and this callback will be executed immediately after the component is mounted. 
The referenced component will be passed in as a parameter, and the callback function may use the component immediately, or save the reference for future use (or both).



## Demo08: this.state ([source](https://github.com/rndme/react-demos/blob/master/demo08/index.html))  ([live](http://danml.com/vcc/react-demos/demo08/))

VCC thinks of component as state machines, and uses `this.state` to hold component's state, `getInitialState()` to initialize `this.state`(invoked before a component is mounted), `this.setState()` to update `this.state` and re-render the component.

```js
var LikeButton = VCC({
 displayName: "likebutton",
  getInitialState: function() {
    return {liked: false};
  },
  handleClick: function(event) {
    this.setState({liked: !this.state.liked});
  },
  render: function() {
    var text = this.state.liked ? 'like' : 'haven\'t liked';
    return `
      <p on-click=this.handleClick>
        You ${text} this. Click to toggle.
      </p>
    `;
  }
});
```

```html
  <vcc-likebutton ></vcc-likebutton>
```

You could use component attributes to register event handlers, just like `on-click`, `on-keydown`, `on-copy`, etc.



## Demo09: Form ([source](https://github.com/rndme/react-demos/blob/master/demo09/index.html))  ([live](http://danml.com/vcc/react-demos/demo09/))

According to VCC's design philosophy, `this.state` describes the state of component and is mutated via user interactions, and `this.props` describes the properties of component and is stable and immutable.

Since that, the `value` attribute of Form components, such as &lt;input&gt;, &lt;textarea&gt;, and &lt;option&gt;, is unaffected by any user input. 
If you wanted to access or update the value in response to user input, you could use the on-change event.

```js
var Input = VCC({
  displayName: "input",
  getInitialState: function() {
    return {value: 'Hello!'};
  },
  handleChange: function(event) {
    this.setState({value: event.target.value});
  },
  render: function () {
    var value = this.state.value;
    return `
      <div>
        <input type="text" value=${value} on-change=this.handleChange />
        <p>${value}</p>
      </div>
    `;
  }
});
```

```html
<vcc-input></vcc-input>
```

## Demo10: Component Lifecycle ([source](https://github.com/rndme/react-demos/blob/master/demo10/index.html))  ([live](http://danml.com/vcc/react-demos/demo10/))

Components have three main parts of their lifecycle: Mounting(being inserted into the DOM), Updating(being re-rendered) and Unmounting(being removed from the DOM). 
VCC provides hooks into these lifecycle parts. `will` methods are called right before something happens, and `did` methods which are called right after something happens.

```js
   var Hello = VCC({
      displayName: "hello",
      getInitialState: function () {
        return {
          opacity: 1.0
        };
      },
       componentDidMount: function () {
        this.timer = setInterval(function () {
          var opacity = this.state.opacity;
          opacity -= 0.05;
          if (opacity < 0.1) {
            opacity = 1.0;
          }
          this.setState({
            opacity: opacity
          });
        }.bind(this), 100);
      },
       render: function () {
        return `
          <div style="opacity: ${this.state.opacity}">
            Hello ${this.props.name}
          </div>
        `;
      }
  });
```

```html
  <vcc-hello name="world"></vcc-hello>
```


The following is a list of lifecycle methods.

- **componentWillMount()**: Fired once, before initial rendering occurs. Good place to wire-up message listeners. `this.setState` doesn't work here.
- **componentDidMount()**: Fired once, after initial rendering occurs. Can use `this.getDOMNode()`.
- **componentWillUpdate(object nextProps, object nextState)**: Fired after the component's updates are made to the DOM. Can use `this.getDOMNode()` for updates.
- **componentDidUpdate(object prevProps, object prevState)**: Invoked immediately after the component's updates are flushed to the DOM. This method is not called for the initial render. Use this as an opportunity to operate on the DOM when the component has been updated.
- **componentWillUnmount()**: Fired immediately before a component is unmounted from the DOM. Good place to remove message listeners or general clean up.
- **componentWillReceiveProps(object nextProps)**:  !NA! (for now)  Fired when a component is receiving new props. You might want to `this.setState` depending on the props.
- **shouldComponentUpdate(object nextProps, object nextState)**: Fired before rendering when new props or state are received. `return false` if you know an update isn't needed.




## Demo11: Ajax ([source](https://github.com/rndme/react-demos/blob/master/demo11/index.html))  ([live](http://danml.com/vcc/react-demos/demo11/))

How to get the data of a component from a server or an API provider? The answer is using Ajax to fetch data in the event handler of `componentDidMount`. 
When the server response arrives, store the data with `this.setState()` to trigger a re-render of your UI.

```js
var UserGist = VCC({
  displayName: "usergist",
  getInitialState: function() {
    return {
      username: '',
      lastGistUrl: ''
    };
  },

  componentDidMount: function() {
    $.get(this.props.source, function(result) {
      var lastGist = result[0];
      if (this.isMounted()) {
        this.setState({
          username: lastGist.owner.login,
          lastGistUrl: lastGist.html_url
        });
      }
    }.bind(this));
  },

  render: function() {
    return `
      <div>
        ${this.state.username}'s last gist is
        <a href=${this.state.lastGistUrl}>here</a>.
      </div>
    `;
  }
});
```

```html
  <vcc-usergist source="https://api.github.com/users/octocat/gists" ></vcc-usergist>
```



## Demo12: Display value from a Promise ([source](https://github.com/rndme/react-demos/tree/master/demo12/index.html))  ([live](http://danml.com/vcc/react-demos/demo12/))

This demo is inspired by Nat Pryce's article ["Higher Order React Components"](http://natpryce.com/articles/000814.html).

If a VCC component's data is received asynchronously, we can use a Promise object as the component's property.


The above code takes data from Github's API, and the `RepoList` component gets a Promise object as its property.

Now, while the promise is pending, the component displays a loading indicator. When the promise is resolved successfully, the component displays a list of repository information. If the promise is rejected, the component displays an error message.

```javascript
  var RepoList = VCC({
  displayName: "repolist",
  getInitialState: function() {
    return {
      loading: true,
      error: null,
      data: null
    };
  },

  componentDidMount() {
    this.props.promise.then(
      value => this.setState({loading: false, data: value}),
      error => this.setState({loading: false, error: error}));
  },

  render: function() {
    if (this.state.loading) {
      return `<span>Loading...</span>`;
    }
    else if (this.state.error !== null) {
      return `<span>Error: ${this.state.error.message}</span>`;
    }
    else {
      var repos = this.state.data.items;
      var repoList = repos.map(function (repo) {
        return `
          <li><a href=${repo.html_url}>${repo.name}</a> (${repo.stargazers_count} stars) <br/> ${repo.description}</li>
        `;
      }).join('');
      return `
        <div>
          <h1>Most Popular JavaScript Projects in Github</h1>
          <ol>${repoList}</ol>
        </div>
      `;
    }
  }
});
```

```html
   <vcc-repolist promise="$.getJSON('https://api.github.com/search/repositories?q=javascript&sort=stars')" ></vcc-repolist>
```

## Extras

## Useful links

- [VCC's official repo](https://github.com/rndme/vcc/)
- [React's official site](http://facebook.github.io/react)
- [React's official examples](https://github.com/facebook/react/tree/master/examples)
- [React (Virtual) DOM Terminology](http://facebook.github.io/react/docs/glossary.html), by Sebastian Markbåge
- [The React Quick Start Guide](http://www.jackcallister.com/2015/01/05/the-react-quick-start-guide.html), by Jack Callister
- [Learning React.js: Getting Started and Concepts](https://scotch.io/tutorials/learning-react-getting-started-and-concepts), by Ken Wheeler
- [Getting started with React](http://ryanclark.me/getting-started-with-react/), by Ryan Clark
- [React JS Tutorial and Guide to the Gotchas](https://zapier.com/engineering/react-js-tutorial-guide-gotchas/), by Justin Deal
- [React Primer](https://github.com/BinaryMuse/react-primer), by Binary Muse
- [jQuery versus React.js thinking](http://blog.zigomir.com/react.js/jquery/2015/01/11/jquery-versus-react-thinking.html), by zigomir

## License

BSD licensed - thanks to https://github.com/ruanyf/react-demos

