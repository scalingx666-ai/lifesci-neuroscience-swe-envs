# Build notes - Neuroscience & Neuroimaging

Repo2RLEnv pr_diff (v0.9.6), Harbor-format output. Domain: neuroscience.

## Funnel

| Stage | Count |
|---|---|
| Domain candidates (deduped) | 632 |
| After active+star+noise filter | 310 |
| Final target list (cap 200) | 200 |
| Repos with >=1 task | 161 |
| Repos with 0 tasks | 39 |
| Total tasks (<=3/repo) | 360 |

## Generation

- Shared task bank: each repo generated once (pr_diff, limit=3,
  max_files_per_pr=15, min_loc_changed=2), 4-way concurrency,
  rate-limit-aware with .done checkpoint resume.
- Per-domain consolidation: rank by reward_calibration.loc_changed desc,
  keep at most 3 tasks per repo. Zero-yield repos are NOT fabricated.
- Overlap with the 3 published cancer editions is allowed (user decision);
  domains are independent selections, not a partition of one repo set.

## Zero-yield repos (informational, 39)

- tirthajyoti/Data-science-best-resources
- drop-ice/dear-github-2.0
- svaksha/Julia.jl
- offchan42/machine-learning-curriculum
- YichiZhang98/SAM4MIS
- bitzhangcy/Neural-PDE-Solver
- hollobit/GenAI_LLM_timeline
- open-thought/system-2-research
- brainpy/BrainPy
- mikeroyal/Neuromorphic-Computing-Guide
- wyg5208/nct
- SpikingChen/SNN-Daily-Arxiv
- zhao62/Deep-Residual-Shrinkage-Networks
- ankitshah009/all-about-ai-residency
- CHAOZHAO-1/DG-PHM
- snailpt/CTNet
- facebookresearch/algonauts-2025
- ucdavis/erplab
- BlueBrain/Brayns
- SpikeCalls/FlyDrones
- neurolabusc/AppleSiliconForNeuroimaging
- MouseLand/rastermap
- Garinmckayl/anima
- SuperBruceJia/EEG-Motor-Imagery-Classification-CNNs-TensorFlow
- Frankweb33/flybrain-robot-bridge
- ZitongLu1996/NeuroRA
- slinderman/stats320
- spm/spm12
- mne-tools/mne-cpp
- RonanB96/Low-Cost-EEG-Based-BCI
- arjunmnath/books
- Aharoni-Lab/Miniscope-v4
- m-beau/NeuroPyxels
- canlab/Neuroimaging_Pattern_Masks
- ozcelikfu/brain-diffuser
- gongzix/NeuroClips
- MIC-DKFZ/MITK-Diffusion
- paulcbogdan/NiChord
- mickcrosse/mTRF-Toolbox

## Caveats

- Static validation only; real grading needs an LLM judge key + Docker.
- inline_dockerfile mode skips image build (no Docker daemon needed to publish).
- Provider secrets in upstream diffs were redacted before public release.
