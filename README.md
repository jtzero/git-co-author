# git-co-author

Easily use 'Co-authored-by' trailers in the commit template.

```text
usage: git co-author                Show co-authors in commit template
   or: git co-author <initials>...  Update co-authors in commit template
   or: git co-author clear          Remove all co-authors from commit template
   or: git co-author authors        List authors in git config
   or: git co-author select         Select authors in git log
```

This command enables pairs and mobs of programmers to attribute commits to all the authors. For convenience, co-authors are added using their initials. Their names and email addresses are stored in git config.

GitHub has first-class support for `Co-authored-by` trailers and recognises the author and co-authors of commits. For more information on co-authoring commits, see:

- [Git Together By Co-Authoring Commits](https://github.community/t5/Support-Protips/Git-Together-By-Co-Authoring-Commits/ba-p/27480)
- [Creating a commit with multiple authors](https://help.github.com/en/github/committing-changes-to-your-project/creating-a-commit-with-multiple-authors)

## Install

Install the command using Homebrew:

```bash
brew install jamesjoshuahill/tap/git-co-author
```

Or manually into your `$PATH`, for example:

```
curl -O https://raw.githubusercontent.com/jamesjoshuahill/git-co-author/master/git-co-author
install git-co-author /usr/local/bin/
```

Configure the commit template path, for example:

```bash
git config --global commit.template '~/.git-commit-template'
```

Ensure there is a commit template file:

```bash
touch ~/.git-commit-template
```

## Add authors to git config

Configure the name and email address of authors with their initials. For example Ann and Bob:

```bash
git config --global co-authors.aa 'Ann Author <ann.author@example.com>'
git config --global co-authors.bb 'Bob Book <bob.book@example.com>'
```

The co-authors will appear in your global git config:

```
[co-authors]
    aa = Ann Author <ann.author@example.com>
    bb = Bob Book <bob.book@example.com>
```

You must use an email address associated with the co-author's GitHub account.

**Tip:** You can help a co-author find their preferred email address by sharing this information:

- To find your GitHub-provided `no-reply` email, navigate to your email settings page under "Keep my email address private."
- To find the email you used to configure Git on your computer, run `git config user.email` on the command line.

## Usage

This command expects you to commit using the commit template. If you run `git commit` with the message option `-m/--message` then the commit template is not used and any Co-authored-by trailers in the commit template won't be commited.

Pair with Ann:

```bash
$ git co-author aa
Co-authored-by: Ann Author <ann.author@example.com>
```

Mob with Ann and Bob:

```bash
$ git co-author aa bb
Co-authored-by: Ann Author <ann.author@example.com>
Co-authored-by: Bob Book <bob.book@example.com>
```

Solo (without co-authors):

```bash
$ git co-author clear
```

List authors in git config:

```bash
$ git co-author authors
aa  'Ann Author <ann.author@example.com>'
bb  'Bob Book <bob.book@example.com>'
```

Find authors in git log:

```bash
$ git co-author find
Ann Author <ann.author@example.com>
Bob Book <bob.book@example.com>
```

## Test

The command is tested using the [Bats](https://github.com/sstephenson/bats) testing framework for Bash.

The tests expect no global git config, so they are run in a container to isolate
them.

### Docker

Install Docker:

```bash
brew cask install docker
```

Build the test image:

```bash
docker build -t git-co-author-test .
```

Run the tests in a container:

```bash
docker run git-co-author-test
```

For active development you can mount the host directory into the test container,
so that you can edit the code locally and run the tests in the container
repeatedly:

```bash
$ docker run --volume "$PWD:/git-co-author" -it git-co-author-test bash
bash-5.0# ./test/git-co-author.bats
```

## Notes

- The command does not modify Git config.
- This approach assumes the user has configured `user.name` and `user.email` so that they are attributed as an author.
- Only the `Co-authored-by` trailers in the commit template file are modified or removed. The rest of the commit template is unaffected.
- GitHub deduplicates multiple authors of a commit, so if you commit as author and co-author you will only be shown once.
- Config in the current Git repo takes precedence over global Git config. To set config for one repo use the `git config --local` option.
- Inspired by [`git-author`](https://github.com/pivotal/git-author).
