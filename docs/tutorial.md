# Tutorial

## Introduction

This tutorial will ...

The simulated use-case is ...

The different roles/users are  ...

While going through the simulated use-case from A to Z, the tutorial will be a mix of conceptual talks and hands-on execution of commands.

## Experiment Inception phase

### Experiment Documentation

Document the experiment goals, timelines, expected outcomes, ...

### Cohort Selection

Identify the needed data characteristics, write down instructions for hospitals to "onboard" this data.
    - by the way... MedPerf is aiming to facilitate this step for the clinicians, e.g. integration of common clinical data storage frameworks with MedPerf, like XNAT and PACS

Note that in our tutorial, the given data (run `ls workspace/data`) is considered in the stage after cohort selection.

### Call for Participation

spread out the experiment website and call for hospitals to be prepared to join. Prepare data agreements...
    - note that medperf is aiming to facilitate streamlining "business" agreements... (TODO: find in paper)

### Define and Prepare the Workflow Pieces

- Prepare the data preparation pipeline logic that will transform the raw clinical data into AI-ready data. This will be an MLCube.
  - Here is the `mlcube.yaml` and (some brief description of what the mlcube will do)

- Prepare the metrics calculation logic that will be used for the evaluation and the benchmarking of multiple trained models on unseen data. This will basically define what the experiment owner wants to measure and compare between models. This will be an MLCube.
  - Here is the `mlcube.yaml` and (some brief description of what the mlcube will do)

#### Training Experiment Definition using OpenFL and GaNDLF

