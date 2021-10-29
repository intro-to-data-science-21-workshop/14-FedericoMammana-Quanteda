---
title: "quanteda Live Tutorial"
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



## Part 1 - Preprocessing

### Tokenizing text


1. Tokenize the words in the following poem (*Ozymandias* by Percy Bysshe Shelley). Be sure to remove all punctuations and symbols while you do it.

I met a traveller from an antique land,\
Who said - “Two vast and trunkless legs of stone\
Stand in the desert... Near them, on the sand,\
Half sunk a shattered visage lies, whose frown,\
And wrinkled lip, and sneer of cold command,\
Tell that its sculptor well those passions read\
Which yet survive, stamped on these lifeless things,\
The hand that mocked them, and the heart that fed;\
And on the pedestal, these words appear:\
My name is Ozymandias, King of Kings;\
Look on my Works, ye Mighty, and despair!\
Nothing beside remains. Round the decay\
Of that colossal Wreck, boundless and bare\
The lone and level sands stretch far away.

2. Make the tokenized text lowercase.

3. Remove (english) stopwords from the tokenized text.


                                                                                                                                                      Answers ↓

```{.r .fold-hide}
library(quanteda)
library(dplyr)

#1.

poem_shelley = ("I met a traveller from an antique land,\
Who said - “Two vast and trunkless legs of stone\
Stand in the desert... Near them, on the sand,\
Half sunk a shattered visage lies, whose frown,\
And wrinkled lip, and sneer of cold command,\
Tell that its sculptor well those passions read\
Which yet survive, stamped on these lifeless things,\
The hand that mocked them, and the heart that fed;\
And on the pedestal, these words appear:\
My name is Ozymandias, King of Kings;\
Look on my Works, ye Mighty, and despair!\
Nothing beside remains. Round the decay\
Of that colossal Wreck, boundless and bare\
The lone and level sands stretch far away.")

#2. 

shelley_token = tokens(poem_shelley,
          remove_punct = TRUE,
          remove_symbols = TRUE) %>% 
  tokens_tolower()


#3.

shelley_token = tokens_remove(shelley_token, stopwords("en"))
```


---

## Part 2 - Visualize Text Data


### With a Frequency Plot of all Words and all Presidents

```r
library(quanteda.textstats) # for textstat_frequency()
library(ggplot2)

# Tokenize Data

inaugural_token = tokens(data_corpus_inaugural,
                         remove_punct = TRUE,
                         remove_symbols = TRUE) %>% 
                  tokens_tolower() %>% 
  tokens_remove(stopwords("en"))



# Make into DFM and Ggplot

dfm(inaugural_token) %>% #make into dfm
textstat_frequency(n = 50) %>% #select top 50
ggplot(aes(x = reorder(feature, -rank), y = frequency)) + #reorder from least frequent word to most
geom_point() +
theme(axis.text.x = element_text(angle = 90, hjust = 1))+ # turn words sideways
labs(x = "Feature", y = "Frequency")
```

![](quanteda-Live-Tutorial_files/figure-html/unnamed-chunk-2-1.png)<!-- -->


### With Keyword for each President


```r
# Get frequency of each feature by president and make dfm

president_dfm = textstat_frequency(dfm(inaugural_token), 
                                   groups = inaugural_token$President)


# Subset for just "freedom"

freq_freedom = subset(president_dfm, president_dfm$feature %in% "freedom") 

  
# Plot!

ggplot(freq_freedom, aes(x = group, y = frequency)) +
    geom_point() + 
    scale_y_continuous(limits = c(0, 14), breaks = c(seq(0, 14, 2))) +
    xlab(NULL) + 
    ylab("Frequency") +
    theme(axis.text.x = element_text(angle = 90, hjust = 1))+
    labs(x = "President", y = "Frequency", title = "Frequency for use of 'freedom' by President")
```

![](quanteda-Live-Tutorial_files/figure-html/unnamed-chunk-3-1.png)<!-- -->






