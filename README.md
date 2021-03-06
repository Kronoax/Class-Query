# PHP Class-Query
This is a script to make life easier for beginners, medium and advanced programmers.
This script connects to the database using mysqli method further down has some usage examples. 

### Key Features
* uses Mysqli for database access (more security)
* Where between added
* Inner join added
* Builds pagination numbering added
* Can be used with friendly URL
* Queries using the class-Query is simple and practical
* Can use $q>show() and shows the SQL query
* Taken almost all functions have already been implemented in SQL
* Main functions are already mentioned with an example of SQL

## Requirements

* **PHP 5.3.7+**, PHP 5.4+ or PHP 5.5+
* **MySQL 5** database

#### ON YOUR SERVER (we use Linux Mint 17 Qiana here):
1. install Apache, MySQL, PHP and eventually PHPMyAdmin: [How to setup a LAMP stack on Ubuntu 12.04](http://www.dev-metal.com/setup-basic-lamp-stack-linux-apache-mysql-php-ubuntu-12-04/)
2. remove all files from the */var/www* (should only be Apache's index.html and your phpinfo()-containing .php right now) with `rm -r /var/www/*`,
otherwise things will get messy and git won't download the repo into a non-empty folder
3. copy the contents of the extracted class-query repository into /var/www ! In this tutorial we don't use a sub-folder,  so your index.php should go into /var/www !
Best way to do is cloning via git: `git clone https://github.com/offboard/class-query.git /var/www`
4. Run the three SQL statements in the *_installation/sql_statements/* run in the right order

#### CONFIGS IN THE CODE:

In *lib/Query/src/Config.php*:

5. enter your database credentials in $DB_USER, $DB_PASS etc.
6. enter your charset in $charset (we can do the configuration before using the code).
7. in $li you can Use true to enable li tag in your page with pagination.
8. in $after you decide whether to show the "after" button.
9. in $before you decide whether to show the "before" button.
10. in $message put true if you do not want the button before and after receive text messages put false will show symbols.
11. in $class_active is the class name of your stylesheet, if your page is active paging (put null if you want to put anything).
12. in $class_inactive is the class name of your stylesheet, if your page is inactive paging (put null if you want to put anything).
13. in $class_before use a class on the "before" (put null if you want to put anything).
14. in $class_after use a class on the "after" (put null if you want to put anything).


## Quick Install
```php
// Loading all required classes
require_once( __DIR__ . '/lib/autoload.php');
```

## Example Select From Table
```php
<?php
// Loading all required classes
require_once( __DIR__ . '/lib/autoload.php');

$q=new Query;
$q
        ->select()
        ->from('`user`')
        ->run();
/* -> 
        SELECT
                *
        FROM
                `user`
*/
?>
```

## Example Select With Order By And Limit
```php
<?php
// Loading all required classes
require_once( __DIR__ . '/lib/autoload.php');

// Find the user_id, name and email for all users from the `user` table
$q=new Query;
$q
        ->select(
                array(
                        '`user`.`user_id`',
                        '`user`.`name`',
                        '`user`.`email`'
                )
        )
        ->from('`user`')
        ->order_by('`user`.`name` ASC')
        ->limit(3)
        ->run();
/* -> 
        SELECT
                `user`.`user_id`,
                `user`.`name`,
                `user`.`email`
        FROM
                `user`
        ORDER BY
                `user`.`name` ASC
        LIMIT
                3
*/
if($q){
        $users=$q->get_selected();
        foreach($users as $user){
                echo
                        'Name:'.$user['name'].'<br />'.
                        'Email:'.$user['email'].'<br />'.
                        'User Id:'.$user['user_id'].'.<br />'.
                        '-----<br />'.
                        '';
        }
}
else{
        echo 'Sorry, no users found.';
}
?>
```

## Example Select With Criteria And Limit
```php
<?php
// Loading all required classes
require_once( __DIR__ . '/lib/autoload.php');

$id = 'BRA';
$q = new Query;
$q
        ->select()
        ->from('country')
        ->where_equal_to(
                array(
                    'Code' => $id,
                )
        )
        ->limit(1)
        ->run();
$data = $q->get_selected();
$count = $q->get_selected_count();
if (!($count > 0)) {
    echo 'Countries not found.' . "\n";
} else {
    // return print result
    echo
    'Code: ' . $data['Code'] . "<br>" .
    'Name: ' . $data['Name'] . "<br>" .
    'Population: ' . $data['Population'] . "<br>" .
    "<hr>";
}
?>
```

## Example Select With where between
```php
<?php
// Loading all required classes
require_once( __DIR__ . '/lib/autoload.php');

$q = new Query;
$q
        ->select()
        ->from('country')
        ->where_between(
                array(
                    'SurfaceArea' => array(50, 50000),
                    'Population' => array(10, 75000)
                )
        )
        ->run();

$data = $q->get_selected();
$count = $q->get_selected_count();

if (!($count > 0)) {
    echo 'Countries not found.' . "\n";
} else {
    foreach ($data as $dados) {
        echo
        'Code: ' . $dados['Code'] .
        'Name: ' . $dados['Name'] .
        'Population: ' . $dados['Population'];
    }
}
?>
```

## Example Select With pagination
```php
<?php
// Loading all required classes
require_once( __DIR__ . '/lib/autoload.php');

// get id of pagination
$id_post = filter_input(INPUT_GET, 'id');
// record limit
$limit = 5;
// defines if the parameter does not exist
$id = isset($id_post) ? $id_post : 1;

$q = new Query;
$q
        ->select()
        ->from('country')
        ->where_equal_to(
                array(
                    'Continent' => 5
                )
        )
        ->page($id)
        ->limit($limit)
        ->order_by('Code ASC')
        ->run();

$data = $q->get_selected();
$count = $q->get_selected_count();

if (!($count > 0)) {
    echo 'Countries not found.' . "\n";
} else {
    foreach ($data as $dados) {
        echo
        'Code: ' . $dados['Code'] . "<br>" .
        'Name: ' . $dados['Name'] . "<br>" .
        'Population: ' . $dados['Population'] . "<br>" .
        "<hr>";
    }
}

// configuration
$q->li = false;
$q->class_active = 'page active';
$q->class_inative = 'page';
$q->class_after = 'page';
$q->class_before = 'page';
$q->message = true;
print $q->make_pages('page.php?id=', $id_post);
?>
```

## Contribute

Please commit only in *develop* branch. The *master* branch will always contain the stable version.

## Current and further development

See active issues and requested features here:
https://github.com/offboard/class-query/issues?state=open

See the milestone tracking of 2.0 and the upcoming 3.0 (early 2015) here:
https://github.com/offboard/class-query/issues/milestones

## License

Licensed under [MIT](http://www.opensource.org/licenses/mit-license.php). Totally free for private or commercial projects.

