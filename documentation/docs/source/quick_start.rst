.. CCEP Manager documentation master file, created by
   sphinx-quickstart on Mon Oct 18 18:32:20 2021.
   
.. _quick start:
   
Quick start
***********

CCEP Manager is a BIDS app allowing the processing of iEEG data coupled with electrical stimulation
for the extraction of CCEP features and the generation of connectivity atlases.

.. figure:: /art/cpman_main.png
	:width: 1000px
	:align: center

	**CCEP Manager.** *Main inferface.*

Before you start
================      

Prepare your working environment
--------------------------------  

CCEP Manager requires :ref:`MatLab 2015b Runtime (9.0) <MATLAB Runtime>` as it is used for
data processing, whereas :ref:`AnyWave <AnyWave>` and :ref:`HiBoP <HiBoP>` softwares are optional
but recommended for data visualization and reviewing.                                                                                                         

Prepare your data
-----------------

CCEP Manager is a BIDS app and only works on a valid :ref:`BIDS database <BIDS database>` as it uses BIDS directory tree
and file naming conventions to gather inputs and generate outputs in adequate derivative folders.
Accordingly, iEEG data should either be in *BrainVision format* or *European Data Format*.

Stimulation events in iEEG files should use the following :ref:`labelling convention<event notes>` : 

	*<label1> <label2> <intensity> mA <frequency> Hz <pulse width> us*

	*(e.g.: Bp02 Bp03 3mA 1Hz 1000us)*
	
iEEG data need to be paired with :ref:`implantation file(s) <implantation files>` that specify electrode coordinates
and corresponding cortical parcels. Implantation file(s) should be prepared using the :ref:`IntrAnat software<IntrAnat>`.

Data processing
===============

The processing pipeline consists in 4 sequential steps:

#. :ref:`Implantation step<implantation step>`
#. :ref:`Preprocessing step<preprocessing step>`
#. :ref:`Cropping step<cropping step>`
#. :ref:`Features step<features step>`

Each step produces substantially different outputs which can be explored using various :ref:`tools<tools>`.
The last :ref:`features step<features step>`, allows the computation of :ref:`connectivity atlases<atlases>`.  

Setting things up
-----------------

Start CCEP Manager double clicking the executable file and specify dependencies for your system once for all clicking |parameters button|
and selecting the *DEPENDENCIES* option (|dependencies button|).
A popup will appear allowing to specify (|open button|) the paths of both :ref:`AnyWave <AnyWave>` and :ref:`HiBoP <HiBoP>` executable files.
Don't forget to save modifications clicking |save button| before closing the popup. 

.. |parameters button| image:: art/parameters_20_100x100.png
	:width: 25px
	
.. |dependencies button| image:: art/dependencies_20_100x100.png
	:width: 25px
	
.. |open button| image:: art/open_20_100x100.png
	:width: 25px
	
.. |save button| image:: art/save_20_100x100.png
	:width: 25px

.. _ui dependencies:

.. figure:: /art/cpman_dependencies.png
	:width: 1000px
	:align: center
	
	**Specify dependencies.** *Specify the paths to* :ref:`AnyWave<AnyWave>` *and* :ref:`HiBoP<HiBoP>` *on your system.*

Then, specify the active BIDS database by browsing (|open button|) and selecting the root directory of the appropriate BIDS database.

.. _ui open:

.. figure:: /art/cpman_bids.png
	:width: 500px
	:align: center
	
	**Specify a BIDS database.** *Specify the path to the* :ref:`BIDS database<BIDS database>` *.*

Once a valid BIDS database is selected the interface will update showing the list of subjects
and the status of each processing step in the form of an :ref:`interactive table<ui table>`.

.. _ui table:

.. figure:: /art/cpman_table.png
	:width: 1000px
	:align: center
	
	**Interactive table.** *The table shows, for each patient, the status of each step forming the processing pipeline.*

Define a processing pipeline
----------------------------

Cells from the :ref:`interactive table<ui table>` are selectable and a number of contextual actions can be performed
on the active selection. Select the first 3 steps of a subject who has the iEEG data you would like
to process and for which the corresponding :ref:`implantation file(s)<implantation files>` have been prepared.
Then, right click the table and select the *RUN* option (|pipeline button|) from the contextual menu to spawn the data:ref:`processing interface<Process UI>`.

.. |pipeline button| image:: art/pipeline_20_100x100.png
	:width: 25px
	
.. figure:: /art/cpman_pipeline.png
	:width: 1000px
	:align: center

	**Prepare a pipeline.** *Right click the table after selecting processing steps (cells) to access contextual options.*

Configure and execute processing steps
--------------------------------------

The :ref:`data processing interface<ui configure>` lets you configure the steps you have previously chosen in
the :ref:`interactive table<ui table>`. 
Selecting a subject allows to configure the step parameters in the corresponding tab.

.. _ui configure:

.. figure:: /art/cpman_configure.png
	:width: 1000px
	:align: center

	**Data processing interface.** *It is possible to configure each step separately
	in the corresponding tab after selecting a subject in the list.*

The :ref:`implantation step parameters<ui implantation>` propose various drop down lists,
corresponding to `BIDS iEEG entities <https://bids-specification.readthedocs.io/en/stable/99-appendices/04-entity-table.html>`_,
(key-value pairs) in order to filter the iEEG data to process from the BIDS database.
Click on the *Implantation file* button to browse and select
the :ref:`implantation file<implantation files>` (\*.csv) that will be matched with the filtered iEEG data.

.. _ui implantation:

.. figure:: /art/cpman_implantation.png
	:width: 400px
	:align: center

	**Implantation step parameters.** *Filter the iEEG data from the BIDS database
	and match them with a carefully crafted* :ref:`implantation file<implantation files>` *.*

