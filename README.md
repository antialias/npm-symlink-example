# npm-symlink-example

This repo has branches illustrating three different scenarios.

For each branch, attempt this: 
```sh
node a
```

### not-working-no-self-symlink
```
.
├── a
│   ├── index.js
│   └── node_modules
│       └── b -> ../../b
└── b
    ├── bBar.js
    └── bFoo.js
```

This scenario models the usage of `npm link b`. It will not work because `a/node_modules/b` is a symlink to b. When `b/bFoo.js` requires `b/bBar.js`, the path `b/bBar.js` is resolved from the perspective of `bFoo.js`'s actual path, not the path arrived at through the symlink at `a/node_modules/b`. The module path resolution algorithm does not look inside of `a/node_modules` and find a module called `b` because `a/node_modules` is not accessible by traversing up the directory tree from the actual path of `b/bFoo.js`

### working-b-actually-inside-a-node_modules
```
.
└── a
    ├── index.js
    └── node_modules
        └── b
            ├── bBar.js
            └── bFoo.js
```
This scenario models a straight- up npm install of a, (assuming package.json lists b as a dependency of a). This will work because b is actually installed inside of `a/node_modules` and is not just a symlink.  

### working-with-self-symlink
```
.
├── a
│   ├── index.js
│   └── node_modules
│       └── b -> ../../b
└── b
    ├── bBar.js
    ├── bFoo.js
    └── node_modules
        └── b -> ..
```
This scenario models the solution to the problem presented in `not-working-no-self-symlink`. Notice the symlink `b/node_modules/b` that points back to `b`. This allows files that are in b to use module-relative paths without the requirement that b actually be installed inside of another module.
