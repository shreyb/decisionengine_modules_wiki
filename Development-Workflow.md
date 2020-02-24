Development workflow
====================

For code review I suggest Google review board. Currently located at  https://fermicloud140.fnal.gov/reviews. Its advantage over GitHub review is that it is far more powerful. 

Go to this site and register. Accessible from Fermilab network only. So need VPN for off-site access. 

Install RBTools 

```
yum install RBTools
```

Optionally (if you work with the same repository most of the time):

Edit `~/.reviweboardrc`

```
echo "REPOSITORY = \"git://github.com/HEPCloud/decisionengine_modules.git\"" > ~/.reviewboardrc
```

Edit `~/.gitconfig` by adding:

```
[reviewboard]
        url = https://fermicloud140.fnal.gov/reviews
```

Suggested development cycle 
===========================

```
 git clone https://github.com/HEPCloud/decisionengine_modules
 git pull
 git checkout -b <my branch>
``` 

Work on you branch, periodically rebase from master 

```
 git rebase master 
```

When ready to post, do 

```
git commit -a 
```

Post patch for review:

```
rbt post --guess-description --summary="summary" --testing-done="how I tested" --disable-ssl-verification
```


If you do not wish to have pre-set default in `~/.reviewboardrc` or `~/.gitconfig`, then include repository on the command line: 

```
rbt post -d --server=https://fermicloud140.fnal.gov/reviews/ --guess-description --summary="ModuleName: patch description"  --repository-url=git://github.com/HEPCloud/decisionengine_modules.git
```

The command will ask your username / password at reviewboard and will spit the URL of your
review request, e.g. https://fermicloud140.fnal.gov/reviews/r/NUMBER/
. Cut&paste it into your browser. Finish review request by checking that diff looks good 
(upper right corner tabs). Fill in necessary additions to Description and Testing. Add Group name "decisionengine"
 under "Reviewers" and click "Publish"

An e-mail be sent to members of "decisionengine" group, and someone will review the request. IF you need to modify
your review. Commit the change locally and run:

```
 rbt post -r NUMBER   
```

or 

```
rbt post -r NUMBER  --server=https://fermicloud140.fnal.gov/reviews/ --repository-url=git://github.com/HEPCloud/decisionengine_modules.git
```

This will post another diff to https://fermicloud140.fnal.gov/reviews/r/NUMBER. Visit the page furnish it with details of your new commit and click publish. 

When review is approved. 

``` 
git checkout master
git pull 
git checkout <my branch>
git rebase master
git checkout master 
git merge --squash <my branch>
git commit -a 
```
Above command normally opens commit message edit dialog. When writing commit message I suggest:

   1. Remove all intermediate commit messages you might have made while developing your patch
   1. Use concise statement to summarize patch. 
   1. Write details in the next line
   1. add reference to rebview board?. 

Here is an example:  

```
decisionengine/logicengine: python3 compliance changes 

Replaced string.join() where appropriate 

  RB : https://fermicloud140.fnal.gov/reviews/r/NUMBER/

```  
Then push to the remote:

```
git push origin HEAD 
```

Bug fixes to frozen branch
==========================

Assumption is: you have fixed the bug and pushed it to master using above procedure. Now we need to 
have a "production" branch patched. 

```
git checkout <branch>
git pull <branch>
git checkout -b fix/<branch>/<rb number>
git cherry-pick -x <HASH of the bugix commit to master>
```

Fix conflicts if any 

```
git push <remote> HEAD
```

Proceed to Web interface and create pull request against `<branch>`. `<remote>` is the name of your remote repository. For instance in my case:

```
[litvinse@orbis decisionengine]$ git remote -v 
litvinse	git@github.com:DmitryLitvintsev/decisionengine_modules.git (fetch)
litvinse	git@github.com:DmitryLitvintsev/decisionengine_modules.git (push)
origin	https://github.com/HEPCloud/decisionengine_modules.git (fetch)
origin	https://github.com/HEPCloud/decisionengine_modules.git (push)
```
The name of remote repository is `litvinse`. 

 

  
