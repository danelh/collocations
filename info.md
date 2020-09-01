bad lemmas: "at" / "t" etc.

persus. not like the suggestions. 

not large enough of corpus.

## Lemmatization
The lemmatization process is done by [CLTK lemmatization backoff method](https://docs.cltk.org/en/latest/latin.html#lemmatization-backoff-method)(More info on that should be found there). As a lemmatization algorithm, it clearly has it's restrictaion and issue. There are 3 main issues: 
1.here are several odd lemmas like one-letter or two-letter lemmas (or even with more letters) such as _t_, _m_. Their source require futher invistigation in order to suggest apporatiate management and fix of the issue. (It seems many of those might be explained and proper names initials). 
2. The second and more irritating problem is simply mis-lemmatization. Such is the distacation between _os_ (mouth) and _os_(bone) seems to be virtually non-existeed and mixed in the lemma _os_ (there is also lemma _os1_ but not sure what it signifies), hence we have both _frango_ (that suggests the meaning of bone) and _vultus_ (that suggests the meaning of mouth) both quite high in the table. another exmple is _uoluo_ that many times is the results of the lemmatization process instead of _uolo_ to judge from the collaction table (see scio)
3. Sometimes what seems to be one lemma is splited into 2 diffent lemams. example to such is _gero_ and _gero1_. They have both very similar collocation tables: this suggests that the split into 2 different lemmas is either unjustified or simply not working well-enough to the time being.

### i-j, u-v, capital letters
In order to avoid duplicate lemams, before the texts were sent to lemmatization, a replace of every "j" with "i" and every "v" with "u" took place as instructed; but also every capital letter were replace to it's lower case. As a results, **we have no j or v in the lemmas**, so the user may encounter the less-familiar _uita_ instead of _vita_. in the Lemma selection textbox however, the user might insert "j" and "v" as they will be autumatically replaced.

## Corpus
The corpus that was used to collect the collocations is "latin_text_perseus" that was access by the dedicated [corpus reader](https://docs.cltk.org/en/latest/latin.html#corpus-readers) tool of CLTK. In this corpus there are 293 works available from Perseus ([full list](https://raw.githubusercontent.com/danelh/collocations/master/docs.txt)).

The user should keep in his mind that **there are indications that this corpus is not large enough, and it's extention will benifit this Tool**. The developer of this Tool hopes, someday in the future, to extend the corpus, and take a deeper look in the [lemmatization process](#lemmatization), however those pursuits are not likely to happen in viable future.

## why I can't find my lemma?
Usually you would expect to find every word that comes to mind, yet rarly you will stumble upon a lemma you expect to find, but the Tool can't locate. There are basically three possible reasons why you can see a lemma you were searching:
1. **Spelling variant.** This tools can handle spelling variation, and suggest to the user, another the lemma. But it can do that long as there is one diffenrt between your word and the lemma. so it can sugget _ap**p**ropinquo_ for input of _a**d**porpinquo_. If, however, the user enters _cl**y**peu**m**_ this Tool can't find _cl**i**peu**s**_ since it is removed by 2 letters. 
2. **Lemmatization**. It seems sometimes the lemmetizer has some mechanism that yields lemmas which are less trivial, or rather when in doubt makes the same selection that brings the other options to very low frequency (see point 3). Example to such is _spons_. It indeed lemmatizes to _spons_, however when in the ablative form of _sponte_, it lemmatizes to _sponte_ (probably the adverb); and since most of the instances of _spons_ are indeed _sponte_, it simply renderes _spons_ to be at very low frequency. Unfortunatelly, there is no easy way I know of to acpture those. In such cases the uses should consult the [full list of lemmas](https://raw.githubusercontent.com/danelh/collocations/master/all_lemmas.txt) and to search it there using possible variants.
3. **Low frequency**. Even if the lemma is absultely valid, it might be off this Tool if it's frequency is extremely low (usually below 10 occourences). For example the word _langa_ which is a kind of lizard is not here since it is too rare.

## algorithms
Thare are two types of algorithms that were used to collect the collocations: "Word Distance"(w) and "Random Slice"(r). 

### Word Distance (w) Algoritm
Let's consider this sentance:

> rabidum in horto canem viderat puer, atque sine mora inde fugit.

After lemmatation, we thus have:

> rabidus in hortus canis video puer, atque sine mora inde fugio.

The W algorithm fixes on a lemma, and counts all the lemmas within a distance of a given number of words. For the lemma _canis_ W**1** algorithms will count *hortus* and *video*. i.e take 1 word from each side; for W**3** it will count 3 words from each side: _rabidus_, _in_, _horto_, _video_, _puer_, _atque_. As we can undestand, since Latin allows great amount of flexlibility to word order. The meanignful pair (_rabidus, _canis_), which we would like to be counter, would not be in W1 or W2 but only in W3 and above. For this reason, we have many W algorithms: W1, W2, W3, W4, W6, W8.

Importantely, this algorithm does **not** award a word more compared to other, even if it is closer than the other: All words within the given distance count the same. Thus, for example, in W2 algorithm and the lemma _mora_ the lemma _sine_ and the lemma _fugio_ would be credited the same, althoug _sine_ is closer to _mora_ than _fugio_ is.  

### Random Slice (r) Algorithm

Unlike W, R algorithms does not fixes on a lemma, rather, as his name suggets, takes a slice of a given length and counts the pairs there. Let's consider yet again our lemmatized sentance:

> rabidus in hortus canis video puer, atque sine mora inde fugio.

If we use R3, that means we take slices of length of 3, which are: (_rabidus_, _in_, _hortus_), (_in_, _hortus_, _canis_), (_hortus_, _canis_, _video_), etc... So for the lemma _hortus_ we can see it counts _in_, _canis_ twice (since they co-occur in 2 diffrent slices), and _rabidus_, _video_ only once. In that way it awards closer words and counts them more. in R3 for example the pair (_sine_ , _mora_) is counted twice while (_sine_, _inde_) only once. This mechanism creates amolification effect for closer words, and hence might results in very high t-values for higher R's.

There are yet 2 more aspect in which R algorithm differs from W algorithm. First, in R algorithms we don't count pairs of the same word. Example:

> ad vim atque ad arma confugere

Here W3 will count the pair (_ad_,_ad_), but R4 in the slice (_ad_, _vis_, _atque_, _ad_) the pair (_ad_, _ad_) is not counter.  

Originally the idea behind R algotihm was to convey the "semantic environment" of a word, for this reason we have very high R's like R12 and R16. (though, in a sense, W8 also implies span of 16 words). For this reason we have anoter diffence between W and R, which R omits high-frequency words. (usually words that occours in more than 1% of the slices) thus the lemma _sum_ for example is not be found in R's. On a second thought however, not sure this was very good idea, and this might change in the future.

### t-value calclation

After counting the pairs accross all our not-large-enough-corpus, a simple t-value calculation (in a manner as decribed [here](https://en.wikipedia.org/wiki/Collocation#Statistically_significant_collocation)) - is peformed in order to revel how signignifcant a given pair is: in other words: base of the frequency of each lemma how large is the gap between the number of co-occurence we would expect to see by pure statistics, and the number of co-occures that there are in actually. the larger this gap is, the larger is the t-vale -- meaning that the pair is more occour together more significatelly.

for W algorithms, this Tool shows the lemmas whose t-values are bigger than 1.8 (to the max of 200 lemmas). For R, howver, since it ususally have larger t-value (due to the amplication mechamism described above), the t-value treshhold was set to 2.0. 

several unncessary techinicalities were omitted from the above W and R algorithms desception. For furthor info, one should console the [source code](https://github.com/danelh/latin_collocation/blob/master/main.py) 

## asymmetry in t-value
Intuitivly, We would rather expect that the strengh of connection between a pair (their t-value) to be indeffient to the order of the pair. Indeed, in works like that in most of the pairs, and even if there is diffece, it is ususally very small. However, sometimes the different might seem quite significat: for example, in W4 for (_sum_, _necesse_), we have t-value of 32.56, but if we take (_necesse_, _sum_), we will have t-value of 27.21. The reason for this observation is this: lets assume we have the lemmatized sentance: 

> sum necesse vir sum magnus amnis

in W2 algorithm, the pair (_sum_, _necesse_) will be counted twice in this sentence! once when the fix on the first _sum_, and the second time on the second _sum_. The pair (_necesse_, _sum_), however, will be counter only once. because every pair can be counted only once per fixed word. (this was done on purpuse, maybe wrongly, under the assumption that this will results in more meaningful results). This explains why this assymetry should be quite rare, and mainly for very high frequency words.

## suggestions
The last column of the collation table is the suggestion column. Each cell consist of links to ["The Packard Humanities Institute" concordence](https://latin.packhum.org/concordance) in attempt to find the co-occurence of the pair. They are to help the user to find those collocations in real texts - but **they are nothing more than suggestions** - as such, they may fail to achive their mission. 

Each lemma has predifined strings starts(max 3) that it is likely to be found using them. for example _cado_ has: "cad", "cecidi", "cecider"; while _sapio_ has: "sapien", "sap" thus for this pair ([W2 t-value of 3.47](https://danelh.github.io/collocations/?algo=w2&lemma=cado)) we have 6 combinations to send to the concordance search. In this case, for example, we were lucky to successfully locate the collocation "cadere in sapientem" from the first combination ["cad" + "sapien"](https://latin.packhum.org/concordance?q=%23cad+%7E+%23sapien%22). The `#cad ~ #sapien` string given to the concardance as parameter for search, means to search sentences where we have words that start with "cad" and "sapien" and are within the distance of maximum 100 chars. The user might use their search tools more efficiently. To undertand futher how their concordance search works see [here](https://latin.packhum.org/help/search)

**The user should keep in mind that the above concordence copus does not match the perseus from which the collocations were collocted.**  There are text that appear here and don't apear there, and vice verse. so theotically we might sometimes not find even one instace of a given pair in the concordance!
