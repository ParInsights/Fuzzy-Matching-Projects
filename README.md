# Fuzzy-Matching-Projects
Just a bunch of projects and files related to my work in matching names. 



# Fuzzy Matching - Fuzzywuzzy Methods Review
## Overview

Review of the four methods that calculate the edit distance between some ordering of the tokens in both input strings. This is done using the difflib.ratio function which will:

      Return a measure of the sequences' similarity (float in [0,1]).

    Where T is the total number of elements in both sequences, and M is the number of matches,      this is 2.0*M / T. 
          
          Note that this is 1 if the sequences are identical, and 0 if they have nothing in common.


The four following fuzzywuzzy methods call difflib.ratio on different combinations of the input strings.

## fuzz.ratio

Simple. Just calls difflib.ratio on the two input strings (code).

    fuzz.ratio("NEW YORK METS", "NEW YORK MEATS")
     > 96


## fuzz.partial_ratio

Attempts to account for partial string matches better. Calls ratio using the shortest string (length n) against all n-length substrings of the larger string and returns the highest score (code).

Notice here that "YANKEES" is the shortest string (length 7), and we run the ratio with "YANKEES" against all substrings of length 7 of "NEW YORK YANKEES" (which would include checking against "YANKEES", a 100% match):

    fuzz.ratio("YANKEES", "NEW YORK YANKEES")
      > 60
    fuzz.partial_ratio("YANKEES", "NEW YORK YANKEES")
      > 100

## fuzz.token_sort_ratio

Attempts to account for similar strings out of order. Calls ratio on both strings after sorting the tokens in each string (code). Notice here fuzz.ratio and fuzz.partial_ratio both fail, but once you sort the tokens it's a 100% match:

    fuzz.ratio("New York Mets vs Atlanta Braves", "Atlanta Braves vs New York Mets")
      > 45
    fuzz.partial_ratio("New York Mets vs Atlanta Braves", "Atlanta Braves vs New York Mets")
      > 45
    fuzz.token_sort_ratio("New York Mets vs Atlanta Braves", "Atlanta Braves vs New York Mets")
      > 100

## fuzz.token_set_ratio

Attempts to rule out differences in the strings. Calls ratio on three particular substring sets and returns the max (code):

- intersection-only and the intersection with remainder of string one
- intersection-only and the intersection with remainder of string two
- intersection with remainder of one and intersection with remainder of two

Notice that by splitting up the intersection and remainders of the two strings, we're accounting for both how similar and different the two strings are:

    fuzz.ratio("mariners vs angels", "los angeles angels of anaheim at seattle mariners")
      > 36
    fuzz.partial_ratio("mariners vs angels", "los angeles angels of anaheim at seattle mariners")
      > 61
    fuzz.token_sort_ratio("mariners vs angels", "los angeles angels of anaheim at seattle mariners")
      > 51
    fuzz.token_set_ratio("mariners vs angels", "los angeles angels of anaheim at seattle mariners")
      > 91
