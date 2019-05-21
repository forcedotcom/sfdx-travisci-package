# sfdx-travisci-package [![Build Status](https://travis-ci.org/forcedotcom/sfdx-travisci-package.svg?branch=master)](https://travis-ci.org/forcedotcom/sfdx-travisci-package)

For a fully guided walkthrough of setting up and configuring continuous integration using scratch orgs and Salesforce CLI, see the [Continuous Integration Using Salesforce DX](https://trailhead.salesforce.com/modules/sfdx_travis_ci) Trailhead module.

This repository shows one way you can successfully use scratch orgs to create new package versions with Travis CI. We make a few assumptions in this README. Continue only if you have completed these critical configuration prerequisites.

- You know how to set up your GitHub repository with Travis CI. (Need help? See the Travis CI [Getting Started guide](https://docs.travis-ci.com/user/getting-started/).)
- You've installed the [Travis CLI](https://github.com/travis-ci/travis.rb#installation). 
- You have properly set up JWT-based authorization flow (headless). We recommend using [these steps for generating your self-signed SSL certificate](https://devcenter.heroku.com/articles/ssl-certificate-self). 

## Warning to Windows Users
Travis CI has a [decryption issue you should check to see if has been resolved](https://github.com/travis-ci/travis-ci/issues/4746) before proceeding.

## Getting Started

1) Make sure that you have Salesforce CLI installed. Run `sfdx force --help` and confirm you see the command output. If you don't have it installed, you can download and install it from [here](https://developer.salesforce.com/tools/sfdxcli).

2) [Fork](http://help.github.com/fork-a-repo/) this repo to your GitHub account using the fork link at the top of the page.

3) Clone your forked repo locally: `git clone https://github.com/<git_username>/sfdx-travisci.git`

4) Setup a JWT-based auth flow for the target orgs that you want to deploy to. This step creates a `server.key` file that is used in subsequent steps.
(https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_jwt_flow.htm)

5) Confirm that you can perform a JWT-based auth: `sfdx force:auth:jwt:grant --clientid <your_consumer_key> --jwtkeyfile server.key --username <your_username> --setdefaultdevhubusername`

   **Note:** For more info on setting up JWT-based auth, see [Authorize an Org Using the JWT-Based Flow](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_jwt_flow.htm) in the [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev).

6) From your JWT-based connected app on Salesforce, retrieve the generated `Consumer Key`.

7) Set your `Consumer Key` and `Username` using the Travis CLI. Note that this username is the username that you use to access your Dev Hub.

    travis env set USERNAME <your_username>\
    travis env set CONSUMERKEY <your_consumer_key>

8) Locate the generated `server.key` and keep track of its location.

9) Open the `.travis.yml` file and remove the first line that starts with `openssl ...`, then save the file.

10) From the root folder of your local project, encrypt your `server.key` value:
    `cd your_project_location`
    `travis encrypt-file your_key_location/server.key assets/server.key.enc --add`

    This step replaces the existing `server.key.enc` with your encrypted version.

11) Copy all the contents of `package-sfdx-project.json` into `sfdx-project.json` and save.

12) Create the sample package running this command: `sfdx force:package:create --path force-app/main/default/ --name "Travis CI" --description "Travis CI Package Example" --packagetype Unlocked`

13) Create the first package version: `sfdx force:package:version:create --package "Travis CI" --installationkeybypass --wait 10 --json --targetdevhubusername HubOrg`

14) In the `.travis.yml`: Update the value in the `PACKAGENAME` variable to be Package ID in your `sfdx-project.json` file.  This ID starts with `0Ho`.

15) Commit the updated `.travis.yml`,`server.key.enc`, and `sfdx-project.json` files.

Now you're ready to go! When you commit and push a change, your change kicks off a Travis CI build.

Enjoy!

## Contributing to the Repository ###

If you find any issues or opportunities for improving this repository, fix them! Feel free to contribute to this project by [forking](http://help.github.com/fork-a-repo/) this repository and making changes to the content. Once you've made your changes, share them back with the community by sending a pull request. See [How to send pull requests](http://help.github.com/send-pull-requests/) for more information about contributing to GitHub projects.

## Reporting Issues ###

If you find any issues with this demo that you can't fix, feel free to report them in the [issues](https://github.com/forcedotcom/sfdx-travisci-package/issues) section of this repository.
