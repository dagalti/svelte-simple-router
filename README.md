# svelte-simple-router

## Svelte 3 History Based Single Page Router With Server Side Rendering (SSR) support

## Installtion

with npm

```bash
npm i svelte-simple-router
```

## Usage Note

Ensure your local server is configured in SPA mode. In a default Svelte installation you need to edit your package.json and add _--single_ to `sirv public`.

```javascript
"start": "sirv public --single"
```

## Integration into your project

### Step 1 : Create Routes File

create routes.js file into your project directory

```javascript
import AdminLayout from './views/layouts/Admin.svelte';
import PublicLayout from './views/layouts/Public.svelte';
import Dashboard from './views/pages/Dashboard.svelte';
import MembersList from './views/pages/MembersList.svelte';
import Page404 from './views/pages/404.svelte';

const routes = {
	groupGuard: [
		{
			url: [/^members/],
			with: async function (routerData, route) {
				return true;
			},
			redirectOnFail: function (routerData, route) {
				return '/dashboard';
			}
		}

	],
	routes: [
		{
			name: "dashboard",
			url: [/^dashboard$/, /^\s*$/],
			guard: {
				with: async function (routerData, route) {
					return true;
				},
				redirectOnFail: '/404'
			},
			layout: AdminLayout,
			component: Dashboard
		},
		{
			name: "members",
			url: [/^members/],
			urlPathMapKeys: ['', 'key1', 'key2'],
			searchFilter: async function (routerData, route) {
				return true;
			},
			guard: {
				with: async function (routerData, route) {
					return true;
				},
				redirectOnFail: '/404'
			},
			layout: AdminLayout,
			component: MembersList
		},
		{
			name: "404",
			url: [/^404$/],
			layout: PublicLayout,
			component: Page404
		}
	]
};

export { routes }
```

### Step 2 : Import the routes.js and **Router** from svelte-simple-router into your main app component 

```javascript
<script>
	import { Router } from 'svelete-simple-router';
	import { routes } from './routes.js';
</script>

<Router routes={routes} />
```

### Step 3 : Create Layout File

for example : import AdminLayout from './views/layouts/Admin.svelte';

we created Admin.svelte file

```javascript
<script>
	import {Route} from 'svelete-simple-router';
	export let currentRoute = {};
</script>
<div>
	<h1>Admin Layout</h1>
	<a href="/dashboard">Dashboard</a>
	<a href="/members">Member List</a>
	<a href="/404">404</a>
	<Route {currentRoute} />
</div>
```


### Step 4 : Create Component/Page File

for example : import Dashboard from './views/pages/Dashboard.svelte';

we created Dashboard.svelte file

```javascript
<script>
	export let currentRoute = {};
</script>
<h1>Dashboard</h1>
```

## Structure of routes object

```javascript
import AdminLayout from './views/layouts/Admin.svelte';
import PublicLayout from './views/layouts/Public.svelte';
import Dashboard from './views/pages/Dashboard.svelte';
import MembersList from './views/pages/MembersList.svelte';
import Page404 from './views/pages/404.svelte';

const routes = {
	groupGuard: [
		{
			url: [/^members/],
			with: async function (routerData, route) {
				return true;
			},
			redirectOnFail: function (routerData, route) {
				return '/dashboard';
			}
		},
		{
			url: [/^dashboard/],
			with: async function (routerData, route) {
				return true;
			},
			redirectOnFail:"/login"
		}

	],
	routes: [
		{
			name: "dashboard",
			url: [/^dashboard$/, /^\s*$/],
			guard: {
				with: async function (routerData, route) {
					return true;
				},
				redirectOnFail: '/404'
			},
			layout: AdminLayout,
			component: Dashboard
		},
		{
			name: "members",
			url: [/^members/],
			urlPathMapKeys: ['', 'key1', 'key2'],
			searchFilter: async function (routerData, route) {
				return true;
			},
			guard: {
				with: async function (routerData, route) {
					return true;
				},
				redirectOnFail: '/404'
			},
			layout: AdminLayout,
			component: MembersList
		},
		{
			name: "404",
			url: [/^404$/],
			layout: PublicLayout,
			component: Page404
		}
	]
};

export { routes }
```
### 1) groupGuard

```javascript
groupGuard: [
	{
		url: [/^members/],
		with: async function (routerData, route) {
			return true;
		},
		redirectOnFail: function (routerData, route) {
			return '/dashboard';
		}
	}

]
```

Using group guard you can guard the multiple routes using matching starting path or any regular expression that you want to check.

