---
layout: page
title: Vocal Synthesis
description: Creating realistic vocal tones using analogue synthesis.
img: assets/img/vocal_synth/vocal_synth_picture.gif
importance: 2
category: all
---

In my Physics Of Music class, we spent a considerable amount of time talking through the mechanics of human speech. While the task of synthesizing fully life-like human speech is very difficult (due to the complex modeling required for certain phonetics), the replication of vowels is relatively straight-forward.

Essentially, the position of our tongue, jaw and lips highlight different resonances (called formants). These unique formants go on to make distinct vowel sounds (see the graphic below for a visual example).

<div class="row justify-content-center">
    <div class="col-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/vocal_synth/vocal_example.gif" title="Vocal Figure" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A visual example of mouth shape corresponding to frequency spectra (Credit: Georgia State University).
</div>

By using [MaxMSP](https://cycling74.com/products/max), I created a program to model this phenomena and re-create vowels outlined by the [IPA](https://en.wikipedia.org/wiki/International_Phonetic_Alphabet) (International Phonetic Alphabet).

A demo and walkthrough can be found by watching my video below.

<div class="row justify-content-center">
    <div class="col-8 mt-3 mt-md-0">
        {% include figure_video.html path="https://youtube.com/embed/FyhVHZkV6FQ" title="Vocal Figure" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Additionally, the .maxpat file can be found [here](https://drive.google.com/file/d/1aG9AmxvosqzcI9h03TReayqfvIFYOnCZ/view?usp=sharing).