---
title       : Basic workflow
description : >-
  This chapter explains what version control is and why you should use
  it, and introduces the most common steps in a common Git workflow.


--- type:PureMultipleChoiceExercise lang:bash xp:50 key:a3732cc273
## What is version control?

A **version control system** is a tool that manages changes made to the files and directories in a project.
Many version control systems exist;
this lesson focuses on one called Git,
which is used by many of the data science tools covered in our other lessons.
Its strengths are:

* Nothing that is saved to Git is ever lost,
  so you can always go back to see which results were generated by which versions of your programs.

* Git automatically notifies you when your work conflicts with someone else's,
  so it's harder (but not impossible) to accidentally overwrite work.

* Git can synchronize work done by different people on different machines,
  so it scales as your team does.

Version control isn't just for software:
books, papers, parameter sets, and anything that changes over time or needs to be shared
can and should be stored and shared using something like Git.

<hr>

Which of the following does Git do?

*** =possible_answers
- Keep track of changes to files.
- Notice conflicts between changes made by different people.
- Synchronize files between different computers.
- [All of the above.]

*** =hint

Git is a very versatile tool.

*** =feedback
- Yes, but that's not all.
- Yes, but that's not all.
- Yes, but that's not all.
- Correct.


--- type:MultipleChoiceExercise lang:shell xp:50 skills:1 key:1dac2b0a28
## Where does Git store information?

Each of your Git projects has two parts:
the files and directories that you create and edit directly,
and the extra information that Git records about the project's history.
The combination of these two things is called a **repository**.

Git stores all of its extra information in a directory called `.git`
located in the root directory of the repository.
Git expects this information to be laid out in a very precise way,
so you should never edit or delete anything in `.git`.

<hr>

Suppose your home directory `/home/repl` contains a repository called `dental`,
which has a sub-directory called `data`.
Where is information about the history of the files in `/home/repl/dental/data` stored?

*** =instructions
- `/home/repl/.git`
- [`/home/repl/dental/.git`]
- `/home/repl/dental/data/.git`
- None of the above.

*** =hint

Use `ls -a` to see all of the contents of a directory.

*** =pre_exercise_code
```{shell}

```

*** =sct
```{python}
e1 = 'No: every repository stores its own history.'
c2 = 'Yes: all of the information about a repository is stored under its root directory.'
e3 = 'No: all of the information about a repository is stored under its root directory.'
e4 = 'No: one of the answers above is correct.'
Ex().has_chosen(2, [e1, c2, e3, e4])
```

--- type:MultipleChoiceExercise lang:shell xp:50 skills:1 key:bc52cf1174
## How can I check the state of a repository?

When you are using Git,
you will frequently want to check the **status** of your repository.
To do this,
run the command `git status`,
which displays a list of the files that have been modified
since the last time changes were saved.

<hr>

You have been put in the `dental` repository.
Use `git status` to discover which file(s) have been changed since the last save.
Which file(s) are listed?

*** =instructions
- `data/summer.csv`.
- `report.txt`.
- Neither of the above.
- Both of the above.

*** =hint

Look for filenames in the output of `git status`.

*** =pre_exercise_code
```{python}
append = '''

Fehrenbach: "Dental Anatomy Coloring Book" (2e), 2013.
'''
with open('dental/report.txt', 'a') as writer:
    writer.write(append)
repl = connect('bash')
repl.run_command('cd dental')
```

*** =sct
```{python}
Ex().has_chosen(2, ['No, that file has not changed.', \
                    'Correct!', \
                    'No, one file has changed.', \
                    'No, only one file has changed.'])
```

--- type:BulletConsoleExercise key:0dd628a298
## How can I tell what I have changed?

Git has a **staging area** in which it stores files with changes you want to save
that haven't been saved yet.
Putting files in the staging area is like putting things in a box,
while **committing** those changes is like putting that box in the mail:
you can add more things to the box or take things out as often as you want,
but once you put it in the mail,
you can't make further changes.

<img src="https://s3.amazonaws.com/assets.datacamp.com/production/course_5355/datasets/staging-area.png" alt="Staging Area" />

`git status` shows you which files are in this staging area,
and which files have changes that haven't yet been put there.
In order to compare the file as it currently is to what you last saved,
you can use `git diff filename`.
`git diff` without any filenames will show you all the changes in your repository,
while `git diff directory` will show you the changes to the files in some directory.

*** =pre_exercise_code
```{python}
with open('dental/data/northern.csv', 'a') as writer:
    writer.write('2017-11-01,bicuspid\n')
repl = connect('bash')
repl.run_command('cd dental')
```

