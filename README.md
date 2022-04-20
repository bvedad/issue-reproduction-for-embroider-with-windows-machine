# windows-machine-projects
## Explanation of the problem
The problem with using the `ember-consumer-app` application on Windows machines when we are using multiple packages that contain `ember-addon-wai` (`ember-addon-outter` and `ember-addon-outter-2`) is that it always throws an error related to `ember-auto-import` and `embroider`. After a deeper look conclusion was made that `ember-addon-wai` can not be consumed by two libraries (`ember-addon-outter` and `ember-addon-outter-2`) that are used by the main application (`ember-consumer-app`) at the same time. That is because `ember-addon-wai` contains `ember-auto-import` and `@embroider/macros` with their configurations inside the index.js file:

```js
options: {
  '@embroider/macros': {
    setOwnConfig: {},
  },
},
```
 
If we have multiple combinations of dependencies and configurations mentioned above it is going to crash on Windows machines. I did not find the reason why that is happening at all on Windows but I have created an application that is using two custom addons and each of the addons is having a dependency on another custom addon that consumes the combination mentioned above. We have created a custom solution that is representing this issue. It has the following projects:

- ember-consumer-app
    - ember-addon-outter
        - ember-addon-wai
    - ember-addon-outter-2
        - ember-addon-wai

## CI representing the issue
CI has been set up to start the project on Ubuntu and Windows machines in order to represent different behaviours and issue on Windows machines. Also there is an MR which is showing how `embroider` configuration inside `index.js` file of `ember-addon-wai` solve the issue [MR: Options config removed from index.js of ember-addon-wai](https://github.com/bvedad/issue-reproduction-for-embroider-with-windows-machine/pull/1)

## Instructions to run the project
To reproduce the issue execute the following steps:
```bash
cd ember-consumer-app
yarn install
yarn start
```

**Expected result**: the bug will occur related to ember-auto-import in the console.


## Logs of the bug
```
PS C:\Users\vedadburgic\repos\issue-reproduction-for-embroider-with-windows-machine\ember-consumer-app> yarn start
yarn run v1.22.15
$ ember serve

Running without permission to symlink will degrade build performance.
See https://cli.emberjs.com/release/appendix/windows/ for details.

Build Error (broccoli-persistent-filter:Babel > [Babel: ember-addon-wai]) in ember-addon-wai/components/my-component.js

Duplicate plugin/preset detected.
If you'd like to use two separate instances of a plugin,
they need separate names, e.g.

  plugins: [
    ['some-plugin', {}],
    ['some-plugin', {}, 'some unique name'],
  ]

Duplicates detected are:
[
  {
    "alias": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js",
    "options": {
      "MARKER": "eaimeta@70e063a35619d71f"
    },
    "dirname": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app",
    "ownPass": false,
    "file": {
      "request": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js",
      "resolved": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js"
    }
  },
  {
    "alias": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js",
    "options": {
      "MARKER": "eaimeta@70e063a35619d71f"
    },
    "dirname": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app",
    "ownPass": false,
    "file": {
      "request": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js",
      "resolved": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js"
    }
  }
]Error: Duplicate plugin/preset detected.
If you'd like to use two separate instances of a plugin,
they need separate names, e.g.

  plugins: [
    ['some-plugin', {}],
    ['some-plugin', {}, 'some unique name'],
  ]

Duplicates detected are:
[
  {
    "alias": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js",
    "options": {
      "MARKER": "eaimeta@70e063a35619d71f"
    },
    "dirname": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app",
    "ownPass": false,
    "file": {
      "request": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js",
      "resolved": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js"
    }
  },
  {
    "alias": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js",
    "options": {
      "MARKER": "eaimeta@70e063a35619d71f"
    },
    "dirname": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app",
    "ownPass": false,
    "file": {
      "request": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js",
      "resolved": "C:\\Users\\vedadburgic\\repos\\issue-reproduction-for-embroider-with-windows-machine\\ember-consumer-app\\node_modules\\ember-auto-import\\js\\analyzer-plugin.js"
    }
  }
]
    at assertNoDuplicates (C:\Users\vedadburgic\repos\issue-reproduction-for-embroider-with-windows-machine\ember-consumer-app\node_modules\@babel\core\lib\config\config-descriptors.js:239:13)
    at createDescriptors (C:\Users\vedadburgic\repos\issue-reproduction-for-embroider-with-windows-machine\ember-consumer-app\node_modules\@babel\core\lib\config\config-descriptors.js:147:3)
    at createDescriptors.next (<anonymous>)
    at createPluginDescriptors (C:\Users\vedadburgic\repos\issue-reproduction-for-embroider-with-windows-machine\ember-consumer-app\node_modules\@babel\core\lib\config\config-descriptors.js:138:17)
    at createPluginDescriptors.next (<anonymous>)
    at C:\Users\vedadburgic\repos\issue-reproduction-for-embroider-with-windows-machine\ember-consumer-app\node_modules\@babel\core\lib\config\config-descriptors.js:94:32
    at Generator.next (<anonymous>)
    at Function.<anonymous> (C:\Users\vedadburgic\repos\issue-reproduction-for-embroider-with-windows-machine\ember-consumer-app\node_modules\@babel\core\lib\gensync-utils\async.js:25:3)
    at Generator.next (<anonymous>)
    at evaluateSync (C:\Users\vedadburgic\repos\issue-reproduction-for-embroider-with-windows-machine\ember-consumer-app\node_modules\gensync\index.js:251:28)      


Stack Trace and Error Report: C:\Users\VEDADB~1\AppData\Local\Temp/error.dump.e614ffe5cc33f5055d2f524c88216185.log
```
