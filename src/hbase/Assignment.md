# Assignment 4 - Columnar Databases and HBase

## 7in7 - HBase - Day 1

### Part 1 - Interactive Reading

Read Day 1 and work through the examples in the chapter.
Save your final database in a directory `day1` as follows.

```
./save.bash wiki day1
```

You can use the same script to save your database between work sessions.
But you cannot save over an existing save. So use temporary names like:
`day1a`, `day1b`, etc. You can use the `./load.bash` script to restore
an empty database from a saved state.

Here are some additional tips:

* Skip **Configuring HBase**.
* Begin with the last command on p57: `version`.
* p59 - Do not run these in the shell, which is Ruby.
    These are illustrative examples in python.
* p64 - The last example is split over 2 lines. `hbase>` and `hbase*` are
    not part of the command.
* I have not been able to use the colons inside of quotes in the hbase commands. If they don't work with the colon, try them without.
* The command to allow unlimited  versions is:

`alter 'wiki', {NAME => 'text', VERSIONS => 2147483647 }`

### Part 2 - 7in7 - HBase Day 1 - Find

1. Figure our how to use the shell to do the following:

    * Delete individual column values in a row:

        ```
       hbase(main):012:0> delete 'wiki', 'Home', 'text:'
       0 row(s) in 0.0240 seconds

       hbase(main):013:0> get 'wiki', 'Home'
COLUMN           CELL
        0 row(s) in 0.0140 seconds
        ```

    * Delete an entire row
hbase(main):014:0> deleteall 'wiki', 'Home'
0 row(s) in 0.0080 seconds

hbase(main):015:0> get 'wiki', 'Home'
COLUMN           CELL
0 row(s) in 0.0080 seconds


2. Bookmark the HBase API documentation for the version of HBase you’re using.
https://hbase.apache.org/2.4/apidocs/

### Part 3 - Create a family database

#### Step 1 - Create an hbase table that represents a family.

Specifically, you should have column families for the following:
* personal information: names of family members and birthdays
* favorites: foods and vacation locations
* location information: addresses including street, city, state, and zip. and phone numbers

Place the Hbase code to create the families below:
```
create 'family', { NAME => 'personal information' }, { NAME => 'favorites' }, { NAME => 'location information' }
0 row(s) in 1.2290 seconds

=> Hbase::Table - family
hbase(main):017:0> describe 'family'
Table family is ENABLED
family
COLUMN FAMILIES DESCRIPTION
{NAME => 'favorites', BLOOMFILTER => 'ROW', VERSIONS => '1', I
N_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK
_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE',
MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZE => '65536
', REPLICATION_SCOPE => '0'}
{NAME => 'location information', BLOOMFILTER => 'ROW', VERSION
S => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALSE',
 DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION
=> 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZ
E => '65536', REPLICATION_SCOPE => '0'}
{NAME => 'personal information', BLOOMFILTER => 'ROW', VERSION
S => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALSE',
 DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION
=> 'NONE', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZ
E => '65536', REPLICATION_SCOPE => '0'}
3 row(s) in 0.0280 seconds




```

