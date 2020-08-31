bad lemmas: "at" / "t" etc.

persus. not like the suggestions. 

not large enough of corpus.

## Lemmatization
os - might be both bone and mounth 
gero1 and gero - not sure why this split

## why I can't find my lemma?
Usually you would expect to find every word that comes to mind, yet rarly you will stumble upon a lemma you expect to find, but the Tool can't locate. There are basically three possible reasons why you can see a lemma you were searching:
1. **Spelling variant.** This tools can handle spelling variation, and suggest to the user, another the lemma. But it can do that long as there is one diffenrt between your word and the lemma. so it can sugget _ap**p**ropinquo_ for input of _a**d**porpinquo_. If, however, the user enters _cl**y**peu**m**_ this Tool can't find _cl**i**peu**s**_ since it is removed by 2 letters. 
2. **Lemmatization**. It seems sometimes the lemmetizer has some mechanism that yields lemmas which are less trivial. Example to such is _spons_. it lemmatizes to _sponte_. Unfortunatelly, there is no easy way I know of to acpture those. in such cases the uses should consult the full list [ TODO: add link] and to search it there using possible variants. [to inquire whetehr spons lemetizes to sponte or not]  
3. **Low frequency**. Even if the lemma is absultely valid, it might be off this Tool if it's frequency is extremely low (usually below 10 occourences). For example the word _langa_ which is kind of lizard is not here since it is too rare.

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

If we use R3, that means we take slices of length of 3, which are: (_rabidus, _in_, _hortus_), (_in_, _hortus_, _canis_), (_hortus_, _canis_, _video_), etc... So for the lemma _hortus_ we can see it counts _in_, _canis_ twice (since they co-occur in 2 diffrent slices), and _rabidus_, _video_ only once. In that way it awards closer words and counts them more. in R3 for example the pair (_sine_ , _mora_) is counted twice while (_sine_, _inde_) only once. This mechanism creates amolification effect for closer words, and hence might results in very high t-values for higher R's.

There are yet 2 more aspect in which R algorithm differs from W algorithm. First, in R algorithms we don't count pairs of the same word. Example:

> ad vim atque ad arma confugere

Here W3 will count the pair (_ad,_ad_), but R4 in the slice (_ad_, _vis_, _atque_, _ad_) the pair (_ad_, _ad_) is not counter.  

Originally the idea behind R algotihm was to convey the "sematic field" of a word, for this reason we have very high R's like R12 and R16. (though, in a sense, W8 also implies span of 16 words). For this reason we have anoter diffence between W and R, which R omits high-frequency words. (usually words that occours in more than 1% of the slices) thus the lemma _sum_ for example is not be found in R's. On a second thought however, not sure this was very good idea, and this might change in the future.

### t-value calclation

After counting the pairs accross all our not-large-enough-corpus, a simple t-value calculation (in a manner as decribed here) - is peformed in order to revel how signignifcant a given pair is: in other words: base of the frequency of each lemma how large is the gap between the number of co-occurence we would expect to see by pure statistics, and the number of co-occures that there are in actually. the larger this gap is, the larger is the t-vale -- meaning that the pair is more occour together more significatelly.

for W algorithms, this Tool shows the lemmas whose t-values are bigger than 1.8 (to the max of 200 lemmas). For R, howver, since it ususally have larger t-value (due to the amplication mechamism described above), the t-value treshhold was set to 2.0. 

several unncessary techinicalities were omitted from the above W and R algorithms desception. For furthor info, one should console the source code #TODO 

## asymmetry in t-value
Intuitivly, We would rather expect that the strengh of connection between a pair (their t-value) to be indeffient to the order of the pair. Indeed, in works like that in most of the pairs, and even if there is diffece, it is ususally very small. However, sometimes the different might seem quite significat: for example, in W4 for (_sum_, _necesse_), we have t-value of 32.56, but if we take (_necesse_, _sum_), we will have t-value of 27.21. The reason for this observation is this: lets assume we have the lemmatized sentance: 

> sum necesse vir sum magnus amnis

in W2 algorithm, the pair (_sum_, _necesse_) will be counted twice in this sentence! once when the fix on the first _sum_, and the second time on the second _sum_. The pair (_necesse_, _sum_), however, will be counter only once. because every pair can be counted only once per fixed word. (this was done on purpuse, maybe wrongly, under the assumption that this will results in more meaningful results). This explains why this assymetry should be quite rare, and mainly for very high frequency words.

## suggestions
The last column of the collation table is the suggestion column. Each cell consist of links to ["The Packard Humanities Institute" concordence](https://latin.packhum.org/concordance) in attempt to find the co-occurence of the pair. They are to help the user to find those collocations in real texts - but **they are nothing more than suggestions** - as such, they may fail to achive their mission. 

Each lemma has predifined strings starts(max 3) that it is likely to be found using them. for example _cado_ has: "cad", "cecidi", "cecider"; while _sapio_ has: "sapien", "sap" thus for this pair (W2 t-value of 3.47) we have 6 combinations to send to the concordance search. In this case, for example, we were lucky to successfully locate the collocation "cadere in sapientem" from the first combination ["cad" + "sapien"](https://latin.packhum.org/concordance?q=%23cad+%7E+%23sapien%22). The `#cad ~ #sapien` string given to the concardance as parameter for search, means to search sentences where we have words that start with "cad" and "sapien" and are within the distance of maximum 100 chars. The user might use their search tools more efficiently. To undertand futher how their concordance search works see [here](https://latin.packhum.org/help/search)

**The user should keep in mind that the above concordence copus does not match the perseus from which the collocations were collocted.**  There are text that appear here and don't apear there, and vice verse. so theotically we might sometimes not find even one instace of a given pair in the concordance!
