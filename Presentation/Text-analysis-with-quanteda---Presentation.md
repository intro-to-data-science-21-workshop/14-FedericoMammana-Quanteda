---
title: "Text analysis with quanteda - Presentation"
author: "Federico Mammana & Kathryn Malchow"
date: "04/11/2021"
output:  
  html_document:
    code_folding: show
    df_print: paged
    highlight: tango
    number_sections: no
    theme: cosmo
    toc: no
    keep_md: true
---



## What is quanteda?

quanteda is an R package to perform a variety of natural language processing tasks: corpus management, tokenization, analysis, visualization.

→ *quanteda is to text analysis what dplyr and tidyr are to data wrangling*

In quanteda, text is processed as:

- corpus → after having converted text data in corpus format (through the corpus function) we can work on it with quanteda; a corpus holds documents separately from each other.

- tokens → usually each word in a text, but also single characters or sentences if we want

- document-feature matrix (“dfm”) → the analytical unit in which perform analysis; text documents are organized in matrices, with original texts as rows and features as columns. “Features” are more generally defined than “terms”, as they may be raw terms, stemmed terms, terms without stopwords, etc.

![Document Feature Matrix](..\14-FedericoMammana-Quanteda-\DFM Pic.png)

## Why use quanteda?

quanteda is built to be faster and more efficient than any other R or Python package for processing large textual data. Infrastructure on three main pillars:

- stringi package for text processing

- Matrix package for sparse matrix objects

- computationally intensive processing (e.g. for tokens) handled in parallelized C++

**Intuitive, powerful, and flexible**


## 

Now lets see text preprocessing workflow and some functions!

## Loading Text Data into R


```r
library(readtext) # companion package to Quanteda to read text (.txt) files or comma-separated-value (.csv) files
library(quanteda) # for making a corpus and the rest of our text processing
```


### Read in text


