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
-Within the repo on github, click on the actions link
-Select the node.js workflow
-Rename to something meaningful to you eg `hubspot master branch deploy`
-update the trigger
   I build a lot of my code in my developer portal and have a development branch for it. When I push to the development branch, there is an action that is triggered only by a push to that branch
   An example of what the code looks like
   ```on: 
  push:
    branches: 
      - development```
      