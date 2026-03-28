# SPAR-LLM-Data

Exploration Robotics Laboratory, Johns Hopkins University
Institute for Assured Autonomy (JHU IAA), Baltimore, MD
NVIDIA NIM-powered AUV fault-recovery dataset

PI: James G. Bellingham (Bloomberg Distinguished Professor, Executive Director JHU IAA)
Platform Developer: Khalid Halba (Associate Research Scientist, JHU IAA)

## How NVIDIA NIM is used

Every trial uses two NVIDIA NIM model calls via integrate.api.nvidia.com:

1. Recovery mission generation: nvidia/nemotron-3-super-120b-a12b receives a structured
   fault report (fault type, vehicle state, physics context, mission constraints) and
   generates a .mission file with the recovery behavior, strategy, and parameters.

2. Post-run multimodal analysis: meta/llama-3.2-90b-vision-instruct receives all
   telemetry tab data plus the battery plot PNG (SOC/voltage/current/power) and produces
   a written critique of the recovery strategy, flags near-misses, and suggests parameter
   improvements. Both text and image inputs are used in the same API call.

This two-model NIM stack replaces the previous OpenAI GPT pipeline. All trial metadata
records llm_provider: nvidia_nim in session_summary.json.

The dataset is used for fine-tuning and training compact onboard edge models
(nvidia/nvidia-nemotron-nano-9b-v2) for deployment on NVIDIA Jetson AGX Orin
within the AUV power and compute envelope (60W, 275 TOPS, 64GB).

## Dataset scope

Hundreds of runs generated across varying:
- Fault types: rudder stuck, depth sensor bias, thruster dead, elevator stuck,
  battery cell failure, USBL dropout, and 6 docking-specific faults:
  USBL bearing bias, compass offset, intermittent rudder dropout,
  accidental drop-weight release, bottom-collision buoyancy change, fin loss
- Fault trigger times (injected at different mission phases)
- Fault parameter magnitudes (rudder angle, sensor bias in meters, etc.)
- Prompt tiers: FULL (physics + strategy + params), GUIDED, CHALLENGE (minimal)
- Recovery strategies: drop-weight ascent, active spiral, setpoint hold, surface abort
- Environmental conditions: island present vs. absent, different bathymetry depths

## Repository structure

experiments/
  rudder_stuck_baseline/
    trial_001/
      experiment_config.json    -- fault params, LLM provider, model, prompt tier
      session_summary.json      -- outcome PASS/FAIL, tokens, llm_provider: nvidia_nim
      telemetry_et.csv          -- 38-column 1Hz (position, depth, actuators, battery, USBL)
      fault_trajectory.csv      -- fault phase markers aligned to telemetry
      evaluation.txt            -- evaluator decision and reasoning
      ai_analysis.txt           -- NVIDIA NIM Llama 90B Vision strategy critique
      trajectory_plot.png       -- 3D/top/side trajectory with fault/recovery colors
      swim_lane_timeline.png    -- behavior state timeline
      battery_plots_and_data/   -- SOC, voltage, current, power subplots
  docking_depth_sensor_bias/
    trial_001/
      (same structure)
docs/
  DATA_FORMAT.md                -- column definitions for telemetry_et.csv

## Citation

K. Halba and J. G. Bellingham, "A Simulation Platform for AUV Fault Recovery:
Exploring LLM-Based Planning Strategies," IEEE AUV 2026, Southampton, UK -- ACCEPTED.

## License

MIT License. Generated in-house at JHU Institute for Assured Autonomy, Baltimore MD.
No confidential or personal information. No NDA required.
