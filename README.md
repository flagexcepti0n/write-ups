# write-ups
This is the repository where we publish all of our write-ups! feel free to read them here or go to https://fx0.fr/writeups

<br/>

## Providing this repository with new write-ups
### [Creating a new working area for the CTF](https://github.com/flagexcepti0n/write-ups#creating-a-new-working-area-for-the-ctf-1)
### [Add your Write-ups](https://github.com/flagexcepti0n/write-ups#add-your-write-ups-1)
### [Merging your Write-ups](https://github.com/flagexcepti0n/write-ups#merging-your-write-ups-1)

<br/>

# Steps

## Creating a new working area for the CTF

### Creating a new branch (Or a new repo)
Usually, providing write-ups during the time of the CTF is **forbidden**, so you might want to **fork this repository** and put it in **private** to publish your write-ups
* If there's only one Fx0 team, use the github organisation private repo
* If you're alone or there's multiple Fx0 teams please create a new repo (make sure it is private)

### Add the people you're working with as collaborators
* If your team is 100% Fx0 members, you don't have to worry, every member already has access to the private repository.
* If there are some people on your team that aren't Fx0 members, add them to the repo as external colaborators
* If you're working on a private repo because there are multiple FX0 teams, please add the people on your team to your repo
* If you're alone, please do not add anybody playing on the same CTF as you are

## Add your Write-ups

### Create a new Folder named after the ctf
* Copy and paste the official name of the CTF so the name perfectly match
* Try matching the naming conventions of the other folders present on the repo
* Make sure it is as much humanly readable as possible (you might want to tweak how it is formated if not)

### Create new Files for the challenges
* Please name the files so that they match the name of the challenge.
* Try matching the naming conventions of the other folders present on the repo
* Make sure it is as much humanly readable as possible (you might want to tweak how it is formated if not)
* All files should end with the extention `.md` (Markdown)
* They should also be placed into the folder of the CTF they belong to

## Merging your Write-ups
Merging your write-ups will require a bit of git magic so follow these steps carefully

Fisrt, start by adding this repo to your git origins and fetching it
```sh
# add this repo to your origins and call it "upstream"
git remote add upstream https://github.com/flagexcepti0n/write-ups.git

#fetch the branches of the upstream repo
git fetch upstream
```

Then we'll merge your branch into the main of this repo (**You can also use a GUI interface for this step**)
```sh
#merge your branch into upstream/main
git checkout upstream/main
git merge new-ctf-branch
```

Congratulation, you now just need to push your changes to origin
```sh
git push
```
