# mk-learning-db

This is a learning path for database.


## Choose the database

- Row-based database vs column-based database

Most relational databases, e.g. Mysql, are row-based databases. All column data of one row are stored in one block. If they are too big, they are stored in multiple blocks. Hopeful these blocks are side by side. 

Some databases are column-based databases, e.g. HBase. The same column data of many rows are stored in one block. Hopeful these blocks are side by side.

- which one is faster for read and write?

For business case A, a file has many attibutes like file name, file size, who updated the person, and etc. When change the file, most of these fields would be changed. We expect one IO read or write can read or write all fields of the row. So we prefer to save them in one block, or two blocks side by side (in case it is bigger than one block). For this case row-based database is the best choice. Further more, if trying to change 3 files, those 3 rows are same in 5 blocks (each row takes 1.5 blocks. Block 1, 2, 3 are used for row 1 and 2. Row 3 takes another 2 blocks, but they are far away from block 1, 2, and 3, stored in Block 90, and 91. These means it need more time for IO read/write due to seek the location of Block 90 and 91. By these case you may have a brief idea of database read and write performance. We you step further, you need consider more: indexes, data fragments, and etc. In brief, row-based database is suitable for random row read/write AND read/write mutliple (or even most) columns each time.

For business case B, a motor has 200 attributes to recording its running status. When scientists analyze the motor, they only focus on 10 attributs but cover all/large-volume records. Some other scientists like to do research on other 20 attributs of quit a lot rows. When writing, either write one row per write, or write hundreds of rows per write, and rarely make updates. For this case, a better way is to save one column data of all rows in one block or multiple blocks closer to each other. They goal is to read them out using less IO-reads. When batch writing, we expect to write the same column data as close as the same column data of pre-stored rows, to benefit the goal. It is rare case to update many/most columns of one record in the future. It is possible and reasonable to update a few columns mostly for large volume rows after the first writing. For this case, column-based database is preferred. 

## time-series database

Mostly, time-series database is column-based database, which suitable to write once (mostly) and read many times. There are two special designs: 1) A dataset (or table) has only one indexed column AND the column is timestamp. 2) Some selected columns can be indexed (or an alternative way of indexing) to speed up queries.   

Every row can have multiple timestamp columns/fields, but only one timestamp column is indexed. The timestamp precision can be customized when creating the dataset. This means if writing a new row with the same timestamp as a old row, for example, having 2 columns with the same column names and 3 columns with new column names, the database will overwrite those 2 columns and add 3 new columns according to the timestamp.   

While writing a new row with the same timestamp of a old row, if they have different indexed columns, they are treated as 2 different rows and would be overlapped.

So in brief, the uniqu key is the timestamp column plus indexed columns. The database are suitable to record the history truth data (mostly not changed) with perfest writing performance (less indexes). It also works speedly to get a few data (by timestamps and by column names) with very fast speed. It works efficiently to get large volumn data of a few columns. The key idea is to save the same data in the same/close blocks. 

## suggested databases

- time-series database: InfluxDB (very popular to record the history truth)
- row-based database: MySQL (very popular e-bussiness)
- column-based database: HBase (popular for all kinds of areas)

## Tutorials for InfluxDB


## Tutorials for MySQL


## Tutorials for HBase

TBD

