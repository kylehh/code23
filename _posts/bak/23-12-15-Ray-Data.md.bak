---
title: Ray Data
mathjax: true
toc: true
categories:
  - Code
tags:
  - ML
---

This is not quite the ML topic, but more about using Ray Data library to run batch progressing. Yes, the core function of Ray Data is batch progress, and here are some learnings
## 1. Data ingestion
It can read in multiple data formats by `ray.data.read_format()`. Here I want to demo from a basic dictionary as input
```python
all_docs = []
for i in range(100):
    all_docs.append({"id": i,"doc": "this is a dummy doc with id "+str(i)})
    
ds = ray.data.from_items(all_docs)
```
You can view the data by `show()` or `take_batch()` function.
```python
ds.show(2)
#{'id': 0, 'doc': 'this is a dummy doc with id 0'}
#{'id': 1, 'doc': 'this is a dummy doc with id 1'}
```
```python
ds.take_batch(2)
#{
#  'id': array([0, 1]),
#   'doc': array(['this is a dummy doc with id 0', 'this is a dummy doc with id 1'],       dtype=object)}
```
The `take_batch` output is actually the input into map functions.
## 2. map, flat_map and map_batch
Let's define an embedding function first
```python
import openai
def generate_embedding(doc_batch):
    # Call embedding endpoint
    client = openai.OpenAI(
        base_url = "https://api.endpoints.anyscale.com/v1",
        api_key = "ANYSCALE_API_TOKEN"
    )
     # Note: not all arguments are currently supported and will be ignored by the backend.
    resp = client.embeddings.create(
        model="thenlper/gte-large",
        input=list(doc_batch['doc']),
    )
    #return doc_batch
    doc_batch["embeddings"] = [resp.data[i].embedding for i,_ in enumerate(doc_batch["doc"])]    
    return doc_batch
```
When you call **map_batch**, the `doc_batch` input to the embedding function, is same as the output of `ds.take_batch(5)`, because we set `batch_size` to 5.   
```python
ds_out = ds.map_batches(generate_embedding, batch_size=5)
# generate_embedding will get input as 
# {'id': array([0, 1, 2, 3, 4]), 'doc': array()}
# will iterate over all input
```
And when you write output, you should also write a new key 'embeddings' with List value, lising all 5 embedding arrays. Then you can use `show` or `take_batch` to see the contents of `ds_out`.

You can use **map** call as well, so instead of sending in a batch of items, it will send in one item at a time, so it's like the output `show`. So you need to modify function as below
```python
def generate_embedding(doc_single_row):
    ...
    resp = client.embeddings.create(
        model="thenlper/gte-large",
        #input=list(doc_batch['doc']),
        # If you call list(), it will generate a character list of the string, like ['t','h','i',...]
        input=doc_single_row['doc'],
    )
    ...
```
`map` **have to** return a dic, and value could be a list , but list of dics are NOT allowed
```python
def duplicate_row(row):
    ## This gives error
    # return {'key':['value':row['id']]}
    return {'key':[row['id'],row['id']]}
print(
    ray.data.range(3)
    .map(duplicate_row)
    .take_all()
)
##print results
##[{'key': [0, 0]}, {'key': [1, 1]}, {'key': [2, 2]}]
```

Last concept is **flat_map**, which is same as **map** to process one row at a time, but flat the results.

```python
def generate_embedding(doc_single_row):
    ...
    doc_batch["embeddings"] = resp.data[0].embedding
    # Need to add [] for flat map
    return [doc_batch]
    # You can also try following code to see how exactly flat wors
    #return [doc_batch] * 2
```
`flat_map` has to return **a list of dics w same keys**(or consider it as a list of `map` output)
```python
def duplicate_row(row):
    ## error if keys are different
    # return [{'key1':row['id']},{'key2':row['id']}]
    return [{'key':row['id']},{'key':row['id']}]
print(
    ray.data.range(3)
    .flat_map(duplicate_row)
    .take_all()
)
##print results
##[{'key': 0}, {'key': 0}, {'key': 1}, {'key': 1}, {'key': 2}, {'key': 2}]
```
## 3. batch size and some other parameters
*bath_size* controls the size of the batch, which is the most significant parameter.
