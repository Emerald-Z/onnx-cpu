# `onnx-cpu` modular resource

This module allows you to run ONNX models with the Viam Vision Service on Android.

## Requirements

Before configuring your ML model, you must [create a machine](https://docs.viam.com/fleet/machines/#add-a-new-machine).

## Build and run

To use this module, follow these instructions to [add a module from the Viam Registry](https://docs.viam.com/registry/configure/#add-a-modular-resource-from-the-viam-registry) and select the `viam-labs:mlmodel:onnx-cpu` model from the [`onnx-cpu` module](https://github.com/viam-labs/onnx-cpu).

## Configure your `onnx-cpu` sensor

Navigate to the **Config** tab of your machine's page in [the Viam app](https://app.viam.com/).
Click on the **Services** subtab and click **Create service**.
Select the `mlmodel` type, then select the `onnx-cpu:timesyncsensor` model.
Click **Add module**, then enter a name for your ML model and click **Create**.

On the new component panel, copy and paste the following attribute template into your ML model’s **Attributes** box:

```json
{
  "model_path": "/path/to/onnx_file/detector_googlenet.onnx",
  "label_path": "/path/to/labels.txt"
}
```

> [!NOTE]
> For more information, see [Configure a Machine](https://docs.viam.com/manage/configuration/).

### Attributes

The following attributes are available for the `viam-labs:mlmodel:onnx-cpu` ML model service:

| Name    | Type   | Inclusion    | Description |
| ------- | ------ | ------------ | ----------- |
| `model_path` | string | **Required** | The full path to the ONNX file. |
| `label_path` | string | Optional | The full path to the names of the category labels. |

### Example configuration

```json
    {
      "name": "onnx",
      "type": "mlmodel",
      "model": "viam-labs:mlmodel:onnx-cpu",
      "attributes": {
        "model_path": "/path/to/onnx_file/detector_googlenet.onnx"
        "label_path": "/path/to/labels.txt"
      }
    }
```

### Configure a vision service

Navigate to the **Config** tab of your machine's page in [the Viam app](https://app.viam.com/).
Click on the **Services** subtab and click **Create service**.
Select the `vision` type, then select the `mlmodel` model.
Enter a name for your ML model and click **Create**.

On the new service panel, select the `onnx-cpu` model you configured.

You will most likely need to [rename the input and outputs tensors](https://docs.viam.com/ml/vision/mlmodel/#tensor-names) coming from the ONNX file to use the tensor names that the vision service requires. To rename the tensors, go to your Raw JSON configuration and add the `remap_output_names` and `remap_input_names` fields to the attributes of your vision service config. For more information see [the documentation for remapping tensor names](https://docs.viam.com/ml/vision/mlmodel/#tensor-names).

Here is an example:

```json
   {
      "type": "vision",
      "model": "mlmodel",
      "attributes": {
        "mlmodel_name": "onnx",
        "remap_output_names": {
          "detection_classes": "category",
          "detection_boxes": "location",
          "detection_scores": "score"
        },
        "remap_input_names": {
          "input_tensor": "image"
        }
      },
      "name": "onnx-vision"
    }
```

Save your config, before testing your vision service.

### Next steps

You have now configured a vision service to use an ONNX ML model.
Follow these steps to [test your `mlmodel` vision service](https://docs.viam.com/ml/vision/mlmodel/#test-your-detector-or-classifier).

## Local Development

This module is written in Go. If you need to package it up into a binary to create a module, run the following commands:

```bash
go build -o module cmd/module/main.go
chmod a+x module
tar -czf module.tar.gz module third_party/
```

`third_party` contains all of the `.so` files for the ONNX runntime. You can package only the one you need.

For Android, the Makefile will create the tar file.

You can then add the module to app.viam.com locally. the model triplet is `viam-labs:mlmodel:onnx-cpu`.

## License

Copyright 2021-2023 Viam Inc. <br>
Apache 2.0
