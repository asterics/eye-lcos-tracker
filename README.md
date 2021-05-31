# Eye-LCOS-Tracker

The Eye-LCOS-Tracker (Eye-Low Cost Open Source-Tracker) repository is an initiative to create a low-cost open source eye tracker with consumer hardware. As many eye tracker on the market are either closed source or very expensive (starting with €1.500,-), the goal of this initiative is to provide an alternative.

## Requirements

The eye tracker should fulfill the following requirements:

* **Open Source**: All elements of the tracking device (hardware, firmware, neural models, software, ...) should be open source to ensure community commitment and prevent vendor-lock-in.
* **Low Cost**: The overall cost of the tracker should be **below €300,-**
* **Off-the-shelf availability**: The eye tracker should be a plug and play device and be purchasable by the end users globally. If not available as one device there should only be a few mainstream components and a simple DIY tutorial.
* **Use Cases**: The main use case is computer control for people with disabilities. It is not intended to be used for user experience evaluation, which needed higher frame rates and the support for eye saccade detection.
* **Tracking quality**: 
    * A tracking accuracy of up to 3°? is aimed for. The lower the better.
    * Good tracking results should be achieved with and without eye glasses
    * Good stability against head movmeents and head orientation (-> high FOV, probably 2 cameras)
    * FPS: not critical, 60-100Hz desired
 

## Eye-Tracking Essentials

Soe importnat term like bright/dark pupil center corneal reflection techniques, 3D-eye model calibration, timing/accuracy/precision are explained here:

* [Eye Tracking essentials overview by Tobii](https://www.tobiipro.com/learn-and-support/learn/eye-tracking-essentials/)

## Hardware

The [OAK-1 and OAK-D](https://shop.luxonis.com/) devices are open source devices supporting spatial AI on device (edge) processing. The [Intel&reg; Movidius&trade; Myriad X&trade; (VPU)](https://www.intel.com/content/www/us/en/design/products-and-solutions/processors-and-chipsets/movidius-myriad-x-vision-processing-unit/technical-library.html?grouping=EMT_Content%20Type&sort=title:asc&filter=rdctopics:internetofthingsiot) is used to do embedded neural inference. Additionally, there are many open hardware design variants that make it easy to start with a baseboard and add another camera module or interfacing component.

### Luxonis Hardware

* [Luxonis Open Hardware designs](https://github.com/luxonis/depthai-hardware)

### Camera Options

* [Raspberry Pi HQ Cam Adapter Board](https://github.com/luxonis/depthai-hardware/tree/master/BW0253_R0M0E0_RPIHQ_ADAPTER)
* [How do I get different field of view or lenses for depthai?](https://docs.luxonis.com/en/latest/pages/faq/#how-do-i-get-different-field-of-view-or-lenses-for-depthai-and-megaai)
* [ArduCam lenses and sets](https://www.arducam.com/docs/lens/lens-products-from-arducam/)
  * [12MP IMX477 IR-CUT Camera Module](https://www.arducam.com/docs/cameras-for-raspberry-pi/native-raspberry-pi-cameras/12mp-imx477-camera-2/)
  * [Pi camera modules with Switchable IR-cut Filter](https://www.arducam.com/docs/cameras-for-raspberry-pi/noir-camera/)

### Other Embedded Vision HW

* [JeVois Smart Machine Vision Camera](http://jevois.org/)

## Approaches

The VPU is primarily intended for neural inference processing of machine learning models. The [OpenVINO toolkit](https://docs.openvinotoolkit.org/latest/index.html) is an Intel&reg; abstraction toolkit to convert from different source models and to optimize the model for the VPU processing.

### Machine Learning

The VPU is able to process several machine learning models serially or in parallel.

The [OAK gaze estimation](https://github.com/luxonis/depthai-experiments/tree/master/gen2-gaze-estimation) example demonstrates the processing of face detection, landmark detection, head pose estimation and gaze estimation models.

![Gaze estimation pipeline using several machine learning models](https://raw.githubusercontent.com/LCTyrell/Gaze_estimation/master/results/graph.jpeg)

There is also a fork with [integrated relativ mouse control by gaze movement](https://github.com/deinhofer/depthai-experiments/tree/madinaustria/gaze-estimation-mouse-controller/gen2-gaze-estimation).
The projects are based on the [OpenVINO-compatible version of LCTyrell](https://github.com/LCTyrell/Gaze_estimation).

Other interesting ML models

* [Mediapipe Iris landmark detection](https://google.github.io/mediapipe/solutions/iris.html)
* [MRL Eye Data Set](http://mrl.cs.vsb.cz/eyedataset): This dataset contains infrared images in low and high resolution, all captured in various lightning conditions and by different devices.
* [OpenFace](https://github.com/TadasBaltrusaitis/OpenFace): OpenFace is a C++ implementation for facial behaviourial tracking with a webcam using a combination of CV algorithms and machine learning models. It contains some interesting source code, models and publications, e.g.
  * Eye Gaze Synthesizing: [Rendering of Eyes for Eye-Shape Registration and Gaze Estimation](https://arxiv.org/pdf/1505.05916.pdf)

### Embedded CV processing

In cases where the CV algorithm is not based on machine-learning it would be beneficial to execute traditional OpenCV operations on device in order to keep the host CPU free for user operations. The [OpenCL support for the Myriad X VPU](https://docs.openvinotoolkit.org/latest/openvino_docs_IE_DG_Extensibility_DG_VPU_Kernel.html) is currently in preview mode. The OpenCL support is also planned to be integrated into the depthai python API for the OAK devices.
But the depthai already supports or will support some [basic hardware accelerated pre-processing and filtering operations (e.g. edge detection, object tracking, motion estimation, ...)](https://docs.luxonis.com/en/latest/pages/faq/#what-hardware-accelerated-capabilities-exist-in-depthai-and-or-megaai)

Further information can be found in the FAQs:
  * [Can DepthAI run custom CV code?](https://docs.luxonis.com/en/latest/pages/faq/#can-depthai-run-custom-cv-code-say-cv-code-from-pytorch)

### ML Frameworks for Vectorized Math

Another approach to execute traditional computer vision operations is to embed computer vision operations into a machine learning model and add it to the inference pipeline of the VPU. See the example of rahulrav to [convert an RGB video into a gray-scale video](https://rahulrav.com/blog/depthai_camera.html) using pytorch.

### CPython Embedded Scripting

The OAK devices have [CPython support](https://docs.luxonis.com/en/latest/pages/faq/#use-case-3-using-depthai-as-the-only-processor-on-a-device) (currently alpha stage) in order to communicate with the HW components and for being able to add embedded processing of application logic.

### Active Appearance Model

TODO: Add eye tracking algorithms using Active Appearance Models

## Interesting Papers

* [RemoteEye: an open source high speed remote eyetracker](https://www.hci.uni-tuebingen.de/assets/pdf/publications/hospBRM2020RemoteEye.pdf)
* [Towards accurate eye tracker calibration – methods and procedures](https://www.researchgate.net/publication/275541615_Towards_Accurate_Eye_Tracker_Calibration_-_Methods_and_Procedures)
* [Guidelines for the eye tracker calibration using points of regard](https://www.researchgate.net/publication/262932778_Guidelines_for_eye_tracker_calibration_using_points_of_regard)
* [Rendering of Eyes for Eye-Shape Registration and Gaze Estimation](https://arxiv.org/pdf/1505.05916.pdf): Data augmentation by eye gaze synthesizing.
* [Automatic facial expression analysis](publications/Automatic_facial_expression_analysis.pdf)
* [TAUPE: Visualizing and analyzing eye-tracking data](https://www.researchgate.net/publication/259118316_TAUPE_Visualizing_and_analyzing_eye-tracking_data)
* [Papers with code](https://paperswithcode.com/methods) e.g. [fast MSER algorithm](https://paperswithcode.com/paper/fast-mser)

### Human Computer Interaction

Quantitative evaluation and comparison of the efficiency of input devices can be done using Fitt's law and comforming to the ISO 9241-9. The **throughput metric** is a good metric for the comparison of input devices. See the publication [FittsFace: Exploring Navigation and Selection Methods for Facial Tracking](http://www.yorku.ca/mack/hcii2017.html) as an example.

Further resources:
* [Web page of Scott MacKenzie](http://www.yorku.ca/mack/) with many HCI publications about Fitt's law and input device validation.
* The [tool GoFitts](http://www.yorku.ca/mack/FittsLawSoftware/) can be used to perform input device validation experiments including statistical analyzes. Simply download and start GoFitts.jar.
* [Other Software for Validation Experiments](http://www.yorku.ca/mack/ExperimentSoftware/)

#### Interesting papers

* [A Fitts’ Law Evaluation of Hands-Freeand Hands-On Input on a Laptop Computer](http://www.yorku.ca/mack/hcii2019.pdf)
* [Pointing by Gaze, Head, and Foot in a Head-Mounted Display](http://www.yorku.ca/mack/etra2019a.pdf)
* [TAGSwipe: Touch Assisted Gaze Swipe for Text Entry](http://www.yorku.ca/mack/chi2020a.html)
* [Comparison of Two Methods to Control the Mouse Using a Keypad](http://www.yorku.ca/mack/icchp2016b.pdf)