*** =type1: ConsoleExercise
*** =key1: e9c198755a

*** =xp1: 100

*** =instructions1

You have been put in the `dental` repository.
Use `git diff` to see what changes have been made to the files.

*** =hint1

Run the command without any extra arguments.

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
git diff
```

*** =sct1
```{python}
Ex().multi(
    has_cwd('/home/repl/dental'),
    check_or(
        has_expr_output(),
        has_code(r'\s*git\s+diff\s*', incorrect_msg='Use `git` followed by `diff` without extra arguments.')
    )
)
```


--- type:MultipleChoiceExercise lang:shell xp:50 skills:1 key:eef645517f
## What is in a diff?

A **diff** is a formatted display of the differences between two sets of files.
Git displays diffs like this:

    diff --git a/report.txt b/report.txt
    index e713b17..4c0742a 100644
    --- a/report.txt
    +++ b/report.txt
    @@ -1,4 +1,4 @@
    -# Seasonal Dental Surgeries 2017-18
    +# Seasonal Dental Surgeries (2017) 2017-18
     
     TODO: write executive summary.

This shows:
- The command used to produce the output (in this case, `diff --git`).
  In it, `a` and `b` are placeholders meaning "the first version" and "the second version".
- An index line showing keys into Git's internal database of changes.
  We will explore these in the next chapter.
- `--- a/report.txt` and `+++ b/report.txt`,
  which indicate that lines being *removed* are prefixed with `-`,
  while lines being added are prefixed with `+`.
- A line starting with `@@` that tells where the changes are being made. The pairs of numbers are `start line,number of lines changed`.
  Here, the diff output shows that 4 lines from line 1 are being removed and replaced with new lines.
- A line-by-line listing of the changes
  with `-` showing deletions and `+` showing additions.
  (We have also configured Git to show deletions in red and additions in green.)
  Lines that *haven't* changed are sometimes shown before and after the ones that have
  in order to give context;
  when they appear,
  they *don't* have either `+` or `-` in front of them.

Desktop programming tools like [RStudio](https://www.rstudio.com/) can turn diffs like this
into a more readable side-by-side display of changes;
you can also use standalone tools like [DiffMerge](https://sourcegear.com/diffmerge/)
or [WinMerge](http://winmerge.org/).

<hr>

You have been put in the `dental` repository.
Use `git diff data/northern.csv` to look at the changes to that file.
How many lines have been changed?

*** =instructions
- None.
- 1.
- 2.
- 20.

*** =hint

Count the number of lines with `+` or `-` in front of them in the diff.

*** =pre_exercise_code
```{shell}
with open('dental/data/northern.csv', 'a') as writer:
    writer.write('2017-11-01,bicuspid\n')
repl = connect('bash')
repl.run_command('cd dental')
```

*** =sct
```{python}
err_some = 'No, the commit changed some of the lines.'
correct = 'Yes, the commit added one line.'
err_fewer = 'No, the commit did not change that many lines.'
Ex().has_chosen(2, [err_some, correct, err_fewer, err_fewer])
```

<!-- -------------------------------------------------------------------------------- -->

--- type:BulletConsoleExercise key:55204eee61
## What's the first step in saving changes?

You commit changes to a Git repository in two steps:

1. Add one or more files to the staging area.
2. Commit everything in the staging area.

To add a file to the staging area,
use `git add filename`.

*** =pre_exercise_code
```{python}
append = '''

Fehrenbach: "Dental Anatomy Coloring Book" (2e), 2013.
'''
with open('dental/report.txt', 'a') as writer:
    writer.write(append)
repl = connect('bash')
repl.run_command('cd dental')
```

*** =type1: ConsoleExercise
*** =key1: a34bf017f0

*** =xp1: 50

*** =instructions1

You have been put in the `dental` repository.
Use `git add` to add the file `report.txt` to the staging area.

*** =hint1

Remember: `git add` is the command, and `report.txt` is its argument.

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
git add report.txt
```

*** =sct1
```{python}
Ex().multi(
has_cwd('/home/repl/dental'),
  has_expr_output(
    expr='git diff --name-only --staged | grep report.txt',
    output = 'report.txt',
    strict=True, 
    incorrect_msg = "It seems that `report.txt` was not added to the staging area. Have you used `git add report.txt`?"
  )
)
```

*** =type2: ConsoleExercise
*** =key2: 961661800c

*** =xp2: 50

*** =instructions2

