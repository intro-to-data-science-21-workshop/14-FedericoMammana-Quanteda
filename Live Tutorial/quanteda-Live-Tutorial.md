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



## Preprocessing

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
The lone and level sands stretch far away.”

2. Make the tokenized text lowercase.

3. Remove (english) stopwords from the tokenized text.

