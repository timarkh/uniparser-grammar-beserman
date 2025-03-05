# Beserman morphological analyzer
This is a rule-based morphological analyzer for Beserman (formerly a dialect of Udmurt ``udm``; Uralic > Permic). It contains a formalized description of Beserman morphology as established by the Beserman documentation project, based mostly on the spoken data from the village of Shamardan (Yukamenskoye district, Udmurtia). It uses [uniparser-morph](https://github.com/timarkh/uniparser-morph) for parsing. It performs full morphological analysis of Beserman words (lemmatization, POS tagging, grammatical tagging, glossing).

This package uses a project-internal Latin-based spelling system. Cyrillic and UPA analyzers will hopefully follow later. Right now, see [translit-udmurt](https://github.com/timarkh/translit-udmurt) for transliteration options.

**Warning**: This is a project-internal tool. If you think you might need it, you are probably wrong. If what you need is standard Udmurt analyzer, you can find one [here](https://github.com/timarkh/uniparser-grammar-udm). If you are not sure, feel free to send an email to the developer (Timofey Arkhangelskiy, timarkh@gmail.com).

## How to use
### Python package
The analyzer is available as a Python package. If you want to analyze Beserman texts in Python, install the module:

```
pip3 install uniparser-beserman-lat
```

Import the module and create an instance of ``BesermanLatAnalyzer`` class. After that, you can either parse tokens or lists of tokens with ``analyze_words()``, or parse a frequency list with ``analyze_wordlist()``. Here is a simple example:

```python
from uniparser_udmurt import BesermanLatAnalyzer
a = BesermanLatAnalyzer()

analyses = a.analyze_words('Gožtemjosəz')
# The parser is initialized before first use, so expect
# some delay here (usually several seconds)

# You will get a list of Wordform objects
# The analysis attributes are stored in its properties
# as string values, e.g.:
for ana in analyses:
        print(ana.wf, ana.lemma, ana.gramm, ana.gloss)

# You can also pass lists (even nested lists) and specify
# output format ('xml' or 'json')
# If you pass a list, you will get a list of analyses
# with the same structure
analyses = a.analyze_words([['A'], ['Mon', 'tone', 'jaratišʼko', '.']],
	                       format='xml')
analyses = a.analyze_words(['Gožtemjosəz', [['A'], ['Mon', 'tone', 'jaratišʼko', '.']]],
	                       format='json')
```

Refer to the [uniparser-morph documentation](https://uniparser-morph.readthedocs.io/en/latest/) for the full list of options.

### Disambiguation
Apart from the analyzer, this repository contains a set of [Constraint Grammar](https://visl.sdu.dk/constraint_grammar.html) rules that can be used for partial disambiguation of analyzed Beserman texts. They reduce the average number of different analyses per analyzed token from about 1.7 to about 1.4. If you want to use them, set ``disambiguation=True`` when calling ``analyze_words``:

```python
analyses = a.analyze_words(['Mon', 'tone', 'jaratišʼko'], disambiguate=True)
```

In order for this to work, you have to install the ``cg3`` executable separately. On Ubuntu/Debian, you can use ``apt-get``:

```
sudo apt-get install cg3
```

On Windows, download the binary and add the path to the ``PATH`` environment variable. See [the documentation](https://visl.sdu.dk/cg3/single/#installation) for other options.

Note that each time you call ``analyze_words()`` with ``disambiguate=True``, the CG grammar is loaded and compiled from scratch, which makes the analysis even slower. If you are analyzing a large text, it would make sense to pass the entire text contents in a single function call rather than do it sentence-by-sentence, for optimal performance.

### Word lists
Alternatively, you can use a preprocessed word list. The ``wordlists`` directory contains a list of words from a 250-thousand-word [Beserman multimedia corpus](http://multimedia-corpus.beserman.ru/search) (``wordlist.csv``), list of analyzed tokens (``wordlist_analyzed.txt``; each line contains all possible analyses for one word in an XML format), and list of tokens the parser could not analyze (``wordlist_unanalyzed.txt``). The recall of the analyzer on the corpus texts is about 98%.

## Description format
The description is carried out in the ``uniparser-morph`` format and involves a description of the inflection (paradigms.txt), a grammatical dictionary (lexemes.txt), and a short list of analyses that should be avoided (bad_analyses.txt). The dictionary contains descriptions of individual lexemes, each of which is accompanied by information about its stem, its part-of-speech tag and some other grammatical/borrowing information, its inflectional type (paradigm), and Russian translation. See more about the format [in the uniparser-morph documentation](https://uniparser-morph.readthedocs.io/en/latest/format.html).