#### Step 2 - Load five rows of data.
Make sure to have at least one row with more than one favorite food and at least one row with more than one favorite vacation location. Place the Hbase code below:
```
hbase(main):018:0> put 'family', 'mom', 'personal information:name', 'Siva Padma'
0 row(s) in 0.0110 seconds

hbase(main):019:0> put 'family', 'mom', 'favorites:food1', 'Masala Dosa'
0 row(s) in 0.0140 seconds

hbase(main):020:0> put 'family', 'mom', 'favorites:vacation1', 'Warangal'
0 row(s) in 0.0130 seconds

hbase(main):021:0> put 'family', 'mom', 'favorites:vacation2', 'Delhi'
0 row(s) in 0.0080 seconds
put 'family', 'mom', 'location information:city', 'Hyderabad'
0 row(s) in 0.2750 seconds

hbase(main):002:0> put 'family', 'mom', 'location information:city', 'Secunderabad'
0 row(s) in 0.0130 seconds

hbase(main):003:0> scan 'family'
ROW              COLUMN+CELL
 mom             column=favorites:food1, timestamp=17630078025
                 47, value=Masala Dosa
 mom             column=favorites:vacation1, timestamp=1763007
                 850680, value=Warangal
 mom             column=favorites:vacation2, timestamp=1763007
                 877899, value=Delhi
 mom             column=location information:city, timestamp=1
                 763008052796, value=Secunderabad
 mom             column=personal information:name, timestamp=1
                 763007771182, value=Siva Padma
1 row(s) in 0.0510 seconds

hbase(main):004:0> put 'family','child2','personal information:name', 'Satwik'
0 row(s) in 0.0140 seconds

hbase(main):005:0> put 'family','child2','favorites:food1', 'Biryani'
0 row(s) in 0.0110 seconds
hbase(main):006:0> put 'family','child2','favorites:vacation1', 'Australia'
0 row(s) in 0.0080 seconds

hbase(main):007:0> put 'family','child2','location information:city', 'Gandhi Nagar'
0 row(s) in 0.0070 seconds
hbase(main):009:0> 'scan family'
=> "scan family"
hbase(main):010:0> scan 'family'
ROW              COLUMN+CELL
 child2          column=favorites:food1, timestamp=1763008413
                 229, value=Biryani
 child2          column=favorites:vacation1, timestamp=176300
                 8442703, value=Australia
 child2          column=location information:city, timestamp=
                 1763008529473, value=Gandhi Nagar
 child2          column=personal information:name, timestamp=
                 1763008377292, value=Satwik
 mom             column=favorites:food1, timestamp=1763007802
                 547, value=Masala Dosa
 mom             column=favorites:vacation1, timestamp=176300
                 7850680, value=Warangal
 mom             column=favorites:vacation2, timestamp=176300
                 7877899, value=Delhi
 mom             column=location information:city, timestamp=
                 1763008052796, value=Secunderabad
 mom             column=personal information:name, timestamp=
                 1763007771182, value=Siva Padma
2 row(s) in 0.0410 seconds

hbase(main):011:0> put 'family', 'child1', 'personal information:name', 'Susmitha'
0 row(s) in 0.0100 seconds

hbase(main):012:0> put 'family','child1','favorites:food1','Ice Cream'
0 row(s) in 0.0070 seconds
hbase(main):013:0> put 'family','child1','favorites:vacation1','Venice'
0 row(s) in 0.0090 seconds

hbase(main):014:0> put 'family','child1','location information:city','RTC X Roads'
0 row(s) in 0.0100 seconds
                                                            palakura Pappu'
0 row(s) in 0.0120 seconds

hbase(main):016:0> scan 'family'
ROW              COLUMN+CELL
 child1          column=favorites:food1, timestamp=17630090488
                 53, value=Ice Cream
 child1          column=favorites:food2, timestamp=17630091223
                 64, value=palakura Pappu
 child1          column=favorites:vacation1, timestamp=1763009
                 064232, value=Venice
 child1          column=location information:city, timestamp=1
                 763009083509, value=RTC X Roads
 child1          column=personal information:name, timestamp=1
                 763009027656, value=Susmitha
 child2          column=favorites:food1, timestamp=17630084132
                 29, value=Biryani
 child2          column=favorites:vacation1, timestamp=1763008
                 442703, value=Australia
 child2          column=location information:city, timestamp=1
                 763008529473, value=Gandhi Nagar
 child2          column=personal information:name, timestamp=1
                 763008377292, value=Satwik
 mom             column=favorites:food1, timestamp=17630078025
                 47, value=Masala Dosa
 mom             column=favorites:vacation1, timestamp=1763007
                 850680, value=Warangal
 mom             column=favorites:vacation2, timestamp=1763007
                 877899, value=Delhi
 mom             column=location information:city, timestamp=1
                 763008052796, value=Secunderabad
 mom             column=personal information:name, timestamp=1
                 763007771182, value=Siva Padma
3 row(s) in 0.0630 seconds

hbase(main):017:0> get 'family', 'child1'
COLUMN           CELL
 favorites:food1 timestamp=1763009048853, value=Ice Cream
 favorites:food2 timestamp=1763009122364, value=palakura Pappu
 favorites:vacat timestamp=1763009064232, value=Venice
 ion1
 location inform timestamp=1763009083509, value=RTC X Roads
 ation:city
 personal inform timestamp=1763009027656, value=Susmitha
 ation:name
5 row(s) in 0.0240 seconds

hbase(main):018:0> put 'family', 'dad', 'personal information:name', 'Sridhar'
0 row(s) in 0.0090 seconds

hbase(main):019:0> put 'family', 'dad', 'favorites:food1', 'Upma'
0 row(s) in 0.0090 seconds
                                                             Idli'
0 row(s) in 0.0100 seconds

hbase(main):021:0> put 'family', 'dad', 'favorites:vacation1', 'Vijayawada'
0 row(s) in 0.0140 seconds

hbase(main):022:0> put 'family', 'dad', 'location information:city','Kashi'
0 row(s) in 0.0100 seconds

hbase(main):023:0> scan 'family'
ROW              COLUMN+CELL
 child1          column=favorites:food1, timestamp=17630090488
                 53, value=Ice Cream
 child1          column=favorites:food2, timestamp=17630091223
                 64, value=palakura Pappu
 child1          column=favorites:vacation1, timestamp=1763009
                 064232, value=Venice
 child1          column=location information:city, timestamp=1
                 763009083509, value=RTC X Roads
 child1          column=personal information:name, timestamp=1
                 763009027656, value=Susmitha
 child2          column=favorites:food1, timestamp=17630084132
                 29, value=Biryani
 child2          column=favorites:vacation1, timestamp=1763008
                 442703, value=Australia
 child2          column=location information:city, timestamp=1
                 763008529473, value=Gandhi Nagar
 child2          column=personal information:name, timestamp=1
                 763008377292, value=Satwik
 dad             column=favorites:food1, timestamp=17630093810
                 44, value=Upma
 dad             column=favorites:food2, timestamp=17630094030
                 83, value= Idli
 dad             column=favorites:vacation1, timestamp=1763009
                 436340, value=Vijayawada
 dad             column=location information:city, timestamp=1
                 763009457400, value=Kashi
 dad             column=personal information:name, timestamp=1
                 763009366968, value=Sridhar
 mom             column=favorites:food1, timestamp=17630078025
                 47, value=Masala Dosa
 mom             column=favorites:vacation1, timestamp=1763007
                 850680, value=Warangal
 mom             column=favorites:vacation2, timestamp=1763007
                 877899, value=Delhi
 mom             column=location information:city, timestamp=1
                 763008052796, value=Secunderabad
 mom             column=personal information:name, timestamp=1
                 763007771182, value=Siva Padma
4 row(s) in 0.0600 seconds

hbase(main):024:0> get 'family', 'dad'
COLUMN           CELL
 favorites:food1 timestamp=1763009381044, value=Upma
 favorites:food2 timestamp=1763009403083, value= Idli
 favorites:vacat timestamp=1763009436340, value=Vijayawada
 ion1
 location inform timestamp=1763009457400, value=Kashi
 ation:city
 personal inform timestamp=1763009366968, value=Sridhar
 ation:name
5 row(s) in 0.0110 seconds

hbase(main):025:0> hbase(main):009:0> put 'family', 'grandma', 'personal information:name', 'Kamala'
SyntaxError: (hbase):25: syntax error, unexpected ':'

hbase(main):009:0> put 'family', 'grandma', 'personal information:name', 'Kamala'
           ^

hbase(main):026:0> put 'family', 'grandma', 'favorites:food1', 'Moong Dal Dosa'
0 row(s) in 0.0140 seconds

hbase(main):027:0> put 'family', 'grandma', 'favorites:vacation1', 'Gunadala'
0 row(s) in 0.0090 seconds

hbase(main):028:0> put 'family', 'grandma', 'location information:city', 'Kanakavali'
0 row(s) in 0.0120 seconds

hbase(main):029:0> scan 'family'
ROW              COLUMN+CELL
 child1          column=favorites:food1, timestamp=17630090488
                 53, value=Ice Cream
 child1          column=favorites:food2, timestamp=17630091223
                 64, value=palakura Pappu
 child1          column=favorites:vacation1, timestamp=1763009
                 064232, value=Venice
 child1          column=location information:city, timestamp=1
                 763009083509, value=RTC X Roads
 child1          column=personal information:name, timestamp=1
                 763009027656, value=Susmitha
 child2          column=favorites:food1, timestamp=17630084132
                 29, value=Biryani
 child2          column=favorites:vacation1, timestamp=1763008
                 442703, value=Australia
 child2          column=location information:city, timestamp=1
                 763008529473, value=Gandhi Nagar
 child2          column=personal information:name, timestamp=1
                 763008377292, value=Satwik
 dad             column=favorites:food1, timestamp=17630093810
                 44, value=Upma
 dad             column=favorites:food2, timestamp=17630094030
                 83, value= Idli
 dad             column=favorites:vacation1, timestamp=1763009
                 436340, value=Vijayawada
 dad             column=location information:city, timestamp=1
                 763009457400, value=Kashi
 dad             column=personal information:name, timestamp=1
                 763009366968, value=Sridhar
 grandma         column=favorites:food1, timestamp=17630096248
                 26, value=Moong Dal Dosa
 grandma         column=favorites:vacation1, timestamp=1763009
                 648681, value=Gunadala
 grandma         column=location information:city, timestamp=1
                 763009673141, value=Kanakavali
 mom             column=favorites:food1, timestamp=17630078025
                 47, value=Masala Dosa
 mom             column=favorites:vacation1, timestamp=1763007
                 850680, value=Warangal
 mom             column=favorites:vacation2, timestamp=1763007
                 877899, value=Delhi
 mom             column=location information:city, timestamp=1
                 763008052796, value=Secunderabad
 mom             column=personal information:name, timestamp=1
                 763007771182, value=Siva Padma
5 row(s) in 0.0290 seconds

hbase(main):030:0> get 'family', 'grandma'
COLUMN           CELL
 favorites:food1 timestamp=1763009624826, value=Moong Dal Dosa
 favorites:vacat timestamp=1763009648681, value=Gunadala
 ion1
 location inform timestamp=1763009673141, value=Kanakavali
 ation:city
3 row(s) in 0.0080 seconds
```
```

#### Step 3 – Create HBase queries for the items below.

Place the Hbase code **and the results** after each query.

**Query 1:** Get complete information for a specific family member.
```
hbase(main):031:0> get 'family', 'dad'
COLUMN           CELL
 favorites:food1 timestamp=1763009381044, value=Upma
 favorites:food2 timestamp=1763009403083, value= Idli
 favorites:vacat timestamp=1763009436340, value=Vijayawada
 ion1
 location inform timestamp=1763009457400, value=Kashi
 ation:city
 personal inform timestamp=1763009366968, value=Sridhar
 ation:name
