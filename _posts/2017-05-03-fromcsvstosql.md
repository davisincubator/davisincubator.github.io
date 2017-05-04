---
layout: post
title: "From CSVs to SQL"
date: 2017-05-03
author:
 - ryanphillips
---

Everyone knows that matlab is terrible, and I never want to use it again once I get out of this rattrap. But in order to do some serious data work in the serious world, you need to use a combination of Python and SQL. On the third hand, I couldn’t just throw my grad school life away and break free (I tried that, and it didn’t work).

So to resolve all of these competing interests, I undertook the task of moving my task data into a sql database.

But my data’s too small, you say? Don’t be so hard on yourself!

While it’s true Postgres is designed for working with millions of rows, it can still do a lot of very convenient things with a couple hundred, and, with a little experience, it can do them far faster and more easily than any other language I’ve come across.

# Outline:
- Installation
 - Installation of Python
 - Installation of Postgres
 - Installation of Text Editor (sublimetext)
- Uploading the data
 - From csv to postgres database
 - From postgres database to pandas table
- Connection
- Operation

----

# Installation

- Installation of Python
I don’t know, this is hard. (fill in details here)
- Installation of Postgres
Instructions here: https://www.postgresql.org/download/macosx/

Getting it running:

The first time you open up postgres, you define a database and a user name.

You can use the CREATE TABLE command to create tables within the database.

It’s a little confusing, but you need to know what table you want to ultimately have and work backwards, in terms of columns.

- Installation of text editor

My workflow is all about tweaking SQL queries, but I can find that they can get lost in python code/workspaces.

Also, generally, you set out to query some specific data, and once you have that data just the way you like, with all relevant caveats removed and stupid column names renamed, you probably want to keep it that way.

So I tend to save each SQL query in a separate text document, and upload those to github with very simple descriptive names and dates. I would recommend Sublimetext, although other editors work fine I’m sure.

I would also recommend a SQLBeautifier for sublime text, as it can make your code much more pleasant to look at.

# Uploading the Data

So, if you’re going to have subject names and one column of scores, you would do the following query:

~~~~

	CREATE TABLE subject_scores (
		subject_name varchar(255),
		score int
	)
	;
	
~~~~
	
varchar(255) and int refer to datatypes within sql.

Importantly, these columns need to match the columns on your csv, 
		IN ORDER
	And
		EXHAUSTIVELY.

This can be quite annoying and laborious, but I haven’t found a way around it short of editing the csv myself, which is about as much trouble. Don’t worry! Renaming columns and shuffling them around is what SQL is all about. It’s absolutely trivial.

It’s also why my create table looked like this:

~~~~
CREATE TABLE allcolumns ("ExperimentName" varchar(1000), Subject int, --Subject
 SESSION varchar(1000), ATarget varchar(1000), BakColor varchar(1000), "Clock.Information" varchar(1000), CueColor varchar(1000), CuePoint varchar(1000), "CueStim[Session]" varchar(1000), "DataFile.Basename" varchar(1000), "Display.RefreshRate" varchar(1000), ExperimentVersion varchar(1000), FixColor varchar(1000), FixPoint varchar(1000), FontBold varchar(1000), FontType varchar(1000), FontTypeFix varchar(1000), "Group" int, ProbeColor varchar(1000), ProbePoint varchar(1000), "ProbeStim[Session]" varchar(1000), RandomSeed varchar(1000), RuntimeCapabilities varchar(1000), RuntimeVersion varchar(1000), RuntimeVersionExpected varchar(1000), SessionDate date, --SessionDate
 SessionStartDateTimeUtc varchar(1000), SessionTime varchar(1000), StudioVersion varchar(1000), XTarget varchar(1000), Block varchar(1000), Allowed varchar(1000), BlockNum int, "Cue.ACC" varchar(1000), "Cue.CRESP" varchar(1000), "Cue.OnsetTime" double precision, "Cue.RESP" int, "Cue.RT" double precision, "Cue.RTTime" varchar(1000), CueCorrect varchar(1000), CueResponseAllow varchar(1000), "CueStim[Block]" varchar(1000), CueTime varchar(1000), DisplayStr varchar(1000), "DisplayText.RT" varchar(1000), DisplayWordTime varchar(1000), FixationTime varchar(1000), highSRP varchar(1000), highSRPnon varchar(1000), ISITime varchar(1000), ITITime varchar(1000), LoopControl varchar(1000), "LoopControl.Cycle" varchar(1000), "LoopControl.Sample" varchar(1000), lowSRP varchar(1000), lowSRPnon varchar(1000), "Probe.ACC" int, "Probe.CRESP" varchar(1000), "Probe.OnsetTime" double precision, "Probe.RESP" int, "Probe.RT" double precision, "Probe.RTTime" varchar(1000), ProbeCorrect varchar(1000), ProbeResponseAllow varchar(1000), "ProbeStim[Block]" varchar(1000), ProbeTime varchar(1000), PROCEDURE varchar(1000), Running varchar(1000), TimingFile varchar(1000), TrialNum int, TrialText varchar(1000), TrialType varchar(1000), Word varchar(1000), "WordResponse.RESP" int, WordResponseTime varchar(1000), WordType varchar(1000) ) ;
 
