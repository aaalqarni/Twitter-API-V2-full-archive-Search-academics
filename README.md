# Twitter for Academic Research

## Introduction
This is a fully functional example on how "Search Tweets (all)" Twitter API Endpoint (API V2) works. To make it work, you'll need:
- Request Access to the full [Twitter Archive for Academic Research](https://developer.twitter.com/en/solutions/academic-research)
- A Bearer Token (once your request access is aproved, you'll need to create a new "project" and generate the Bearer Token).

## Before Using
Before using, please carefully read the documentation available on the twitter API V2. This code is not intended to be a perfect example, but it can help you better understand how the V2 Twitter API works, and how to perform queries taking advantage of the access level for researchers. There you will find answers to many of the questions you may have.

[Search Tweets READ THE DOCS](https://developer.twitter.com/en/docs/twitter-api/tweets/search/introduction)

## Possible Dependencies
```bash
os
json
requests
Pandas
Openyxl # For xlsx datasets
tqdm (for progress bar)

Note: To install this packages use "pip install package_name" in your termninal.
```
## Setup
You'll need:
- Python3 installed
- Commandline interface (Windows/linux/MacOs terminals)
- Recommend PyCharm IDE or similar, with/or virtual enviroments.

**Define credentials (credentials.py)**
Copy/Paste your Twitter API Bearer Token in credentials.py

## workflow
### 1. Define your search jobs in capture_jobs.csv
You can define one or **multiple searchs** (one row per search). The script will iterate over the csv file to search tweets within parameters.
|start     |start_time|end       |end_time|query             |date_format|time_format|capture_name    |
|----------|----------|----------|--------|------------------|-----------|-----------|----------------|
|dd/mm/yyyy|hh:mm:ss  |dd/mm/yyyy|hh:mm:ss|your query|DD-MM-YYY  |HH:MM:SS   |capture folder name|

**Example 1**
|start     |start_time|end       |end_time|query             |date_format|time_format|capture_name    |
|----------|----------|----------|--------|------------------|-----------|-----------|----------------|
|20/01/2021|00:00:00  |27/01/2022|23:59:00|(#ucrania) lang:es|DD-MM-YYY  |HH:MM:SS   |ucrania_hashtags|

will search all tweets between 20 january 2021 at 00:00:00, to january 27 2022 at 23:59:59 with the hashtag #ucrania in spanish language

You can create **multiple rows**. Each row is a new capture, and it's useful to create a work queue.

**OUTPUT FOLDER** 
Define the output folder in "capture_name" field

### 2. Launch the script 
In terminal:
```
python3 main.py
```

## Script Workflow ##

```mermaid  
graph TD;  
	capture_jobs.csv -->|Step 0 Define all your capture parameters and run| main.py;
	main.py -->|Step 1| query.py;
	query.py -->|Create API request| Twitter_API;
	Twitter_API --> |API response control|api_response;
	api_response --> |create json file|json_dumper.py;
	main.py -->|Step 2| json_parser.py;
	json_dumper.py -->|create output folder|outputfolder;
	outputfolder -->|save API response|api_response_loop_n.json;
	json_parser.py -->|get all .json files|outputfolder;
	json_parser.py -->|generate csv| output.csv;
	
```

## API RATE LIMITS
Twitter API V2, and more precisely, Twitter Full-archive search for Academic Research, have a rate limit of 300 request in a 15 min window. Please, don't change sleep times between queries. 

## Handling errors

During execution, the API query, or when parsing the data, various errors can occur. The script contains the following error handling:
- Request too many requests to the API
- API Timeout
- Empty API responses
- ### Known Errors:
- On big extractions: Native Python3 Recursion limit to 1000 loops. Then recursion limit error. This may occur near 500k tweets retrieved.