5 row(s) in 0.0180 seconds
```

**Query 2:** View only personal information for all family members.
```
hbase(main):032:0> scan 'family', { COLUMNS => 'personal information' }
ROW              COLUMN+CELL
 child1          column=personal information:name, timestamp=1
                 763009027656, value=Susmitha
 child2          column=personal information:name, timestamp=1
                 763008377292, value=Satwik
 dad             column=personal information:name, timestamp=1
                 763009366968, value=Sridhar
 mom             column=personal information:name, timestamp=1
                 763007771182, value=Siva Padma
4 row(s) in 0.0340 seconds
```

**Query 3:** Get the name, favorite foods, and vacation locations for one family member.
```
hbase(main):033:0> get 'family', 'dad', { COLUMNS => ['personal information:name', 'favorites'] }
COLUMN           CELL
 favorites:food1 timestamp=1763009381044, value=Upma
 favorites:food2 timestamp=1763009403083, value= Idli
 favorites:vacat timestamp=1763009436340, value=Vijayawada
 ion1
 personal inform timestamp=1763009366968, value=Sridhar
 ation:name
4 row(s) in 0.0110 seconds
```

**Query 4:** Get a range of at least two family members.
```
hbase(main):034:0> scan 'family', { STARTROW => 'mom', STOPROW => 'dad~' }
ROW              COLUMN+CELL
0 row(s) in 0.0120 seconds
```

**Query 5:** Get the addresses for all family members.
```
hbase(main):035:0> scan 'family', {COLUMNS => ['location information:street', 'location information:city', 'location information:state', 'location information:zip', 'location information:phone'] }
ROW              COLUMN+CELL
 child1          column=location information:city, timestamp=1
                 763009083509, value=RTC X Roads
 child2          column=location information:city, timestamp=1
                 763008529473, value=Gandhi Nagar
 dad             column=location information:city, timestamp=1
                 763009457400, value=Kashi
 grandma         column=location information:city, timestamp=1
                 763009673141, value=Kanakavali
 mom             column=location information:city, timestamp=1
                 763008052796, value=Secunderabad
