---
layout: post
title:  "MIR basics"
author: "Mikołaj Szawerda"
tags: MIR
---

Buzz words

-  MFCC
-  Self similiarity matrices

One of the attributes distinguishing music from random sound sources is the hierarchical structure in which music is organized.
At the lowest level, one has events
such as individual notes, which are characterized by the way they sound, their timbre, pitch, and duration.
Combining various sound events, one obtains larger structures such as motifs, phrases, and sections, and these structures again form larger constructs that determine the overall layout of the composition.


To study musical structures and their mutual relations, one general idea is
to convert the music signal into a suitable feature sequence and then to compare each
element of the feature sequence with all other elements of the sequence.


First, repetition-based methods are used to identify recurring patterns.
Second, novelty-based methods are employed to detect transitions between contrasting parts.
Third, homogeneity-based methods are used to determine passages
that are consistent with respect to some musical property.

## Music perception

Besides melody and harmony, the instrumentation and timbral characteristics are
of great importance for the human perception of music structure.

### MFCC

In the context of timbre-based structure analysis, one often uses
mel-frequency cepstral coefﬁcients (MFCCs), which were originally developed
for automated speech recognition. Parametrizing the rough shape of the spectral envelope, MFCC-based features capture timbral properties of the signal.

### Self similiarity metrics

As we will see, one crucial
property of self-similarity matrices is that repetitions typically yield path-like structures,
whereas homogeneous regions yield block-like structures.

Given a feature sequence X = (x1 , x2 , . . . , xN ), the idea is to compare all elements of the sequence with
each other. This results in an N-square self-similarity matrix S ∈ RN×N deﬁned by
S(n, m) := s(xn , xm ),

Therefore, the resulting SSM has a diagonal with large
values. More generally, recurring patterns of the given feature sequence become visible in the SSM in the form of structures with large similarity values.

The two most prominent structures induced by such patterns are often referred to as blocks and
paths (see Figure 4.7a for an illustration). First, if the feature sequence captures musical properties that stay somewhat constant over the duration of an entire musical
part, each of the feature vectors is similar to all other feature vectors within this segment. As a result, an entire block of large values appears in the SSM. In other words,
homogeneity properties correspond to block-like structures. Second, if the feature
sequence contains two repeating subsequences (e.g., two segments corresponding
to the same musical part), the corresponding elements of the two subsequences are
similar to each other. As a result, a path (or stripe) of high similarity running parallel to the main diagonal becomes visible in the SSM.
In other words, repetitive
properties correspond to path-like structures.


## Audo thumbnailing

Given a music recording, the objective
is to automatically determine the most representative section, which may serve as a
kind of “preview” giving a listener a ﬁrst impression of the song or piece of music.

First, it indicates how well a given segment explains other related segments, and second, it indicates how much of the overall music recording is covered by all these related segments
