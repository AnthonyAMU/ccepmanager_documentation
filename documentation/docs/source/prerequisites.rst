.. _prerequisites:

Prerequisites
*************

CCEP Manager has :ref:`dependencies <dependencies>` to external softwares and libraries which are detailed in this chapter. 
Briefly, :ref:`MatLab 2015b Runtime <MATLAB Runtime>` is mandatory as it is used for data processing,
whereas :ref:`AnyWave <AnyWave>` and :ref:`HiBoP <HiBoP>` softwares are optional but advised for data visualization and reviewing.

CCEP Manager is a BIDS app and can only work on a valid :ref:`BIDS database <BIDS database>` using :ref:`compatible file
formats <file formats>`.
i(EEG) data has to be :ref:`prepared <prepare data>` according to BIDS guidelines `(bids-specification.readthedocs.io) <https://bids-specification.readthedocs.io/en/stable>`_.
Notably, :ref:`stimulation notes <event notes>` have to be cleaned up and :ref:`implantation files <implantation files>` have to be
carefully crafted before executing the processing pipeline using the :ref:`IntrAnat <IntrAnat>` software..
CCEP Manager and all its dependencies are available for Windows and Linux.

.. _dependencies:

Dependencies
============

.. _MATLAB Runtime:

MATLAB Runtime
--------------

CCEP Manager requires MATLAB Runtime R2015b (9.0) to be installed ([MATLAB_2015]_).
The MATLAB Runtime is free to use and can be downloaded from the `MathWorks website <https://mathworks.com/products/compiler/matlab-runtime.html>`_.

Optional 
--------

CCEP Manager should be associated with other software, namely :ref:`AnyWave <AnyWave>` and :ref:`HiBoP <HiBoP>`, for optimal usage.
The paths to these software have to be specified once using the dependencies button accessible in the :ref:`parameters menu<parameters UI>`.

.. _AnyWave:

AnyWave
^^^^^^^

AnyWave ([Colombet_2015]_) is a software designed to easily open and view data recorded by EEG or MEG acquisition
systems. AnyWave can be associated with the CCEP Manager for the visualization of time series as well as the
specification of bad channels (see :ref:`time series visualization <time series visualization>` and :ref:`quality control<quality control>`). AnyWave is available on Windows OS, macOS and Linux, is free to use,
and can be downloaded from the `Marseille MEG platform wiki <https://meg.univ-amu.fr/wiki/AnyWave:Download>`_.
Additional documentation can be found in the dedicated `GitLab webpage <https://gitlab-dynamap.timone.univ-amu.fr/anywave/anywave/-/wikis/home>`_. 

.. _HiBoP:

HiBoP
^^^^^

HiBoP is a software dedicated to the visualization of intracranial brain recordings such as intracranial
electroencephalography (iEEG), Cortico-Cortical Evoked Potentials (CCEP) and functional magnetic resonance imaging (fMRI).
HiBoP can be associated with CCEP Manager for the 3D visualization of CCEPs (see :ref:`CCEP visualization <CCEP visualization>`).
HiBoP is freely available on Windows OS, macOS and Linux, and can be downloaded from the dedicated
`GitHub webpage <https://github.com/hbp-HiBoP/HiBoP>`_, which also hosts the documentation.

.. _IntrAnat:

IntrAnat
^^^^^^^^

IntrAnat ([Deman_2018]_) is a software used to perform virtual electrode implantation in patients’ neuroanatomy
and to overlay results of epileptic and functional mapping as well as resection masks from the surgery.
IntrAnat should be used to generate :ref:`implantation files<implantation files>`
that store electrode coordinates with corresponding cortical parcels 
and are used during the :ref:`implantation step<implantation step>`.

IntrAnat is available on Linux for free and can be downloaded from the dedicated
`IntrAnat GitHub webpage <https://github.com/IntrAnatSEEGSoftware/IntrAnat>`_, which also hosts the installation instructions
and documentation.

IntrAnat now proposes accessibility options to ease the importation of imaging data from a BIDS database
and a feature to export generated implantation files in suitable BIDS derivative folders.

.. _prepare data:

Preparing the data
==================

.. _BIDS database:

BIDS database
-------------

The Brain Imaging Data structure (BIDS) is a standard to organize and describe neuroimaging, neurophysiology
and behavioral data. It is widely used as it facilitates data sharing and analysis ([Gorgolewski_2016]_).