5 row(s) in 0.0330 seconds

hbase(main):036:0> scan 'family', { COLUMNS => 'favorites', FILTER => "ValueFilter(=, 'substring:Dosa')" }
ROW              COLUMN+CELL
 grandma         column=favorites:food1, timestamp=17630096248
                 26, value=Moong Dal Dosa
 mom             column=favorites:food1, timestamp=17630078025
                 47, value=Masala Dosa
2 row(s) in 0.0570 seconds

hbase(main):037:0> scan 'family', { COLUMNS => ['personal information','favorites'], FILTER => "MultipleColumnPrefixFilter('name','vacation')" }
ROW              COLUMN+CELL
 child1          column=favorites:vacation1, timestamp=1763009
                 064232, value=Venice
 child1          column=personal information:name, timestamp=1
                 763009027656, value=Susmitha
 child2          column=favorites:vacation1, timestamp=1763008
                 442703, value=Australia
 child2          column=personal information:name, timestamp=1
                 763008377292, value=Satwik
 dad             column=favorites:vacation1, timestamp=1763009
                 436340, value=Vijayawada
 dad             column=personal information:name, timestamp=1
                 763009366968, value=Sridhar
 grandma         column=favorites:vacation1, timestamp=1763009
                 648681, value=Gunadala
 mom             column=favorites:vacation1, timestamp=1763007
                 850680, value=Warangal
 mom             column=favorites:vacation2, timestamp=1763007
                 877899, value=Delhi
 mom             column=personal information:name, timestamp=1
                 763007771182, value=Siva Padma
