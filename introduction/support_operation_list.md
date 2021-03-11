# 算子支持

Tengine共支持99个算子；Tengine的Serializer只加入了88个；在operator文件夹下有98个（无NUM）


| Tengine              | Caffe                | MXNet                 | TensorFlow            | TF-Lite                      | ONNX              |
| -------------------- | -------------------- | --------------------- | --------------------- | ---------------------------- | ----------------- |
| Accuracy             | √                    |                       |                       |                              |                   |
| BatchNormalization   | BatchNorm            | BatchNorm             | FusedBatchNorm        |                              | √                 |
|                      |                      |                       | ComposedBN            |                              |                   |
| Resize               |                      |                       |                       | RESIZE_NEAREST_NEIGHBOR      |                   |
| Concat               | √                    | √                     | ConcatV2              | CONCATENATION                | √                 |
| Const                |                      |                       |                       |                              |                   |
| Convolution          | √                    | √                     | Conv2D                | CONV_2D                      | Conv              |
|                      | DepthwiseConvolution |                       | DepthwiseConv2dNative | DEPTHWISE_CONV_2D            |                   |
|                      | ConvolutionDepthwise |                       |                       |                              |                   |
| Deconvolution        | √                    | √                     | Conv2DBackpropInput   |                              |                   |
| DetectionOutput      | √                    |                       |                       |                              |                   |
| Dropout              | √                    | Copy                  | √                     |                              | √                 |
| Eltwise              | √                    | _minus_scalar         | Add                   | ADD                          | Add               |
