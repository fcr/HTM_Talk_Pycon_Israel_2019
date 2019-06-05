## How to Run

To run and output data to a local file:

    ./run.py

To run and output data to a **matplotlib** graph:

    ./run.py --plot

> You must have **matplotlib** properly installed for this option to work.

## Program Description

This example code assumes a swarm has already been run against the input data (see [One Hot Gym Prediction Tutorial](../../prediction/one_gym/README.md) for details). Model parameters therefore already exist in the `model_params` directory, and the only step to run this tutorial is to simply execute `./run.py`.

## What's Different?

You might be wondering how to convert your prediction model into an anomaly model. This is quite simple. For this example, the only thing necessary was to change the `inferenceType` within the model parameters from `TemporalMultistep` to `TemporalAnomaly`. After this change, model results will contain an `anomalyScore` value in addition to the multi-step inferences.

## Anomaly Likelihood

This sample code includes usage of the `anomaly_likelihood` module, which is a post-processor of the model results. This is used to include an "anomaly likelihood" in addition to NuPIC's "anomaly score". For more information on this, see [Subutai's talk on Anomaly Detection in the CLA](https://www.youtube.com/watch?v=nVCKjZWYavM). Example usage below.

```
from nupic.algorithms import anomaly_likelihood

...

result = model.run({
  "timestamp": timestamp,
  "kw_energy_consumption": consumption
})
anomalyScore = result.inferences["anomalyScore"]
anomalyLikelihood = anomaly_likelihood.AnomalyLikelihood()
likelihood = anomalyLikelihood.anomalyProbability(
  consumption, anomalyScore, timestamp
)

```

## How is the Output Handled?

Most of the code changes for this tutorial were done outside of instantiating and running an OPF HTMPredictionModel. For this example, the `nupic_output.py` file was replaced with `nupic_anomaly_output.py`, which properly handles the additional anomaly data being produced by the model. For file output (sans `--plot` option), the "anomaly score" and "anomaly likelihood" values are simply written to the CSV file as additional columns. For the matplotlib `--plot` option, it plots the "anomaly score" and "anomaly likelihood" values in a separate chart aligned with the energy consumption predictions.

### The Chart Explained

The chart produced with the `--plot` option contains yellow highlights on weekends in the top chart. Regions in the bottom anomaly chart where anomaly likelihood is above 90% are also highlighted in red. The 90% threshold is low enough that it may cause false positive anomaly indications. You may want to tune this number as you wish.