```r
austen_texts = readtext("data/Austen_texts/*.txt", 
                       docvarsfrom= "filenames", dvsep = "_",
                       docvarnames = c("Author", "Book"))
austen_texts
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
  </script>
</div>


### Create a corpus


```r
book_corpus = corpus(austen_texts)
summary(book_corpus)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["Text"],"name":[1],"type":["chr"],"align":["left"]},{"label":["Types"],"name":[2],"type":["int"],"align":["right"]},{"label":["Tokens"],"name":[3],"type":["int"],"align":["right"]},{"label":["Sentences"],"name":[4],"type":["int"],"align":["right"]},{"label":["Author"],"name":[5],"type":["chr"],"align":["left"]},{"label":["Book"],"name":[6],"type":["chr"],"align":["left"]}],"data":[{"1":"austen_emma.txt","2":"8453","3":"196809","4":"7476","5":"austen","6":"emma","_rn_":"1"},{"1":"austen_northanger.txt","2":"7026","3":"95042","4":"3757","5":"austen","6":"northanger","_rn_":"2"},{"1":"austen_persuasion.txt","2":"6729","3":"100970","4":"3811","5":"austen","6":"persuasion","_rn_":"3"},{"1":"austen_pride.txt","2":"7468","3":"147401","4":"6111","5":"austen","6":"pride","_rn_":"4"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


## Preprocessing

### Tokenize the text


```r
text <- c("I <3 little pumpkins! OMG they're so cute.")


tokens(text, what = "sentence")
```

```
## Tokens consisting of 1 document.
## text1 :
## [1] "I <3 little pumpkins!" "OMG they're so cute."
```

```r
tokens(text, what = "character")
```

```
## Tokens consisting of 1 document.
## text1 :
##  [1] "I" "<" "3" "l" "i" "t" "t" "l" "e" "p" "u" "m"
## [ ... and 23 more ]
```

```r
text_token = tokens(text, what = "word") #default
text_token
```

```
## Tokens consisting of 1 document.
## text1 :
##  [1] "I"        "<"        "3"        "little"   "pumpkins" "!"       
##  [7] "OMG"      "they're"  "so"       "cute"     "."
```


### Remove punctuation, etc within tokens() function


```r
tokens(text,
remove_punct = TRUE,
remove_numbers = FALSE,
remove_symbols = TRUE)
```

```
## Tokens consisting of 1 document.
## text1 :
## [1] "I"        "3"        "little"   "pumpkins" "OMG"      "they're"  "so"      
## [8] "cute"
```


###  Make lower case


```r
# char_tolower(text) before tokenizing

tokens_tolower(text_token) #after tokenizing
```

```
## Tokens consisting of 1 document.
## text1 :
##  [1] "i"        "<"        "3"        "little"   "pumpkins" "!"       
##  [7] "omg"      "they're"  "so"       "cute"     "."
```


### You can also keep acronyms by adding: keep_acronyms = TRUE


```r
#char_tolower(text, keep_acronyms = TRUE) before tokenizing

tokens_tolower(text_token, keep_acronyms = TRUE)
```

```
## Tokens consisting of 1 document.
## text1 :
##  [1] "i"        "<"        "3"        "little"   "pumpkins" "!"       
##  [7] "OMG"      "they're"  "so"       "cute"     "."
```


### Removing Stop Words


```r
tokens_remove(text_token, stopwords("en"))
```

```
## Tokens consisting of 1 document.
## text1 :
## [1] "<"        "3"        "little"   "pumpkins" "!"        "OMG"      "cute"    
## [8] "."
```


### Removing other unwanted words


```r
tokens_remove(text_token, c("pumpkins", "cute"))
```

```
## Tokens consisting of 1 document.
## text1 :
## [1] "I"       "<"       "3"       "little"  "!"       "OMG"     "they're"
## [8] "so"      "."
```


### Word Stemming


```r
dip_text = c("When I dip, he dips, we are dipping.")

dip_token = tokens(dip_text)

tokens_wordstem(dip_token)
```

```
## Tokens consisting of 1 document.
## text1 :
##  [1] "When" "I"    "dip"  ","    "he"   "dip"  ","    "we"   "are"  "dip" 
## [11] "."
```


## KeyWords in Context, a cool function


```r
lr_text = c("Three Rings for the Elven-kings under the sky,
Seven for the Dwarf-lords in their halls of stone,
Nine for Mortal Men doomed to die,
One for the Dark Lord on his dark throne
In the Land of Mordor where the Shadows lie.
One Ring to rule them all, One Ring to find them,
One Ring to bring them all, and in the darkness bind them,
In the Land of Mordor where the Shadows lie.")

lr_token = tokens(lr_text)

kwic(lr_token, "Ring", window=2)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["docname"],"name":[1],"type":["chr"],"align":["left"]},{"label":["from"],"name":[2],"type":["int"],"align":["right"]},{"label":["to"],"name":[3],"type":["int"],"align":["right"]},{"label":["pre"],"name":[4],"type":["chr"],"align":["left"]},{"label":["keyword"],"name":[5],"type":["chr"],"align":["left"]},{"label":["post"],"name":[6],"type":["chr"],"align":["left"]},{"label":["pattern"],"name":[7],"type":["fct"],"align":["left"]}],"data":[{"1":"text1","2":"48","3":"48","4":". One","5":"Ring","6":"to rule","7":"Ring"},{"1":"text1","2":"55","3":"55","4":", One","5":"Ring","6":"to find","7":"Ring"},{"1":"text1","2":"61","3":"61","4":", One","5":"Ring","6":"to bring","7":"Ring"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


## Document Feature Matrix


```r
sample_token = tokens(data_corpus_inaugural)

sample_dfm = dfm(sample_token)

sample_dfm
```

```
## Document-feature matrix of: 59 documents, 9,439 features (91.84% sparse) and 4 docvars.
##                  features
## docs              fellow-citizens  of the senate and house representatives :
##   1789-Washington               1  71 116      1  48     2               2 1
##   1793-Washington               0  11  13      0   2     0               0 1
##   1797-Adams                    3 140 163      1 130     0               2 0
##   1801-Jefferson                2 104 130      0  81     0               0 1
##   1805-Jefferson                0 101 143      0  93     0               0 0
##   1809-Madison                  1  69 104      0  43     0               0 0
##                  features
## docs              among vicissitudes
##   1789-Washington     1            1
##   1793-Washington     0            0
##   1797-Adams          4            0
##   1801-Jefferson      1            0
##   1805-Jefferson      7            0
##   1809-Madison        0            0
## [ reached max_ndoc ... 53 more documents, reached max_nfeat ... 9,429 more features ]
```


### DFM Subset


```r
sample_dfm[1:5,1:7]
```

```
## Document-feature matrix of: 5 documents, 7 features (34.29% sparse) and 4 docvars.
##                  features
## docs              fellow-citizens  of the senate and house representatives
##   1789-Washington               1  71 116      1  48     2               2
##   1793-Washington               0  11  13      0   2     0               0
##   1797-Adams                    3 140 163      1 130     0               2
##   1801-Jefferson                2 104 130      0  81     0               0
##   1805-Jefferson                0 101 143      0  93     0               0
```


### DFM Trim


```r
dfm_trim(sample_dfm, min_termfreq = 30, max_termfreq = 100)
```

```
## Document-feature matrix of: 59 documents, 360 features (56.04% sparse) and 4 docvars.
##                  features
## docs              fellow-citizens could greater order day present hand whose
##   1789-Washington               1     3       1     2   2       5    3     2
##   1793-Washington               0     0       0     0   0       1    0     0
##   1797-Adams                    3     1       0     4   1       2    0     0
##   1801-Jefferson                2     0       1     1   1       0    0     2
##   1805-Jefferson                0     2       0     3   1       3    0     3
##   1809-Madison                  1     1       0     0   0       1    0     2
##                  features
## docs              love hopes
##   1789-Washington    2     1
##   1793-Washington    0     0
##   1797-Adams         5     0
##   1801-Jefferson     2     1
##   1805-Jefferson     1     0
##   1809-Madison       0     1
## [ reached max_ndoc ... 53 more documents, reached max_nfeat ... 350 more features ]
```
### DFM Top Features


```r
topfeatures(sample_dfm, 5)
```

```
##   the    of     ,   and     . 
## 10183  7180  7173  5406  5155
```


## Language Options

Quanteda automatically loads stop words from the "Stopwords" package with the "Snowball" collection of 15 languages


```r
library(stopwords)

stopwords_getsources()  #to see other sources with different languages 
```

```
## [1] "snowball"      "stopwords-iso" "misc"          "smart"        
## [5] "marimo"        "ancient"       "nltk"          "perseus"
```

```r
stopwords_getlanguages("snowball") #to see language options of sources
```

```
##  [1] "da" "de" "en" "es" "fi" "fr" "hu" "ir" "it" "nl" "no" "pt" "ro" "ru" "sv"
```



```r
it_poem = ("E chiese al vecchio dammi il pane
Ho poco tempo e troppa fame
E chiese al vecchio dammi il vino
Ho sete e sono un assassino
Gli occhi dischiuse il vecchio al giorno
Non si guardò neppure intorno
Ma versò il vino e spezzò il pane
Per chi diceva ho sete e ho fame")

tokens(it_poem) %>% 
  tokens_remove(stopwords("it"))
```

```
## Tokens consisting of 1 document.
## text1 :
##  [1] "chiese"  "vecchio" "dammi"   "pane"    "poco"    "tempo"   "troppa" 
##  [8] "fame"    "chiese"  "vecchio" "dammi"   "vino"   
## [ ... and 16 more ]
```
