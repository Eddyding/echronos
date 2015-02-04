The RTOS change management is described in the Breakaway Consulting Pty. Ltd. Quality Manual.
Although that manual describes the quality control for the Breakaway Quality Management System, the same process applies to the RTOS.

In short: every RTOS change goes into a branch and needs to be reviewed before it is integrated into the mainline branch.

We usually ask reviewers to review code informally via e-mail or chat.
When asked for a review, the reviewer finds a file such as pm/reviews/TASKNAME/review-N.REVIEWERNAME in the respective RTOS repository , depending on where the changes were made.

A review consists of:

- reviewing the task description in pm/tasks/TASKNAME and checking whether the task is necessary and sound in the first place

- reviewing the diff whether it:
  * fulfills the requirements of the task description
  * is correct, complete, minimal, efficient, clean, and elegant

- reviewing the tests in the code and on TeamCity whether
  * the new or modified functionality is covered by tests
  * whether the tests pass

The RTOS code must meet very high quality standards, so constructive pickyness is expected and encouraged.

The results of these reviews, including the overall verdict (accept/rework) then go into the review form mentioned above, which is committed and pushed to the server repository.