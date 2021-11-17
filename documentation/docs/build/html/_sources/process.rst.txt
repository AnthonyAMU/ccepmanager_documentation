.. _processing data:

Processing data
***************

The processing pipeline in CCEP Manager consists in 4 sequential steps :

#. :ref:`implantation step<implantation step>`.
#. :ref:`preprocessing step<preprocessing step>`.
#. :ref:`cropping step<cropping step>` + :ref:`quality control<quality control>`.
#. :ref:`features step<features step>`.

In practice terms, processing data simply requires to select the desired steps in the :ref:`interactive table<panels UI>`
and execute them in the dedicated :ref:`processing interface <Process UI>`. After the execution, :ref:`reports <step report>` are generated
and :ref:`step statuses <step status>` are updated.
Each step generates different outputs which can be exlored using dedicated :ref:`tools <tools>`.

.. _Step execution:

Processing steps
================

.. _implantation step:

Implantation step
-----------------

The implantation step is the very first step of the processing pipeline and the objective is
to associate iEEG files found in the BIDS database with :ref:`implantation file(s)<implantation files>`.
To do so, the implantation tab of the :ref:`processing interface<Process UI>` allows the user to filter the iEEG data
found in the BIDS database and associate an implantation file. 

The implantation step will produce several output files found in the following directory:

	*BIDS root/derivatives/ccep_manager/step_implantation/sub-<id>/ses-<session>/ieeg*
	
The outputs are *<iEEG file>_implantation.csv* files which are a copy of the original implantation files but reuse
the name of the iEEG file they have been associated with as a prefix. 

.. _preprocessing step:

Preprocessing step
------------------

The objective of the preprocessing step is to prepare the iEEG files for the rest of the pipeline.
This step extracts electrode coordinates from the :ref:`implantation file<implantation files>` 
and parses the notes of the iEEG files to get the :ref:`stimulation parameters<event notes>`.
The preprocessing tab in the :ref:`processing interface<Process UI>` lets the user specify a default *Pulse Width*,
which will be used if it cannot be deduced from the notes.

The preprocessing step uses the *<iEEG file>_implantation.csv* files generated during the :ref:`implantation step<implantation step>` as
inputs to identify and convert associated iEEG files into SPM objects.

The output files are described in the :ref:`table below<preprocessing outputs table>` and are stored in the following directory:

	*BIDS root/derivatives/ccep_manager/step_preprocessing/sub-<id>/ses-<session>/ieeg*

.. _preprocessing outputs table:

+---------------------------------------+------------------------------------------------------+
| OUTPUT                                | DESCRIPTION                                          |
+=======================================+======================================================+
| <iEEG file>.dat                       | SPM object. Voltage values.                          | 
+---------------------------------------+------------------------------------------------------+
| <iEEG file>.mat                       | SPM object. Header.                                  |
+---------------------------------------+------------------------------------------------------+
| <iEEG file>_log.txt                   | Log file of the conversion process.                  | 
+---------------------------------------+------------------------------------------------------+
| contactsunmatched_<iEEG file>.txt     | Log file containing the list of discarded channels.  | 
+---------------------------------------+------------------------------------------------------+

	**Preprocessing step outputs.** *List of output files produced by the preprocessing step. iEEG file is the name of the preprocessed i(EEG) file.*

.. _cropping step:

Cropping step
-------------

The objective of the cropping step is to split iEEG files according to stimulation periods
so the resulting output files contain a single stimulation.

If applicable, the cropped stimulation will start -40s before the first stimulation pulse and end +1s after
the last stimulation pulse. 

All time series found in the cropped stimulations 
are tested for bad channels using ensemble machine learning and classified either “good” or “bad”
depending on multiple metrics detailed in Tuyusenge et al. (2018) ([Tuyisenge_2018]_).

The cropping tab in the :ref:`processing interface<Process UI>` allows to specify a particular stimulation to crop
using its index (0 means all stimulations will be cropped).
It is important to follow :ref:`note labelling convention <event notes>` to ensure a robust cropping procedure.

The cropping step relies on the SPM objects generated during the :ref:`preprocessing step<preprocessing step>`
to detect stimulation events from the notes along with a threshold-based detection algorithm
applied on time series ([Trebaul_2018]_).

