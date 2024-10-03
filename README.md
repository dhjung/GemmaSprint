# Gemma Sprint
Aa part of Google ML bootcamp, I made fine tuned LLM model based on Google’s open model Gemma2-2b-instruct via HuggingFace and PyTorch.
Even though I used my local machien with nVidia GPU but this notebook will work on Google Colab with a single T4 GPU as well. (Maybe reducing train dataset volumn and parameters need to be adjusted to fit with Colab environment)

My goal is to fine-tune Gemma for a specific purpose using a technique known as Q-LoRA.
I focused on fine-tuning the Gemma2-2B Insturct model to translate from Text to SQL to remove hurdles to handle database for general users.
Fine-tuning involves refining LLM by training it further on a specific dataset tailored for a particular task.
This process sharpens the model’s ability to perform that task with higher accuracy.

## Base LLM Model
Gemma is a family of lightweight, state-of-the-art open models from Google, built from the same research and technology used to create the Gemini models. They are text-to-text, decoder-only large language models, available in English, with open weights for both pre-trained variants and instruction-tuned variants.
* https://huggingface.co/google/gemma-2-2b-it

## Training Dataset
I borrowed dataset from Huggingface (https://huggingface.co/datasets/gretelai/synthetic_text_to_sql)
The idea is to output a SQL query given a SQL Question and SQL Context.

To train Gemma Instruct model, train data had been adjusted to follow Gemma prompt format as follows:
```
<start_of_turn>user You are a powerful text-to-SQL model. Your job is to answer questions about a database. You are given a question and context regarding one or more tables:

 SQL Question: How many construction labor hours were spent on projects in the year 2019? 

 SQL Context: CREATE TABLE labor_hours (labor_hour_id INT, project_id INT, city VARCHAR(20), hours INT, year INT); INSERT INTO labor_hours (labor_hour_id, project_id, city, hours, year) VALUES (1, 201, 'Dallas', 100, 2020), (2, 201, 'Dallas', 200, 2019), (3, 202, 'Houston', 150, 2020);

 Generated SQL Query: <end_of_turn>

<start_of_turn> model SELECT SUM(hours) FROM labor_hours WHERE year = 2019; <end_of_turn>
```

## Fine-Tuned Gemma Model
* https://huggingface.co/DH21ML/gemma-2-2b-it-SQL-FineTuned

## Test with Chinook Database
* https://www.sqlitetutorial.net/sqlite-sample-database/
```
The Chinook sample database has 11 tables as follows:
employees table stores employee data such as id, last name, first name, etc. It also has a field named ReportsTo to specify who reports to whom.
customers table stores customer data.
invoices & invoice_items tables: these two tables store invoice data. The invoices table stores invoice header data and the invoice_items table stores the invoice line items data.
artists table stores artist data. It is a simple table that contains the id and name.
albums table stores data about a list of tracks. Each album belongs to one artist. However, one artist may have multiple albums.
media_types table stores media types such as MPEG audio and AAC audio files.
genres table stores music types such as rock, jazz, metal, etc.
tracks table stores the data of songs. Each track belongs to one album.
playlists & playlist_track tables: playlists table stores data about playlists. Each playlist contains a list of tracks. Each track may belong to multiple playlists. The relationship between the playlists and tracks tables is many-to-many. The playlist_track table is used to reflect this relationship.
```
- Text Question
--  Name the top 10 albums with the highest sales amount
- Generated SQL Query
```
SELECT a.Title, SUM(i.Quantity * t.UnitPrice) as total_sales 
FROM albums a 
INNER JOIN tracks t ON a.AlbumId = t.AlbumId 
INNER JOIN invoice_items i ON t.TrackId = i.TrackId 
INNER JOIN invoices inv ON i.InvoiceId = inv.InvoiceId 
GROUP BY a.Title 
ORDER BY total_sales DESC 
LIMIT 10; 
```
- Query Result
|   | Title | total_sales |
|:---|:---|:---|
| 0 |	Battlestar Galactica (Classic), Season 1 |	35.82 |
| 1 |	The Office, Season 3                     |	31.84 |
| 2 |	Minha Historia                           |	26.73 |
| 3 |	Lost, Season 2                           |	25.87 |
| 4 |	Heroes, Season 1                         |	25.87 |
| 5 |	Greatest Hits                            |	25.74 |
| 6 |	Unplugged                                |	24.75 |
| 7 |	Battlestar Galactica, Season 3           |	23.88 |
| 8 |	Lost, Season 3                           |	21.89 |
| 9 |	Acústico                                 |	21.78 |

## References
* Efficient Fine-Tuning for Llama-v2–7b on a Single GPU
* Understanding and Using Supervised Fine-Tuning (SFT) for Language Models (substack.com)
* SQL Generation in Text2SQL with TinyLlama’s LLM Fine-tuning (analyticsvidhya.com)
