+++
title = 'Submitting Patches'
date = 2024-06-17T23:47:42+02:00
+++
In order to review code better and use established lines of communication, we accept code in the form of Pull Requests
on our [Gitea](https://git.codingworkshop.eu.org/xt-sys/exectos/pulls) instance. The process may seem a little bit
complicated at first, but it will become very natural. It is important for us, to make sure the author and email settings
for your GIT are configured correctly. You should follow our '[Working with GIT](/contributing/working-with-git)' document,
but the key point is to use your real name and valid e-mail address. This is not just for developers to show a stronger
commitment and establish trust, but also helps discourage irresponsible programmers from submitting unacceptable code.
You will need a Gitea account which must also use your real name. You should be precise; having the names match helps in
identifying commits.

### Patch Guidelines
In order to make your patches as easy to check as possible, try to keep them small, clear, and atomic:
 * Include only closely related changes in one commit.
 * Don't implement extra things that no one uses.
 * Try to limit each merge request to only a few commits.
 * Limit a commit to changing a single file or component.

Above rules make the Pull Request easier to review.

### Commit Message


### Quality Check
It never hurts to check through your changes one last time. Take a while, to look at each line of the patch and make sure
there are no unintentional or unrelated changes. Also make sure that the layout of your code conforms to our
[coding style](/contributing/coding-style).

### Sending Pull Request
After checking in your local changes, you can submit a Pull Request on Gitea to get your changes upstream. This requires
you to be working on a Gitea fork. You should make up a temporary branch name for your changes. That branch will be deleted
once the merge request is merged, so it's better to avoid using the master branch directly.

### Managing Pull Request
After you've submitted your merge request, a reviewer will be assigned to it, if someone is responsible for that area of
the code. You can also assign a reviewer yourself if you know who the right person is. If a reviewer gives some feedback
or a critique, carefully think about what they say and fix your patch before resubmitting. When updating your changes, you
can simply push the fixes as a new commit on top of your Pull Request. This is prefferred way, as allows us to compare
code before and after your fixes. All commits can be squashed by a reviewer upon merge.

If your Pull Request remains in the queue for several weeks without being committed and without feedback, you can ask for
further suggestions on our [Discord](/contact-us) server. If you have patches rejected multiple times, don't be discouraged.
You might consider finding a mentor willing to pre-review your changes before you submit them again.

### Reviewing Pull Request
If you are asked to review a merge request, and find things that need to be improved, you should add a comment to the merge
request from the Gitea interface. You can also fix things up yourself and push updated commits to the original branch, once
submitter explicitly give you access to his GIT repository. If you think that the merge request is good to go in, you
should mark it approved. As a rule, a merge request won't be committed until all reviewers have approved it. If you have
been assigned as a reviewer but you feel that you cannot meaningfully review the changes, feel free to unassign yourself.
