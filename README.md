# array-where-select: GraphQL-style Array.select() and Array.where()

array-where-select adds two extra functions to the Array prototype. Together you can filter and transform an array of objects, by specifying the shape of the objects.


## Table of Contents

1. [Background](#background)
2. [Install](#install)
3. [Usage](#usage)
4. [API](#api)
   - [Array.select()](#array-select)
   - [Array.where()](#array-where)
5. [Contributions](#contributions)
6. [License](#license)

<a name="background"></a>
## Background

There are many libraries that add a kind of query language to javascript arrays. GraphQL is one of those. But all the libraries I have found add a custom query language. Either by adding specific functions that mimic SQL, or by explicitly defining a query language like GraphQL. In all cases this means that you give up the power of javascript itself and must switch to a different, less capable language.

So this library is explicitly not a query language itself, but it uses some javascript trickery to add some syntactic sugar to the native Array.map and Array.filter functions so that you can get most of the ease of use of something like GraphQL, while staying squarely in javascript country.

There are no speed improvements or indexes over normal Array.filter and Array.map.

<a name="install"></a>
## Install

Using NPM:

```bash
npm install array-where-select
```

Then in your javascript:

```javascript
import _ from 'array-where-select'
```

Or in a browser:

```html
<script src="path/to/array-where-select/src/whereselect.mjs" type="module"></script>
```

This library uses ES6 import/export syntax, but there is only a single javascript file. You can include that directly, or use a bundler.

<a name="usage"></a>
## Usage

The examples below all use the data below. The data uses [JSONTag](https://www.npmjs.com/package/@muze-nl/jsontag), to allow for multiple links to the same object. This is not relevant for the remainder of the examples or needed to use this library.

```javascript
let data = JSONTag.parse(`[
	<object id="John">{
		name: "John",
		lastName: "Doe",
		friends: [
			<link>"Jane"
		]
	},
	<object id="Jane">{
		name: "Jane",
		lastName: "Doe",
		friends: [
			<link>"John"
		]
	}
]`)
```

And this is how you can use this library:

```javascript
data.where({
	friends: {
		name: 'John'
	}
})
.select({
	name: _ => _.name+' '+_.lastName
})
```

<a name="api"></a>
## API

<a name="array-select"></a>
### Array.select()

Select describes which properties from a dataset you would like to have in your result set. This is similar to the way GraphQL works. For example: You can select just the name part like this:

```javascript
data.select({
	name: _
})
```

And the result array will be:
```json
[
	{
		"name": "John"
	},
	{
		"name": "Jane"
	}
]
```

The _ represents the identity function and will return the value matching with the key to the left, in this case 'name'.

You can also specify this like so:

```javascript
data.select({
	name: _.name
})
```

Now lets show the name of each friend.

```javascript
data.select({
	name: _,
	friends: {
		name: _
	}
})
```

You can also use a different name, or alias, in the result:

```javascript
data.select({
	firstName: _.name,
	friends: {
		firstName: _.name
	}
})
```

Or you can use functions in the select parameter, like so:

```javascript
data.select({
	name: _ => _.name+' '+_.lastName
})
```

<a name="array-where"></a>
### Array.where()

While select() allows you to select the properties for your result set, where() allows you to filter the result set only to matching objects. To match a specific property with a specific value do this:

```javascript
data.where({
	name: 'John'
})
```

Or use properties of nested objects like this:

```javascript
data.where({
	friends: {
		name: 'John'
	}
})
```

You can also use regular expressions:

```javascript
data.where({
	name: /J.*/
})
```

And finally you can use match functions:

```javascript
data.where({
	name: _ => _.name[0] == 'J'
})
```

And you can combine where with select like this:

```javascript
data.where({
	friends: {
		name: 'John'
	}
})
.select({
	name: _ => _.name+' '+_.lastName
})
```

<a name="contributions"></a>
## Contributions

Contributions are welcome. Please fork the github repository and make your changes there, then open a pull request.
If you find any bugs or other issues, please use the github repository Issues. Check if your issue has already been posted before you add a new issue.

The github repository is at https://github.com/muze/array-where-select

<a name="license"></a>
## License

This software is licensed under MIT open source license. See the [License](./LICENSE) file.