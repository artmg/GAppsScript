# Clasp

The Command Line utility for Apps ScriPt allows you to use more mature software engineering practices usch and controlled versioning and deployment.

## Installing Clasp

As Apps Script development uses JavaScript, it is logical that the `clasp` Command Line Apps Script Program(?) uses the JavaScript runtime called Node.JS. Node.JS is a widely used platform, so check first in case you have it already.

```
node -v
npm -v
```

If not, we recommend the ‘long term stable’ version that avoids excessive updates and issues, use your preferred package manager to install it, e.g. `choco install nodejs-lts` or e.g. `brew install node@18` (see other versions in https://nodejs.dev/en/about/releases/ and https://formulae.brew.sh/formula/node ). For over three years now clasp's version constraint in `package.json` has been `"node": "^12.20.0 || ^14.13.1 || >=16.0.0"`. If you prefer to manage your local node versions with `nvm` then:

```
nvm ls
nvm install --lts
nvm use --lts
```


Once you have node.JS and its `npm` package manager, then you can install clasp:

```
# optionally check if you have an installed version of clasp already
# npm list -g @google/clasp

npm install -g @google/clasp

# optionally update the package if had already installed an older version
# npm update -g @google/clasp
```

## clasp login

```
# Authenticate clasp with the Google account you want to use
clasp login
# help - clasp command reference - https://github.com/google/clasp
```

* use the url to authorise Clasp to access the main account into which Calendar you want to sync others
* if you are happy to grant this Google utility the permissions displayed, then click **Allow**
	* this will create save the OAuth2.0 authentication token into the Clasp global settings: ` ~/.clasprc.json `

By inspecting the contents of that file, you can see which resources it grants access to (token scope). However, there is no obvious way to see which account was used to obtain that token.

### multiple accounts

There is an alternative way to be logged into different accounts for different clasp project folders on the same PC, but it is more complex to set up and manage

Instructions TBC

## next

* Enable the Apps Script API in your account https://script.google.com/home/usersettings 
* Check for any scripts in your account: https://script.google.com/home/all 
* `clasp clone scriptid` any that are not yet in the repo
* Note that any projects should have a .gitignore file to disregard the `.clasp.json` files, as the link between projects and their scripts should be per-environment and may vary. These will be set up on the first clasp clone

Now you are set up to push and pull with git and then reflect the changes the other way with clasp.

_Do we need to change ‘clasp settings’ to handle multiple containers?_

As an alternative to manually using clasp on larger projects, consider gasgit.

## Clasp and Git

Ensure you are logged in in the right Google User context for the project you want to work on. To diagnose the current clasp context see `~/.clasprc.json`

### Initial clone

* Pick a shortname for the doc/sheet containing the script project
* Get the ID from the Container (URL or Project Settings)
* Add the section to environments.md using ID and shortname

```
mkdir shortname
cd shortname
clasp clone ID
# this ensures that the .clasp.json goes into the container subfolder
```

### Development Workflow

#### Online script editor

Develop on Google servers using their cloud-based script IDE

```
Github    Google       Github
   \     /      \     /
    Local   v^   Local
           Edit
```

* Open a command prompt in your local repo folder
* Check which environment is current
	 - See environments.md
* Get up to date with the upstream code
    - git pull
* Make sure your local repo matches the code in projects
    - clasp pull
See if there was any delta
    - git status
	- You can push it if you want …
* Or you can worry about it later and go back to upstream
    - git stash
* Get ready to do your code modification
    - clasp push
Now you can edit using the online Script Editor

### Clasp Pull issues

If you get confusion between .gs files and .js files when you `clasp pull` you may need to set the fileextension first!

```zsh
clasp login
clasp clone ID
```
Edit your `.clasp.json` file to include an extra option:  ` "fileExtension": "gs" `
```zsh
rm *.js *.gs
clasp pull
git status
```


### VSCode

```
Github    Google    Github
   \     /         /
    Local   ------/
      v^  
     Edit
```
Use the Microsoft free client IDE on your own PC working on local files

## Reconnect to repo

It you start working on a fresh local clone of the git repo, your clasp environment may not be set up. You might get the error `No valid .clasp.json project file.`

```
## check any deployments
clasp deployments

## check if you are logged in
clasp login --status
# a fresh PC may have no default credentials in: ~/.clasprc.json.

# Authenticate clasp with browser, profile and Google account you use
clasp login
# paste in the URL if needed, choose the account and Allow

# repeat the Initial Clone from above
cd shortname
clasp clone ID
# this ensures that the .clasp.json goes into the container subfolder
```

**NOTE**: 

`clasp clone` will also `clasp pull` automatically. 
This **WILL overwrite** any local copy of your scripts from the GCP project. 

If there are any changes you may need to stash these and/or switch branches 
to get the local code version you need. to pick up your work. 

### Alternative for managed environments

As an alternative you might consider simply forcing the creation of `.clasp.json` as proposed in https://stackoverflow.com/q/58645417 to contain `{"scriptId":"<id>","rootDir":"<pwd>"}`

If you manage your environments using `clasp setting` then the initial project 'creation' can be as simple as

```sh
if [[ ! -f .clasp.json ]]; then echo "{\"scriptId\":\"not-set\",\"rootDir\":\"$(pwd)\"}" > .clasp.json ; fi
# tested with bash, sh and zsh
```

## What code is in my environment

If your release management processes do not have a means to track commit versions per environment, you may need to reverse engineer this.

* Create a new branch
* make sure your `clasp settings` point to the relevant environment
* `clasp pull` to bring the code into your local
* ?? how to get git to tell you which commit the current checkout matches ??
	* _Compare_ current code against multiple commits in git repo 
	* see https://stackoverflow.com/q/31426506
* 

## Alternatives

Instead of using clasp and local github repos, you might prefer a simpler workflow using the [Google Apps Script GitHub Assistant chrome extension](https://chrome.google.com/webstore/detail/google-apps-script-github/lfjcgcmkmjjlieihflfhjopckgpelofo). Note, however, that the pull from github just does a direct overwrite of Apps Script files, not a merge.

