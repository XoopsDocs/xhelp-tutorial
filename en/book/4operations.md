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

##  xHelp Events Service

```php
// * All functions should return a boolean unless otherwise noted

/**
 * Getting an instance of the xHelp event service
 */
$_eventSrv =& xhelp_eventService::singleton();

/**
 * Listening for an event
 * @param string $event_name Event to listen for
 * @param object $obj Object that will handle the event
 * @param string $function_name Name of callback function within object
 * @return int Value used to identify callback function in future
 */
$id = $_eventSrv->advise($event_name, $obj, $function_name);

/**
 * Stop listening for an event
 * @param string $event_name Event to stop listening to
 * @param int $id ID for callback function (from advise)
 */
$_eventSrv->unadvise($event_name, $id);

/**
 * Event: batch_delete_ticket
 * Triggered after a batch ticket deletion
 * @param array $tickets The xhelp\Ticket objects that were deleted
 */
function batch_delete_ticket($tickets)
{}

/**
 * Event: batch_dept
 * Triggered after a batch ticket department change
 * @param array $tickets The xhelp\Ticket objects that were modified
 * @param int $department The new department for the tickets
 */
function batch_dept($tickets, $department)
{}

/**
 * Event: batch_owner
 * Triggered after a batch ticket ownership change
 * @param array $tickets The xhelp\Ticket objects that were modified
 * @param int $owner The XOOPS UID of the new owner
 */
function batch_owner($tickets, $owner)
{}

/**
 * Event: batch_priority
 * Triggered after a batch ticket priority change
 * @param array $tickets The xhelp\Ticket objects that were modified
 * @param int $priority The new ticket priority
 */
function batch_priority($tickets, $priority)
{}

/**
 * Event: batch_response
 * Triggered after a batch response addition
 * Note: the $response->getVar('ticketid') field is empty for this function
 * @param array $tickets The xhelp\Ticket objects that were modified
 * @param xhelp\Responses $response The response added to each ticket
 */
function batch_response($tickets, $response)
{}

/**
 * Event: batch_status
 * Triggered after a batch ticket status change
 * @param array $tickets The xhelp\Ticket objects that were modified
 * @param xhelp\Status $status The new ticket status
 */
function batch_status($tickets, $status)
{}

/**
 * Event: close_ticket
 * Triggered after a ticket's status change from a status
 * with a state of XHELP_STATE_UNRESOLVED to a status
 * with a state of XHELP_STATE_RESOLVED
 * Also See: update_status, reopen_ticket
 * @param xhelp\Ticket $ticket The ticket that was closed
 */
function close_ticket($ticket)
{}

/**
 * Event: delete_department
 * Triggered after a department is removed from xHelp
 * @param xhelp\Department $dept Department that was removed
 */
function delete_department($dept)
{}

/**
 * Event: delete_field
 * Triggered after a custom field is removed from xHelp
 * @param xhelp\TicketField $field Custom field that was removed
 */
function delete_field($field)
{}

/**
 * Event: delete_file
 * Triggered after a file is removed from a ticket
 * @param xhelp\File $file File that was removed
 */
function delete_file($file)
{}

/**
 * Event: delete_responseTpl
 * Triggered after a staff auto-response is deleted
 * @param xhelpResponseTemplates $responseTpl auto-response that was deleted
 */
function delete_responseTpl($responseTpl)
{}

/**
 * Event: delete_staff
 * Triggered after a staff member is removed from xHelp
 * @param xhelp\Staff $staff Staff Member that was removed
 */
function delete_staff($staff)
{}

/**
 * Event: delete_ticket
 * Triggered after a ticket is deleted
 * @param xhelp\Ticket $ticket Ticket that was deleted
 */
function delete_ticket($ticket)
{}

/**
 * Event: edit_response
 * Triggered after a response has been modified
 * Also See: new_response
 * @param xhelp\Ticket $newTicket Ticket after modifications
 * @param xhelp\Responses $newResponse Modified response
 * @param xhelp\Ticket $oldTicket Ticket before modifications
 * @param xhelp\Responses $oldResponse Response modifications
 */
function edit_response($newTicket, $newResponse, $oldTicket, $oldResponse)
{}

/**
 * Event: edit_ticket
 * Triggered after a ticket is modified
 * @param xhelp\Ticket $oldTicket Ticket information before modifications
 * @param xhelp\Ticket $newTicket Ticket information after modifications
 */
function edit_ticket($oldTicket, $newTicket)
{}

/**
 * Event: merge_tickets
 * Triggered after two tickets are merged
 * @param int $ticket1 First ticketid being merged
 * @param int $ticket2 Second ticketid being merged
 * @param int $mergedTicket Resulting ticketid after merge
 */
function merge_tickets($ticket1, $ticket2, $mergedTicket)
{}

/**
 * Event: new_faq
 * Triggered after FAQ addition
 * @param xhelp\Ticket $ticket Ticket used as base for FAQ
 * @param xhelp\Faq $faq FAQ that was added
 */
function new_faq($ticket, $faq)
{}

/**
 * Event: new_file
 * Triggered after a file is added to a ticket
 * @param xhelp\Ticket $ticket Ticket file was attached to
 * @param xhelp\File $file Information about file uploaded
 */
function new_file($ticket, $file)
{}

/**
 * Event: new_response
 * Triggered after a response has been added to a ticket
 * @param xhelp\Ticket $ticket Ticket containing response
 * @param xhelp\Responses $newResponse Response that was added
 */
function new_response($ticket, $newResponse)
{}

/**
 * Event: new_response_rating
 * Triggered after a user rates a staff member response
 * @param xhelp\StaffReview $review Review specifics
 * @param xhelp\Ticket $ticket Ticket that was reviewed
 * @param xhelp\Responses $response Exact response that was reviewed
 */
function new_response_rating($review, $ticket, $response)
{}

/**
 * Event: new_ticket
 * Triggered after a ticket is added to the helpdesk
 * @param xhelp\Ticket $ticket Ticket that was added
 */
function new_ticket($ticket)
{}

/**
 * Event: new_user_by_email
 * Triggered after new user account is created during ticket submission
 * @param string $password Password for new account
 * @param XoopsUser $xoopsUser XOOPS user object for new account
 */
function new_user_by_email($password, $xoopsUser)
{}

/**
 * Event: reopen_ticket
 * Triggered after a ticket's status change from a status
 * with a state of XHELP_STATE_RESOLVED to a status
 * with a state of XHELP_STATE_UNRESOLVED
 * Also See: update_status, close_ticket
 * @param xhelp\Ticket $ticket The ticket that was re-opened
 */
function reopen_ticket($ticket)
{}


/**
 * Event: update_owner
 * Triggered after ticket ownership change (Individual)
 * Also See: batch_owner
 * @param xhelp\Ticket $ticket Ticket that was changed
 * @param int $oldOwner UID of previous owner
 * @param int $newOwner UID of new owner
 */
function update_owner($ticket, $oldOwner, $newOwner)
{}

/**
 * Event: update_priority
 * Triggered after a ticket priority is modified
 * Also See: batch_priority
 * @param xhelp\Ticket $ticket Ticket that was modified
 * @param int $oldPriority Previous ticket priority
 */
function update_priority($ticket, $oldPriority)
{}

/**
 * Event: update_status
 * Triggered after a ticket status change
 * Also See: batch_status, close_ticket, reopen_ticket
 * @param xhelp\Ticket $ticket The ticket that was modified
 * @param xhelp\Status $oldStatus The previous ticket status
 * @param xhelp\Status $newStatus The new ticket status
 */
function update_status($ticket, $oldStatus, $newStatus)
{}

/**
 * Event: view_ticket
 * Triggered after a staff member views a ticket
 * @param xhelp\Ticket $ticket Ticket that was viewed
 */
function view_ticket($ticket)
{}

```