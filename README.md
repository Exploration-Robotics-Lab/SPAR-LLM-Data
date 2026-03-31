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

Latest: 4x rudder_stuck NIM API trials added March 28, 2026 -- nemotron-3-super-120b-a12b via integrate.api.nvidia.com

700+ labeled trials generated across varying:
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
  rudder_stuck_nim_nemotron120b/
    trial_001/                          -- 20260328_003116 api_nvidia
    trial_002/                          -- 20260328_004938 api_nvidia
    trial_003/                          -- 20260328_012515 api_nvidia
    trial_004/                          -- 20260328_014221 api_nvidia
      experiment_config.json            -- fault params, LLM provider, model, prompt tier
      session_summary.json              -- outcome PASS/FAIL, tokens, llm_provider: nvidia_nim
      telemetry_et.csv                  -- 38-column 1Hz telemetry
      fault_trajectory.csv              -- fault phase markers
      evaluation.txt                    -- evaluator decision and reasoning
      llm_prompt.txt / llm_response.txt -- full NIM prompt and raw response
      trajectory_plot.png               -- 3D/top/side trajectory
      swim_lane_timeline.png            -- behavior state timeline
      battery_plots_and_data/           -- SOC, voltage, current, power
      VTK views/                        -- 3D rendered views
  rudder_stuck_baseline/
    trial_001/
  docking_depth_sensor_bias/
    trial_001/
missions/
  docking_wp_arc_ramp_5_20260323_101836.mission  -- docking approach mission

## Citation

K. Halba and J. G. Bellingham, "A Simulation Platform for AUV Fault Recovery:
Exploring LLM-Based Planning Strategies," IEEE AUV 2026, Southampton, UK -- ACCEPTED.

## License

MIT License. Generated in-house at JHU Institute for Assured Autonomy, Baltimore MD.
No confidential or personal information. No NDA required.
