# femur-reconstruction-ssm

**What:** Group project: complete femurs reconstructed from partial 3D scans with a Gaussian process shape model and non-rigid ICP.  
**Result:** Compared visually: femur 47 fitted best, femur 56 worst, probably because 47 training femurs do not cover enough shape variation.  
**Stack:** Scala, Scalismo

Group project with Hari Narayanan. We reconstructed complete femur bones from partial 3D
scans with a statistical shape model, in Scala with [Scalismo](https://scalismo.org).

Data: 47 full femur meshes with landmarks, and 10 partial femurs (no. 47 to 56) to
reconstruct. The data is not in this repo.

## Steps

Each step is one file in `src/` and saves its output for the next one.

1. **Rigid alignment** (`FemurRigidAlignment.scala`): align all 47 femurs to a reference
   femur with landmark-based rigid registration.
2. **GP model** (`GPmodel.scala`): Gaussian process with zero mean on the reference femur.
   The kernel is the sum of two diagonal kernels built from Gaussian kernels
   (σ = 40, 60, 100 and σ = 50, 60, 100 for x, y, z). Low-rank approximation with
   `approximateGPCholesky` (tolerance 0.07).
3. **Registration** (`ICP.scala`): for each of the 47 femurs, a posterior of the GP model
   given the landmarks, then non-rigid ICP (150 iterations, 1000 sampled points) to get
   meshes that are all in correspondence.
4. **PCA model** (`PCAmodel.scala`): build a point distribution model with PCA from the
   registered meshes.
5. **Reconstruction** (`Reconstruction.scala`): for each partial femur, sample 1000 points,
   find correspondences and run non-rigid ICP (100 iterations) with the shape model.

## Results

There are no distance measures in the report, we compared the results visually (slices in
x, y and z). Femur 47 fitted best, the y-slice is very close to the partial input. Femur 56
fitted worst. Our explanation was that the shape model, built from only 47 femurs, does not
cover enough variation for that shape. All figures are in `report/FinalReportHariMatin.pdf`,
the theory questions are in `report/AnswersToTheoryQuestions.pdf`.

When we sampled the PCA model, most random samples looked like real femurs, but when all
components are pulled to one side the shape becomes implausible.

## Things I would do differently

- Measure the reconstruction error (average distance, Hausdorff distance) instead of only
  looking at it.
- `Reconstruction.scala` loads `gpmodel.h5` into a variable called `PCAmodel`. So either the
  name is wrong or the reconstruction used the GP model and not the PCA model from step 4. I
  am not sure anymore which one we used for the final results.
- The report says 500 ICP iterations for the reconstruction, the code has 100.

## Run

The code was written for an sbt project based on the Scalismo seed, the build file is not
included. All paths are relative to `data/datasets/`:

```
data/datasets/
  femur.stl, femur.json          reference mesh and landmarks
  full-femurs/meshes/, landmarks/
  partial-femurs/
  alignedfemurs/meshes/, landmarks/   (written by step 1)
  PosteriorFitted/                    (written by step 3)
  results/                            (written by step 5)
```

Create the output folders before running, then run the five objects in order.