5 row(s) in 0.0300 seconds
```

**Query 6:** Get the names of family members who like a specific favorite food (e.g., pizza).
```
hbase(main):038:0> scan 'family', { COLUMNS => 'favorites', FILTER => "ValueFilter(=, 'substring:Dosa')" }
ROW              COLUMN+CELL
 grandma         column=favorites:food1, timestamp=17630096248
                 26, value=Moong Dal Dosa
 mom             column=favorites:food1, timestamp=17630078025
                 47, value=Masala Dosa
2 row(s) in 0.0150 seconds
```

**Query 7:** Create a vacation preference list with names.
```
hbase(main):039:0> scan 'family', { COLUMNS => ['personal information','favorites'], FILTER => "MultipleColumnPrefixFilter('name','vacation')" }
ROW              COLUMN+CELL
 child1          column=favorites:vacation1, timestamp=1763009
                 064232, value=Venice
 child1          column=personal information:name, timestamp=1
                 763009027656, value=Susmitha
 child2          column=favorites:vacation1, timestamp=1763008
                 442703, value=Australia
 child2          column=personal information:name, timestamp=1
                 763008377292, value=Satwik
 dad             column=favorites:vacation1, timestamp=1763009
                 436340, value=Vijayawada
 dad             column=personal information:name, timestamp=1
                 763009366968, value=Sridhar
 grandma         column=favorites:vacation1, timestamp=1763009
                 648681, value=Gunadala
 mom             column=favorites:vacation1, timestamp=1763007
                 850680, value=Warangal
 mom             column=favorites:vacation2, timestamp=1763007
                 877899, value=Delhi
 mom             column=personal information:name, timestamp=1
                 763007771182, value=Siva Padma
5 row(s) in 0.0220 seconds
```

### Part 4 - 7in7 - Day 3 - Wrap Up

Read Day 3 - Wrap Up. Then answer the following.

1. List the pros of HBase as described in our text.

    ```
   Horizontally scalable
   Built for massive datasets
   Real-time read/write access
   Strong consistency
   Flexible Schema
   Automatic Sharding
    ```

2. List the cons of HBase as described in our text.

    ```
   Not suitable for complex queries
   Not suitable for small Datasets
   Requires careful lowkey design
   Not ideal for full table scans
   Not good for highly relational data
    ```


### 7in7 - Day 2 - OPTIONAL

OPTIONAL - You may safely skip Day 2.

This section contains a code-heavy example of loading a large amount
of data into your HBase database. If you feel like a challenge and are
interested, feel free to work through it.

If you do this section, please **do not** use ./save.bash to save
your database, because it may become very large.

So if you are ready for the challenge, below are some instructions to
help you along. Good luck!

1. Download and extract the source code for the text: https://pragprog.com/titles/pwrdata/seven-databases-in-seven-weeks-second-edition/

2. Drag the following files into 02_hbase/local/scripts on GitPod.
    * source_code/02_hbase/import_from_wikipedia.rb
    * source_code/02_hbase/create_wiki_schema.rb

3. Start your database.

4. Run the following to create the wiki table.

    ```
    ./shell.bash create_wiki_schema.rb
    ```

5. Now you should be able to run the command below.
    BEFORE YOU DO... be ready to press CTRL+C to stop the process. This
    command will load a lot of data very fast.

    ```
    curl https://dumps.wikimedia.org/enwiki/latest/enwiki-latest-pages-articles.xml.bz2 | bzcat | ./shell.bash import_from_wikipedia.rb
    ```

6. After it appears to be working, press CTRL+C to stop it.

7. Connect to your database.

8. Run the command below to count the number of
    rows in your 'wiki' table.

    ```
    count 'wiki'
    ```

    Copy and paste the output of this command below.

9. Run the command below to get information about your database's regions.

    ```
    scan 'hbase:meta',{FILTER=>"PrefixFilter('wiki')"}
    ```

    Copy and past the output of this command below.
