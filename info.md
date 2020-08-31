bad lemmas: "at" / "t" etc.

## why I can't find my lemma?
Usually you would expect to find every word that comes to mind, yet rarly you will stumble upon a lemma you expect to find, but the Tool can't locate. There are basically three possible reasons why you can see a lemma you were searching:
1. **Spelling variant.** This tools can handle spelling variation, and suggest to the user, another the lemma. But it can do that long as there is one diffenrt between your word and the lemma. so it can sugget _ap**p**ropinquo_ for input of _a**d**porpinquo_. If, however, the user enters _cl**y**peu**m**_ this Tool can't find _cl**i**peu**s**_ since it is removed by 2 letters. 
2. **Lemmatization**. It seems sometimes the lemmetizer has some mechanism that yields lemmas which are less trivial. Example to such is _spons_. it lemmatizes to _sponte_. Unfortunatelly, there is no easy way I know of to acpture those. in such cases the uses should consult the full list [ TODO: add link] and to search it there using possible variants.   
3. **Low frequency**. Even if the lemma is absultely valid, it might be off this Tool if it's frequency is extremely low (usually below 10 occourences). For example the word _langa_ which is kind of lizard is not here since it is too rare.
