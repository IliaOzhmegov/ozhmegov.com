<!--
.. title: Polars vs Pandas: what is more convenient?
.. slug: 1
.. date: 2022-06-13 15:22:44 UTC+02:00
.. tags: python, polars, pandas, SQL
.. category: 
.. link: 
.. description: 
.. type: text
-->

<p align='center'>
    <img src='https://miro.medium.com/v2/resize:fit:1400/format:webp/1*KjTfbjgf7jq3cevrGxfSkQ.jpeg' alt='Polar vs Panda'>
    <em>“Polar vs Panda — We Bare Bears” By Miltonator</em>
</p>

There are a lot of [awesome dataframe libraries](https://github.com/jcmkk3/awesome-dataframes), and in this post, I would like 
to draw attention to their ergonomics by comparing Pandas and Polars Python libraries.

<!-- TEASER_END -->

It seems when we compare one of the most flexible and powerful data analysis library Pandas with Polars, the latter can boast of only its speed. Is it really true?

Well, the primary purpose of both libraries is to work with rectangular data (dataframes). So let’s take a few tables, an SQL query as a task, re-implement the query in Python using these libs and look at the resulting code.

## User Data

In order to make sure that both implementations produce identical outputs, we 
generate two tables “Bank Account Transactions” and “Customers” by a simple python [script](https://github.com/IliaOzhmegov/polars_vs_pandas/blob/master/data/generate_random_data.py).

<p align='center'>
    <img src='https://miro.medium.com/v2/resize:fit:1400/format:webp/1*ffaFkDtcAToy_XANqTCD6w.png' alt='Polar vs Panda'>
    <em>Customers Table</em>
</p>

<p align='center'>
    <img src='https://miro.medium.com/v2/resize:fit:1400/format:webp/1*YLlob3CKApV7rBy_l9_dfA.png' alt='Polar vs Panda'>
    <em>Bank Account Transactions table</em>
</p>

In general Docker file that launches the Postgres database and uploads the generated tables can be found in this GitHub repository as well as Jupyter notebook and source data.

## SQL query as the task


During data analysis may occur situation when a resulting task can be formulated as an SQL query below:

```{SQL}
SELECT
  t.transaction_category_id,
  sum(t.transaction_amount) AS sum_amount,
  count(DISTINCT t.user_id) AS num_users
FROM "transactions" t
JOIN "users" u USING (user_id)
WHERE t.is_blocked = False
  AND u.is_blocked = False
GROUP BY t.transaction_category_id
ORDER BY sum_amount DESC;}

```

And the expected output:

```{SQL}
transaction_category_id  | sum_amount | num_users
-------------------------+------------+-----------
                       2 |  5798.3896 |       752
                       7 |  1305.5405 |        63
                       8 |     -44.61 |        22
                       1 |  -732.8049 |       734
                       6 |   -1975.81 |       200
                       5 |  -5241.469 |       408
                       0 | -12952.933 |       538
                       3 | -14186.124 |       693
                       4 | -17524.623 |       596
(9 rows)
```

## Pandas Implementation
Both libraries require the same steps:

Both libraries require the same steps:

1. Reading
1. Joining
1. Filtering
1. Grouping and aggregating
1. Renaming and Sorting

And for Pandas one of the most probable implementation may look like this:

.. gist:: e86c0b683babf08cedb2fd7390ae8257


In this implementation one of the most annoying things is inability to filter out rows by only column referencing without putting dataframe’s name in the condition. Because this issue does not allow connect joining and grouping actions into a single pipeline block. Does Polars allow this?


### UPDATE on 28.11.2022:
Due to receiving a lot of feedback on my implementation of Pandas (Thanks guys), let me also add an alternative implementation. The main highlight of the current implementation is the usage of method query.

**Note**: Column names containing spaces or punctuations (besides underscores) or starting with digits must be surrounded by backticks. However, in my case, that is redundant.

The rest of the post I will leave as it was before :)


.. gist:: 9857a7921688b436ee1d8ff7951008d2

## Polars Implementation

Well, actually yes! Polars library allows such kind of self-referencing with the help of `pl.col`!

.. gist:: 35e8338233df6a83ba1e272ede8d2e33

It can be seen that code for polars looks more readable in general: it takes 
transactions table `t` and users table `u`, joins them and filters out blocked 
users and transactions by referencing itself, while pandas makes to save some 
middle state data of merging and filtering out, which is still okay. After 
that, both code snippets do grouping by category and summarization action over 
the corresponding column. However, polars allows us to give it an alias or 
rename it immediately. Sorting does not have much distinguishability that 
could deserve attention.


## Results
One of the most important features for the current case is that polars does not require creating a variable that keeps some middle state of data, it brings some sort of immutability which is always welcomed.

It is also important to note that the advertised new library has such clear caveats as a lack of some functionality (that may be never discovered for some projects or companies), less developed community that may mean a significant lack of dummy questions on StackOverflow and etc. Nevertheless, it is just an alternative way of manipulating dataframes.
