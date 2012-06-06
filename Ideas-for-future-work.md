This page can be used to collect some ideas for what could be done if there was time.

Voice building
--------------

aim: introduce robustness, transparency (so you know what's going on)
and the ability to paralelize into the process.

idea: insert the concept of a "data item" which knows its current status.

A data item object represents the output or input of a processing step.
For example, a data item can represent a wav file, or a pitchmark file, i.e.
a certain processing result for an individual basename;
but it can also represent an agglomerative output, such as a waveform timeline or a duration prediction tree.

Each component has a list of required data items and a list of produced data items.

This means for the data item "unit selection voice" we can look backwards how we can build this.

For simplicity, our data item sequence is predefined, not "emerging" as in MARY TTS. That is, the sequence of steps is clear; the components implementing the steps can be exchanged or selected (e.g., several possible pitchmarkers).

At startup, the database layout checks which data items are up to date.