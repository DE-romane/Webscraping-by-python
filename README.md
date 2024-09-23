
---

# Web Scraping using Python - Project


## Project Overview
This project extracts information about the **top 50 movies** with the best average ratings from a specified webpage. The extracted data, which includes the **Average Rank**, **Film Title**, and **Year of Release**, is stored in both **CSV** format and an **SQLite database** for further use.

### Webpage Used for Extraction
- URL: [Top 100 Highly-Ranked Films (EverybodyWiki)](https://web.archive.org/web/20230902185655/https://en.everybodywiki.com/100_Most_Highly-Ranked_Films)

## Libraries Used

- **`pandas`**: For data storage and manipulation.
- **`BeautifulSoup`** (from `bs4`): For parsing the HTML document.
- **`requests`**: To retrieve the webpage HTML.
- **`sqlite3`**: For creating and interacting with an SQLite database.


## Project Structure

### 1. Initialization

- **URL of the webpage**: The webpage containing the top 100 highly-ranked films.
- **Database and Table Name**: For storing the extracted data in SQLite.
- **CSV File Path**: For saving the data in CSV format.

```python
url = 'https://web.archive.org/web/20230902185655/https://en.everybodywiki.com/100_Most_Highly-Ranked_Films'
db_name = 'Movies.db'
table_name = 'Top_50'
csv_path = 'top_50_films.csv'

df = pd.DataFrame(columns=["Average Rank","Film","Year"])
count = 0
```

### 2. Web Scraping

We send an HTTP request to fetch the webpage's content and use **BeautifulSoup** to parse the HTML.

```python
html_page = requests.get(url).text
data = BeautifulSoup(html_page, 'html.parser')
```

### 3. Extracting the Data

We extract the movie information from the table in the HTML document:
- **`find_all('tbody')`**: Finds all table bodies.
- **`find_all('tr')`**: Finds all rows in the table.
- A loop is used to iterate over the rows, and the relevant data (Average Rank, Film Title, Year) is extracted.

```python
tables = data.find_all('tbody')
rows = tables[0].find_all('tr')

for row in rows:
    if count < 50:
        col = row.find_all('td')
        if len(col) != 0:
            data_dict = {
                "Average Rank": col[0].contents[0],
                "Film": col[1].contents[0],
                "Year": col[2].contents[0]
            }
            df1 = pd.DataFrame(data_dict, index=[0])
            df = pd.concat([df, df1], ignore_index=True)
            count += 1
    else:
        break

print(df)
```

### 4. Storing the Data

#### a. Saving to CSV
The extracted data is saved to a CSV file:

```python
df.to_csv(csv_path)
```

#### b. Saving to SQLite Database
The data is also stored in an SQLite database, with the table being named `Top_50`:

```python
conn = sqlite3.connect(db_name)
df.to_sql(table_name, conn, if_exists='replace', index=False)
conn.close()
```

## Outputs
- **CSV File**: `top_50_films.csv` – A file containing the top 50 movies.
- **SQLite Database**: `Movies.db` – A database containing a table `Top_50` with the same data.

## Project Summary
This project demonstrates how to:
- **Web scrape** data using `requests` and `BeautifulSoup`.
- **Extract** specific data from HTML tables.
- **Store** the data in both a CSV file and an SQLite database using `pandas` and `sqlite3`.