Use another Git command to check the repository's status.

*** =hint2

Use `git status` to check the *status* of the repository.

*** =sample_code2
```{shell}
```

*** =solution2
```{shell}
git status
```

*** =sct2
```{python}
Ex().multi(
    has_cwd('/home/repl/dental'),
    check_correct(
        has_expr_output(),
        has_code(r'\s*git\s+status\s*', incorrect_msg = "Have a look at the status of your repository with `git status`.")
    )
)
```

<!-- -------------------------------------------------------------------------------- -->

--- type:BulletConsoleExercise key:f208f45d7d
## How can I tell what's going to be committed?

To compare the state of your files with those in the staging area, you can use `git diff -r HEAD`. The `-r` flag means "compare to a particular revision", and `HEAD` is a shortcut meaning "the most recent commit".

You can restrict the results to a single file or directory using `git diff -r HEAD path/to/file`, where the path to the file is relative to where you are (for example, the path from the root directory of the repository).

We will explore other uses of `-r` and `HEAD` in the next chapter.

*** =pre_exercise_code
```{python}
with open('dental/data/northern.csv', 'a') as writer:
    writer.write('2017-11-01,bicuspid\n')
with open('dental/data/eastern.csv', 'a') as writer:
    writer.write('2017-11-02,molar\n')
repl = connect('bash')
repl.run_command('cd dental')
repl.run_command('git add data/northern.csv')
```

*** =type1: ConsoleExercise
*** =key1: 77c975a5c8

*** =xp1: 30

*** =instructions1

You have been put in the `dental` repository, where `data/northern.csv` has been added to the staging area. Use `git diff` with `-r` and an argument to see how files differ from the last saved revision.

*** =hint1

Remember that `HEAD` refers to the most recent saved version.

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
git diff -r HEAD
```

*** =sct1
```{python}
Ex().multi(
    has_cwd('/home/repl/dental'),
    check_correct(
        has_expr_output(),
        has_code(r'\s*git\s+diff\s+-r\s+HEAD.*', incorrect_msg = 'Use `git diff` with the `-r` flag and `HEAD` (with or without a directory name).')
    )
)
```

*** =type2: ConsoleExercise
*** =key2: 5a866b3ef0

*** =xp2: 30

*** =instructions2

Use a single Git command to view the changes in the file that has been staged
(and *only* that file).

*** =hint2

Use the relative path to the file `data/northern.csv`.

*** =sample_code2
```{shell}
```

*** =solution2
```{shell}
git diff -r HEAD data/northern.csv
```

*** =sct2
```{python}
Ex().multi(
    has_cwd('/home/repl/dental'),
    check_correct(
        has_expr_output(),
        has_code(r'\s*git\s+diff\s+-r\s+HEAD.*', incorrect_msg = 'Use `git diff` with the `-r` flag, `HEAD`, and `data/northern.csv` to view the changes of the staged file only.')
    )
)
```

*** =type3: ConsoleExercise
*** =key3: 538a9c35d4

*** =xp3: 40

*** =instructions3

`data/eastern.csv` hasn't been added to the staging area yet. Use a Git command to do this now.

*** =hint3

Use `git add` with the name of the file to be added.

*** =sample_code3
```{shell}
```

*** =solution3
```{shell}
git add data/eastern.csv
```

*** =sct3
```{python}
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(expr='git diff --name-only --staged | grep data/eastern.csv',
                    output = 'data/eastern.csv',
                    strict = True,
                    incorrect_msg = "It seems that `data/eastern.csv` wasn't added to the staging area. Have you used `git add data/eastern.csv`?")
)
Ex().success_msg("Great! Before we dive into actually committing the changes you have added to the staging area, let's quickly recap how to work with text editors in the terminal.")
```

<!-- -------------------------------------------------------------------------------- -->

--- type:ConsoleExercise lang:shell xp:100 skills:1 key:4f71d1f1a0
## Interlude: how can I edit a file?

Unix has a bewildering variety of text editors.
In this course,
we will sometimes use a very simple one called Nano.
If you type `nano filename`,
it will open `filename` for editing
(or create it if it doesn't already exist).
You can then move around with the arrow keys,
delete characters with the backspace key,
and so on.
You can also do a few other operations with control-key combinations:

- Ctrl-K: delete a line.
- Ctrl-U: un-delete a line.
- Ctrl-O: save the file ('O' stands for 'output').
- Ctrl-X: exit the editor.

*** =instructions

Run `nano names.txt` to edit a new file in your home directory
and enter the following four lines:

```
Lovelace
Hopper
Johnson
Wilson
```

To save what you have written,
type Ctrl-O to write the file out,
then Enter to confirm the filename,
then Ctrl-X and Enter to exit the editor.

*** =solution
```{shell}
# This solution uses another command
# because our automated tests can't edit files interactively.
echo -e "Lovelace\nHopper\nJohnson\nWilson" > names.txt
```

*** =sct
```{python}
patt = "Have you included the line `%s` in the `names.txt` file? Use `nano names.txt` again to update your file. Use Ctrl-O to save and Ctrl-X to exit."
Ex().multi(
    has_cwd('/home/repl'),
    check_file('/home/repl/names.txt').multi(
        has_code(r'Lovelace', incorrect_msg=patt%'Lovelace'),
        has_code(r'Hopper', incorrect_msg=patt%'Hopper'),
        has_code(r'Johnson', incorrect_msg=patt%'Johnson'),
        has_code(r'Wilson', incorrect_msg=patt%'Wilson')
    )
)
Ex().success_msg("Well done! Off to the next one!")
```

<!-- -------------------------------------------------------------------------------- -->

--- type:BulletConsoleExercise key:dbf5aa722c
## How do I commit changes?

To save the changes in the staging area,
you use the command `git commit`.
It always saves everything that is in the staging area as one unit:
as you will see later,
when you want to undo changes to a project,
you undo all of a commit or none of it.

When you commit changes,
Git requires you to enter a **log message**.
This serves the same purpose as a comment in a program:
it tells the next person to examine the repository why you made a change.

By default,
Git launches a text editor to let you write this message.
To keep things simple,
you can use `-m "some message in quotes"` on the command line
to enter a single-line message like this:

```
git commit -m "Program appears to have become self-aware."
```

If you accidentally mistype a commit message, you can change it using the `--amend` flag.

```
git commit --amend - m "new message"
```

*** =pre_exercise_code
```{python}
append = '''

Fehrenbach: "Dental Anatomy Coloring Book" (2e), 2013.
'''
with open('dental/report.txt', 'a') as writer:
    writer.write(append)
