
# How-To : Set Up Dislocation Dynamics Package
<em>Note: The commands in this tutorial are primarily written for a Fedora system, so there may be slightly differences depending on your computer. </em>


### If starting from Windows OS…<p>
1. Download and partition system to install Fedora (or other Linux operating system) 
    - It is highly recommended that you backup your system prior to going through this process
    - You may need to format a USB to be a disk drive to install Fedora – then reboot the computer in BIOS mode
    - https://getfedora.org/
    - https://www.lifewire.com/guide-to-installing-fedora-linux-2202074 (how-to guide)
    - Partitioning roughly 100-200GB should be enough to start with 
    
### If starting from Linux OS or after successfully partitioned system…
1. Download and install Mercurial (hg) (https://www.mercurial-scm.org/)
    - Package will assist with source control management and branch updates 
```cpp
sudo dnf install hg
```
2. Download and install VTK and VTK developer tools (https://vtk.org/)
    - Package will provide visualization tools for DD

```cpp
sudo dnf install vtk
sudo dnf install vtk-devel
```

3. Download latest gcc version
```cpp
sudo dnf install gcc-c++
```
4. Clone the Model (DD) package using Mercurial (https://bitbucket.org/model/model/wiki/Home)
    - Use the terminal to move to the Documents folder (or wherever you want to place the Model code)
```cpp
hg clone https://model@bitbucket.org/model/model Model
cd Model
hg update DiscreteCrackMechanics 
```

5. Download Eigen package using Mercurial 
    - Library to assist with matrix math computation throughout the dislocation dynamics code
    - Use the terminal to move to the Documents folder (or wherever you want to place the Eigen library)
    - hg clone https://bitbucket.org/eigen/eigen/ Eigen #uses hg to pull code and creates the Eigen folder 
    - OPTIONALLY, users can create symbolic links to the Eigen package with the terminal commands;
```cpp
sudo ln -s /home/cmcelfresh/Documents/Eigen Eigen
sudo ln -s /home/cmcelfresh/Documents/Eigen/unsupported unsupported
```

Alternatively, you can edit the Makefiles in the Model code (to be discussed later)

6. Download and install FFmpeg package
    - FFmpeg package assists with creating videos from the produced DD figures
```cpp
sudo dnf install ffmpeg
```
7. Download and install the TetGen package (http://wias-berlin.de/software/tetgen/)
    - TetGen assists with generating customizable meshes for DD simulations
    - Download online http://wiasberlin.de/software/index.jsp?id=TetGen&lang=1#Download
    - Follow instructions on compiling TetGen code to create executable. 
          - Put all downloaded material in its own Tetgen folder
          -  Move into the folder using the command line:
```cpp
cd TetGen
make
```
8. Download and install Intel Math Kernel Library 
    - Find download at https://software.intel.com/en-us/mkl/choose-download
    - Select “Intel Parallel Studio XE”
    - Follow instructions for downloading for Linux as a student. You will be required to fill out a brief questionnaire about the usage of MKL for DD. 
    - Once submitted it may take a day or so to received email confirmation of approval for the package. Download the package and follow the Linux installation guide. 
    - NOTE: During the installation make note of where the package is installed – its path will be necessary for updating the Model makefiles. 

### If starting from UCLA hoffman2 (or other supercomputer)…
1. Download Model (DD) package using Mercurial (https://bitbucket.org/model/model/wiki/Home)
    - Use the terminal to move to the Documents folder (or wherever you want to place the Model code)
```cpp
hg clone https://model@bitbucket.org/model/model Model  
cd Model
hg update DiscreteCrackMechanics
```

2. Download Eigen package using Mercurial 
    - Eigen assists with matrix math computation throughout the dislocation dynamics code
    - Use the terminal to move to a folder outside of the newly created Model folder (or wherever you want to place the Eigen library)
```cpp
hg clone https://bitbucket.org/eigen/eigen/ Eigen #uses hg to pull code and creates the Eigen folder 
```
- OPTIONALLY, users can create symbolic links to the Eigen package with the terminal commands:
```cpp
sudo ln -s /home/cmcelfresh/Documents/Eigen Eigen
sudo ln -s /home/cmcelfresh/Documents/Eigen/unsupported unsupported
```
Alternatively, you can edit the path in the Makefile in the Model code (to be discussed later)
    
3. Load most recent gcc compiler. 
    - Note that you may need to do this each new interactive session on hoffman2, as the default compiler may older than that required by DD. 
    - Show and load gcc compilers using the following commands in the interactive terminal: 
```cpp
module available gcc #lists the available versions of gcc 
module load cmake/”most recent version #” #loads desired version of gcc 
```
### If starting from macOS<p>
1. Use Homebrew as a package manager to make the installation process easier. You can read about Homebrew here: https://brew.sh/. Homebrew can be installed by using the following command:
    
```cpp
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)" 
```
2. Install cmake
```cpp
brew install cmake
```
3. Install Eigen
```cpp
brew install eigen
```
4. Build a symbolic link for the Eigen library and put it in the /header_symlinks/ folder. 
```cpp
sudo ln -s /usr/path/to/Eigen/ Eigen
```
5. Install VTK
```cpp
brew install vtk
```
6. Build DDvtk in the tools/DDvtk/ folder by running:
```cpp 
cmake . 
make
```
If an error occurs with the inclusion of fopenmp, it may be necessary to specify the version of g++ compiler in the Makefile such as changing "g++" to "g++-9". More information can be found by reading through the following thread: https://github.com/microsoft/LightGBM/issues/3. 

## How-To: Update the Makefile (for both local and hoffman2/supercomputer use)…
1. Go into the Makefile in Model/tutorials/DislocationDynamics/Makefile
2. Update the path to the Eigen library
    - Update the “EIGEN_INCLUDE = … “ line at the beginning to be your path for Eigen
    - Example: 
```cpp
EIGEN_INCLUDE = /home/cmcelfresh/Documents/Eigen/Eigen
```
3. Update the path to MKL (only if using on local machine)
    - Update the “MKL_INCLUDE = … “ line at the beginning to be your path for  MKL
    - Note that this will only be necessary if you installed MKL on your local machine and it was not deposited where DD expected it. This step is not necessary if using DD on hoffman2
    - Example
```cpp
MKL_INCLUDE=/home/cmcelfresh/intel/mkl/include
```
4. Once MKL has been successfully linked to DD, set the usePARDISO value to 1
    - usePARDISO = 1
    - This greatly increases computational efficiency, particuarly on the FEM-aided simulations. 
5. Update the compiler flags for MKL PARDISO (only if using on local Linux machine)
    - Change line 78 to include your personal path to lib/intel64. 
    - Example:
```cpp
MKL_LIB=/home/cmcelfresh/intel/mkl/lib/intel64
```
6. During compilation of the DD code if the MKL library is not recognized, it may be necessary to directly export path using  the two terminal submitted commands that include your personal path to intel/lib/intel64 and mkl/lib/intel64. 
    - Example:
```cpp
export LD_LIBRARY_PATH=/home/cmcelfresh/intel/mkl/lib/intel64:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/home/cmcelfresh/intel/lib/intel64:$LD_LIBRARY_PATH
```

## How-To: Updating visualization aid VTK
Dislocations Dynamics has been developed in a way that all simulations can be viewed in a frame-by-frame fashion – which provides excellent insight into the mechanisms dominating dislocation interactions. As such, it is highly recommended that the VTK visualization aid to used alongside all simulations!
HOWEVER, VTK is not currently compatible with hoffman2 (as of 2019) , so it it recommended that you run VTK through a hoffman2-mounted folder on your local machine. More on mounting later.
1. Ensure that a recent version of cmake is installed. 
    - If not, the most recent version can be installed on Fedora using:
```cpp
sudo dnf install cmake
```
2. Similarly, the more recent versions of cmake can be loaded on hoffman2 by submitted the command
```cpp
module available cmake #lists the available versions of cmake 
module load cmake/”most recent version #" #loads desired version of cmake 
```
3. Go to the tutotrials folder of your locally mounted DD code and re”make” the code to update the header files
    - Go to → Model/tutorials/DislocationDynamics/finiteDomains_NO_FEM/uniformLoadController
```cpp
make
```

4. Compile DDvtk
    - Go into the VTK DD folder /home/cmcelfresh/Documents/Model/tools/DDvtk
    - Run the following commands:
```cpp
cmake .
make
```
This creates a DDvtk executable that can be placed in any of DD tutorial files! DDvtk can be run before the simulation (to see initial microstructure), during the simulation (it continues to update the structure as the folders are populated), and after the simulation for post processing. 

5. Copy the DDvtk file and place it in any of the following tutorial folders:
    - Model/tutorials/DislocationDynamics/finiteDomains_NO_FEM/uniformLoadController
    - Model/tutorials/DislocationDynamics/finiteDomains_FEM/cantileverBeam_strainControl
    - Model/tutorials/DislocationDynamics/finiteDomains_FEM/uniaxialPillar_StressControl
    - Note that exact paths may change with updates to DD code – a good check is that the DDvtk file must be on the same folder level as your main.cpp

<em> Note: When you are updating the the mesh files (.msh) you must also remake DDvtk. </em>


## How-To: Run Dislocation Dynamics
Assuming all previous steps have been followed correctly and each piece is installed, running DD can be done with the following commands. As previous, the most recent gcc compiler will be necessary to successfully run DD. Ensure that you are in the proper directory within the tutorial folders, which could be.

- Model/tutorials/DislocationDynamics/finiteDomains_NO_FEM/uniformLoadController
- Model/tutorials/DislocationDynamics/finiteDomains_FEM/cantileverBeam_strainControl
- Model/tutorials/DislocationDynamics/finiteDomains_FEM/uniaxialPillar_StressControl
    
1. Update header files
```cpp
make
```
2. Create and run the microstructureGenerator
```cpp
make microstructureGenerator 
./microstructureGenerator 
```
3. Remove old evl files (node/line position)
```cpp
make empty
```
4. Remove old executable, and make new
```cpp
make clean
make DDomp
```
5. Run the simulation!
```cpp
./DDomp
```

## How-To:  Update the DD Code when Changes are added to Bitbucket
When changes are posted to the MODEL code on Bitbucket, it is import to update your version to fix bugs or project-specific updates. You can check to see if changes were added by going to the Bitbucket page https://bitbucket.org/model/model/branches/

IMPORTANT NOTE: Prior to updating your code it is important that you externally backup any files that you do not want to rewrite. This applies to Makefile, evl_0.txt, polycrystal.txt, DD.txt, and any other locally-specific file that you may have. 

To update the code through Mercurial the the following commands within the Model folder:

```cpp
hg pull
hg update --clean
```

After the package has been updated, it is recommended that you remake the DDvtk executable. 

## How-To: Create Your Own Meshes with Neper
Download and install Neper and all supporting libraries (gmsh, Netgen, POV-Ray, libScotch). Follow documentation found with the Neper package http://www.neper.info/

1. Execute simple Neper commands to build .tess files.
    - To build a single-grained 1000x1000x3000 rectangular prism:
```cpp
neper -T -n 1 -domain “cube(1000,1000,3000)”
```
2. Then open the mesh in gmsh and save it as a ASCII version 2.2 .msh file. 
Than be performed by either:
     1. gmsh → then open the .tess or .geo file → export as ASCII version 2.2 msh, or
     2. Neper command line operations such as
```cpp
neper -M n1-id1.tess  #assuming the .tess file previously produced is n1-id1.tess)
```
3. The location of the .msh file should then be specified in the inputFiles/polycrystal.txt file.

Note:  After a new .msh file is generated and specified in polycrystal.txt, the corresponding microstructureGenerator, Ddvtk, and DDomp executables must ALSO be regenerated.

Neper Installation Notes: For MacOS, a "gsl library not found" error may occur when gsl is in fact installed. This can be circumvented by adding

```cpp
include_directories("path/to/gsl/2.6./include")
``` 
in the CMakeLists.txt file in the src folder. The same goes for the NLopt library if cmake is unable to link. 

## How-To: Use DDvtk
Follow instructions on how to install VTK found here: https://vtk.org/Wiki/VTK/Building/Linux. Once there is a operational form of VTK installed, do the following:
1. cd into the Model/tools/DDvtk folder
2. Run the commands:
```bash
cmake .
make
```
3. This creates an DDvtk executable that can then be moved into one of the simulation folders such as;
- Model/tutorials/DislocationDynamics/finiteDomains_NO_FEM/uniformLoadController
- Model/tutorials/DislocationDynamics/finiteDomains_FEM/cantileverBeam_strainControl
- Model/tutorials/DislocationDynamics/finiteDomains_FEM/uniaxialPillar_StressControl
4. Once there is dislocation node/segment and mesh data present, DDvtk can be run to observe the dislocation structure by executing:
```cpp
./DDvtk
```
From there, the visualization window can be interacted with by several keyboard and mouse commands:
- up/down arrow : step frames forwards and backwards
- i : change the increment of frames
- e : change dislocation edge appearance (follow console instruction)
- v : change dislocation node appearance (follow console instruction)
- s : save images (follow console instruction)
- y : change y-axis of graph (follow console instruction)
- x : change x-axis of graph (follow console instruction)
- mouse : drag mouse to change view, scroll to zoom in/out
- a : add or remove axis

## How-To: Record Video using FFmpeg
1. Ensure that FFmpeg is downloaded and installed (following the steps above)
2. Run a DD simulation until a desirable amount of data is collecetd. 
3. Run DDvtk
```cpp
./DDvtk
```
4. Organize the visualization space however is preferred (remove the graph, axis, rotate the orientation, zoom, etc.)
5. Press "i" and then follow the instructions for however many frames per screenshot - this will largely determine the native framerate.
5. When the DDvtk visualization window is open and selected press "s" and then spacebar, followed by "2". This turns saving ON and selects JPG as the type of image being saved. 
6. Press on the down arrow to cycle forward in time and consecutively save each image. When the last desired frame has been reached hit "s" and then spacebar again to stop saving. 
7. cd into the "jpg" folder and run:
```cpp
bash renameJpg.sh
bash jpg2mpg.sh
```
This will rename the images and then compile them into video.mpg. The video can be further post-processed in an external program. 

Example Video: Nanopillar Compression
TBD

Example Video: Simulated Climb
![](https://github.com/cameronmcelfresh/images/blob/master/dislocation_line_gif.gif)

## How-To: Understand the Units Used in MODEL
Care must be taken to account for the units specific to MODEL. Some of the units that are encountered most often are:

- Unit of Distance = burgers Vector [m]
- Unit of Speed = Shear Wave Speed [m/sec]
- Unit of Time = Unit of Distance / Unit of Speed [sec]
- Unit of Stress = Shear Modulus of Material [Pa]
- Unit of Force = Shear Modulus  * (burgers vector)^2 [N]
- Strain Rate = Strain / Unit of Time

The distance units must be accounted for if dislocation structure post-processing is desired because all positions in the evl files are printed with burgers vector units. Similarly, to properly set the strain rate the unit of time must be accounted for. 

For instance, if a material had a  the burgers vector is set to b = 0.14nm and a shear wave speed of 2000m/s:

A DD node position of (0, 5, 10) would be transformed to (0, 0.7nm, 1.4nm) in real units

A DD strain rate of 1E-9 would be transformed to 1E-14 / (0.14nm/2000) = 0.14 in real units

## How-To: Read the evl files
The spatial information from the dislocation structures can be found in the evl folder with labels that correspond to different runIDs. For example, evl_5.txt is from runID=5. However if outputBinary=1 (set in the DD.txt input file) the evl files will be in binary, though they can still be directly analyzed using the graphing functionality in DDvtk. 

A simple example of an evl with a dislocation loop consisting of 4 nodes, 4 segments, a burgers vector of [010] and a plane normal of [100] could be :

```cpp
4
1
4
0	0.000000000000000e+00 -2.000000000000000e+02 1.000000000000000e+02	0.000000000000000e+00 0.000000000000000e+00 0.000000000000000e+00	1.000000000000000e+00	0	0	0
1	0.000000000000000e+00 2.000000000000000e+02 1.000000000000000e+02	0.000000000000000e+00 0.000000000000000e+00 0.000000000000000e+00	1.000000000000000e+00	0	0	1
2	0.000000000000000e+00 -2.000000000000000e+02 -1.000000000000000e+02	0.000000000000000e+00 0.000000000000000e+00 0.000000000000000e+00	1.000000000000000e+00	0	0	2
3	0.000000000000000e+00 2.000000000000000e+00 -1.000000000000000e+02	0.000000000000000e+00 0.000000000000000e+00 0.000000000000000e+00	1.000000000000000e+00	0	0	3
0	0.000000000000000e+00  1.000000000000000e+00  0.000000000000000e+00	1.000000000000000e+00 0.000000000000000e+00 0.000000000000000e+00	1.625275402303664e+03 2.649178338026089e+03 8.790044633940530e+03	1	0	-1	0.000000000000000e+00 0.000000000000000e+00 0.000000000000000e+00	0.000000000000000e+00	0.000000000000000e+00	0.000000000000000e+00
0	0	1	0
0	1	2	0
0	2	3	0
0	3	0	0

```

The example evl file reads as follows:

```cpp
(# of nodes)
(# of loops)
(# of loop segments)
(node1 ID)	(node1 x-pos) (node1 y-pos) (node1 z-pos)   (node1 x-vel) (node1 y-vel) (node1 z-vel) [non-physical information]
(node2 ID)	(node2 x-pos) (node2 y-pos) (node2 z-pos)   (node2 x-vel) (node2 y-vel) (node2 z-vel) [non-physical information]
(node3 ID)	(node3 x-pos) (node3 y-pos) (node3 z-pos)   (node3 x-vel) (node3 y-vel) (node3 z-vel) [non-physical information]
(node4 ID)	(node4 x-pos) (node4 y-pos) (node4 z-pos)   (node4 x-vel) (node4 y-vel) (node4 z-vel) [non-physical information]
(loop1 ID) ( 3 - sequence burgers vector of loop1)  ( 3 - sequence plane normal vector of loop1) [non-physical information]
(loop ID of segment1) (node source) (node sink) (bool for boundary link)
(loop ID of segment2) (node source) (node sink) (bool for boundary link)
(loop ID of segment3) (node source) (node sink) (bool for boundary link)
(loop ID of segment4) (node source) (node sink) (bool for boundary link)
```

Therefore we know that node 0 is at position = (0,-200,100) and connects to both node 1 and node 3 as non-boundary segments.

A more abridged form of the evl file could be written as:


```cpp
(# of nodes)
(# of loops)
(# of loop segments)
(node position info) x number of nodes
(loop info) x number of loops
(node connectivity) x number of segments
```

Note that here, as in the rest of MODEL, positions are given in burgers vector units. The burgers vectors and plane normals in the evl files are similarly normalzied to 1. 

## How-To: Change the orientation of the crystal 
The orientation of the crystal is controlled by altering the C2G1 entry in inputFiles/polycrystal.txt. The C2G1 variables is a 3x3 matrix in which each row corresponds to original crystal direction that now represents the new x-axis, y-axis, and z-axis for rows 1, 2, and 3 of the C2G1 matrix, respectively. Each row much be normalized to 1, each row must be orthogonal to one another, and as such, the determinant must be 1. 

For example, if it was desirable to have the [010] direction oriented along the z-axis, an acceptable C2G1 matrix could be;
```cpp
C2G1 =  1   0   0
        0   0   -1
        0   1   0;
```

If it is of interest to have the crystal oriented along a certain direction, simply select that direction as either the x, y, or z-axis and then use cross-product and dot product rules to find the other appropriate axes. Changing the orientation of the crystal does NOT change the orientation of the stresses or mesh - it changes the orientation of the glide planes internal to the mesh. 

## How-To: Navigate the MODEL code using Doxygen
To learn the dependencies of the MODEL codebase Doxygen can be used to navigate through the code. Firstly, follow standard instructions to download and install Doxygen (http://www.doxygen.nl). After running doxygen, which can easily be done with the "doxywizard" command in the terminal, simply run Doxygen in the doxygen folder of the MODEL library.