The output files are described in the :ref:`table below<cropping outputs table>` and are stored in the following directory:

	*BIDS root/derivatives/ccep_manager/step_cropping/sub-<id>/ses-<session>/ieeg*

.. _cropping outputs table:

+-------------------------------------------+--------------------------------------------------------+
| OUTPUT                                    | DESCRIPTION                                            |
+===========================================+========================================================+
| <iEEG>_stimulations.tsv                   | List of all cropped stimulations.                      | 
+-------------------------------------------+--------------------------------------------------------+
| <iEEG>/...                                | Folder reusing the name of the cropped iEEG file.      |                                 
+-------------------------------------------+--------------------------------------------------------+
| .../<stimulation>.mat                     | SPM object. Header.                                    | 
+-------------------------------------------+--------------------------------------------------------+
| .../<stimulation>.dat                     | SPM object. Voltage values.                            | 
+-------------------------------------------+--------------------------------------------------------+
| .../<stimulation>.csv                     | Machine learning metrics.                              | 
+-------------------------------------------+--------------------------------------------------------+
| .../<stimulation>_bChans.txt              | Machine learning bad channels (indexes + labels).      | 
+-------------------------------------------+--------------------------------------------------------+
| .../<stimulation>_bIdx.txt                | Machine learning bad channels (indexes).               | 
+-------------------------------------------+--------------------------------------------------------+
| .../recordings_monopolar_<stimulation>.txt| List of monopolar channels in the cropped stimulation. | 
+-------------------------------------------+--------------------------------------------------------+

	**Cropping step outputs.** *List of output files produced by the cropping step. <iEEG> is the name of the cropped iEEG file. <stimulation> is the name the cropped stimulation and is derived from the corresponding note.*

It is possible to visualize time series of cropped stimulations using :ref:`AnyWave<time series visualization>`
and visualize mean responses (CCEP) in 3D using :ref:`HiBoP<CCEP visualization>`.

.. _quality control:

Quality control
---------------

The objective of the quality control step is to review the results of the :ref:`machine learning classification<cropping step>`
and correct “good” and “bad” channels if necessary.

This step is human dependent and it is possible to use :ref:`AnyWave<time series visualization>`
to visualize the time series of cropped stimulations in order to review and validate bad channels.
After a cropped stimulation has been validated
the corresponding SPM object and all :ref:`companions files<cropping outputs table>` are updated. 

In practice, :ref:`AnyWave<AnyWave>` is not mandatory and this step only requires to create
a *<stimulation>_bIdxChecked.txt* file for each cropped stimulation containing the indexes of bad channels.
The *<stimulation>_bIdxChecked.txt* files are thus very similar to the *<stimulation>_bIdx.txt* files
generated during the :ref:`cropping step<cropping step>`, except they are populated by the user instead of the classifier.

The validation procedure performed via the :ref:`time series tool<time series visualization>` then uses the indexes found in the *<stimulation>_bIdxChecked.txt*
(or the *<stimulation>_bIdx.txt* if the latter is empty) to update the output files of the :ref:`cropping step<cropping step>`.

.. _features step:

Features step
-------------

The objective of the features step is to compute the mean response of cropped stimulations
and extract :ref:`features<CCEP features table>`. The features tab in the :ref:`processing interface<Process UI>`
allows to specify a set of :ref:`parameters<features parameters table>` that will be used during the extraction of the features. 

The features step is actually an aggregate of multiple treatments whose technical aspects
are described in Trebaul et al. (2018) ([Trebaul_2018]_). Briefly, iEEG signals are
re-referenced with a local bipolar montage between adjacent contacts of the same electrode to increase
the spatial specificity of the effects. A stimulation artifact correction with a model-based method is then
applied on single-trials to consider the first milliseconds after stimulation for the
CCEP analysis ([Trebaul_2016]_). Finally, the re-referenced signals are band-pass
filtered between 1 and 45 Hz to best identify the CCEP components. CCEP are obtained using a robust-averaging
method aiming at removing any possible spurious component due to non-stimulation triggered events,
such as interictal epileptic spikes. Responses over pulses are averaged together,
but excluding the trials showing a response energy 3 times higher than the median response
energy over the whole stimulation run ([David_2013]_). After averaging,
the CCEP on each channel is baseline-corrected and z-scored with respect to the same baseline.
Z-scoring quantifies the signal-to-noise ratio on each channel,
thereby allowing easy intra- and inter-patient statistics.
The absolute value of the z-scored CCEP is used to identify the first significant component
which is characterized extracting several :ref:`features<CCEP features table>`.
The features are extracted using several statistical thresholds ranging from z=1 to z=7.

