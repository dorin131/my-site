---
title: "How To Publish an NPM Package"
draft: false
date: 2019-12-08T12:40:00Z
slug: "how-to-publish-npm-package"
tags: ["javascript", "npm"]
---
### Introduction
In essence, publishing an npm package is just one command, but there are some things that you have to take care of before doing that.

### Step-by-Step
Here are the steps I followed before publishing my first package:
1. Create a free account on https://www.npmjs.com/.
2. Log into the npm CLI by running `npm login`.
3. Create a folder for your new package which would normally have the same name.
4. Make sure that you ran `npm init` and have all the right values filled-in in the `package.json` file.
5. Carefully choose the name, as that is going to be the name that everyone is going to use to install your package.
6. Set the version number using the semantic versioning format.
It should look something like this: “v1.2.3”. The first number is the major version and should be incremented every time you deploy a breaking change.
The second number is the minor version and should go up with every new non-breaking feature. And, lastly, we have the patch/fix number.
Also, at the same time, create a new release in GitHub (or your other VCS) with a matching version. [(Read more)](https://docs.npmjs.com/about-semantic-versioning)
7. Add a `types` field which will point to your types definition file.
You don’t have to do this step but with the rapid increase of TypeScript and better IDEs, you are doing the developer a big favor. The types file will be a *.ts file written in TypeScript and describing the types, interfaces, etc. of your package. [(Read more)](https://www.typescriptlang.org/docs/handbook/declaration-files/publishing.html#including-declarations-in-your-npm-package)
8. Specify the place where your code is hosted by filling in the repository field.
[(Read more)](https://docs.npmjs.com/files/package.json#repository)
9. Have a think about how you want to license your package and set the correct license value. If you are not sure, go to this website https://choosealicense.com/ which will make this very easy for you.
10. Check your `.gitignore` file and verify that you are not including any personal or unnecessary files in your repository.
11. Add an `.npmignore` file which will exclude specific files from your npm package. I personally have added the test files in here, as we don’t need to have them in the package.
12. Take your time to write a nice `README.md` file, where you explain to your future users how to install the package, how to use it, and maybe give some examples. The contents of this file will also appear on [this website](https://www.npmjs.com/).
13. Now you’re almost ready to publish, but before you do so, run `npm pack`, which will generate a `*.tgz` file containing all the files exactly how they will end up in your npm package.
This will let you double-check that everything has been set up correctly and you are going to publish the right thing.
14. Just before publishing, you are going to run a quick test locally. Create a new folder, initialize npm (`npm init`) and install your package with `npm install -S ./path/to/your/package`.
This will install the package from your local directory and you can try to use it as if it was already published.
15. Assuming that you have done all the steps above and it all worked as expected, you can now publish your package with `npm publish`.

### Conclusion
Congratulations, you now have a brand new npm package.

You can see your package on npm like so: https://www.npmjs.com/package/inline-webassembly