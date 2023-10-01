# Tutorial

## Introduction

This tutorial will ...

The simulated use-case is ...

The different roles/users are  ...

While going through the simulated use-case from A to Z, the tutorial will be a mix of conceptual talks and hands-on execution of commands...

## Admin-level Setup (THIS SHOULD GO TO THE PRESENTATION?)

As we described in the presentation, here is the architecture:

"visual showing auth backend, api server, and client"

Already hosted are the main medperf server and the main medperf auth provider (auth0) managed by the MLCommons organization.

But an admin (e.g. an organization staff) can host their medperf server and configure an auth provider...

For the tutorial, we will be using a local MedPerf server and a local mocked auth provider.

## Experiment Inception phase

Document the experiment goals, timelines, expected outcomes, ...

Cohort selection: Identify the needed data characteristics, write down instructions for hospitals to "onboard" this data.
    - by the way... MedPerf is aiming to facilitate this step for the clinicians, e.g. integration of common clinical data storage frameworks with MedPerf, like XNAT PACS

call for participation: spread out the experiment website and call for hospitals to be prepared to join. Prepare data agreements...
    - note that medperf is aiming to facilitate streamlining data agreements...
    - note that some hospitals will join to provide data for federated training, some for federated evaluation.

Prepare the data preparation pipeline logic (mlcube-it) that will transform the raw clinical data into AI-ready data.

Prepare the metrics calculation logic (mlcube-it) that will be used for evaluation (after training) and benchmarking multiple trained models. This will basically define what does the experiment owner wants to measure and compare between models...

### Experiment Definition using OpenFL and GaNDLF

Prepare the training logic using OpenFL and GaNDLF:
    - Here, some explanation and description of the OpenFL-GaNDLF MLCube. (THIS SHOULD GO TO THE PRESENTATION?)
    - We should note that medperf is not only tied with OpenFL and GaNDLF. (THIS SHOULD GO TO THE PRESENTATION?)
    - Talk about the used gandlf and openfl config.

## Training Setup with MedPerf (Experiment Owner)

MedPerf signup, install client, login

Register the data preparation MLCube

Register the training experiment
    - The server admin should approve the experiment

Register an aggregator

Associate the aggregator with the experiment
    - the reason is that the aggregator could be a different user / different machine
    - During this, aggregator certificate is created and signed

## Data preparation (Training Data Owner)

MedPerf signup, install client, login

Process your data using the data prep mlcube

Register your dataset

Request participation in the training experiment
    - during this, a certificate signing request is issued

## Accepting Training Participation (Experiment Owner)

Accept participation requests
    - During this, datasets certificates is signed
        - we note here that the current design is certificate per dataset, but we might change the design for scalability

Start the aggregator

## Run Training (Training Data Owner)

Start training

## Inference Setup with MedPerf (Experiment Owner)

Export the trained model to an MLCube using GaNDLF

Register a benchmark (i.e. an inference experiment) whose components are the the data prep mlcube, the metrics mlcube, and the trained model mlcube
    - The server admin should approve the benchmark

## Participate as an algorithm owner (Some algorithm Owner)

MedPerf signup, install client, login

Register your model

Request participation in the benchmark

## Participate as a data owner (Inference data Owner)

MedPerf signup, install client, login

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
