Limitations and perspectives
****************************

Multiple sets of parameters
===========================

For a given subject, it is impossible to process the data using different sets of parameters.
The last execution will systematically overwrite the previous one, the data associated with a previous execution will be lost.
A feature allowing to create and choose presets of parameters for each step and keep the corresponding output files may be implemented. 

Overwrite or keep preexisting data
==================================

CCEP Manager will execute a step using all available inputs (except for the implantation step)
and will re-process and overwrite already processed data.
A feature allowing the user to select a subset of inputs for a given step may be implemented.

Use bipolar or monopolar montage
================================

CCEP Manager is an implementation of the F-Tract processing pipeline which computes bipolar montage
before :ref:`feature extraction<features step>`.
It is currently not possible to extract features from monopolar channels but an option may be available in upcoming versions.