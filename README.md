# FishTally

FishTally is a comprehensive AI-driven tool tailored for marine biology, streamlining the fish counting process to support ecological surveying and real-time monitoring. Developed using state-of-the-art YOLOv8 and ByteTrack technology, FishTally excels in environments with occlusions or species movement, efficiently distinguishing and tracking various marine life forms. From static cameras to mobile ROVs, its adaptable threshold mechanisms — single line, multi line, and polygon — offer ecologists and researchers versatile applications, from species-specific tracking to expansive seabed mapping. Though requiring pre-loaded weights and pending tests on edge devices, FishTally is poised to revolutionize data collection for aquatic population dynamics, promising accuracy and minimizing common enumeration errors.

## Pipeline architecture

<div align="center">
   <img src="https://github.com/kluless13/fishtally/blob/main/Assets/pipeline_arch.png"  width="100%">
</div>

## Demo

#### Here is FishTally being used on a video of Yellow Tang.
<div align="center">
   <img src="https://github.com/kluless13/fishtally/blob/main/Assets/Tang-result.gif" width="49%"/>
   <img src="https://github.com/kluless13/fishtally/blob/main/Assets/tang-tracker%20(2).gif" width="49%"/>
</div>

#### An example for FishTally would be to use it to count invasive species, such as Lionfish.
<div align="center">
   <img src="https://github.com/kluless13/fishtally/blob/main/Assets/lionfish.gif">
</div>

#### Here is a test to check if the tool worked at a multi-species level:
<div align="center">
   <img src="https://github.com/kluless13/fishtally/blob/main/Assets/multiclass.gif">
</div>

#### FishTally goes beyond a single line threshold, it contains source code to adjust for more than one line, and keep a count for each line used.
<div align="center">
   <img src="https://github.com/kluless13/fishtally/blob/main/Assets/2lines.gif" width="49%"/>
   <img src="https://github.com/kluless13/fishtally/blob/main/Assets/3lines.gif" width="49%"/>
</div>

#### Polygon zones can also be introduced to check species count in a particular zone. More than one polygon (as many as the user inputs) can be used as well. 
<div align="center">
   <img src="https://github.com/kluless13/fishtally/blob/main/Assets/polygon.gif" width="49%"/>
  <img src="https://github.com/kluless13/fishtally/blob/main/Assets/multipoly.gif" width="49%"/> 
</div>

#### Distance from the camera via `dist.py`:

<div align="center">
   <img src="https://github.com/kluless13/fishtally/blob/test/Assets/lionfish_distance.gif" width="49%"/>
  <img src="https://github.com/kluless13/fishtally/blob/test/Assets/dam_dist.gif" width="49%"/> 
</div>

Snippets of videos have been used to demonstrate the theory into practice. A simple version of the FishTally tool has been created into a CLI tool which can be used with ease. The source code can be adjusted accordingly.

# Setup Guide

## Prerequisites

Ensure you have the following prerequisites installed on your system:

- Python 3.6 or higher
- Git
- Pip (Python package manager)

### Installation Steps
Follow along.

1. **Clone the Repository**

   First, clone the repository containing the `fishtally.py` tool and its associated files to your local machine.

   ```bash
   git clone https://github.com/kluless13/fishtally.git
   cd fishtally
   ```

2. **Install Requirements**

   The `fishtally.py` tool relies on ByteTrack for object tracking. Run the provided setup script to clone and set up ByteTrack.

   ```bash
   chmod +x setup.sh
   ./setup.sh
   ```

3. **Verification**

   To verify that the installation is successful, you can run a simple test command or check the versions of critical components like Python, Git, and Pip. (not important)

   ```bash
   python --version
   git --version
   pip --version
   ```
# Quick demo of FishTally on a video of Yellow Tang

**Follow the code, copy paste one after the other**

First we check the classes present in the model's weights, using the `list_classes.py` script.

```bash
python list_classes.py --model_weights ~/fishtally/Assets/multiclass-wts.pt
```

Now that we know we are after class ID '3', we can take a look at our video and check to see how to create tigger threshold boundaries, using the `frames.py` script.

Rename the image to whatever you want.

```bash
python frames.py --source_video ~/fishtally/Assets/tangtest.mp4 --save_path ~/fishtally/Assets/tangtest_1.jpg
```

*Press 's' to save the image to examine it. Now you can identify points to plot your threshold triggers.*

Now we use a single line threshold detector at point A (100,100) and point B (1100,600). [Points read as (x,y)]
This is a diagnonal line that is going to cut across the screen for maximum coverage.
Threshold points can be chosen based on camera movement, it should be straightforward for survey transects.

Let's tally some fish!

