# Riotz Configs :: Project :: CircleCI for Node.js

This is a CircleCI with Node.js(inc. TypeScript) configuration commonly used in the Riots Works projects.


## Overview
This is a project-specific configuration for CircleCI with Node.js project.  
Users of this repository are assumed to be engineers of Riotz Works.  

Though we intend to utilize this repository as an internal use of Riotz Works, there is no restriction on using this if it's useful to you.  


*Why is this repository necessary?*  
Even in repositories of the same programming language and/or architecture, build scripts can be different.
However, we think that setting of the CI and steps of the build to be executed can be abstracted and generalized.
If the steps of the build are managed by each repository, gaps will gradually occur. And sometimes it could be a serious situation that cannot be properly managed anymore.  
The build step is unified to solve the problem. And only scripts to be executed in the step are managed in each repository  


## Requirements
Requires the following to use:
- [Git](https://git-scm.com/) 2.14+
- [Node.js](https://nodejs.org/) 6.11+
- [CircleCI](https://circleci.com/)


## Usage
### Getting Started
In the directory of the project to be developed with Node.js and CircleCI, follow the steps below to add common settings.
- Add this repository as a remote repository to the project
- Add the tree as `subtree add --prefix=.circleci` in the project
- Create `scripts` of` package.json` corresponding to build of CircleCI

For example, the following command:
```console
git remote add -f circleci git@github.com:riotz-works/riotz-configs-project-circleci-nodejs.git
git subtree add --squash --prefix=.circleci circleci master
git push
```

An example of `scripts` in the corresponding` package.json` is as follows.
```json
  "scripts": {
    "setup": "npm install && npm dedupe",
    "validate": "echo skip validate",
    "build": "tslint -p tsconfig.json && tsc -p tsconfig.json",
    "test": "echo skip test",
  },
```

This change is pushed to the repository and the source code is shared to other engineers.  
However, the configuration file added with the `git remote add` command is a local repository setting and is not shared to other engineers.  
If another engineers receives an update or updates the sub tree, it is necessary to make `git remote add` for each engineers.  


### Upgrade
To receive this repository upgrade, update the subtree in the project's directory with the `--squash` option.
For example, the following command:
```console
git subtree pull --squash --prefix=.circleci circleci master
git push
```


## Contribution
This repository is assumed to be used by engineers of Riotz Works.  
However, feedbacks are welcome for improvement of this repository. For more information please see [CONTRIBUTING](/.github/CONTRIBUTING.md).  


## Licence
This repository and deliverables are published under the MIT license. For the full license text please see [LICENSE](/LICENSE).  
© Riots Works  