To configure the other steps, please refer to CCEP Manager full documentation which contain in-depth
description of the :ref:`processing steps<processing data>` and associated parameters.
If you are unsure, keep the default values.

.. |run button| image:: art/run_20_100x100.png
	:width: 25px
	
Clicking *RUN* (|run button|) will execute the pipeline. Processing logs will be display in real-time in the *REPORT* area.
In the meantime, it is possible to define an other pipeline and process data following the same approach.

Perform quality control
-----------------------
.. |refresh button| image:: art/refresh_20_100x100.png
	:width: 25px
	
Once data processing is over, click |refresh button| to refresh the table.

.. _ui refresh:

.. figure:: /art/cpman_refresh.png
	:width: 500px
	:align: center
	
	**Refresh button.** *Parses the BIDS database to update the* :ref:`interactive table<ui table>` *.*
	
.. |notready status| image:: art/notready.png
	:width: 150px
	
At this point, stimulation periods in iEEG files have been detected and cropped into individual files.
However, the :ref:`cropping step<cropping step>` is |notready status| because the cropped stimulations need to be
qualitatively controlled and validated first.

.. |timeseries tool| image:: art/timeseries_20_100x100.png
	:width: 25px
	
.. |wrench button| image:: art/wrench_20_100x100.png
	:width: 25px
	
Select the :ref:`cropping step<cropping step>` and then the *TIME SERIES* option (|timeseries tool|)
after clicking |wrench button|.

.. _ui timeseries:

.. figure:: /art/cpman_timeseries.png
	:width: 300px
	:align: center
	
	**Visualize time series.** *It is possible to visualize time series for
	the* :ref:`preprocessing<preprocessing step>` *or* :ref:`cropping<cropping step>` *steps using* :ref:`AnyWave<AnyWave>`.

.. |anywave software| image:: art/anywave_20_100x100.png
	:width: 25px
	
The popup :ref:`illustrated below<ui validate>` allows the selection of one or multiple cropped
stimulations to *DISPLAY* (|anywave software|) in :ref:`AnyWave<AnyWave>`.

.. _ui validate:

.. figure:: /art/cpman_validate.png
	:width: 500px
	:align: center
	
	**Display and validate cropped stimulation.** *Select files to display in* :ref:`AnyWave<AnyWave>` *and validate bad channels.*

In :ref:`AnyWave<AnyWave>`, you can edit the montage and specify bad channels as :ref:`illustrated below<ui AnyWave>`.

.. _ui AnyWave:

.. figure:: /art/cpman_anywave.png
	:width: 600px
	:align: center

	**Specify bad channels.** *Use* :ref:`AnyWave<AnyWave>` *to edit the montage and specify bad channels before validating a cropped stimulation.*

.. |valdiate button| image:: art/validate_20_100x100.png
	:width: 25px
			
.. |ready status| image:: art/ready.png
	:width: 125px
	
Once a cropped stimulation has been carefully reviewed in :ref:`AnyWave<AnyWave>`, *VALIDATE* it 
clicking |valdiate button|.
Once all the cropped stimulations are validated, :ref:`refresh<ui refresh>` (|refresh button|) the interactive table so statuses are updated
and the :ref:`cropping step<cropping step>` shows as |ready status|, meaning it is ready for CCEP computation and feature extraction.


Extract CCEP features
---------------------

Once the cropped stimulations have been qualitatively controlled and validated, the :ref:`cropping step<cropping step>` is |ready status|.
It is possible to execute the :ref:`features step<features step>` which will extract CCEPs features.
Following the same procedure as before, click the features step in the table,
then select the *RUN* option (|pipeline button|) from the contextual menu (right click) to initiate the :ref:`data processing interface<ui configure>`.
From there, it is possible to configure the step in the corresonding tab and execute the one-step pipeline
clicking the *RUN* button (|run button|).

Results and visualizations
==========================

Each processing step generates output files (mostly MATLAB/SPM object files) stored in the */derivatives/ccep_manager*
directory of the active BIDS database.
Please refer to the :ref:`data processing chapter <processing data>` for the exact location and detailed description
of the output files. 

You can access several :ref:`tools<tools>` by clicking |wrench button|, which is available for each processing step:

* 	The :ref:`TIME SERIES<time series visualization>` tool (|timeseries tool|) is accessible for both the :ref:`preprocessing <preprocessing step>`
	and :ref:`cropping <cropping step>` steps. 
	An interface allows the selection of one or several preprocessed iEEG file or
	cropped stimulations to be displayed in :ref:`AnyWave<AnyWave>`.

.. |ccep tool| image:: art/ccep_20_100x100.png
	:width: 25px
	
*	The :ref:`CCEP<CCEP visualization>` tool (|ccep tool|) is only accessible for the :ref:`cropping step <cropping step>`.
	The cropped stimulations will be assigned to an HiBoP project that will be automatically loaded
	in the :ref:`HiBoP<HiBoP>` software in order to visualize CCEPs in 3D.

.. |features tool| image:: art/features_20_100x100.png
	:width: 25px
	
*	The :ref:`FEATURES<feature tables>` tool (|features tool|) is only accessible for the :ref:`features step <features step>`.
	It can be used to store the various scores extracted from CCEP features into convenient text (\*.txt) files.
	
.. |atlas tool| image:: art/atlas_20_100x100.png
	:width: 25px
	
*	The :ref:`ATLAS<feature tables>` tool (|atlas tool|) is only accessible for the :ref:`features step <features step>`.
	It can be used to generate :ref:`connectivity atlases<atlases>` computed from CCEP features.