Fields | Description
-------|------------
**groupGuard:** | array of object hold all your group routes for your application
**groupGuard:url:** | array of regular expression that you want to match for that particular group route
**groupGuard:with:** | a callback function for checking and guarding particular route. must return either **true** or **false**
**groupGuard:redirectOnFail:** | a function or string url for redirect if guard is fail. if it is a function then must return valid **url**

groupGuard always check and run after specific url guard is completed.

### 2) routes

```javascript
routes: [
	{
		name: "dashboard",
		url: [/^dashboard$/, /^\s*$/],
		searchFilter: async function (routerData, route) {
			return true;
		},
		guard: {
			with: async function (routerData, route) {
				return true;
			},
			redirectOnFail: '/404'
		},
		layout: AdminLayout,
		component: Dashboard
	},
	{
		name: "404",
		url: [/^404$/],
		layout: PublicLayout,
		component: Page404
	}
]
```

Fields | Description
-------|------------
**routes:** | array of object hold all your routes for your application
**routes:name:** | you need to provide unique name for each of your routes.
**routes:url:** | array of regular expression that you want to match for that particular route
**routes:urlPathMapKeys:** | array of keyname that assign to each path item.
**routes:searchFilter:** | a callback function for extra custom matching function if you required other than regular expression
**routes:guard:** | if you want to guard that particular route for some conditional check.
**routes:guard:with:** | a callback function for checking and guarding particular route. must return either **true** or **false**
**routes:guard:redirectOnFail:** | a function or string url for redirect if guard is fail. if it is a function then must return valid **url**
**routes:layout:** | layout component that you want to load, if you dont provide then only component is loaded.
**routes:component:** | page component that you want to load under the layout or without layout.

#### Mandatory routes

```javascript
{
	name: "404",
	url: [/^404$/],
	layout: PublicLayout,
	component: Page404
}
```

**404** named route is required to display the component if no component or layout is found.

## currentRoute object

every layout and component passed **currentRoute** props to easily available some router related information.

for example if you visit this url

http://localhost:5000/members/name/rajdeep?quervar1=value1

```javascript
{
  "routePosition": 1,
  "routeName": "members",
  "pathName": "/members/name/rajdeep",
  "pageName": "members",
  "singleParams": [
    "members",
    "name",
    "rajdeep"
  ],
  "namedParams": {
    "name": "rajdeep"
  },
  "queryParams": {
    "quervar1": "value1"
  },
  "layout": {
    "viewed": false
  },
  "component": {
    "viewed": false
  }
}
````

## searchFilter, group:with, group:redirectOnFail, groupGuard:with, groupGuard:redirectOnFail

you can define all above callback function in **Synchronous** or **Asynchronous** manner as per your application need.

all above function in your routes object passed two parameter

### **1) routerData**

**routerData** contains

for example 

```javascript
"pathName": "/members/name/rajdeep",
"pageName": "members",
"singleParams": [
"members",
"name",
"rajdeep"
],
"namedParams": {
"name": "rajdeep"
},
"queryParams": {
"quervar1": "value1"
}
```


### **2) route**

**route** contains whole object of your route that you defined in **routes** object

for example

```javascript
{
	name: "dashboard",
	url: [/^dashboard$/, /^\s*$/],
	searchFilter: async function (routerData, route) {
		return true;
	},
	guard: {
		with: async function (routerData, route) {
			return true;
		},
		redirectOnFail: '/404'
	},
	layout: AdminLayout,
	component: Dashboard
}
```

## ```<a href="">``` in router

By default router will capture all the ```<a href="">``` onclick event and based on the href it will match the route

if it's not same domain link then it will just do the regular redirect 

if it's same domain link then it will start the matching process and load the matched route

### ```<a href="" class="no-follow">```

if you dont want capture some of your ```<a>``` link in router by default.

then just add the **class="no-follow"** and those click event will not goes into matching.

## Manually / Code level redirect

```javascript
import {RouterRedirect} from 'svelte-simple-router';
```

will provide you redirect functionality at code level.

usage example

```javascript
RouterRedirect('/some-other-url');
RouterRedirect('https://someother.url');
```

**noFollow** you can also pass no **noFollow** true or false in second parameter to tell router whether you bypass the matching or not.

By default **noFollow** is set to **false**

usage example

```javascript
RouterRedirect('/some-other-url',true); //will not goes into route matching and directly do regular page redirect
```

## Settings For Server Side Rendering (SSR)

there is example in examples/ssr folder on github.

### rollup.config.js

```javascript
import svelte from 'rollup-plugin-svelte';
import resolve from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import livereload from 'rollup-plugin-livereload';
import { terser } from 'rollup-plugin-terser';

