
=======
Generic Diffusion tensor preprocessing with fsl
=======
-----------------
Goal:
-----------------
The generic_diffsl is a generic script  to preprocess dti data 

-----------------
Configuration:
-----------------

generic_diffsl needs FSL>=5.09 to be installed on your system and properly set up (http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/FslInstallation).

-----------------
Preprocessing steps:
-----------------
The pipeline consists in the following steps:

1. Linear registration with eddy current correction and bvec rotation using the first volume of the dti data as reference.
2. Brain extraction
3. Tensor computation with weighted least squares

-----------------
Inputs:
-----------------
The general use is:

generic_diffsl <dti> <pdir>

* dti,the 4D image including the diffusion weighted images.
* pdir, output directory for preprocessed data

All images must be in ANALYZE, NIFTI or NIFTI.GZ.

-----------------
Outputs:
-----------------

The script creates one folder per subject with inside:

======================================= =======================================================================================
file            Description
======================================= =======================================================================================
subject_dti			                        the raw dti data
subject_dti.bval			                  the b values
subject_dti.bvec			                  the gradient directions
subject_dti_ecc			                    the eddy current corrected dti data
subject_dti_ecc.bvec		                the bvec table corrected for rotation
subject_dti_ecc_mask		                the brain mask after eddy current correction
subject_dti_ecc_brain		                the brain extracted and eddy current corrected dti data
subject_dti_ecc_brain_*                 the diffusion measures from the tensor estimation with wls
subject_dtit.slices				              mean value per axial slices and diffusion-weighted volumes
subject_dti_ecc.log				              transformation matrix during the affine registration
subject_dti_ecc.rotation			          total angle rotation in radians and flag for 2 and 5 degrees
subject_dti_ecc_brain_tf.log			      summary of the wls tensor fitting after eddy current correction
subject_dti_ecc_dc_brain_tf.log		      summary of the wls tensor fitting after distortion correction

======================================= =======================================================================================

*: _V1 - 1st eigenvector; _V2 - 2nd eigenvector; _V3 - 3rd eigenvector; _L1 - 1st eigenvalue; _L2 - 2nd eigenvalue; _L3 - 3rd eigenvalue; _RD – radial diffusivity
_MD - mean diffusivity; _FA - fractional anisotropy; _MO - mode of the anisotropy (oblate ~ -1; isotropic ~ 0; prolate ~ 1); _S0 - raw T2 signal with no diffusion weighting

-----------------
Automatic QC:
-----------------
Several automatic QC steps can be performed on these output files:

1. Volumes and slices

QCing the number of volumes and slices.

* File: subject_dtit.slices
* Flag: if # of volumes != 36 or # of slices != 60

2. Slice-dropout

QCing slice by slice for divergent values indicating a signal dropout.

* File: subject_dtit.slices
* Flag: if # > 1

3. Head motion

QCing volume by volume for head rotation based on the matrix used for the spatial registration.

* File: subject_dti_ecc.rotation
* Flag: if # > 1

4. Tensor computation

QCing the tensor computation by using a k-means clustering on the global FA, MD, L1, L2, L3 and MO values.

* Files: subject_dti_ecc_brain_tf.log
* Flag: if not in the two main clusters.
