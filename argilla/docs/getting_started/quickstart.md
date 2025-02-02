---
description: Quickstart of Argilla on how to create your first dataset.
---

# Quickstart

This guide provides a quick overview of the Argilla SDK and how to create your first dataset.

## Setting up your Argilla project

### Install the SDK with pip

To work with Argilla datasets, you need to use the Argilla SDK. You can install the SDK with pip as follows:

```console
pip install argilla --pre
```

### Run the Argilla server

If you have already deployed Argilla Server, you can skip this step. Otherwise, you can quickly deploy it in two different ways:

* Remotely using a [HF Space](https://huggingface.co/new-space?template=argilla/argilla-template-space).

!!! note
    As this is a release candidate version, you'll need to manually change the version in the HF Space Files > Dockerfile to `argilla/argilla-quickstart:v2.0.0rc2`.

* Locally using Docker.

```console
docker run -d --name quickstart -p 6900:6900 argilla/argilla-quickstart:v2.0.0rc2
```

### Connect to the Argilla server

Get your `<api_url>`:

* If you are using Hugging Face Spaces, the URL should be constructed as follows: `https://[your-owner-name]-[your_space_name].hf.space`
* If you are using Docker, the URL is the URL shown in your browser (by default `http://localhost:6900`)

Get your `<api_key>` in `My Settings` in the Argilla UI (by default `owner.apikey`).

!!! note
    Make sure to replace `<api_url>` and `<api_key>` with your actual values. If you are using a private Hugging Face Space, you need to specify your `HF_TOKEN` which can be found [here](https://huggingface.co/settings/tokens).

```python
import argilla as rg

client = rg.Argilla(
    api_url="<api_url>",
    api_key="<api_key>"
    # headers={"Authorization": f"Bearer {HF_TOKEN}"}
)
```

## Create your first dataset

To create a dataset with a simple text classification task, first, you need to define the dataset settings.

```python
settings = rg.Settings(
    guidelines="Classify the reviews as positive or negative.",
    fields=[
        rg.TextField(
            name="review",
            title="Text from the review",
            use_markdown=False,
        ),
    ],
    questions=[
        rg.LabelQuestion(
            name="my_label",
            title="In which category does this article fit?",
            labels=["positive", "negative"],
        )
    ],
)
```

Now you can create the dataset with the settings you defined. Publish the dataset to make it available in the UI and add the records.

!!! note
    The `workspace` parameter is optional. If you don't specify it, the dataset will be created in the default workspace `admin`.

```python
dataset = rg.Dataset(
    name=f"my_first_dataset",
    settings=settings,
    client=client,
)
dataset.create()
```

## Add records to your dataset

Retrieve the data to be added to the dataset. We will use the IMDB dataset from the Hugging Face Datasets library.

```python
pip install -qqq datasets
```

```python
from datasets import load_dataset

data = load_dataset("imdb", split="train[:100]").to_list()
```

Now you can add the data to your dataset. Use a `mapping` to indicate which keys/columns in the source data correspond to the Argilla dataset fields.

```python
dataset.records.log(records=data, mapping={"text": "review"})
```

🎉 You have successfully created your first dataset with Argilla. You can now access it in the Argilla UI and start annotating the records.

## More references

* [Installation guide](installation.md)
* [How-to guides](../how_to_guides/index.md)
* [API reference](../reference//argilla/client.md)