const production = !process.env.ROLLUP_WATCH;

export default [
	//browser bundel
	{

		input: 'src/main.js',
		output: {
			sourcemap: true,
			format: 'iife',
			name: 'app',
			file: 'public/build/bundle.js'
		},
		plugins: [
			svelte({
				// enable run-time checks when not in production
				dev: !production,
				// we'll extract any component CSS out into
				// a separate file - better for performance
				css: css => {
					css.write('public/build/bundle.css');
				},
				hydratable: true
			}),

			// If you have external dependencies installed from
			// npm, you'll most likely need these plugins. In
			// some cases you'll need additional configuration -
			// consult the documentation for details:
			// https://github.com/rollup/plugins/tree/master/packages/commonjs
			resolve({
				browser: true,
				dedupe: ['svelte']
			}),
			commonjs(),

			// In dev mode, call `npm run start` once
			// the bundle has been generated
			!production && serve(),

			// Watch the `public` directory and refresh the
			// browser on changes when not in production
			!production && livereload('public'),

			// If we're building for production (npm run build
			// instead of npm run dev), minify
			production && terser()
		],
		watch: {
			clearScreen: false
		}
	},
	//server ssr bundel
	{
		input: "src/App.svelte",
		output: {
			sourcemap: false,
			format: "cjs",
			name: "app",
			file: "public/App.js"
		},
		plugins: [
			svelte({
				generate: "ssr"
			}),
			resolve(),
			commonjs(),
			production && terser()
		]
	}

];

function serve() {
	let started = false;

	return {
		writeBundle() {
			if (!started) {
				started = true;

				require('child_process').spawn('npm', ['run', 'start', '--', '--dev'], {
					stdio: ['ignore', 'inherit', 'inherit'],
					shell: true
				});
			}
		}
	};
}
```

### App.svelte
```javascript
<script>
	import { Router } from 'svelte-simple-router';
	import { routes } from './routes.js';

	// Used for SSR. A falsy value is ignored by the Router.
	export let url = "";
</script>

<Router routes={routes} url="{url}" />
```

### main.js (entry point of your browser bundel app)
```javascript
import App from './App.svelte';

const app = new App({
	target: document.body,
	hydrate: true
});

export default app;
```

### node.js server

you can use any server of your choice 

```javascript
const http = require('http');
const App = require('./public/App.js');
const port = 3000

const requestHandler = (request, response) => {
	let url = 'http://' + request.headers.host + request.url;
	const { head, html, css } = App.render({ url: url });
	let output = `<!DOCTYPE html>
	<html lang="en">
	<head>
		<meta charset="UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1.0">
		<title>Document</title>
		<link rel='stylesheet' href='http://localhost:5000/global.css'>
		<link rel='stylesheet' href='http://localhost:5000/build/bundle.css'>
		<script defer src='http://localhost:5000/build/bundle.js'></script>
	</head>
	<body>${html}</body>
	</html>`;
	response.end(output);
}

const server = http.createServer(requestHandler)

server.listen(port, (err) => {
	if (err) {
		return console.log('something bad happened', err)
	}
	console.log(`server is listening on ${port}`)
})
```

## Settings For Code Splitting

there is example in examples/code-splitting folder on github.

### rollup.config.js

```javascript
import svelte from 'rollup-plugin-svelte';
import resolve from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import livereload from 'rollup-plugin-livereload';
import { terser } from 'rollup-plugin-terser';

const production = !process.env.ROLLUP_WATCH;

export default [
	//browser bundel for code splitting
	{

		input: 'src/main.js',
		output: {
			sourcemap: true,
			format: 'es',
			dir: 'public/module/'
		},
		plugins: [
			svelte({
				// enable run-time checks when not in production
				dev: !production,
				// we'll extract any component CSS out into
				// a separate file - better for performance
				css: css => {
					css.write('public/build/bundle.css');
				},
				hydratable: true
			}),

			// If you have external dependencies installed from
			// npm, you'll most likely need these plugins. In
			// some cases you'll need additional configuration -
			// consult the documentation for details:
			// https://github.com/rollup/plugins/tree/master/packages/commonjs
			resolve({
				browser: true,
				dedupe: ['svelte']
			}),
			commonjs(),

			// In dev mode, call `npm run start` once
			// the bundle has been generated
			!production && serve(),

			// Watch the `public` directory and refresh the
			// browser on changes when not in production
			!production && livereload('public'),

			// If we're building for production (npm run build
			// instead of npm run dev), minify
			production && terser()
		],
		watch: {
			clearScreen: false
		}
	}

];

