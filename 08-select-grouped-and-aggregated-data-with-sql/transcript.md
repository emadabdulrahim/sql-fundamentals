Our `Users` table currently has four rows of data. We've got a `tyler`, `debbie`, `mary`, and `patrick`. They each have unique user handles, but two of them share the same create date, 2019 January 10th. One has one in 2019 and the other is February 1st of 2019.

```sql 
select * from Users; 
  create_date |             user_handle              | last_name | first _name 
--------------+--------------------------------------+-----------+-------------
  2018-06-06  | 2839f831-f82c-faj3-aof3-fj28ddks39ek | clark     | tyler  
  2019-02-01  | 6ab3b2d2-8e02-890c-bb6d-61a67cd43f31 | jones     | debbie    
  2010-01-10  | a0eebc99-9c0b-42f8-g3eh-6bb9bd380a11 | freemon   | mary  
  2018-01-10  | ddda5534-f29b-45d3-83c0-21b84e0c9786 | johnson   | patrick  
(4 rows )

```

When weselect out data, I want to be able to group common column values together. For instance, I want to get a `count` of shared `create_dates`. 

```sql 
select count(*) from Users group by create_date;
  count
---------
      2
      1
      1

(3 rows)

```

The `group by` clause does exactly what you would assume. It combines the rows returned from theselect statement into groups. This is telling us that there are three groups of rows. Even though there are four rows in our users table, since we're grouping by `create_date`, and two rows share the same value, we get three groups. Just to show both sides, when none of the rows share, let's say, a `user_handle`, we'll get those four rows.

```sql 
select count(*) from Users group by user_handle;
  count
---------
      1
      1
      1
      1

(4 rows)

```

Just seeing the count isn't really helpful, because we don't know which create dates are actually shared. Let's pull that out as well inside of our `select` statement. Now we know that the January 10th date is the one that has two rows grouped together. 

```sql 
select count(*), create_date from Users group by create_date;
  count   | create_date 
----------+-----------------
      2   | 2019-01-10
      1   | 2018-06-06
      1   | 2019-02-01

(3 rows)

```

Let's also pull out the `first_name` column values as well. As you can see, we're getting an error. 

```sql 
select count(*), create_date, first_name from Users group by create_date;
ERROR: column "users.first_name" must appear in the GROUP BY clause or be used in an aggregate function
LINE 1:select count(*), create_date, first_name from Users group by...


```

This is telling us that the `first_name` column needs to be added to the `group by` in order to pull this out. Remember, our rows are being grouped together by create date. Because more than one row can be in a group, as in our January 10th example, our database doesn't know which `first_name` value to pull out of the grouping, which is why we're getting this error.

If we add `first_name` to the `group by`, we no longer get this error, but instead get all four rows back. 

```sql 
select count(*), create_date, first_name from Users group by create_date, first_name;
  count   | create_date    | first_name 
----------+----------------+--------------
      1   | 2018-06-06     | tyler
      1   | 2019-01-10     | patrick
      1   | 2019-01-10     | mary
      1   | 2019-02-01     | debbie

(4 rows)


```

This is because our `group by` is now looking to create groups that share `create_date` and `first_name`. Because none of our rows share these two columns, we get all four rows out. Keeping this mentality of grouping in mind, most SQL databases have built-in aggregate functions. For example, if we wanted to find what the lowest value create date was within our table, we'd use the `min()` function. 

```sql 
select min(create_date) from Users;
    min
------------
  2018-06-06
(1 row) 


```

Aggregate functions are closely related to grouping operations like group by, which is why we'll get the same error as we did before when trying to pull out our `first_name` column with the `min()` function.

```sql 
select min(create_date), first_name from Users;
ERROR: column "users.first_name" must appear in the GROUP BY clause or be used in an aggregate function
LINE 1:select min(create_date), first_name from Users;


```

As a side note, if you're curious on how to accomplish the task I talked about in this lesson, I'll post those two queries in the notes to this video. We need to do a little bit more than just `group by` and aggregate the `create_date` values. Some other common aggregate functions are `max()`, which does the opposite of `min()`, there's `sum()`, which sums up values, and average. 

```sql 
select max(create_date) from Users;
    min
------------
  2019-02-01
(1 row) 


```

Be sure to check the notes to this video for links to popular SQL databases that implement these, as well as their own functions.
