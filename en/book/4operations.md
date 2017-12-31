# 4.0 Operating Instructions

Email Ticket Submission
-----------------------------------------------------------------------
To configure email ticket submission some additional steps are
necessary. First you need to create a POP3 email account for each
department that will receive email. Next, go to "Manage Departments" in
the xhelp Admin Panel. Next, edit the department you wish to hold the newly
created tickets. Next, Add a new mailbox to monitor:

* **Mailbox Type** - currently the only option is POP3.
* **Server **- DNS name of mail server (get from your hosting provider)
* **Port **- Mail Service Port Number.  For POP3 this is usually 110.
* **Username **- Username to access mailbox (get from your hosting provider)
* **Password **- Password to access mailbox (also get from your hosting provider)
* **Default Ticket Priority** - Adjust default priority for incoming tickets.
* **Reply-To Email Address** - the email address for this account. Used for
    handling replies (responses) to tickets.

Repeat this process for each mailbox you wish to monitor.

Once all mailboxes have been added, you need to setup a scheduled task
or a cron job to check these mailboxes on a regular basis.

For *nix machines the following crontab line should do the trick:

*/2 * * * * /usr/bin/wget -q <XOOPS_URL>/modules/xhelp/checkemail.php

The above line will check all the configured mailboxes, every other minute.

For windows servers you can try using Task Scheduler or runat with
this variant:
C:\php\php.exe "<XOOPS_ROOT_PATH>\modules\xhelp\checkemail.php"

If PHP was installed into a different directory from the default you
will need to adjust the path to php.exe accordingly.

In addition, there is a version of wget for windows:

ftp://ftp.sunsite.dk/projects/wget/windows/wget-1.9.1b-complete.zip

Unfortunately we cannot support you in the configuration of this
scheduled task. Please contact your hosting provider with any questions.