# sfdx-travisci-package [![Build Status](https://travis-ci.org/forcedotcom/sfdx-travisci-package.svg?branch=master)](https://travis-ci.org/forcedotcom/sfdx-travisci-package)

For a fully guided walk through of setting up and configuring this sample, see the [Continuous Integration Using Salesforce DX](https://trailhead.salesforce.com/modules/sfdx_travis_ci) Trailhead module.

This repository shows one way you can successfully setup Salesforce DX to create new package versions with Travis CI. We make a few assumptions in this README:

- You know how to get your Github repository setup with Travis CI. (Here's their [Getting Started guide](https://docs.travis-ci.com/user/getting-started/).)
- You've installed the [Travis CLI](https://github.com/travis-ci/travis.rb#installation). 
- You have properly setup JWT-Based Authorization Flow (i.e. headless). I recommend using [these steps for generating your Self-Signed SSL Certificate](https://devcenter.heroku.com/articles/ssl-certificate-self). 

If any any of these assumptions aren't true, the following steps won't work.

## Getting Started

1) Make sure you have the Salesforce CLI installed. Check by running `sfdx force --help` and confirm you see the command output. If you don't have it installed, you can download and install it from [here](https://developer.salesforce.com/tools/sfdxcli).

2) [Fork](http://help.github.com/fork-a-repo/) this repo into your github account using the fork link at the top of the page.

3) Clone your forked repo locally: `git clone https://github.com/<git_username>/sfdx-travisci.git`

4) Setup a JWT-based auth flow for the target orgs that you want to deploy to.  This step will create a server.key file that will be used in subsequent steps.
(https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_jwt_flow.htm)

5) Confirm you can perform a JWT-based auth: `sfdx force:auth:jwt:grant --clientid <your_consumer_key> --jwtkeyfile server.key --username <your_username> --setdefaultdevhubusername`

   **Note:** For more info on setting up JWT-based auth see [Authorize an Org Using the JWT-Based Flow](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_jwt_flow.htm) in the [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev).

6) From your JWT-Based connected app on Salesforce, retrieve the generated `Consumer Key`.

7) Set your `Consumer Key` and `Username` using the Travis CLI. Note that this username is the username that you use to access your Dev Hub.

    travis env set USERNAME <your_username>
    travis env set CONSUMERKEY <your_consumer_key>

8) Locate your `server.key` that you generated previously and keep track of its location.

9) Open the `.travis.yml` file and remove the first line that starts with `openssl ...` and save the file.

10) From the root folder of your local project, encrypt your `server.key` value:
    `cd your_project_location`
    `travis encrypt-file your_key_location/server.key assets/server.key.enc --add`

    This should have replaced the existing server.key.enc with your encrypted version.

11) Copy all the contents of package-sfdx-project.json into sfdx-project.json and save.

12) Create the sample package running this command `sfdx force:package:create --path force-app/main/default/ --name "Travis CI" --description "Travis CI Package Example" --packagetype Unlocked`

13) Create the first package version `sfdx force:package:version:create --package "Travis CI" --installationkeybypass --wait 10 --json --targetdevhubusername HubOrg`

14) In the .travis.yml Update the value in the `PACKAGENAME` variable to be Package Id in your sfdx-project.json file.  This id will start with 0Ho.

15) Commit the updated `.travis.yml`,`server.key.enc`, and `sfdx-project.json` files.

And you should be ready to go! Now when you commit and push a change, your change will kick off a Travis CI build.

Enjoy!

## Contributing to the Repository ###

If you find any issues or opportunities for improving this repository, fix them!  Feel free to contribute to this project by [forking](http://help.github.com/fork-a-repo/) this repository and make changes to the content.  Once you've made your changes, share them back with the community by sending a pull request. Please see [How to send pull requests](http://help.github.com/send-pull-requests/) for more information about contributing to Github projects.

## Reporting Issues ###

If you find any issues with this demo that you can't fix, feel free to report them in the [issues](https://github.com/forcedotcom/sfdx-travisci-package/issues) section of this repository.