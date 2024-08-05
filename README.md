# SEBI_GOV_Data_Scrapping_Project
The project involved automating the data extraction process from the Securities and Exchange Board of India (SEBI) website using a combination of Selenium for web interaction and BeautifulSoup for HTML parsing. The goal was to scrape various tables from a dynamic web page and save the collected data into an Excel file for further analysis.



### Project Description: Web Scraping and Data Extraction from SEBI Website

**Objective:**  
The project involved automating the data extraction process from the Securities and Exchange Board of India (SEBI) website using a combination of `Selenium` for web interaction and `BeautifulSoup` for HTML parsing. The goal was to scrape various tables from a dynamic web page and save the collected data into an Excel file for further analysis.

**Steps Involved:**

1. **Setup and Initialization:**
   - Imported necessary libraries: `BeautifulSoup` for parsing HTML, `requests` for HTTP requests, `Selenium` for browser automation, and `pandas` for data manipulation.
   - Configured the `Selenium` WebDriver with `webdriver.Chrome()` to interact with the Chrome browser and automate tasks.

2. **Web Navigation and Interaction:**
   - **Open Web Page:** Used `Selenium` to navigate to the SEBI URL: `https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doPmr=yes`.
   - **Extract Dropdown Options:** Retrieved dropdown options from a `select` element on the page to determine the number of fields available.

3. **Dynamic Data Extraction:**
   - **Year and Month Selection:** Selected the year and month from dropdowns using `Selenium`'s `Select` class.
   - **Iterate Through Dropdown Options:** Loop through the dropdown options, selecting each option and clicking the "Go" button to generate the corresponding data.

4. **Data Extraction and Parsing:**
   - **Fetch Page Source:** After each interaction, retrieved the page source using `Selenium` and parsed it with `BeautifulSoup`.
   - **Extract Table Data:** Located and extracted data from multiple tables on the page:
     - **Tables:** Parsed various tables by their indices, extracting data from table cells.
     - **Data Cleaning:** Cleaned the extracted data, handling different structures and columns as necessary.

5. **Data Storage and Saving:**
   - **Create DataFrames:** Created multiple DataFrames (`df`, `df2`, `df3`, `df4`, `df5`, `df6`, `df7`, `df8`) to store the extracted data from different tables.
   - **Combine DataFrames:** Combined all DataFrames into a single DataFrame using `pd.concat()` to consolidate the data.
   - **Save to Excel:** Saved the final DataFrame to an Excel file named `SEBI_Gov_Scrapped_Data.xlsx` using `pandas`.

**Code Explanation:**

```python
from bs4 import BeautifulSoup
import requests
from selenium import webdriver 
from selenium.webdriver.common.by import By
from selenium.webdriver.support.select import Select
import time
import pandas as pd

# Initialize the Chrome WebDriver
url = "https://www.sebi.gov.in/sebiweb/other/OtherAction.do?doPmr=yes"
driver = webdriver.Chrome()
driver.get(url)
response = requests.get(url)
print(response)

# Retrieve and print the number of dropdown options
n_list = driver.find_elements(By.XPATH, '//select[@name="pmrId"]')
name_list = []
for i in n_list:
    name_list = i.text.split("\n")
len_name = len(name_list)
print(len_name)

# Select year and month
year = driver.find_element(By.NAME,"year")
yr_ele = Select(year)
yr_ele.select_by_visible_text("2024")

month = driver.find_element(By.NAME,"month")
mn_ele = Select(month)
mn_ele.select_by_visible_text("June")

# Initialize empty DataFrames
df = pd.DataFrame()
df2 = pd.DataFrame()
df3 = pd.DataFrame()
df4 = pd.DataFrame()
df5 = pd.DataFrame()
df6 = pd.DataFrame()
df7 = pd.DataFrame()
df8 = pd.DataFrame()

# Iterate through dropdown options and extract data
for num in range(1, len_name + 1):
    name = driver.find_element(By.NAME, "pmrId")
    name_ele = Select(name)
    name_ele.select_by_visible_text(name_list[num])

    go = driver.find_element(By.XPATH, '//div[@class="go_area go-area"]')
    go.click()
    time.sleep(1)
    
    # Get page source and parse with BeautifulSoup
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')

    try:
        # Extract data from various tables
        for idx, df_instance in enumerate([df, df2, df3, df4, df5, df6, df7, df8]):
            tab = soup.find_all("table")[idx]
            data = tab.find_all("td")
            cleaned_data = [i.text.strip() for i in data]
            df_instance.loc[len(df_instance)] = cleaned_data

    except Exception:
        # Handle exceptions and set empty row
        df.loc[len(df)] = None
    
# Combine all DataFrames into one
result = pd.concat([df, df2, df3, df4, df5, df6, df7, df8], axis=1)

# Save the combined DataFrame to an Excel file
result.to_excel('SEBI_Gov_Scrapped_Data.xlsx', index=False)
print("Table scraped and saved to 'SEBI_Gov_Scrapped_Data.xlsx'.")
```

**Technologies Used:**
- **Python Libraries:** `BeautifulSoup` for HTML parsing, `requests` for HTTP requests, `Selenium` for web automation, and `pandas` for data handling.
- **WebDriver:** `ChromeDriver` for browser automation.

**Key Achievements:**
- Automated the extraction of structured data from a dynamic web page.
- Successfully handled dropdown selections and data extraction from multiple tables.
- Consolidated and saved the data into an Excel file for easy access and analysis.

**Future Enhancements:**
- Implement more robust error handling and logging for better debugging.
- Enhance the data extraction logic to handle any additional or missing table structures.
- Consider integrating data validation and cleaning processes to ensure the accuracy of the saved data.

---

Feel free to adjust or expand upon this description based on specific details or results from your project.
