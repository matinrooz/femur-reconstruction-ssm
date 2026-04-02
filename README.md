# Femur Reconstruction with Statistical Shape Modelling

This project reconstructs full femur shapes from partial femur data using Statistical Shape Models (SSM) and Gaussian Processes.

## Pipeline

1. Rigid Alignment (landmarks)
2. Gaussian Process model
3. Non-rigid ICP
4. PCA model
5. Reconstruction

## Structure

- src/ → Scala code
- report/ → project report + theory answers
- models/ → trained models
- results/ → reconstructed femurs

## Results

Sample reconstructed femurs are available in:
`results/reconstructed_meshes/`

## Notes

- Dataset not included due to size
- Paths in code may need adjustment

