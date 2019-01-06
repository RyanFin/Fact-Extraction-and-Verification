# Fact-Extraction-and-Verification

This is the repository for the task of Fact Extraction and Verification described in the NAACL2018 paper: : [FEVER: A large-scale dataset for Fact Extraction and VERification.]()

>Unlike other tasks and despite recent interest, research in textual claim verification has been hindered by the lack of large-scale manually annotated datasets. In this paper we introduce a new publicly available dataset for verification against textual sources, FEVER: Fact Extraction and VERification. It consists of 185,441 claims generated by altering sentences extracted from Wikipedia and subsequently verified without knowledge of the sentence they were derived from. The claims are classified as Supported, Refuted or NotEnoughInfo by annotators achieving 0.6841 in Fleiss κ. For the first two classes, the annotators also recorded the sentence(s) forming the necessary evidence for their judgment. 

## Data Preparation

Download the FEVER dataset from the [website](http://fever.ai/data.html) into the data directory

    mkdir data
    mkdir data/fever-data
    
    # We use the data used in the baseline paper
    wget -O data/fever-data/train.jsonl https://s3-eu-west-1.amazonaws.com/fever.public/train.jsonl
    wget -O data/fever-data/dev.jsonl https://s3-eu-west-1.amazonaws.com/fever.public/paper_dev.jsonl
    wget -O data/fever-data/test.jsonl https://s3-eu-west-1.amazonaws.com/fever.public/paper_test.jsonl
    
The data preparation consists of three steps: downloading the articles from Wikipedia, indexing these for the Evidence Retrieval and performing the negative sampling for training.  

### 1. Download Wikipedia data
Download the pre-processed Wikipedia articles from [the website](https://sheffieldnlp.github.io/fever/data.html) and unzip it into the data folder.
    
    wget https://s3-eu-west-1.amazonaws.com/fever.public/wiki-pages.zip
    unzip wiki-pages.zip -d data
    
Wiki-pages data is in the dictionary form. The keys are "id", "text" and "lines" that mean the wiki-page URL, content of the wiki-page and each sentence in the content. Note that the sentence is seperated by \n and the marking number starts from 0. 
 
### 2. Construct SQLite Database
Construct an SQLite Database. A commercial personal laptop seems not work when dealing with the entire database as a single file so we split the Wikipedia database into a few files too. 
    
    python build_db.py

### 3. Create Term-Document count matrix and the TF-IDF matrix
Create a term-document count matrix 

    python build_count_matrix.py data/fever/fever0.db data/index
    
Create the TF-IDF 

    python reweight_count_matrix.py data/index/tfidf-fever0-ngram=1-hash=16777216.npz data/index --model tfidf
 

## Baseline

 The baseline model retrieve documents and select senetences based on TF-IDF respectively, which is done in IPython notebook `fever.ipynb` and implemented in `fever.py`. The class `Oracle` reads either TF-IDF matrix and have methods for finding relevant documents and sentences given the input claim.
  
## Find Out More

 Visit [http://fever.ai](http://fever.ai) to find out more about the shared task and download the data.