function serve() {
	let started = false;

	return {
		writeBundle() {
			if (!started) {
				started = true;

				require('child_process').spawn('npm', ['run', 'start', '--', '--dev'], {
					stdio: ['ignore', 'inherit', 'inherit'],
					shell: true
				});
			}
		}
	};
}
```

### routes.js (which hold all your route in json object)

```javascript
import AdminLayout from './views/layouts/Admin.svelte';
import PublicLayout from './views/layouts/Public.svelte';
import Page404 from './views/pages/404.svelte';

const routes = {
	groupGuard: [
		{
			url: [/^members/],
			with: async function (routerData, route) {
				return true;
			},
			redirectOnFail: function (routerData, route) {
				return '/dashboard';
			}
		}

	],
	routes: [
		{
			name: "dashboard",
			url: [/^dashboard$/, /^\s*$/],
			guard: {
				with: async function (routerData, route) {
					return true;
				},
				redirectOnFail: '/404'
			},
			layout: AdminLayout,
			component: async function () {
				let com = await import('./views/pages/Dashboard.svelte');
				return com.default;
			}
		},
		{
			name: "members",
			url: [/^members/],
			searchFilter: async function (routerData, route) {
				return true;
			},
			guard: {
				with: async function (routerData, route) {
					return true;
				},
				redirectOnFail: '/404'
			},
			layout: AdminLayout,
			component: async function () {
				let com = await import('./views/pages/MembersList.svelte');
				return com.default;
			}
		},
		{
			name: "404",
			url: [/^404$/],
			layout: PublicLayout,
			component: Page404
		}
	]
};

export { routes }
```

#### As you can see the difference in above example

instead of import required component from the start and direct assign into component

we are loading it dynamically using function, only when that route is called

without code splitting its look like this

```javascript
import Dashboard from './views/pages/Dashboard.svelte';

{
	component: Dashboard
}

```

with code splitting its like this

```javascript
{
	component: async function () {
				let com = await import('./views/pages/Dashboard.svelte');
				return com.default;
			}
}
```


### public/index.html

as you notice instead of loading the bundel.js 

now we are loading main.js which is main entry point of application which load other chunk dynamically.

```html
<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset='utf-8'>
	<meta name='viewport' content='width=device-width,initial-scale=1'>
	<title>Svelte app</title>
	<link rel='icon' type='image/png' href='http://localhost:5000/favicon.png'>
	<link rel='stylesheet' href='http://localhost:5000/global.css'>
	<link rel='stylesheet' href='http://localhost:5000/build/bundle.css'>
	<script type="module" src='http://localhost:5000/module/main.js'></script>
</head>

<body>
</body>

</html>
```


## Settings For Server Side Rendering (SSR) + Code Splitting

**If you are using Code Splitting + SSR, then you also need to change the rollup.config.js for SSR part**

**All other setting for SSR and Code Splitting remain as it is**

### rollup.conig.js

as you can see now instead of output single file now we also change to directory **public/ssr/** which hold the App.js file and chunk files

```javascript
//server ssr bundel
{
	input: "src/App.svelte",
	output: {
		sourcemap: false,
		format: "cjs",
		name: "app",
		dir: "public/ssr/"
	},
	plugins: [
		svelte({
			generate: "ssr"
		}),
		resolve(),
		commonjs(),
		production && terser()
	]
}
```

### node.js server

you can use any server of your choice 

```javascript
onst http = require('http');
const App = require('./public/ssr/App.js');
const port = 3000

const requestHandler = (request, response) => {
	let url = 'http://' + request.headers.host + request.url;
	const { head, html, css } = App.render({ url: url });
	let output = `<!DOCTYPE html>
	<html lang="en">
	<head>
		<meta charset="UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1.0">
		<title>Document</title>
		<link rel='stylesheet' href='http://localhost:5000/global.css'>
		<link rel='stylesheet' href='http://localhost:5000/build/bundle.css'>
		<script type="module" src='http://localhost:5000/module/main.js'></script>
	</head>
	<body>${html}</body>
	</html>`;
	response.end(output);
}

const server = http.createServer(requestHandler)

server.listen(port, (err) => {
	if (err) {
		return console.log('something bad happened', err)
	}
	console.log(`server is listening on ${port}`)
})
```






