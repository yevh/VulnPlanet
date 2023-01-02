# Insecure direct object reference (IDOR)

The code below is vulnureable to IDOR


```
<?php

require_once('../_helpers/strip.php');

// this database contains a table with 2 rows
$db = new SQLite3('test.db');

$id = $_GET['id'];

if (strlen($id) > 0) {
  $query = $db->query('select * from secrets where id = ' . (int)$id);

  while ($row = $query->fetchArray()) {
    echo 'Secret: ' . $row['secret'];
  }

  echo '<br /><br /><a href="/">Go back</a>';
} else {
  // view all the user's secrets (WHERE user_id = 1)
  $query = $db->query('select * from secrets where user_id = 1');

  echo '<strong>Your secrets</strong><br /><br />';

  while ($row = $query->fetchArray()) {
    echo '<a href="/?id=' . $row['id'] . '">#' . $row['id'] . '</a><br />';
  }
```

# Why it's vulnerable?
The code is vulnerable to insecure direct object reference (IDOR). In this code, the $id variable is taken from the $_GET array and passed to the select statement in the $query variable without any sanitization or authorization checks. 

# Impact?
An attacker could potentially manipulate the id parameter in the URL and use it to access any secret from the secrets table, regardless of whether they are authorized to do so.

# How to fix?
To prevent this vulnerability, the code should enforce access control checks to make sure that the user is authorized to access the secret with the given id. This can be done by adding a clause to the select statement that only allows secrets with a matching user_id to be selected, or by checking the user's credentials before allowing the select statement to be executed. Additionally, the id parameter should be sanitized to prevent SQL injection attacks.