CCEP Manager is a BIDS App and can only work on a valid BIDS database as it uses BIDS directory tree and file naming
conventions to gather inputs and generate outputs in adequate derivative folders.
More information related to BIDS standard can be found on the dedicated website `(bids-specification.readthedocs.io) <https://bids-specification.readthedocs.io/en/stable>`_.

In addition, a tool purposely made for the creation, validation and maintenance of BIDS databases,
BIDS Manager ([Roehri_2021]_), is available on `GitHub <https://github.com/Dynamap/BIDS_Manager>`_ with associated documentation.
 
Required data
-------------

CCEP Manager is primarily designed for the extraction and characterization of CCEPs.
It only requires iEEG data coupled with electrical stimulation and associated :ref:`implantation file(s) <implantation files>` to work.
Extra data, such as imaging data, may be used by external software such as :ref:`HiBoP <HiBoP>`, but remain optional.

.. _file formats:

Compatible formats
------------------

It is mandatory to follow BIDS guidelines `(bids-specification.readthedocs.io) <https://bids-specification.readthedocs.io/en/stable>`_ 
and convert the data into BIDS friendly formats before processing it using CCEP Manager. 
Accordingly, iEEG data should be either in *BrainVision format* or *European Data Format*.
Imaging data, if any, should be in *NIfTI format*.

Suitable formats are listed in the :ref:`table of compatible iEEG formats <Compatible iEEG>`:

.. _Compatible iEEG:

+------------------------------------------------+-------------------------+
| FILE FORMAT                                    | FILE EXTENSION(S)       |
+================================================+=========================+
| BrainVision                                    | \*.hdr, \*.vmrk, \*.eeg | 
+------------------------------------------------+-------------------------+
| European Data Format (continuous only) (EDF+C) | \*.edf                  | 
+------------------------------------------------+-------------------------+
| Microdmed                                      | \*.trc                  | 
+------------------------------------------------+-------------------------+

	**Compatible iEEG formats.**  *List of iEEG data formats compatible with CCEP Manager.*

.. _event notes:

iEEG notes
------------

CCEP Manager relies on the notes found in iEEG files (see :ref:`preprocessing step <preprocessing step>`)
together with a threshold-based detection algorithm (see :ref:`cropping step <cropping step>`) 
to identify stimulation periods. In order to ease the identification process and avoid note parsing errors,
it is strongly advised to clean the notes beforehand by using the following labelling convention:

	*<label1> <label2> <intensity> mA <frequency> Hz <pulse width> us*
	
	*(e.g.: Bp02 Bp03 3mA 1Hz 1000us)*

* *<label1> <label2>* are the labels of the stimulating electrodes (alphanumeric).
* *<intensity>* is the stimulation intensity in mA (number).
* *<frequency>* is the stimulation frequency in Hz (number).
* *<pulse width>* is the pulse width in us (number).

.. _implantation files:

Implantation file
-----------------

During the very first step of the processing pipeline, each iEEG file will be associated with
an implantation file (see :ref:`implantation step <implantation step>`).
Implantation file(s) thus have to be prepared beforehand.
In its current iteration, CCEP Manager expects implantation files
generated by the :ref:`IntrAnat<IntrAnat>` software ([Deman_2018]_).

IntrAnat is used to localize and label the electrode contacts in 3D space
and generate Comma-Separated Values (\*.csv) implantation files which specify the coordinates of each
electrode in native and MNI spaces, along with corresponding cortical parcels. Cortical parcels are obtained for different
anatomical atlases defined either in the MRI native space:

* MarsAtlas ([Auzias_2016]_)
* Freesurfer ([Destrieux_2010]_)
* Lausanne Atlas ([Hagmann_2008]_)

or in the MNI space:

* Brodmann ([Brodmann_1909]_)
* Automated Anatomical Labeling AAL ([Tzourio-Mazoyer_2002]_)
* MaxProbMap ([Hammers_2003]_)
* Human Connectome Project ([Glasser_2016]_)

Each electrode contact is assigned to the grey or white matter and to specific anatomical parcels by taking the most
frequent voxel label in a sphere of 3 mm radius around each contact center ([Deman_2018]_).
All this information is summarized in the implantation file used by CCEP Manager.

In addition, IntrAnat now proposes accessibility options to ease the importation of imaging data from a BIDS database
and a feature to export generated implantation files in suitable BIDS derivative folder.



