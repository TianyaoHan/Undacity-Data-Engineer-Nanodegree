## Data modeling on Sparkify Songs and User Activity

This project mainly focus on extracting and transforming JSON logs on user activity and JSON metadata of songs stored in Sparkify, a music streaming APP, into database. This database is built to support user activity analysis like what songs users are listening to. So star schema is chosen and concepts like fact and dimension table is used in the data modeling process.

Right down below you can read some instructions on how you can create the database.

### Running the ETL

First you should create the PostgreSQL database schema, by doing:

```
python create_tables.py
```

Then parse the logs and songs files:

```
python etl.py
```

Then you can get a database ready for analysis

### Database Schema

To get a clear understanding of the schema of this database, you can read the following table descriptions.

#### Song Plays table

- *Name:* `songplays`
- *Type:* Fact Table

| Column | Type | Description|
| ------ | ---- | ----|
|`songplay_id` |`INTEGER`|\ |
|`start_time` |`TIMESTAMP`|The time user start to play the song|
|`user_id` |`VARCHAR(50)`|\ |
|`level`| `VARCHAR(4)`|The level stands for the user app plans (`paid` or `free`)|
|`song_id` |`VARCHAR(20)`| \ |
|`artist_id` |`VARCHAR(20)`|\ | 
|`session_id` |`INTEGER`|\ |
|`location` |`VARCHAR(50)`|User's location |
|`user_agent` |`TEXT`|User's system, web browser or mobile app|

#### Users table

- *Name:* `users`
- *Type:* Dimension table

| Column | Type | Description |
| ------ | ---- | ----------- |
| `user_id` | `VARCHAR(50)` | \ |
| `first_name` | `VARCHAR(50)` | First name of the user |
| `last_name` | `VARCHAR(50)` | Last name of the user. |
| `gender` | `CHAR(1)` | The gender is stated with just one character `M` (male) or `F` (female). Otherwise it can be stated as `NULL` |
| `level` | `VARCHAR(4)` | The level stands for the user app plans (`paid` or `free`) |


#### Songs table

- *Name:* `songs`
- *Type:* Dimension table

| Column | Type | Description |
| ------ | ---- | ----------- |
| `song_id` | `VARCHAR(20)` | \ | 
| `title` | `VARCHAR(100)` | The title of the song. |
| `artist_id` | `VARCHAR(20)` | \ |
| `year` | `INTEGER` | The year that this song was made |
| `duration` | `REAL` | The duration of the song |


#### Artists table

- *Name:* `artists`
- *Type:* Dimension table

| Column | Type | Description |
| ------ | ---- | ----------- |
| `artist_id` | `VARCHAR(20)` | \ |
| `name` | `VARCHAR(100)` | \ |
| `location` | `VARCHAR(100)` | The location where the artist are from |
| `latitude` | `REAL` | The latitude of the location that the artist are from |
| `longitude` | `REAL` | The longitude of the location that the artist are from |

#### Time table

- *Name:* `time`
- *Type:* Dimension table

| Column | Type | Description |
| ------ | ---- | ----------- |
| `start_time` | `TIMESTAMP` | The timestamp itself, serves as the main identification of this table |
| `hour` | `INTEGER` | The hour in the start_time  |
| `day` | `INTEGER` | \ |
| `week` | `INTEGER` | \ |
| `month` | `INTEGER` | \ |
| `year` | `INTEGER` | \ |
| `weekday` | `INTEGER` | \ |

### ETL Pipeline

First built `sql_queries.py` as the query repository and import this library to other Python files to make code nicer.
Then built database using the `create_tables` file. Now an empty database is built.
Finally built the ETL pipeline using `etl.py`. In this file we wrote functions do deal with JSON logs and songs file. And we wrote a scanning function to walk through file system containing JSON files. Finally in our main function we wrote a for loop to parse every JSON file in our data folder. Below are some tips we summarized during the ETL procedure.  


### Some Tricky Tips:

#### Read json files to python in a batch manner in Linux 
```python
def get_files(filepath):
all_files = []
for root, dirs, files in os.walk(filepath):
    files = glob.glob(os.path.join(root,'*.json'))
    for f in files :
        all_files.append(os.path.abspath(f))

return all_files
```

#### Transform timestamp(ms) to datetime in Python
```python 
pd.to_datetime(timestamp,unit='ms')
```

#### Pass parameters to sql query
using tuples to pass parameters 
```python
cur.execute(song_select, (row.song, row.artist, row.length))
```
#### Pay much attention to write disconnect code in the script.
```python 
try:
    some sql executions 
except Exception as err:
    print(err)
    con.close() 

```
#### Linux login postgres database
```
sudo su - postgres 
```
