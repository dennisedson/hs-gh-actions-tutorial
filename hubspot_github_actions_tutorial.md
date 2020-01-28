# Github Action

## What is a Github Action?

At the simplest level, a github action allows you to automate aspects of your development workflow.  You can build, test and deploy your code by building a workflow.  
For this lesson, we will be creating a simple workflow that will checkout a branch from our repo, installs node,  install the hubspot cli, and push the code to hubspot
Once you have your base workflow setup, the sky is the limit in what you can do.  You can introduce webpack or gulp. You can run a pagespeed test on your site.  You can have a slack notification sent to you reporting the outcome of your workflow.

## Benefits of using Github Actions

Working in a team, there is the chance that several people could be editing a file at the same time which could lead to overwritten files.
For example let's say we have 2 developers working on a site.  They are both using the local development tools.  Developer one adds a block of code to the `styles.css` file.  The CLI notices the change and pushes the changes to the portal.  Developer two also adds a block to the css file and the CLI pushes her code.  But, when she pushes her code, the cli does not look to see if there were changes to the file on hubspot and throw up a warning.  Instead, pushes her version of the and overwriting developer 1's changes.  

If we introduce an automated action that is triggered by a push to github, we can mitigate this issue.

## Building the workflow
_should i build from scratch locally as well building on github..._

### Building on Github
- Within the repo on github, click on the actions link
- Select the node.js workflow
- Rename to something meaningful to you eg `hubspot master branch deploy`
- update the trigger
   I build a lot of my code in my developer portal and have a development branch for it. When I push to the development branch, there is an action that is triggered only by a push to that branch
   An example of what the code looks like
   ```
   on: 
     push:
       branches: 
         - development
    ```
- Within the workflow job, you can remove the strategy as we only need to run on one version of node.  

#### Breaking down the steps

The first step is another github action provided by the github team  it checks out your repository.
_*please note that it checks out the master branch so if you want to be pushing a different branch, you will need to checkout the correct branch*_
The next step is optional, but i do recommend it.  You have learned that for the hubspot cli to work, you need a config file.  This file has information that you probably dont want to be seen.  Looking at you API key
What we are going to do is 
1.  Create secrets for this repository.  Secrets are encrypted environment variables that can only be used by github actions and they are isolated to individual repositories

To add a secret:
   - go to the repository settings on github and click on "Secrets" in the sidebar
   - Click Add a new secret.  
   - Give the secret a meaningful name and paste in the value (API key for example)
   - Add secret

to reference your secret, your code would look something like this where api_key is the name of the secret
`${{ secrets.api_key }}

2.  Write a simple script that will create the config file with our secrets
from your code editor of choice.  
   - Create a folder (mine is bin)
   - Within that folder, create a new file. (mine is called create.Config.js)
   - Paste in this code
   ```
   const path = require("path");
const fs = require("fs");
const yaml = require("js-yaml");

const portalId = process.env.HUBSPOT_PORTAL_ID;
const apiKey = process.env.HUBSPOT_API_KEY;

const portal = {
  name: Production,
  portalId,
  authType: "apikey",
  apiKey
};

const config = {
  defaultPortal: "Production",
  portals: [portal]
};

fs.writeFile(
  path.join(process.cwd(), "hubspot.config.yml"),
  yml.safeDump(config)
);
```

the `portalId` and the `apiKey` variables are referencing the secrets that we add to the environment in the action file.  We will add those in the next set of steps

   - Return to your github action and we will add the following 
      ```
      - name: Create hubspot config file
              env:
                HUBSPOT_PORTAL_ID: ${{ secrets.portalID }}
                HUBSPOT_API_KEY: ${{ secrets.hsAPI }}
              run: bin/createConfig.js
      ```
As the name suggests, we are creating the config file.  Under the `env`, is where we will pull in our references to the secrets

Now that we have a a config file, it is time to add our last step: install the hubspot cli and upload our files
Directly under the last run command, add this code: 
```
      - name: Hubspot, I'm coming for you
        run: |
          npm install @hubspot/cms-cli
          npx hs upload  --portal=PORTALNAME <src> <dest>
```          
This should be looking familiar.  In this last step we have installed the cli and then run the upload command.  

the total workflow file should look something like this:
```
name: Hubspot Deploy

on:
  push:
    branches:
      - Development

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v1
      - name: Use Node.js
        uses: actions/setup-node@v1
        with:
          node-version: 12.x
      - name: Create hubspot config file
        env:
          HUBSPOT_PORTAL_ID: ${{ secrets.portalID }}
          HUBSPOT_API_KEY: ${{ secrets.hsAPI }}
        run: bin/createConfig.js
      - name: Hubspot, I'm coming for you
        run: |
          npm install @hubspot/cms-cli
          npx hs upload  --portal=bacon ../beacon beacon
        env:
          CI: true
```          

Once you have finished this, go ahead and commit your changes.  If you go to the actions tab, you should see it running and then it fails :(
We have yet to push our new bin directory up to github.  Go ahead and stash your changes locally, pull down the changes you just made on github (you will be pulling a new directory called `.github` ), add your stash and push the code
If everything is in order, you should be able to head back to your actions tab on github and see it (successfully) run!  

*Way to go champ*