.. _features parameters table:

+--------------------------+----------------------------------------------------------------------------+
| PARAMETER                | DESCRIPTION                                                                |
+==========================+============================================================================+
| Interpolation start/end  | Time frame around each stimulation pulse used for artefact correction.     | 
+--------------------------+----------------------------------------------------------------------------+
| Cutoff frequency         | Upper cutoff frequency of the band pass filter.                            |                                 
+--------------------------+----------------------------------------------------------------------------+
| Minimum baseline         | Time before each pulse used for baseline computation.                      | 
+--------------------------+----------------------------------------------------------------------------+
| Maximum response         | Time after each pulse used for response computation.                       | 
+--------------------------+----------------------------------------------------------------------------+

	**Features step parameters.** *List of adjustable parameters accessible in the features tab of the* :ref:`processing interface<Process UI>` *.*

.. _CCEP features table:

+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| FEATURE       | DESCRIPTION                                                                                                                                                                                                                                                 |
+===============+=============================================================================================================================================================================================================================================================+
| Amplitude     | Amplitude of the first peak above the significance threshold.                                                                                                                                                                                               | 
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Duration      | First time window with consecutive samples exceeding the significance threshold. If the Signal amplitude went down this threshold but stayed above a minimal threshold (z = 4) for less than 5ms, this signal part was still considered as significant.     |                                 
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Integral      | Signal integral computed over of the first significant time window.                                                                                                                                                                                         | 
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Latency start | Latency of the first sample with an amplitude exceeding the significance threshold.                                                                                                                                                                         | 
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Onset delay   | Latency of the deflection point preceding the first peak.                                                                                                                                                                                                   | 
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Peak delay    | Latency of the first peak above the significance threshold.                                                                                                                                                                                                 |                                 
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Probability   | Boolean indicating if the Amplitude was below (0) or over (1) the threshold.                                                                                                                                                                                | 
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Slope         | Response slope between latency start and peak delay.                                                                                                                                                                                                        | 
+---------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

	**CCEP features.** *List of features extracted from CCEPs (* [Trebaul_2018]_ *).*
	
The features step processes the cropped stimulations generated during the :ref:`cropping step<cropping step>` and extracts
features from the averaged response.

The :ref:`outputs<features outputs table>` and :ref:`results<features results table>` files are described in the tables below and are stored in the following directory:

	*BIDS root/derivatives/ccep_manager/step_averaging/sub-<id>/ses-<session>/ieeg*

.. _features outputs table:

+------------------------------------+----------------------------------------------------------------------------+
| OUTPUT                             | DESCRIPTION                                                                |
+====================================+============================================================================+
| <iEEG>_stimulations.tsv            | List of all cropped stimulations.                                          | 
+------------------------------------+----------------------------------------------------------------------------+
| <iEEG>/...                         | Folder reusing the name of the cropped iEEG file.                          |                                 
+------------------------------------+----------------------------------------------------------------------------+
| .../<stimulation>.dat              | SPM object. Voltage values.                                                | 
+------------------------------------+----------------------------------------------------------------------------+
| .../<stimulation>.mat              | SPM object. Header.                                                        | 
+------------------------------------+----------------------------------------------------------------------------+
| .../bp_<stimulation>_log.txt       | Log file of the bipolar montage process.                                   | 
+------------------------------------+----------------------------------------------------------------------------+
| .../recordings_bp_<stimulation>.txt| List of bipolar channels.                                                  | 
+------------------------------------+----------------------------------------------------------------------------+

	**Features step outputs.** *List of output files produced by the features step. <iEEG> is the name of the cropped iEEG file. <stimulation> is the name the cropped stimulation and is derived from the corresponding note.*

.. _features results table:

