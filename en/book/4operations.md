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

## Custom Fields Use Cases

#### 1. Get the custom fields for a given department
```php
$hTicketCustField = xoops_getModuleHandler('ticketCustomField');
$fields =& $hTicketCustField->getByDept($deptid);

foreach($fields as $field) {
    echo $field->getVar('name'); //Display Pretty Name for field
    echo $field->getVar('description'); //Display Pretty Description
    echo $field->getVar('fieldname'); //name of element in form
    echo $field->getVar('controltype'); //which type of form element to display
    echo $field->getVar('required'); //Is this entry required?
    echo $field->getVar('weight'); //Which order should this field be displayed
    $values = $field->getValues(); // An hash of name / value pairs

    echo $field->getVar('defaultvalue'); //Default value of field
    $validators = $field->getValidators(); //An array of validation objects to validate data entry
    echo $field->getVar('promptuser');

    echo $ticket->getCustomVar($field->getVar('fieldname')); // get the current fields value
}
```
#### 2. Create a new custom field
```php
$hTicketCustField = xoops_getModuleHandler('ticketCustomField');
$field =& $hTicketCustField->create();
$field->setVar('name', $name);
$field->setVar('description', $description);
//... additional code

$field->addValidator($validatorObj);
$field->addValues(array(1 => 'desc1', 2 => 'desc2'));
$field->addValue(3, 'desc3');
$field->addDepartments(array(1,3,5));
$field->addDepartment(4);

$field->removeDepartment(5);

$ret = $hTicketCustField->insert($field);
```

#### 3. Retrieving a custom ticket var from form submission and storing it in the ticket

```php

// ... Retrieve a list of fields expected for this department (in $fields)
// ... Set normal ticket values

// Set value for each custom field
foreach ($fields as $field) {
    $fieldname  = $field->getVar('fieldname');
    $submission = $_POST[$fieldname];

    //... Validate Entry

    if ($validEntry) {
        $ticket->setCustomVar($fieldname, $submission);
    }
}

// Store ticket (and custom fields)
$ret = $hTicket->insert($ticket);
```

#### 4. Get Custom Fields for a ticket (after dept is set)

```php
$fields = $ticket->getCustomFields();
```