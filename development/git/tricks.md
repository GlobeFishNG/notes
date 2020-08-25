# Tricks

## Change the author information of past commits

### Using --amend for the very last commit

```bash
$ git commit --amend --author="ufonion <mayongcong@gmail.com>"
```

### Using interactive rebase

* Start interactive rebase

```bash
$ git rebase -i -p 0ad14fa5
```

* Mark with "edit" for each commits which are the change targets.

```bash
pick d7893a8 Version: 0.2.0
edit 21d5600 fix scripts' mod
pick 4d0ca9e almost done
edit 3dd4753 0.4.0
pick fa455a7 ver 0.4.1
pick 6e1197f 0.5.0
pick 2a86a8f 0.5.1
pick d30b456 0.5.2
```

* Change the author of the target commits one by one

```bash
$ git commit --amend --author="ufonion <mayongcong@gmail.com>" --no-edit
$ git rebase --continue
```

### Using git filter-branch

```bash
$ git filter-branch --env-filter '
WRONG_EMAIL="wrong_user@wrong_email_server.com"
NEW_NAME="ufonion"
NEW_EMAIL="mayongcong@gmail.com"

if [ "$GIT_COMMITTER_EMAIL" = "$WRONG_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$NEW_NAME"
    export GIT_COMMITTER_EMAIL="$NEW_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$WRONG_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$NEW_NAME"
    export GIT_AUTHOR_EMAIL="$NEW_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```

