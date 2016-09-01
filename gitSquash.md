#How to squash commits before pushing to a remote for a PR review

**See the following links for more information**

- https://ariejan.net/2011/07/05/git-squash-your-latests-commits-into-one
- https://help.github.com/articles/changing-a-commit-message

**We have 3 separate commits we'd like to squash into one**

```
$ git log -3

commit 03ecb2d067c19f672412abcfcc2d6a34a1d063b2
Author: Scott Davidson <davidson@us.ibm.com>
Date:   Tue Aug 23 17:04:48 2016 -0400

    Add Change THREE.

commit 7b6a30090ccdb5e36ffff5c40b71cbf33ad67027
Author: Scott Davidson <davidson@us.ibm.com>
Date:   Tue Aug 23 17:02:09 2016 -0400

    Remove Change TWO.

commit c4d17ddc4eaec8b555ee6acca15e0934cd39a683
Author: Scott Davidson <davidson@us.ibm.com>
Date:   Tue Aug 23 16:57:50 2016 -0400

    Add Change ONE and Change TWO.
```

**The rebase command is used to squash (or reword, etc) previous commits**

`$ git rebase -i HEAD~3`

**The following comes up in your default editor:**

```
  1 pick c4d17dd Add Change ONE and Change TWO.
  2 pick 7b6a300 Remove Change TWO.
  3 pick 03ecb2d Add Change THREE.
  4
  5 # Rebase 48e6eeb..03ecb2d onto 48e6eeb (3 command(s))
  6 #
  7 # Commands:
  8 # p, pick = use commit
  9 # r, reword = use commit, but edit the commit message
 10 # e, edit = use commit, but stop for amending
 11 # s, squash = use commit, but meld into previous commit
 12 # f, fixup = like "squash", but discard this commit's log message
 13 # x, exec = run command (the rest of the line) using shell
 14 # d, drop = remove commit
 15 #
 16 # These lines can be re-ordered; they are executed from top to bottom.
 17 #
 18 # If you remove a line here THAT COMMIT WILL BE LOST.
 19 #
 20 # However, if you remove everything, the rebase will be aborted.
 21 #
 22 # Note that empty commits are commented out
```

**Edit the above to look like this (note there must be a "pick" before the first "squash"):**

```
  1 pick c4d17dd Add Change ONE and Change TWO.
  2 squash 7b6a300 Remove Change TWO.
  3 squash 03ecb2d Add Change THREE.
```

**After write-quit from above the following is shown in the editor:**

```
  1 # This is a combination of 3 commits.
  2 # The first commit's message is:
  3 Add Change ONE and Change TWO.
  4
  5 # This is the 2nd commit message:
  6
  7 Remove Change TWO.
  8
  9 # This is the 3rd commit message:
 10
 11 Add Change THREE.
 12
 13 # Please enter the commit message for your changes. Lines starting
 14 # with '#' will be ignored, and an empty message aborts the commit.
 15 #
 16 # Date:      Tue Aug 23 16:57:50 2016 -0400
 17 #
 18 # interactive rebase in progress; onto 48e6eeb
 19 # Last commands done (3 commands done):
 20 #    squash 7b6a300 Remove Change TWO.
 21 #    squash 03ecb2d Add Change THREE.
 22 # No commands remaining.
 23 # You are currently editing a commit while rebasing branch 'testsquash' on '48e6eeb'.
 24 #
 25 # Changes to be committed:
 26 #       modified:   airflow_home/dags/dag_utils.py
```

**Edit the above to look like this:**

```
  1 # This is a combination of 3 commits.
  2 # The first commit's message is:
  3 Add Change ONE and THREE
  4
  5 # Please enter the commit message for your changes. Lines starting
  ...
```

**Write-quit the 2nd editor session and the following is shown:**

```
[detached HEAD 121ddc2] Add Change ONE and THREE
 Date: Tue Aug 23 16:57:50 2016 -0400
 1 file changed, 2 insertions(+), 2 deletions(-)
Successfully rebased and updated refs/heads/testsquash.
```

**After that all three commits are squashed into one:**

```
$ git log -3
commit 121ddc2831718d74718ae25c21b1e8386011339b
Author: Scott Davidson <davidson@us.ibm.com>
Date:   Tue Aug 23 16:57:50 2016 -0400

    Add Change ONE and THREE

commit 48e6eebcf5b0ce9263e633a386c6ce5b89b27071
Author: Scott Davidson <davidson@us.ibm.com>
Date:   Fri Aug 12 13:40:47 2016 -0400

    Change cloudant-metrics DAG so it sets timestamp to last day of current
    month w/out time part

    fixes #442
...
```

**Note the commit hash is different.**
**The resulting commit has the net result of of all 3 commits:**

```
$ git show 121ddc283
commit 121ddc2831718d74718ae25c21b1e8386011339b
Author: Scott Davidson <davidson@us.ibm.com>
Date:   Tue Aug 23 16:57:50 2016 -0400

    Add Change ONE and THREE

diff --git a/airflow_home/dags/dag_utils.py b/airflow_home/dags/dag_utils.py
index e921ef5..9380e36 100644
--- a/airflow_home/dags/dag_utils.py
+++ b/airflow_home/dags/dag_utils.py
@@ -1,10 +1,10 @@
 """ Contains a collection of DAG utilities. """
+# Change ONE
 from __future__ import absolute_import
-
+# Change THREE
 from datetime import datetime
 from copy import deepcopy
 import logging
-
 from airflow.exceptions import AirflowException

 def add_dates(metric_info,
```
