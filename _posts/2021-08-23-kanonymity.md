---
title: A first look at k-anonymity.
author:
  name: Rogério Pontes
date: 2021-08-23 10:00:00 0000
categories: [k-anonymity]
tags: [Privacy]
img_path: assets/img/k-anonymity/
---

## Introduction 


![Hidden in the Noise (Photo by Chris Yang Unsplash)](intro.jpeg)
_Hidden in the Noise (Photo by Chris Yang Unsplash)_


We don't always think about the information we publicly disclose, but we are often too quick to provide private data that can uniquely identify us. Sometimes we do it as part of our civic duty such scheduling a COVID-19 Test or vaccine, but other times we do it just to subscribe to a new social media platform. In either case, the data that we provide is stored in a database table that can be shared and sold to third parties.

In the best of cases the entity collecting and sharing the data anonymizes the individual records by removing explicit identifiers such as names and addresses. However, not all the data can be hidden without losing some of its value. There is an intrinsic asymmetry between protecting the individual privacy and preserving the utility of the data. k-Anonymity provides a solution to this challenge and in this post, I will attempt synthesize its basic ideas and security guarantees. All the information in here is based on the original paper by Pierangela Samarati and Latanya Sweeney [^1].

## What does it mean to be anonymous?

To provide a tangible example on the importance of privacy in shared datasets, consider the classical study "Simple Demographics Often Identify People Uniquely" published by Latanya Sweeney in 2000. In this study, Latanya shows that there is a high likelihood that a subset of the US population can be uniquely identified from seeming anonymized data such as census, medical records, and voter lists. In fact, the study shows that 87% of the US population had personal attributes that made them unique based solely on their zip codes, gender, and date of birth. If even carefully sanitized data can be used to identify unique individuals, then in today's data hungry world it is clearly hard to remain anonymous.

k-Anonymity originated as a solution to a simplified version of the problem of sharing private data. Overall, the goal of k-anonymity is to create a public anonymized table from a private table with records that can identify individuals. In this problem, a table has multiple attributes that can be divided between explicit identifiers (e.g.: name, address) and quasi-identifiers, columns that store characteristics that when combined create a unique identity that can be used to de-identify individuals (e.g.: zip code, gender, and date of birth). Intuitively, the core idea is to transform a private table T in a public table T', such that for every combination of attributes in public table T' there are at least k records with the same data. Additionally, all the explicit identifiers in a table are either removed or encrypted. As an example, consider the table in Figure 1 as a sample of a governmental database that has four quasi-identifiers, the nationality, gender, zip code, and date of birth of several citizens.

![Figure 1: Example of an anonymized table](example-table.jpeg)
_Figure 1: Example of an anonymized table_

Depending on the country size and diversity, the data in table T can be used to de-identify an individual even though it does not contain any explicit identifiers. For example, in Portugal there are around 6k Bulgarian residents. If we assume an equal gender division, we still have around 3k female Bulgarians. But if we continuing to drill down on this data based on ZIP code and date of birth, it's possible to reduce the set of possibilities a small sample of people, maybe to just a single individual. However, the same process cannot be as easily applied to public table T'. The anonymization process has removed some information and ensures that for each combination of quasi-identifiers there are at least 2 records. From the public table we can learn at most that there are two females in the zip area of 895 but we know very little about their nationality and date of birth.

The frequency distribution of the population has a crucial role in k-anonymity. Let's take a moment to look at the classical security definition and briefly analyze it's relation to the frequency distribution of a population.

> Let *T(A1,…An)* be a table and *Q* be its quasi-identifiers. *T* is said to satisfy k-anonymity iff for each quasi-identifier *q* that belongs to *Q*, each sequence of values in *T[Q]* appears at least with *k* occurrences in *T[Q]*.

This definition is deceptively simple and not too different from the intuitive description. The definition does not attempt to define what are anonymized records or how to anonymize it. Additionally, it does not define what should be considered quasi-identifiers, it actually leaves it open to the the data owner of the private table. Instead, the only requirement it has is that every sequence of quasi-identifiers has at least k occurrences. What this requirement effectively does is force some precision of the data to be lost by reducing the number of values in the the frequency distribution of the table. As the data collected in a table is a sample from the real population, the loss of accuracy ensures that at least k elements in the population are mapped to the the values in the public table. As such, any member of the population that could be identified by a combination of attributes remains "hidden by the crowd".

![Photo by Hugh Han on Unsplash](hidden.jpeg)
_Photo by Hugh Han on Unsplash_

## Moving up!

Now that we have a good understanding of the security definition let's quickly go over one of the fundamental approaches to create a k-anonymized table, the generalization hierarchy. This concept builds on the idea that every value of a database table belongs to a domain, and that domains can have an hierarchy, where each additional top level has less accuracy. For example, a zip code 8805 belongs to the ground domain that contains all zip codes with 5 digits. But the ground domain has a mapping to a more general domain that always has the last digit of the domain equal to 0, for example 8805 is mapped to 8800. This hierarchy continues until there is a top level domain with a single value.

![Figure 2.: Example domain and value hierarchy for zip codes.](example-lattice.png)
_Figure 2.: Example domain and value hierarchy for zip codes._

The generalization hierarchy can be used on any attribute in a database table. It can also be used on the more general case where combinations of multiple attributes need to be anonymized. In this case a lattice of possible generalization options can be created. For example, for two attributes Z and E, where E has a hierarchy of height 2 as depicted in Figure 2 and E has a hierarchy of height 1, then it's possible to create a lattice with five different combinations of attributes that may satisfy k-anonymity.

![Figure 3.: Example of a lattice of two attributes E and Z.](final-lattice.png)
_Figure 3.: Example of a lattice of two attributes E and Z._

## Final Remarks

I won't go into any more details on k-anonymity for now, but I hope this introduction left you interested in the topic. For more information I would suggest you have a look at the original paper which explains all these ideas in greater detail. I would also like to leave you with an additional post published by [Troy Hunt](www.troyhunt.com) which presents a practical use case for k-anonymity. While it tackles a slightly different problem, it's a really interesting application of a public service to identify compromised login credentials without disclosing compromising it's users privacy.


## References

[^1]: [Protecting privacy when disclosing information: k-anonymity and its enforcement through generalization and suppression](https://dataprivacylab.org/dataprivacy/projects/kanonymity/paper3.pdf)