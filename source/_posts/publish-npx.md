---
title: Steps to publish an npm package
date: 2019-11-08 16:14:30
tags:
  - npm
  - npx
  - javascript
---

The purpose of this entry is to provide very straight-forward steps that should be followed in order to publish an npm executable package (npx).

For demonstration pusposes, we will publish a very basic package that prompts for a user name and greets the user. It would be used like this:

```bash
✗ npx npm-says-hello
npx: installed 1 in 3.068s
Hey, what's your name?
Santa
hello, Santa
```

### Prerequisites

- You should have an **npm account**.  
  If you don't, create one here: https://www.npmjs.com
- The package can only be published if it has a corresponding git remote repository. This is why it's better to **run all the following commands inside a new, empty repository**. Create a new repository and give it the name you wish to give to the npm package. In our examples, we will use `npm-says-hello` for the package name.

### Create the npm package

Inside the repository root folder, run the following command:  
`✗ npm init`  
This will create a `package.json` file. Just accept the defaults.

Congratulations, the package is now ready to be published :). It doesn't do anything but well, here it is. The next "bonus" sections add some functionality to it, but you can also skip to the "publish" section directly.

### Bonus 1: add some basic functionality

Let's add a shell file that greets us so that when we'll run this package, npm will say "hi".

Add a `sayhello.sh` file in the same location as `package.json`. You can paste the following code inside, what it does is verify if the program was given an argument and if not, request for one. Then it greets the given name.

``` bash
NAME=$1
if [ $# -eq 0 ] ; then echo "Hey, what's your name? "; read NAME ; fi
echo "hello, $NAME"
```

To specify that this file will be our main execution entry point, add the following value to the `package.json` file:
`"bin": "sayhello.sh"`

Your `package.json` should now look something like the following:  
(with "npm-says-hello" replaced by you folder name)

``` json
{
  "name": "npm-says-hello",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "bin": "sayhello.sh",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/i-bar/npm-says-hello.git"
  },
  "author": "i-bar",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/i-bar/npm-says-hello/issues"
  },
  "homepage": "https://github.com/i-bar/npm-says-hello#readme"
}
```

### Bonus 2: add a brief description

We can also add a `README.md` file to get a nice package description. Add in again in the project root, i.e. the same location where the package.json was created.  
We can add a few lines to it:

```markdown
## npm says hello
### How to use
In bash:
`npx npm-says-hello`
```

### Test the package locally

Npm provides a means to pack and test our changes locally. These are the steps to do this:

- Run the following command in the same root folder:  
  `✗ npm pack`  
  This will create an archived version of the package and store it in the same root folder. In our example, it will create the file `npm-says-hello-1.0.0.tgz`.
- Use the archived package like you would use the published version. Run the following command: 
  `✗ npx npm-says-hello-1.0.0.tgz`
  
  The output should look like the following:

```shell
✗ npx npm-says-hello-1.0.0.tgz
npx: installed 1 in 3.068s
Hey, what\'s your name?
Santa
hello, Santa
```

### Publish the package

Again in the root package, run the following:  
`✗ npm publish`

If the command is successful, the package should be available at https://www.npmjs.com/package/npm-says-hello.  
To test if it works, we can run `✗ npx npm-says-hello` and we should get the same output as when we ran it locally.

### Troubleshooting

The publish command will fail in one of the following cases:

- We haven't logged into npm, case in which all we need to do is run npm login;
- We are not running the command from within a remote repository.

### Important notice! about removing a package from the npm registry
If you are just creating this package as a test, **do not forget to unpublish it within the first 72 hours**, otherwise you will have to write to the npm team and give good reasons why you want to remove it.

In order to remove a package from the registry, use the following command:  
`✗ npm unpublish npm-says-hello -f`


*Farewell :)*