+-------------------+-------------------------------------------------------------+
| PREFIX            | DESCRIPTION                                                 |
+===================+=============================================================+
| \*bp\*            | Time series after bipolar montage.                          | 
+-------------------+-------------------------------------------------------------+
| \*artcorr\*       | Time series after artefact correction.                      | 
+-------------------+-------------------------------------------------------------+
| \*filter\*        | Time series  after filtering.                               | 
+-------------------+-------------------------------------------------------------+
| \*avg\*           | Time series after response averaging.                       | 
+-------------------+-------------------------------------------------------------+
| \*str\*           | Time series after standard deviation computation.           | 
+-------------------+-------------------------------------------------------------+
| \*zavg\*          | Time series after response averaging and z-transformation.  | 
+-------------------+-------------------------------------------------------------+
| \*duration\*      | Duration scores.                                            | 
+-------------------+-------------------------------------------------------------+
| \*integral\*      | Integral scores.                                            | 
+-------------------+-------------------------------------------------------------+
| \*latencystart\*  | Latency start scores.                                       | 
+-------------------+-------------------------------------------------------------+
| \*onsetdelay\*    | Onset delay scores.                                         | 
+-------------------+-------------------------------------------------------------+
| \*peakdelay\*     | Peak delay scores.                                          | 
+-------------------+-------------------------------------------------------------+
| \*prob\*          | Probability scores.                                         | 
+-------------------+-------------------------------------------------------------+
| \*slope\*         | Slope score.                                                | 
+-------------------+-------------------------------------------------------------+
| \*ampl\*          | Amplitude scores.                                           | 
+-------------------+-------------------------------------------------------------+

	**Features step results.** *List of prefixes used as naming convention for result files produced by the features step. Prefixes stack as the file is processed.*

It is possible to extract the features stored in the SPM objects into plain text files using the :ref:`dedicated tool<feature tables>` .

Step information
================

.. _step status:

Step status
-----------

The general status of a step is directly visible in the :ref:`interactive table<panels UI>` 
and described in the :ref:`table<general status table>` below.
It is also possible to :ref:`explore step(s)<Details UI>` and access more detailed information.

In the current iteration of CCEP Manager, statuses are not automatically updated and the :ref:`interactive table<panels UI>` needs
to be :ref:`refreshed<BIDS UI>` after
:ref:`processing data<processing data>` or :ref:`validating cropped stimulations<quality control>`.

.. _general status table:

+------------------+----------------------------------------------------------------+
| GENERAL STATUS   | DESCRIPTION                                                    |
+==================+================================================================+
| MISSING          | Data associated with the step cannot be found.                 | 
+------------------+----------------------------------------------------------------+
| NOT READY        | Data associated with the step is incomplete.                   | 
+------------------+----------------------------------------------------------------+
| READY            | Data associated with the step is complete.                     | 
+------------------+----------------------------------------------------------------+
| OUTDATED         | Step execution is older than the previous step last execution. | 
+------------------+----------------------------------------------------------------+
| ERROR            | Unexpected status.                                             |  
+------------------+----------------------------------------------------------------+

	**General step status.** *Possible statuses of a step.*
	
.. _step report:

Step report
-----------

Executing a step will generate a report in the corresponding derivative directory.
The report is a *\*.json* file using the following naming convention.

	*sub-<id>_<step>_<timestamp>_execution_report.json.*

	* <id> is the subject identifier.
	* <step> is the step name (csv, preprocessing, cropping, averaging).
	* <timestamp> is a 10-digit timestamp 

The report file contains information regarding the step and its execution as described in the :ref:`table below<report table>`.
It is also possible to display, clean and save the content of the :ref:`console<Process UI>`,
which show execution logs as the data is being processed.

.. _report table:

+----------+----------------------------------+
| REPORT   | DESCRIPTION                      |
+==========+==================================+
| subject  | Subject ID.                      | 
+----------+----------------------------------+
| step     | Step name.                       | 
+----------+----------------------------------+
| date     | Execution date.                  | 
+----------+----------------------------------+
| args     | Arguments of the command line.   | 
+----------+----------------------------------+
| Input    | Input directory.                 | 
+----------+----------------------------------+
| Output   | Output directory.                | 
+----------+----------------------------------+
| Status   | Execution status.                | 
+----------+----------------------------------+
| Stdout   | Standard stream output.          | 
+----------+----------------------------------+
| stderr   | Standard stream error.           | 
+----------+----------------------------------+

	**Execution report.** *Entries found in the report automatically created after executing a processing step.*

