This Read Me is for the Flir View Software

The flirview service is started at boot, make sure the camera is plugged
into the USB ports before plugging in the raspberry  pi

Software Parts
The Server code designed to run on the linux system is FlirView.py
This code reads from the video device likely /dev/video0 and creates an RTSP Server by default on
IP 0.0.0.0 port 8445

The default connection string from your rtsp client is
rtsp://<SERVER IP>:8554/FLIR


A second server is also created on the same ip address to allow for remote control of the RTSP stream
This Service listens on
IP 0.0.0.0 port 65432


When connecting to the RTSP server from a windows machine it is recomended to use FFmpegs FFplay tool
You can get this software here
https://www.gyan.dev/ffmpeg/builds/

But the easiest install is the winget command if on windows 10 or above
winget install ffmpeg

or if on windows 7 and below windows 10
winget install "FFmpeg (Essentials Build)"


Server Command line options are as follows
usage: FlirView.py [-h] [--config CONFIG] [--video VIDEO] [--port PORT] [--factory FACTORY] [--timestamp]
[--crosshair | --tree] [--colormap {0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20} | --raw] [--invert]

RTSP Server Configuration

options:
  -h, --help            show this help message and exit
  --config CONFIG       Path to config file
  --video VIDEO, -v VIDEO
                        The video device or file to use (e.g., /dev/video0). Default is /dev/video0.
  --port PORT, -p PORT  The port number to run the RTSP server on. Default is 8554.
  --factory FACTORY, -f FACTORY
                        The RTSP factory path (e.g., /RTSP). Default is /FLIR.
  --timestamp           Toggle the timestamp on the video stream. Default is off.
  --crosshair           Toggle the crosshair on the video stream. Default is off.
  --tree                Toggle the tree drawing on the video stream. Default is off.
  --colormap {0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20},
  -c {0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20}
                        Set the colormap (0-21) for the video stream.
  --raw                 Display the raw video stream without any colormap. Default is on.
  --invert              Invert the colors before applying the colormap. Default is off.

  NOTE providing a configfile will override any other cli arguments

 The config file break down is as follows

[RTSP]
# Video source expected as a string and linux file path
video = /dev/video0
# port number to set up th rtsp server on
port = 8554
# factory for the rtsp uri. This defines the end of the rtsp connection string ie rtsp://192.168.86.33:8554/FLIR
factory = /FLIR
# a bool to toggle the yellow timestamp on and off
timestamp = False
# Note DO NOT try to set the cross hair and tree to true at the same time
# a bool to toggle the crosshair on and off
crosshair = False
# a bool to toggle the tree overlay on and off
tree = False
# set your color map options, color map options can be found in open CV documentation here and must be an int 0-21
# https://docs.opencv.org/4.x/d3/d50/group__imgproc__colormap.html#gga9a805d8262bcbe273f16be9ea2055a65afde88b0caed8acc01447906d700b99fb
# If you want to set raw to true make sure you set the value of color Map to None
colormap = None
# a bool to just take raw video from the camera and apply no color map options
raw = True
# invert the pallet making hottest darkest or lightest depending on needs, this will work with the color maps
invert = False


The remote GUI program CamControl.py requires pygame installed
This program requires the Flirview Server IP and port to be input for the remote control. The default Remote port is 65432
The GUI remote does not get state from the FlirView server this means that some buttons may need to be toggled on and off
before their state engines line up.

MISSION PLANNER
The gstreamer URL for getting video into mission planner is as follows, make sure to replace the ip address of 192.168.86.33 with your RTSP server IP
rtspsrc location=rtsp://192.168.86.33:8554/FLIR ! application/x-rtp ! rtph264depay ! avdec_h264 ! videoconvert ! video/x-raw,format=BGRA ! appsink name=outsink

To put this connection string into mission planner
Open mission planner
Right click on the video window
select the video drop down
then select Set Gstreamer Source
Paste in the above string and if everything is correct in a few seconds your video should pop up in mission planner

Note the remote control will work as expected when streaming the video into mission planner


Note to install python on windows the easy way
Open up cmd.exe
type python, you wont have it in stalled but microsoft should pop a box and offer to install it for you, this is on windows 11
do the install and then type python again to make sure it installed correctly
close that cmd exe window and open another
the following will install pygame for the remote control

python -m pip install pygame

This should take care of any windows dependencies for the remote control 