
# Data Cleaning

If you are downloading files from the link given in the Readme File and below, you must notice that, all files are ```JSON``` files with huge size. Only some of them are in MB's but more of them are in GB's. I designed a way to reduce file size of these ```JSON``` files using some manipulation in their columns. I just removed some un-necessary columns and that helped a lot actually. I recommend you to change the type of ```rating``` column from ```int64``` to ```int32```. That will release a lot of memory.

_You can use this approach in any other project too with such a large files_

## Data Source

 - [uscd.edu](http://deepyeti.ucsd.edu/jianmo/amazon/index.html)
 

  
## Data Cleaning

### Import Required Libraries


```python
import pandas as pd
import json
```

#### _Generic function to Clean Data for both Proposed Methods_


```python
def rearrange_columns(my_df):
    my_df.rename({'overall':'rating', 'asin':'product_id', 'unixReviewTime':'date'}, axis=1, inplace=True)
    my_df.drop(['image', 'style', 'summary', 'reviewerName', 'reviewTime', 'reviewText'], axis=1, inplace=True)
    my_df.drop_duplicates(inplace=True)
```

## _Method - 1_

### Generic Methods to Read all json files in our project


```python
def parse(path):
    raw_file = open(path, 'rb')
    for ele in raw_file:
        yield json.loads(ele)
        

def get_data_frame(path):
    idx = 0
    df_dict = {}
    for curr_dict in parse(path):
        df_dict[idx] = curr_dict
        idx += 1
    return pd.DataFrame.from_dict(df_dict, orient='index')
```

You can also use this method to read all the json files available at the link given in _README_ file for data Downloading.
In this way the code changes to this for reading files is given as


```python
df = get_data_frame('E:\\amazon-eda-datasets\\Your_File.json')
```

### _Note- If you are going to use above method, make sure your computer has enough memory that it won't run short. If Your COMPUTER is short in memory like mine then follow the method given below_



## _Method - 2_

### _Chunking Method_

_This method will not only help you in this project for reading files, but also helps you in reading huge size files in any other project. In this method, we read the file in chunks of desired size, and we will get a JsonReader object that contains number of rows of DataFrame equal to the chunksize_

_Our JSON File contains some characters that are not a part of JSON files. That's why we are using `lines=True` . Here Chunksize is only 50000, that means every chunk will contain 50000 of rows read from JSON file. You can also change it to desired number


```python
chunksize = 100000
chunks_df = pd.read_json('E:\\amazon-eda-datasets\\All_Beauty.json', lines=True, chunksize=chunksize)
```


```python
type(chunks_df)

```
    Output: pandas.io.json._json.JsonReader



_We can not manipulate the JsonReader object as Pandas DataFrame. Thus we need to convert all the chunks in df_chunk to a Pandas DataFrame_


```python
# Create a list
# Append each chunk after performing column manipulation on it
all_chunks = []

for current_chunk in chunks_df:
    
    refined_chunk = rearrange_columns(current_chunk)
    all_chunks.append(refined_chunk)

# Create a main pandas DataFrame by concatenating all the chunks in the list
main_pandas_df = pd.concat(all_chunks)
```
Type the following to get DataFrame information
```python
main_pandas_df.info()
```
Type the following to convert DataFrame to `CSV` File
```python
main_pandas_df.to_csv('E:\\amazon-eda-datasets\\test.csv', index=False)
```
Type the following to read recently created `CSV` File
```python
df_csv = pd.read_csv('E:\\amazon-eda-datasets\\test.csv')
```
Type the following to get DataFrame information
```python
df_csv.info()
```

  
## Support

For support, email arhamrumi007@gmail.com


  
## Connection Links 🔗
[![portfolio](https://img.shields.io/badge/my_portfolio-000?style=for-the-badge&logo=ko-fi&logoColor=white)](https://arham-rumi.netlify.app/)
[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/arham-rumi-94769b180/)
