# 3CPF File Format Documentation

## Overview

The 3CPF (3D Colored Point Frames) file format is designed for storing animated colored point data, specifically tailored for 3D animation and visualization purposes. It encapsulates positional data for a series of colored points across multiple frames of animation. This documentation provides an overview of the 3CPF file structure, byte layout, and rudimentary usage.

## Capabilities

- **Static Point Colors**: Supports the storage of static color data for each point.
- **Dynamic Point Positions**: Supports storing the position (X,Y,Z coordinates) of each point for each frame.
- **Multiple Frames**: Can handle multiple frames of animation, making it suitable for simple animations or motion data.

## Limitations
The 3CPF file format was created due to a lack of existing efficient point cloud file formats with support for animated points. Having originally been designed for a niche project, it has many limitations:
- **No Support for Mesh Topology**: Does not store information about the mesh topology (e.g., edges, faces), only point data.
- **Fixed and Static Color Precision**: Colors are stored as RGB with 8 bits per channel, and does not support the changing of point colors during animation.
- **No Metadata or Extras**: Does not support storing additional metadata or custom data fields related to the 3D model or animation such as framerate or scale.
- **Fixed Number of Points**: Each frame must contain the same number of points.

## File Layout (Byte Structure)

The 3CPF file format is structured as follows, with all values stored in little-endian format:

**Header:**

- **Magic Number (4 bytes):** A fixed identifier for the 3CPF format, set to the ASCII representation of `3CPF`, 0x33 0x43 0x50 0x46.
- **Version Number (4 bytes):** An unsigned integer specifying the version of the 3CPF format. This documentation describes version 1.
- **Checksum (4 bytes):** An unsigned integer representing the CRC32 checksum of the subsequent data (point color data and frame data), used for data integrity verification.
- **Total Points (4 bytes):** An unsigned integer indicating the total number of points in the dataset.
- **Total Frames (4 bytes):** An unsigned integer specifying the number of frames in the animation.

**Point Color Data:**

- **RGB Colors (0-255) (3 bytes per point):** Each point is associated with an RGB color, stored as three consecutive bytes (one byte per color channel, in the order of R, G, B). The total size of this section is 3 bytes multiplied by the total number of points.

**Frame Data:**

- **Position Data (12 bytes per point per frame):** Each entry consists of three 32-bit IEEE-754 floating-point numbers representing the X, Y, and Z coordinates of a point in a frame. The total size of this section is 12 bytes multiplied by the total number of points multiplied by the total number of frames.

## Data Layout

In the context of the 3CPF format, "keys" refer to the index or order in which points and and positional data are stored and accessed within the file.

- **Point Keys:** Each point in the animation is assigned a unique key based on its order in the *point color data section.* The first point is assigned a key of 0, the second a key of 1, and so on. This key is used to associate position data with the corresponding color data across all frames.

- **Frame Keys:** Each frame of animation is also assigned a unique key based on its sequential order in the *frame data section.* The first frame is given a key of 0, the second a key of 1, and so forth. This key determines the order in which frames are played or accessed during animation playback. The order in which positional data appears within each frame corresponds to the aforementioned point keys.

In summary, we store the color data first, each R,G,B color entry representing the color of one point. Then we store each frame, which has one X,Y,Z entry for each entry in the color data section.