~~~~

Next, you upload the data from a csv into a file. So even though the previous step was based on the csv, postgres doesn’t “know” anything about the csv yet. Soon it will.

~~~~
 COPY allcolumns
FROM '/Users/rcphillips/Desktop/Dissertation/data/srp_01_task.csv' 
WITH (FORMAT CSV, HEADER true)
~~~~
Because we formatted the table within our database so exactly, we end up with the csv being reproduced within our database.

Now we can reshape it to get rid of the junk, with a new create table statement, but here we’re going to be creating the table and filling it at the same time. Maybe this is a good point about SQL. It’s designed to make accessing data within the database super, super smooth. No files, no paths… sometimes multiple databases, but often not.
~~~~
CREATE TABLE task_data AS (
SELECT 
	Subject as subject,
	SessionDate as session_date,
	BlockNum as block,
	TrialNum as trial,
	DisplayStr as word,
	"WordResponse.RESP" as word_response,
	"Cue.OnsetTime" as cue_onset,
	"Cue.RESP" as cue_response,
	"Cue.ACC" as cue_acc,
	"Cue.RT" as cue_rt,
	"Probe.OnsetTime" as probe_onset,
	"Probe.RESP" as probe_response,
	"Probe.ACC" as probe_acc,
	"Probe.RT" as probe_rt
FROM allcolumns
WHERE "Probe.RT" IS NOT NULL);
~~~~

The result: A beautiful table, with only the columns we want. I included an arbitrary WHERE “column name” IS NOT NULL to clean up the data still further, so now we have only columns and rows that are useful and nothing else. And we can keep that csv copy in the database, or we can delete it.


# Connection

I’m going to presuppose you have a nice jupyter notebook in front of you.

You’re going to need two things:

Psycopg2, which is a module the postgres people made for connecting to python
(more detail here: https://wiki.postgresql.org/wiki/Using_psycopg2_with_PostgreSQL)

And

SQLAlchemy, which is a module pythonic fans of SQL made for interfacing with psycopg2

And the third thing, which you should already have, is 

Pandas, which is an incredibly popular module that allows Python to do dataframes.

Having these all set up, this bit of code will connect to your local database:

~~~~
def main():
    #Define our connection string
    conn_string = "host='localhost' dbname='rcphillips' user='rcphillips' password='secret' "
    print "Connecting to database\m -> %s" % (conn_string)
    #as far as I know, you can put anything for your password, i don;t think it does anything on your local machine
    conn = psycopg2.connect(conn_string)
    
    cursor = conn.cursor()
    
    print "Connected!\n"
    cursor.execute("SELECT * FROM task_data")
    records = cursor.fetchall()
    pprint.pprint(records)
if __name__=="__main__":
    main()
~~~~

Then, you set up your “engine” using sqlalchemy:

~~~~
from sqlalchemy import create_engine
engine = create_engine('postgresql+psycopg2://rcphillips:secret@localhost/rcphillips')
~~~~


Then, just read your SQL query, with your engine:

~~~~
df = pd.read_sql_query(
    #TRIPLE QUOTES GO HERE               
SELECT 
    COUNT(*),
    cue_acc,
    probe_acc
FROM task_data
GROUP BY 2,3
#TRIPLE QUOTES GO HERE
,engine)
~~~~

Here, we’re even saving our query right into a data frame, which can be displayed (beautifully), or indexed, or any number of wonderful operations, including saving it back out to a csv!



I include this as an example because this is not just the data that we put in. By using the super simple GROUP BY function, I’m able to pull out aggregate information about the data very easily. This could be repeated for all subjects with just 9 more keystrokes :smirk:

I can from here set up a scatterplot using pandas and matplotlib

~~~~
import matplotlib.pyplot as plt; plt.rcdefaults()
import numpy as np
import matplotlib.pyplot as plt

%matplotlib inline

df.plot.bar(title="accuracy")

~~~~


