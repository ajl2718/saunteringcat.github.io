---
layout: post
title:  "A simple geocoder using Postgres and Python"
date:   2021-02-09
categories: SQL Python Geocoding
---

For a work project last year we needed to create at short notice a geocoder for Victorian addresses. While there are a number of open source options such as [libpostal](https://github.com/openvenues/libpostal), we decided to take a different approach, following the ideas in the paper [Exploiting Redundancy, Recurrence and Parallelism: How to Link Millions of Addresses with Ten Lines of Code in Ten Minutes](https://arxiv.org/abs/1708.01402). This allowed us to create a proof-of-concept within a few days and was relatively simple to code from scratch, using Python and Postgres.

<h1>An observation about street addresses</h1>
The paper is partly based on some observations about the statistics of street addresses which imply significant amount of redundancy in the way they are described. For example:
> Based on the [January 2021 release of the GNAF](https://data.gov.au/dataset/ds-dga-19432f89-dc3a-4ef3-b943-5326ef1dbecc/details) there are 3,839,496 addresses in Victoria. 516,926 of which (i.e., over 13%) are uniquely defined by three numbers: flat number, street number and postcode. 

As a result, **these addresses can be identified irrespective of typographical errors in the street name, suburb or other text**. Furthermore, even where the text is necessary to identify an address, often only a subset of the text is needed, and this is generally the parts of the text that occur relatively infrequently.

<h1>The algorithm: general idea</h1>
The authors of the paper use this idea as the starting point for a geocoding algorithm (actually an address linking algorithm in their paper) that has some nice properties:
- It can be coded up in SQL in a few queries;
- There is no need to *standardize* the addresses, i.e., to identify each of the address components and then re-write in a way that can be linked to the GNAF;
- It can be scaled up to allow for fast linking of large datasets (as the title suggests).

Despite its simplicity it works surprisingly well and at least for me was a good project for getting more hands-on experience using Postgres. 

Based on these ideas, the authors developed a simple geocoding algorithm that represents each address in terms of *rare phrases*, i.e., pairs of consecutive tokens that occur below a certain frequency, along with other infrequently occuring elements. 

<h1>The algorithm: details and implementation</h1>

Here are the basic steps of the algorithm for geocoding street addresses:

1. Create a database to store the GNAF data;
2. For both the input addresses and GNAF create corresponding **phrase tables** which generate the phrases for each address, i.e., the pairs of consecutive tokens;
3. An **inverted index** is generated on the phrases which maps each phrase to a set of addresses that contain these phrases;
4. The inverted index tables for the input addresses and GNAF addresses are then linked, only considering those that appear below a certain frequency. This has the advantage of only requiring searches over a small subset of addresses. The result is a set of **candidate matches** for each of the input addresses;
5. For each input address a **similarity score is calculated** with its set of candidate addresses. We used the *similarity* function from *pg_tgrm* module;
6. As a final step, an additional constraint on the numeric tokens is imposed: **the set of numeric tokens in the input address has to be a subset of those in the matched address**. This ensures, for example, that the unit numbers are correct.

The paper has SQL code for most of these steps, with the exception of the GNAF setup and the numerical constraint.

I have written Python module for geocoding that implements this algorithm in Postgres thanks to the nice [results library](https://pypi.org/project/results/). It is available in the repo [PhraseGeocoder](https://github.com/alex2718/PhraseGeocoder).

<h1>Summary and extensions to the algorithm</h1>
I also found this an instructive project for learning more about Postgres, in particular things like Common Table Expressions, string similarity and some operations that I wasn't at all familiar with.

There are a few obvious ways that this algorithm can be extended:
- Intead of using pairs of *consecutive* tokens as phrases, use pairs of *next-to-nearest neighbour* phrases.
- To better handle certain spelling errors, particularly those that occur in the text that is needed for the matching, construct phrases based on trigrams.
- Chain together a sequence of geocoding algorithms, so that with higher data quality addresses can be geocoded by the faster phrase-based geocoder and the lower quality ones then get filtered out and geocoded by the slower but more accurate trigram based geocoder (or another variant).

Some of these variations I have coded up and will upload them at a later date.

<h1>References</h1>
- [Exploiting Redundancy, Recurrence and Parallelism: How to Link Millions of Addresses with Ten Lines of Code in Ten Minutes](https://arxiv.org/abs/1708.01402)
- [Github repo for PhraseGeocoder](https://github.com/alex2718/PhraseGeocoder)