- Prepare the training logic using OpenFL and GaNDLF:
  - Here is the `mlcube.yaml`:
  - Here we will demonstrate how to take an existing [GaNDLF model configuration](https://mlcommons.github.io/GaNDLF/getting_started/) (e.g., for segmentation) and embed this within the OpenFL Federated Learning plan (FL plan) along with the Python* code that defines the model and the data loader. The FL plan is a YAML file that defines the collaborators, aggregator, connections, models, data, and any other parameters that describe the federated training experiment.

    The FL plan is described by the *plan.yaml* file is located in the plan directory of the OpenFL workspace. OpenFL aims to make it as easy as possible to take an existing GaNDLF experiment and make it run across a federation.

    Each YAML top-level section contains the following subsections:

    - `template`: The name of the class including top-level packages names. An instance of this class is created when the plan gets initialized.
    - `settings`: The arguments that are passed to the class constructor.
    - `defaults`: The file that contains default settings for this subsection. Any setting from defaults file can be overridden in the **plan.yaml** file.

    The following is an example of the GaNDLF Segmentation Test **plan.yaml**. Notice the **task_runner/settings/gandlf_config** block where the GaNDLF configuration file is embedded:
    ```    
    aggregator :
      defaults : plan/defaults/aggregator.yaml
      template : openfl.component.Aggregator
      settings :
        init_state_path : save/fets_seg_test_init.pbuf
        best_state_path : save/fets_seg_test_best.pbuf
        last_state_path : save/fets_seg_test_last.pbuf
        rounds_to_train : 3
        write_logs : true
    
    
    collaborator :
      defaults : plan/defaults/collaborator.yaml
      template : openfl.component.Collaborator
      settings :
        delta_updates    : false
        opt_treatment    : RESET
    
    data_loader :
      defaults : plan/defaults/data_loader.yaml
      template : openfl.federated.data.loader_gandlf.GaNDLFDataLoaderWrapper
      settings :
        feature_shape : [32, 32, 32]
    
    task_runner :
      template : openfl.federated.task.runner_gandlf.GaNDLFTaskRunner
      settings :
        train_csv           : seg_test_train.csv
        val_csv             : seg_test_val.csv
        device              : cpu
        gandlf_config  :
          batch_size: 1
          clip_grad: null
          clip_mode: null
          data_augmentation: {}
          data_postprocessing: {}
          data_preprocessing:
            normalize: null
          enable_padding: false
          in_memory: true
          inference_mechanism  :
            grid_aggregator_overlap: crop
            patch_overlap: 0
          learning_rate: 0.001
          loss_function: dc
          medcam_enabled: false
          output_dir: '.'
          metrics:
          - dice
          model:
            amp: true
            architecture: unet        
            base_filters: 32
            batch_norm: false
            class_list:
            - 0
            - 1
            dimension: 3
            final_layer: sigmoid
            ignore_label_validation: null
            norm_type: instance
            num_channels: 1
          nested_training:
            testing: -5
            validation: -5
          num_epochs: 1
          optimizer:
            type: adam
          parallel_compute_command: ''
          patch_sampler: uniform
          patch_size:
          - 32
          - 32
          - 32
          patience: 1
          pin_memory_dataloader: false
          print_rgb_label_warning: true
          q_max_length: 1
          q_num_workers: 0
          q_samples_per_volume: 1
          q_verbose: false
          save_output: false
          save_training: false
          scaling_factor: 1
          scheduler:
            type: triangle
          track_memory_usage: false
          verbose: false
          version:
            maximum: 0.0.14
            minimum: 0.0.13
          weighted_loss: true
    
    
    network :
      defaults : plan/defaults/network.yaml
    
    assigner :
      defaults : plan/defaults/assigner.yaml
    
    tasks :
      aggregated_model_validation:
        function : validate
        kwargs   :
          apply   : global
          metrics :
            - valid_loss
            - valid_dice
        
      locally_tuned_model_validation:
        function  : validate
        kwargs    :
          apply: local
          metrics :
            - valid_loss
            - valid_dice
        
      train:
        function : train
        kwargs   :
          metrics     :
          - loss
          - train_dice
          epochs : 1
    
    
    compression_pipeline :
      defaults : plan/defaults/compression_pipeline.yaml
    ```

    To use a a new GaNDLF configuration, just run the following OpenFL CLI command:

     ```
     fx plan initialize --gandlf_config ${PATH_TO_GANDLF_CONFIG}.yaml
     ```
  - Here is the used GaNDLF config:

## Admin Setup

The MedPerf server should be up and running.

As we described previously in the presentation, here is the architecture:

"visual showing auth backend, api server, and client"

For the tutorial, we will be using a local MedPerf server and a local mocked auth provider. The MedPerf client installed in your virtual machines is preconfigured to communicate with the local server.

Check this by running `medperf profile view`. You will see `server: https://localhost:8000` and the auth configuration used is `Local`. In a real scenario, configuring the client would be only by changing the server and the auth configuration...

## MedPerf Client Installation and Authentication

All involved parties that intend to use the MedPerf client will have to signup for a MedPerf account, install the client, and login prior to using it.

For our tutorial, we already setup the virtual machines with MedPerf preinstalled, and already created multiple profiles that each will correspond to a user. Through the tutorial, we will switch between users...
Run `medperf profile ls` to view...

## Training Setup with MedPerf (Experiment Owner)

Now we will start with defining the training experiment and registering it in the MedPerf server:

Register the data preparation MLCube

Register the training experiment
    - The server admin should approve the experiment (Run this hack: <...> to continue the tutorial)

Register an aggregator

Associate the aggregator with the experiment
    - the reason is that the aggregator could be a different user / different machine
    - During this, aggregator certificate is created and signed

## Data preparation (Training Data Owner)

Process your data using the data prep mlcube

Register your dataset

Request participation in the training experiment
    - during this, a certificate signing request is issued

## Accepting Training Participation (Experiment Owner)

Accept participation requests
    - During this, datasets certificates is signed
        - we note here that the current design is certificate per dataset, but we might change the design for scalability

"Lock" the experiment

Start the aggregator

## Run Training (Training Data Owner)

Start training

## Inference Setup with MedPerf (Experiment Owner)

Export the trained model to an MLCube using GaNDLF

Register a benchmark (i.e. an inference experiment) whose components are the the data prep mlcube, the metrics mlcube, and the trained model mlcube
    - The server admin should approve the benchmark

## Participate as an algorithm owner (Some algorithm Owner)

Register your model

Request participation in the benchmark

## Participate as a data owner (Inference data Owner)

Process your data using the data prep mlcube

Register your dataset

Request participation in the benchmark

## Accepting Inference Participation (Experiment Owner)

Accept inference participation requests (from the algorithm owners and data owners)

## Run Inference (Inference Data Owner)

Start benchmark execution
    - models associated with the benchmark, including the reference model, will be executed on the data

Submit inference results

## Result collection (Experiment Owner)

Pull and view inference results from the medperf server
