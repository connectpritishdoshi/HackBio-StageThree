# HackBio-StageThree
A PyTorch implementation of U-Net for binary brain tumor segmentation on the BraTS2020 dataset. The model takes single-channel FLAIR MRI slices as input and predicts a binary tumour mask. Training uses a combined Dice + BCE loss to directly optimise the overlap metric, with random flips for augmentation, a learning rate scheduler.
