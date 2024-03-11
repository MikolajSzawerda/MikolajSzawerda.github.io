---
layout: post
title:  "MIR introduction"
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

## WIMU - MIDI

### Hamming distance
In information theory, the Hamming distance between two strings or vectors of equal length is the number of positions at which the corresponding symbols are different. In other words, it measures the minimum number of substitutions required to change one string into the other

- maestro dataset
- Byte pair encoding
- performance RNN

### SimCSE - uczenie kontrastywne
Contrastive Learning is a deep learning technique for unsupervised representation learning. The goal is to learn a representation of data such that similar instances are close together in the representation space, while dissimilar instances are far apart.

It has been shown to be effective in various computer vision and natural language processing tasks, including image retrieval, zero-shot learning, and cross-modal retrieval. In these tasks, the learned representations can be used as features for downstream tasks such as classification and clustering.

To do so, we can feed these two images(augmented versions of image) into our deep learning model (Big-CNN such as ResNet) to create vector representations for each image. The goal is to train the model to output similar representations for similar images.

#### Zero-shot learning
One way is to decrease our models’ reliance on labeled data. This is the motivation behind zero-shot learning, in which your model learns how to classify classes that it hasn’t seen before.

Why should the image encoding be as similar as possible to it’s corresponding text encoding? Now that we know what an encoding is and why it’s important to learn good encodings, we can explore why we are forcing the model to make image and text encodings similar.

- Lakh MIDI
- performance * in music - w kontekście artykulacji