repl = connect('bash')
repl.run_command('cd dental')
repl.run_command('git add report.txt')
```

*** =type1: ConsoleExercise
*** =key1: 17e219ea22

*** =xp1: 50

*** =instructions1

You have been put in the `dental` repository,
and `report.txt` has been added to the staging area.
Use a Git command to check the status of the repository.

*** =hint1

Again, you want to use Git to check the *status* of the repository.

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
git status
```

*** =sct1
```{python}
Ex().multi(
    has_cwd('/home/repl/dental'),
    check_correct(
        has_expr_output(),
        has_code(r'\s*git\s+status\s*', incorrect_msg = "Have a look at the status of your repository with `git status`.")
    )
)
```

*** =type2: ConsoleExercise
*** =key2: a5ce3bebb8

*** =xp2: 50

*** =instructions2

Commit the changes in the staging area with the message "Adding a reference."

*** =hint2

Use `-m "some message"` with the appropriate Git command.

*** =sample_code2
```{shell}
```

*** =solution2
```{shell}
git commit -m "Adding a reference."
```

*** =sct2
```{python}
not_committed_msg = "It seems that the staged changes to `report.txt` weren't committed. Have you used `git commit` with `-m \"Adding a reference.\"`?"
bad_message_msg = 'It seems the commit message was incorrect. You can amend it with `git commit --amend -m "new message"`.'
Ex().multi(
    has_cwd('/home/repl/dental'),
    check_or(
        has_expr_output(
            expr='git diff HEAD~ --name-only | grep report.txt',
            output = 'report.txt',
            strict=True,
            incorrect_msg=not_committed_msg
        ),
        has_code(r'\s*git\s+commit', incorrect_msg = not_committed_msg)            
    ),
    has_expr_output(
        expr='git log -1 | grep --only-matching "Adding a reference"',
        output = 'Adding a reference',
        strict=True,
        incorrect_msg=bad_message_msg
    )
)
Ex().success_msg("You committed the file!")
```

<!-- -------------------------------------------------------------------------------- -->

--- type:MultipleChoiceExercise lang:shell xp:50 skills:1 key:97ae2f8613
## How can I view a repository's history?

The command `git log` is used to view the **log** of the project's history.
Log entries are shown most recent first,
and look like this:

```
commit 0430705487381195993bac9c21512ccfb511056d
Author: Rep Loop <repl@datacamp.com>
Date:   Wed Sep 20 13:42:26 2017 +0000

    Added year to report title.
