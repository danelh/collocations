---
layout: default
title: info
---
# Latin Collocations (Part of [Res Latinae](https://danelh.github.io/))

## Table of contents
- [Intro](#intro)
- [Lemmatization](#lemmatization)
  * [i-j, u-v, capital letters](#i-j-u-v-capital-letters)
- [Corpus](#corpus)
- [algorithms](#algorithms)
  * [Word Distance (w) Algorithm](#word-distance-w-algorithm)
  * [Random Slice (r) Algorithm](#random-slice-r-algorithm)
  * [t-value calculation](#t-value-calculation)
- [suggestions](#suggestions)
- [why I can't find my lemma?](#why-i-cant-find-my-lemma)
- [asymmetry in t-value](#asymmetry-in-t-value)
- [Contact information](#contact-information)

## Intro
this Tool was created in order to locate the Latin-language [collocations](https://en.wikipedia.org/wiki/Collocation). The approach taken here is purely automatic, and this Tool is bound to find collocations only in the very strict-technical sense (this document explains in more details how this process takes place). Since Latin is a highly inflected language, the unit of consideration is not _word_, but rather a _lemma_. a _lemma_ is basically, generally speaking, the base, uninflected form a word; thus _navem_, _navibus_, _navium_ are all belong to the lemma _navis_ :usually nominative singular. The lemma of verbs is usually present first-person singular. In the search box, the user is expected to provide a _lemma_, and should expect to see  lemmas in the result-table.

## Lemmatization
The lemmatization process is done by [CLTK lemmatization backoff method](https://docs.cltk.org/en/latest/latin.html#lemmatization-backoff-method)(More info on that should be found there). As a lemmatization algorithm, it clearly has it's restrictions and issues. There are 3 main issues: 
1. There are several odd lemmas like one-letter or two-letter lemmas (or even with more letters) such as _t_, _m_. Their source require further investigation in order to suggest appropiate management and fix of the issue. (It seems many of those might be explained and proper names initials). 
2. The second and more irritating problem is simply mis-lemmatization. Such is the distinction between _os_ (mouth) and _os_(bone) seems to be virtually non-existing and mixed in the lemma _os_ (there is also lemma _os1_ but not sure what it signifies), hence we have both _frango_ (that suggests the meaning of bone) and _vultus_ (that suggests the meaning of mouth) quite high in the collocations-table. another example is _uoluo_ that many times is the results of the lemmatization process instead of _uolo_ to judge from the collocations-table (see _scio_)
3. Sometimes what seems to be one lemma is split into 2 different lemmas. Example to such is _gero_ and _gero1_. They have both very similar collocation tables: this suggests that the split into 2 different lemmas is either unjustified or simply not working well-enough to the time being.

### i-j, u-v, capital letters
In order to avoid duplicate lemmas, before the texts were sent to lemmatization, a replace of every "j" with "i" and every "v" with "u" took place as instructed; but also every capital letter was replaced to it's lower case. As a results, **we have no j or v in the lemmas**, so the user may encounter the less-familiar _uita_ instead of _vita_. in the Lemma selection textbox however, the user might insert "j" and "v" as they will be automatically replaced.

## Corpus
The corpus that was used to collect the collocations is "latin_text_perseus" that was access by the dedicated [corpus reader](https://docs.cltk.org/en/latest/latin.html#corpus-readers) tool of CLTK. In this corpus there are 293 works available from Perseus ([full list](https://raw.githubusercontent.com/danelh/collocations/master/docs.txt)).

The user should keep in his mind that **there are indications that this corpus is not large enough, and it's extension will benefit this Tool**. The developer of this Tool hopes, someday in the future, to extend the corpus, and take a deeper look in the [lemmatization process](#lemmatization).

## algorithms
There are two types of algorithms that were used to collect the collocations: "Word Distance"(w) and "Random Slice"(r). 

### Word Distance (w) Algorithm
Let's consider this sentence:

> rabidum in horto canem viderat puer, atque sine mora inde fugit.

After lemmatization, we thus have:

> rabidus in hortus canis video puer, atque sine mora inde fugio.

The W algorithm fixes on a lemma, and counts all the lemmas within a distance of a given number of words. For the lemma _canis_ W**1** algorithms will count *hortus* and *video*. i.e take 1 word from each side; for W**3** it will count 3 words from each side: _rabidus_, _in_, _horto_, _video_, _puer_, _atque_. As we can understand, since Latin allows great amount of flexibility to word order. The meaningful pair (_rabidus, _canis_), which we would like to be counted, would not be in W1 or W2 but only in W3 and above. For this reason, we have many W algorithms: W1, W2, W3, W4, W6, W8.

Importantly, this algorithm does **not** award a word more compared to other, even if it is closer than the other: All words within the given distance count the same. Thus, for example, in W2 algorithm and the lemma _mora_: the lemma _sine_ and the lemma _fugio_ would be credited the same, although _sine_ is closer to _mora_ than _fugio_ is.  

### Random Slice (r) Algorithm

Unlike W, R algorithms does not fixes on a lemma, rather, as his name suggest, takes a slice of a given length and counts the pairs there. Let's consider yet again our lemmatized sentence:

> rabidus in hortus canis video puer, atque sine mora inde fugio.

If we use R3, that means we take slices of length of 3, which are: (_rabidus_, _in_, _hortus_), (_in_, _hortus_, _canis_), (_hortus_, _canis_, _video_), etc... So for the lemma _hortus_ we can see it counts _in_, _canis_ twice (since they co-occur in 2 different slices), and _rabidus_, _video_ only once. In that way it awards closer words and counts them more. In R3 for example the pair (_sine_ , _mora_) is counted twice while (_sine_, _inde_) only once. This mechanism creates amplification effect for closer words, and hence might results in very high t-values for higher R's.

There are yet 2 more aspect in which R algorithm differs from W algorithm. First, in R algorithms we don't count pairs of the same word. Example:

> ad vim atque ad arma confugere

Here W3 will count the pair (_ad_,_ad_), but R4 in the slice (_ad_, _vis_, _atque_, _ad_) the pair (_ad_, _ad_) is not counted.  

Originally the idea behind R algorithm was to convey the "semantic environment" of a word, for this reason we have very high R's like R12 and R16. For this reason we have another difference between W and R, which R omits high-frequency words. (usually words that occurs in more than 1% of the slices) thus the lemma _sum_, for example, is not be found in R's. On a second thought however, not sure this was very good idea, and it might change in the future.

### t-value calculation

After counting the pairs across all our not-large-enough-corpus, a simple t-value calculation (in a manner as described [here](https://en.wikipedia.org/wiki/Collocation#Statistically_significant_collocation)) - is performed in order to reveal how significant a given pair is; in other words: base of the frequency of each lemma, how large is the gap between the number of co-occurrences we would expect to have by pure statistics, and the number of co-occurrences that in actuality. The larger this gap is, the larger is the t-vale -- meaning that the pair does occur together more significantly.

for W algorithms, this Tool displays the lemmas whose t-values are bigger than 1.8 (to the max of 200 lemmas). For R, however, since it usually has larger t-value (due to the amplification effect described above), the t-value threshold was set to 2.0. 

several unnecessary technicalities were omitted from the above W and R algorithms descriptions. For further info, one should consult the [source code](https://github.com/danelh/latin_collocation/blob/master/main.py).

## suggestions
The last column of the collation table is the suggestion column. Each cell consist of links to ["The Packard Humanities Institute" concordance](https://latin.packhum.org/concordance) in attempt to find the co-occurrences of the pair. They are to help the user to find those collocations in real texts - but **they are nothing more than suggestions** - as such, they may fail to achieve their mission. 

Each lemma has predefined strings starts(max 3) that it is likely to be found using them. for example _cado_ has: "cad", "cecidi", "cecider"; while _sapio_ has: "sapien", "sap" thus for this pair ([W2 t-value of 3.47](https://danelh.github.io/collocations/?algo=w2&lemma=cado)) we have 6 combinations to send to the concordance search. In this case, for example, we were lucky to successfully locate the collocation "cadere in sapientem" from the first combination ["cad" + "sapien"](https://latin.packhum.org/concordance?q=%23cad+%7E+%23sapien%22). The `#cad ~ #sapien` string given to the concordance as parameter for search, means to search sentences where we have words that start with "cad" and "sapien" and are within the distance of maximum 100 chars. The user might use their search tools more efficiently. To understand further how their concordance search works see [here](https://latin.packhum.org/help/search)

**The user should keep in mind that the above concordance corpus does not match the Perseus from which the collocations were collected.**  There are text that appear here and don't appear there, and vice verse. so theoretically we might sometimes fail to find even one instance of a given pair in the concordance.

## why I can't find my lemma?
Usually you would expect to find every word that comes to mind, yet rarely you will stumble upon a lemma you expect to find, but the Tool can't locate. There are basically three possible reasons why you can't see a lemma you were searching:
1. **Spelling variant.** This tools can handle spelling variation, and suggest to the user the other-spelling. But it can do that long as there is one difference between the entered-word and the lemma. so it can suggest _ap**p**ropinquo_ for input of _a**d**porpinquo_. If, however, the user enters _cl**y**peu**m**_, this Tool can't find _cl**i**peu**s**_ since it is removed by 2 letters. 
2. **Lemmatization**. It seems sometimes the lemmatizer has some mechanism that yields lemmas which are less trivial, or rather when in doubt makes the same selection that brings the other options to very low frequency (see point 3). Example to such is _spons_. It indeed lemmatizes to _spons_, however when in the ablative form of _sponte_, it lemmatizes to _sponte_ (probably the adverb); and since most of the instances of _spons_ are indeed _sponte_, it simply renders _spons_ to be at very low frequency. Unfortunately, there is no easy way I know of to capture those. In such cases, the user should consult the [full list of lemmas](https://raw.githubusercontent.com/danelh/collocations/master/all_lemmas.txt) and to search it there using possible variants.
3. **Low frequency**. Even if the lemma is absolutely valid, it might be off this Tool if it's frequency is extremely low (usually below 10 occurrences). For example the word _langa_ which is a kind of lizard is not here since it is too rare.

## asymmetry in t-value
Intuitively, We would rather expect that the strength of connection between a pair (their t-value) to be indifferent to the order of the pair. Indeed, in most of the pair this is the behaviour, and even if there is a difference, it is usually very small. However, sometimes the different might seem quite significat: for example, in W4 for (_sum_, _necesse_), we have t-value of 32.56, but if we take (_necesse_, _sum_), we will have t-value of 27.21. The reason for this observation is this: lets assume we have this lemmatized sentence: 

> sum necesse vir sum magnus amnis

in W2 algorithm, the pair (_sum_, _necesse_) will be counted twice in this sentence! once when the fix on the first _sum_, and the second time when the fix is on the second _sum_. The pair (_necesse_, _sum_), however, will be counter only once. because every pair can be counted only once per fixed word. (this was done on purpose, maybe wrongly, under the assumption that this will results in more meaningful results). This explains why this asymmetry should be quite rare, and mainly for very high-frequency words.


## Contact information 
[See here](https://danelh.github.io/about)