```bash
python fishtally.py --model_weights ~/fishtally/Assets/multiclass-wts.pt --source_video ~/fishtally/Assets/tangtest.mp4 --target_video ~/fishtally/Assets/demo_1.mp4 --detector_type single_line --line_start 100 100 --line_end 1100 600 --class_id 3
```
You can change the name of the output file.

**Congratulations, you've automated fish count! 🥳**

# Running FishTally

This repository contains tools for detecting and counting fish in video footage using YOLO-based models. There are two main scripts: `fishtally.py` for processing videos and `list_classes.py` for listing available classes in the YOLO model.

## Usage

### Listing Available Classes

Before using the fish tallying tool, you can list the available classes in your YOLO model. This will help you identify the correct class ID for detection. [This is the weights file: https://github.com/kluless13/fishtally/blob/main/Assets/multiclass-wts.pt]

**List Classes**

   Run `list_classes.py` with the path to your model weights:

   ```bash
   python list_classes.py --model_weights <path_to_weights>
   ```

   Replace `<path_to_weights>` with the path to your YOLO model weights file. This will print out the class names and their corresponding indices.

### Checking frame to plot points 

To make sure you know where to plot the ploints for your detector, use `frames.py` as mentioned below:


**Frame Check**

   ```bash
   python frames.py --source_video <path_to_source_video.mp4> --save_path <path_to_reference_img.jpg>
   ```

### Fishtally in depth

After identifying the correct class ID, you can proceed to use `fishtally.py`.

1. **Open the Terminal**

   Open a terminal window and navigate to the directory where `fishtally.py` is located.

2. **Running the Tool**

   Use the following command structure to run the tool:

   ```bash
   python fishtally.py --model_weights <path_to_weights> --source_video <path_to_source_video> --target_video <path_to_output_video> --detector_type <detector_type> --class_id <class_id>
   ```

   Replace `<path_to_weights>`, `<path_to_source_video>`, and `<path_to_output_video>` with the respective paths. For `<detector_type>`, choose from `single_line`, `multi_line`, or `polygon`. Replace `<class_id>` with the ID of the class you want to detect.

   - For `single_line` and `multi_line`, specify the line coordinates.
   - For `polygon`, provide the polygon points [bottom left, top left, top right and bottom right].

3. **Example Commands**

   - **Single Line:**
     ```bash
     python fishtally.py --model_weights weights.pt --source_video source.mp4 --target_video output.mp4 --detector_type single_line --line_start 100 200 --line_end 300 400 --class_id 3
     ```

   - **Multiple Lines:**
     ```bash
     python fishtally.py --model_weights weights.pt --source_video source.mp4 --target_video output.mp4 --detector_type multi_line --line1_start 100 200 --line1_end 300 400 --line2_start 500 600 --line2_end 700 800 --class_id 3
     ```

   - **Single Polygon:**
     ```bash
     python fishtally.py --model_weights weights.pt --source_video source.mp4 --target_video output.mp4 --detector_type polygon --polygon_points 100 200 300 400 500 600 700 800 --class_id 3
     ```

    Replace the coordinates and the class ID in these examples with those relevant to your specific use case. Here is the arrangement of the coordinates: bottom left, top left, top right and bottom right.

   - **Multiple Polygons:**
     ```bash
     python fishtally.py --model_weights weights.pt --source_video source.mp4 --target_video output.mp4 --detector_type multi_polygon --polygons 100 200 300 400 500 600 700 800 --polygons 100 200 300 400 500 600 700 800 --class_id 3
     ```

    Replace the coordinates and the class ID in these examples with those relevant to your specific use case. _You can add as many polygons as you want._ Here is the arrangement of the coordinates: bottom left, top left, top right and bottom right.

4. **Viewing the Results**

   After running the command, the tool will process the video and output the results to the path specified in `--target_video`. Check this file to view the results of the fish counting process based on the specified class ID.

### Notes

- Ensure that the paths to the model weights and videos are correct.
- The coordinates for lines or polygons should be adjusted based on the requirements of your specific task.
- The tool can be used from the jupyter notebooks as well, in case more customisation is required.
- Model weights are necessary before running the tool.
- There can be more than 1 polygon, this is the first iteration of FishTally and I will be updating this as I go.


### Current specs

- Single, multi and multi polygon set up to identify, track and count species.
- In and Out counter present in source code, not applied to CLI version - I&O used to mitigate double counting.
- Backbone is a lightweight YOLOv8 engine (object detection only), can be used for fish species, coral species, incoming bodies such as missles (for naval drone ops) and other debris that needs tracking and counting.

### What I want to add

- Semantic segmentation, especially for corals.
- Pipeline for 3D construction in real time.
- Integrate information into database for long term use case.
- Possibly create a front end UI for users to drop videos and run the tool.
