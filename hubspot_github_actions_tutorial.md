# Github Action

##

## Benefits of using Github Actions

Working in a team, there is the chance that several people could be editing a file at the same time which could lead to overwritten files.
For example let's say we have 2 developers working on a site.  They are both using the local development tools.  Developer one adds a block of code to the `styles.css` file.  The CLI notices the change and pushes the changes to the portal.  Developer two also adds a block to the css file and the CLI pushes her code.  But, when she pushes her code, the cli does not look to see if there were changes to the file on hubspot and throw up a warning.  Instead, pushes her version of the and overwriting developer 1's changes.  

If we introduce an automated action that is triggered by a push to github, we can mitigate this issue.