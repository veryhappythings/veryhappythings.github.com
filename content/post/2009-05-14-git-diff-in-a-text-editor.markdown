---
date: '2009-05-14T00:00:00Z'
title: Git Diff in a text editor
wordpress_id: 21
wordpress_url: http://blog.veryhappythings.co.uk/?p=21
---
If you want to have a look through your git diffs in a more useful editor than the default, you can just pipe it in on the command line:

<code>
git diff branch1 branch2 file | mate
</code>

Or, you can set your GIT_PAGER environment variable to your editor of choice.

<code>
export GIT_PAGER=mate
</code>

Thanks to the good people at <a href='http://stackoverflow.com/questions/508276/how-do-i-view-git-diff-with-textmate'>Stack Overflow</a>.
