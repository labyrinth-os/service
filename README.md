# service
Short service for short time

Links:
1. https://dev471447.service-now.com/login.do?user_name=admin&sys_action=sysverb_login&user_password=wi6IB-E%25i3vF

## 8. UI Action
a)
```javascript
// Quick Resolve Incident

current.state = 6;
current.close_notes = 'Incident resolved using Quick Resolve button.';

current.update();

action.setRedirectURL(current);
```

b)
```javascript
// Reopen Incident

current.state = 2;

current.update();

action.setRedirectURL(current);

// specify condition current.state = 6
```
## 9. Client side UI policy
```javascript
function showWorkNotesReminder() {
    alert("Reminder: Please add Work Notes before updating the incident.");

    g_form.setFocus('work_notes');
}
```

## 10.
```javascript
// Escalate Incident Priority

current.impact = 1;
current.urgency = 1;

current.update();

action.setRedirectURL(current);
```

