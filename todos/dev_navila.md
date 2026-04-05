# NaVILA task list

## configure NaVILA
1. download code from [github](https://github.com/AnjieCheng/NaVILA)
2. download datasets from youtube (Locally download by using proxy) or jump to step 3.
3. configure Habitat3 simulation environment.
4. training NaVILA on Habitat3 based on Humanoid and Quadruped robot.



## Locamotion training

- [ ] download code for logomotion training from [github](https://github.com/yang-zj1026/legged-loco)
- [ ] fix version mismatch and code implementation for rsl-rl. (review code in [legged-loco/rsl-rl](https://github.com/yang-zj1026/legged-loco/tree/main/rsl_rl))
- [ ] training locomotion for humanoid and quadruped robot in Isaac Lab
- [ ] save model for VLA training
- [ ] develop new environment for applications

## Data

### ShareGPT4V
- [] download data from [shareGPT4V](https://github.com/ShareGPT4Omni/ShareGPT4V/blob/master/docs/Data.md)

## VLA training 

- [ ] download videos by [yotube ids](https://huggingface.co/datasets/a8cheng/NaVILA-Dataset/blob/main/Human/video_ids.txt)
- [ ] download annotaitons from [hugging face](https://huggingface.co/datasets/a8cheng/NaVILA-Dataset)
- [ ] download dataset for EnvDrop (ref [github](https://github.com/AnjieCheng/NaVILA)).
- [ ] install requirements for simulation environment (Habitat3). 
- [ ] download pretrained weights from [a8cheng/navila-siglip-llama3-8b-v1.5-pretrain](https://huggingface.co/a8cheng/navila-siglip-llama3-8b-v1.5-pretrain)
- [ ] install [VLN-CE](https://github.com/jacobkrantz/VLN-CE)