```

The `commit` line displays a unique ID for the commit called a **hash**;
we will explore these further in the next chapter.
The other lines tell you who made the change,
when,
and what log message they wrote for the change.

When you run `git log`,
Git automatically uses a pager to show one screen of output at a time.
Press the space bar to go down a page or the 'q' key to quit.

<hr>

You are in the directory `dental`,
which is a Git repository.
Use a single Git command to view the repository's history.
What is the message on the very first entry in the log
(which is displayed last)?

*** =instructions
- "Added summary report file."
- "Added seasonal CSV data files"
- "Fixed bug and regenerated results."
- "Added reminder to cite funding sources."

*** =hint

Use the space bar to page down through the log and 'q' to quit.

*** =pre_exercise_code
```{shell}
repl = connect('bash')
repl.run_command('cd dental')
```

*** =sct
```{python}
err = 'Incorrect: please re-run the command and use `git log`.'
Ex().has_chosen(1, ["Correct! `git log` is a convenient way to remind yourself what you've been working on.", err, err, err])
```

<!-- -------------------------------------------------------------------------------- -->

--- type:MultipleChoiceExercise lang:shell xp:50 skills:1 key:77aa525e25
## How can I view a specific file's history?

A project's entire log can be overwhelming,
so it's often useful to inspect only the changes to particular files or directories.
You can do this using `git log path`,
where `path` is the path to a specific file or directory.
The log for a file shows changes made to that file;
the log for a directory shows when files were added or deleted in that directory,
rather than when the contents of the directory's files were changed.

<hr>

You have been put in the `dental` repository.
Use `git log` to display only the changes made to `data/southern.csv`.
How many have there been?

*** =instructions
- 0.
- 1.
- 2.
- 3.

*** =hint

Use `git log` and the path to the file you are interested in.

*** =pre_exercise_code
```{shell}
repl = connect('bash')
repl.run_command('cd dental')
```

*** =sct
```{python}
err = 'Incorrect: please use `git log data/southern.csv` and count the number of log entries.'
Ex().has_chosen(3, [err, err, 'Correct!', err])
```

<!-- -------------------------------------------------------------------------------- -->

--- type:BulletConsoleExercise key:1be0ce9219
## How do I write a better log message?

Writing a one-line log message with `git commit -m "message"`is good enough for very small changes,
but your collaborators (including your future self) will appreciate more information.
If you run `git commit` *without* `-m "message"`,
Git launches a text editor with a template like this:

```

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Your branch is up-to-date with 'origin/master'.
#
# Changes to be committed:
#       modified:   skynet.R
#
```

The lines starting with `#` are comments, and won't be saved.
(They are there to remind you what you are supposed to do and what files you have changed.)
Your message should go at the top, and may be as long and as detailed as you want.

*** =pre_exercise_code
```{python}
append = '''

Fehrenbach: "Dental Anatomy Coloring Book" (2e), 2013.
'''
with open('dental/report.txt', 'a') as writer:
    writer.write(append)
repl = connect('bash')
repl.run_command('cd dental')
repl.run_command('git add report.txt')
```

*** =type1: ConsoleExercise
*** =key1: 2f3aa2a066

*** =xp1: 100

*** =instructions1

You have been put in the `dental` repository,
and `report.txt` has been added to the staging area.
The changes to `report.txt` have already been staged.
Use `git commit` *without* `-m` to commit the changes.
The Nano editor will open up. Write a meaningful message
and use Ctrl+O and Ctrl+X to save and leave the editor.

*** =hint1

Run `git commit` without any arguments.
(Our solution uses `-m` and a message so that we can automate solution testing.)

*** =sample_code1
```{shell}
```

*** =solution1
```{shell}
# This solution uses another command
# because our automated tests can't edit files interactively.
git commit -m "Adding a reference."
```

*** =sct1
```{python}
msg="It seems that the staged changes to `report.txt` weren't committed. Use `git commit` to interactively write a commit message. If you're struggling, you can always use the `-m \"any message\"` flag to avoid the interaction."
Ex().multi(
    has_cwd('/home/repl/dental'),
    has_expr_output(expr='git diff HEAD~ --name-only | grep report.txt',
                    output = 'report.txt', incorrect_msg=msg)
)
Ex().success_msg("Neat! This concludes chapter 1, where you learned about `git diff`, `git status`, `git add` and `git commit`. Quite something! Rush over to chapter 2 to continue your Git adventure!")
```
