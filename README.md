[RoboComp](http://robocomp.org)
===============================

[![Join the chat at https://gitter.im/robocomp/robocomp](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/robocomp/robocomp?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![Docs Links Checker](https://github.com/robocomp/robocomp/actions/workflows/broken_links.yml/badge.svg)](https://github.com/robocomp/robocomp/actions/workflows/broken_links.yml)
[![CodeQL Analysis](https://github.com/robocomp/robocomp/actions/workflows/codeql-analysis.yml/badge.svg)](https://github.com/robocomp/robocomp/actions/workflows/codeql-analysis.yml)
[![Docker Images](https://github.com/robocomp/robocomp/actions/workflows/publish_docker_images.yml/badge.svg)](https://github.com/robocomp/robocomp/actions/workflows/publish_docker_images.yml)
[![Component generation test](https://github.com/robocomp/robocomp/actions/workflows/components_generation.yml/badge.svg)](https://github.com/robocomp/robocomp/actions/workflows/components_generation.yml)
[![Component compilation test](https://github.com/robocomp/robocomp/actions/workflows/components_compilation.yml/badge.svg)](https://github.com/robocomp/robocomp/actions/workflows/components_compilation.yml)
[![robocompdsl test](https://github.com/robocomp/robocomp/actions/workflows/robocompdsl.yml/badge.svg)](https://github.com/robocomp/robocomp/actions/workflows/robocompdsl.yml)
# About

An organization maintained by [RoboLab (Universidad de Extremadura)](http://robolab.unex.es), [Aston University](https://www2.aston.ac.uk/eas), [ISIS (Universidad de Málaga)](http://www.grupoisis.uma.es/) and many other collaborators from the Google Summer of Code program.

RoboComp is an open-source Robotics framework providing the tools to create and modify software components that communicate through public interfaces. Components may *require*, *subscribe*, *implement* or *publish*
interfaces in a seamless way. Building new components is done using two domain-specific languages, IDSL and CDSL. With IDSL you define an interface and with CDSL you specify how the component will communicate with the world. With this information, a code generator creates C++ and/or Python sources, based on CMake, that compile and execute flawlessly. When some of these features have to be changed, the component can be easily regenerated and all the user-specific code is preserved thanks to a simple inheritance mechanism.

**If you already have RoboComp installed, jump to [tutorials](doc/README.md) to start coding!**

:warning: If you want to contribute with something new to Robocomp, please do it on the [development branch](https://github.com/robocomp/robocomp/tree/development).

:question: If you have a question please look for it in the [FAQ](doc/FAQ.md). 
-

- [Installation from source](#installation-from-source)
  * [Requirements](#requirements)
  * [Installation](#installation)
- [Testing the installation using the RCIS robotics simulator](#testing-the-installation-using-the-rcis-robotics-simulator)
  * [Installing some RoboLab's components from GitHub](#installing-some-robolab-s-components-from-github)
  * [Connecting a JoyStick (if no JoyStick available skip to the next section)](#connecting-a-joystick)
  * [Using the keyboard as a JoyStick](#using-the-keyboard-as-a-joystick)
- [Testing the installation using the Coppelia Simulator](#testing-the-installation-using-the--coppelia-simulator)
- [Next steps](#next-steps)
- [Known issues](#known-issues)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>



# Installation from source

Tested in Ubuntu 20.04 and 22.04, with Python3.10 and Python3.12

**Note:** RoboComp is not compatible with Ubuntu 16.04. RoboComp needs to be compiled using C++11. Ice libraries with C++11 support are only available for zeroc-ice 3.7 and the packages for this version are only available since Ubuntu 18.04.

**Note:** RoboComp is not compatible with Ubuntu 18.04. RoboComp needs to be compiled using cmake >= 3.16. It's not available in Ubuntu 18.04.

**Note:** If you have installed Anaconda in your system. [Then you need to change the python from anaconda to default](https://github.com/robocomp/robocomp/issues/248).

## Prerequisites

Before you begin the installation, make sure you have the following prerequisites installed on your system:

### Prerequisites for Robocomp Installation
```bash
sudo apt update
sudo apt install python3 python3-pip cmake vim git wget libopenscenegraph-dev libgsl-dev 
pip3 install vcstool 
```

### Prerequisites for Compiling Robocomp Components
```bash
sudo apt install qt6-base-dev qt6-declarative-dev qt6-scxml-dev libqt6statemachineqml6 libqt6statemachine6 libbz2-dev libssl-dev zeroc-icebox zeroc-ice-all-dev libzeroc-icestorm3.7 libeigen3-dev meld
pip install PySide6 zeroc-ice

mkdir ~/software 2> /dev/null; cd ~/software && git clone https://github.com/GillesDebunne/libQGLViewer.git 
cd libQGLViewer && qmake6 *.pro && make -j12 && sudo make install && sudo ldconfig
```


## Installation Steps

1. Download the robocomp.repos file:
```bash
cd
wget https://raw.githubusercontent.com/robocomp/robocomp2/main/robocomp.repos
```

2. Configure environment variables:
```bash
echo "export ROBOCOMP=/home/robocomp/robocomp" >> ~/.bashrc
echo "export PATH=\$PATH:/home/$USER/.local/bin" >>  ~/.bashrc
source ~/.bashrc
```

3. Import RoboComp packages:
```bash
vcs import . < robocomp.repos --recursive

sudo ln -s /home/$USER /home/robocomp

cd /home/robocomp/robocomp
ln -s core/cmake cmake
ln -s core/classes classes

mkdir components
```

4. Install RoboComp command-line tools:
```bash
pushd . && cd robocomp/tools/cli/ && pip install . && popd
```

```bash
sudo ln -s /usr/include/eigen3/Eigen/ /usr/include/Eigen
```

5. Add an alias for rcnode to your .bashrc:
```bash
echo "alias rcnode='bash /home/robocomp/robocomp/tools/rcnode/rcnode.sh&'" >>  ~/.bashrc
source ~/.bashrc
```


If you are going to develop with Robocomp it's recommendable to install the following packages too:
```bash
sudo apt-get install yakuake

sudo git clone https://github.com/ryanhaining/cppitertools /usr/local/include/cppitertools
cd /usr/local/include/cppitertools
sudo mkdir build
cd build
sudo cmake ..
sudo make install
```

Done! Now let's have some fun.

To test RoboComp with Coppelia you need to:

- Install Coppelia Robotics and Pyrep. There are detailed instructions [here](https://github.com/robocomp/robocomp/blob/development/doc/DSR-start.md)
- Run the bridge, i.e. omniPyrep.py and see that Coppelia starts Ok.
- Connect your new component to the ports offered in omniPyrep.py or
- Connect a joystick or XBox pad to omniRep.py using [this component](https://github.com/robocomp/robocomp-robolab/tree/master/components/hardware/external_control/joystickpublish)
    
#### Installing some RoboLab's components from GitHub

The software of the robots using RoboComp is composed of different components working together, communicating among them. What we just installed is just the core of RoboComp (the simulator, a component generator, and some libraries). To have other features like joystick control we have to run additional software components available from other repositories, for example, robocomp-robolab:

    cd ~/robocomp/components
    git clone https://github.com/robocomp/robocomp-robolab.git
    
The RoboLab's set of basic robotics components are now downloaded. You can see them in `~/robocomp/components/robocomp-robolab/components`

## Connecting a JoyStick (if no JoyStick available skip to the next section)

If you have a joystick around, connect it to the USB port and:

    cd ~/robocomp/components/robocomp-robolab/components/hardware/external_control/joystickComp
    cmake .
    make
    cd bin
    sudo addgroup your-user dialout   // If you find permissions issues in Ubuntu
    check the config file in the component's etc folder and make sure that the port matches the DifferentialRobot endpoint in     RCIS.
    bin/joystick etc/config
    
Your joystick should be now running. It will make the robot advance and turn at your will. If it does not work, 
check where the joystick device file has been created (e.g., `/dev/input/js0`). If it is not `/dev/input/js0`, edit `~/robocomp/components/robocomp-robolab/components/hardware/external_control/joystickComp/etc/config` change it accordingly and restart. Note that you might want to save the *config* file to the component's home directory so it does not interfere with future GitHub updates.


## Using the keyboard as a JoyStick

If you don't have a JoyStick install this component,

    cd ~/robocomp/components/robocomp-robolab/components/hardware/external_control/keyboardrobotcontroller
    cmake .
    make
    src/keyboardrobotcontroller.py etc/config
    
and use the arrow keys to navigate the robot, the space bar to stop it and 'q' to exit.

Note 1: You must have your simulator running in a terminal and only then you can run a component in another terminal. You will get an error message if you run the above component without having RCIS already running.

Note 2: If you have anaconda installed (for python 3), It is recommended to uninstall anaconda first and then install robocomp. (It is only applicable if you have faced errors while running above commands.)

# Testing the installation using the Coppelia Simulator
We are now moving to more advanced robotics simulators that can reduce the gap between simulation and deployment. Our first choice now is [CoppeliaSim](https://www.coppeliarobotics.com/) because it offers a scene editor that can be used during a running simulation, you can "hang" and modify Lua code from the scene nodes in no time, you can choose among 4 physics engines and, thanks to the [PyRep](https://github.com/stepjam/PyRep) library, we have a fast access to almost everything running in the simulator.

Follow the instructions in https://github.com/robocomp/robocomp/blob/development/doc/robocomp-pyrep.md to install CoppeliaSim and PyRep

# Next steps

You can find more tutorials on RoboComp in [tutorials](doc/README.md) 

Drop comments and ask questions in:

- https://gitter.im/robocomp

Please, report any bugs with the github issue system: [Robocomp Issues](https://github.com/robocomp/robocomp/issues)

If you have any suggestions to improve the repository, like features or tutorials, please contact us on [![Join the chat at https://gitter.im/robocomp/robocomp](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/robocomp/robocomp?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) or create a feature request [here](https://github.com/robocomp/robocomp/issues).


# Known issues
- Compatibility problem between pyparsing version and Robocomp tools:
  * One of the main tools of Robocomp, robocompdsl is using pyparsing and the current code doesn't work with 2.4 version of this library. With the previous commands, we are installing the 2.2 version (python-pyparsing=2.2.0+dfsg1-2). If you have a more recent version of pyparsing installed with apt or pip we recommend you to uninstall it and install the 2.2 version. You can check your current version of pyparsing with this command:
```bash
python3 -c "import pyparsing; print(pyparsing.__version__)"
```

- Ubuntu 18.04 and CMake > 3.16
  * Robocomp is currently using the syntax for cmake 3.16. It's the default version in Ubuntu 20.04, but it's not available in Ubuntu 18.04.

