# Blocks

## Block Styles

xHelp 0.7 adds the ability to flag a ticket as overdue. To see this flag in the xhelp blocks \(My Tickets, Recently Viewed Tickets\) you will need to add the following style to your xoops theme's stylesheet:

```css
#xhelp_dept_recent li.overdue {background-color:red;}
#xhelp_bOpenTickets li.overdue {background-color:red;}
```

In addition we recommend adding these styles to your theme's stylesheet

```css
#xhelp_dept_recent li {list-style:none;}
#xhelp_bOpenTickets li {list-style:none;}

#xhelp_dept_recent ul, #xhelp_dept_recent li {margin:0; padding:0;}
#xhelp_bOpenTickets ul, #xhelp_bOpenTickets li {margin:0; padding:0;}

#xhelp_dept_recent li {margin:0;padding-left:18px; background:url('../../modules/xhelp/assets/images/ticket-small.png') no-repeat 0 50%; line-height:16px;padding-bottom:2px;}
#xhelp_bOpenTickets li {margin:0;padding-left:18px; background:url('../../modules/xhelp/assets/images/ticket-small.png') no-repeat 0 50%; line-height:16px;padding-bottom:2px;}
```

