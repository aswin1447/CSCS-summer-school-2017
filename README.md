# CSCS-summer-school-2017
CSCS-ICS-DADSi Summer School: Accelerating Data Science with HPC, September 4 – 6, 2017

# Preparation

## Step 0: Install Docker

Follow instructions on the [Docker website](https://www.docker.com/) to install Docker (don't use `brew`, etc.).

## Step 1: Get the Docker image

Prerequisite: you should have [Docker](https://www.docker.com/) installed.  After you install Docker you should configure it via `Preferences/Advances` to allow the Docker image to use 8Gb of memory.

Start by pulling the `anglican-infcomp` image from the Docker hub:

```
docker pull gbaydin/anglican-infcomp
```

Beware: this can take some time (approximately 15 minutes over a fast LAN connection), so it might be a good idea to start pulling this image as soon as possible before the exercise sessions.


### Optional: Install `nvidia-docker`

If you have CUDA available, you can also install a Docker engine supporting NVIDIA GPUs. This will significantly speed up the neural network training phase, although the exercises we will cover already run fast enough without this.

Please follow instructions at the [nvidia-docker repository](https://github.com/NVIDIA/nvidia-docker).

## Step 2: Clone this repository

At a location of your choosing, run

```
git clone https://github.com/probprog/CSCS-summer-school-2017.git
```

to clone this repository containing the summer school exercises.

## Step 3: Run the Docker container

### Linux

First run

```
xhost +
```

to allow access to the X server from the Docker container (this is needed for one of the exercises where a GUI is used).


Then change into the folder and start an interactive Docker container by running:

```
cd CSCS-summer-school-2017
docker run --rm -it -p 31415:31415 -v $PWD:/workspace -e DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix gbaydin/anglican-infcomp
```

### Mac

First you need to download and install [XQuartz](https://www.xquartz.org/).

Then run

```
socat TCP-LISTEN:6000,reuseaddr,fork UNIX-CLIENT:\"$DISPLAY\"
```

You may need to [install socat](http://macappstore.org/socat/) if you can't run this command.  

In a different terminal, change into the folder and start an interactive Docker container by running:

```
cd CSCS-summer-school-2017
xhost +
ip=$(ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' )
docker run --rm -it -p 31415:31415 -v $PWD:/workspace -e DISPLAY=$ip:0 -v /tmp/.X11-unix:/tmp/.X11-unix gbaydin/anglican-infcomp
```

### Windows 10

Install [Docker](https://docs.docker.com/docker-for-windows/install/) and [Git bash](https://git-for-windows.github.io/).  In the toolbar click the Docker button
and increase the memory it uses under the advanced tab to, ideally, 8GB.

Using Windows Powershell run `bash` then run `ipconfig` to get your machines ip address then run the following command with 999.999.999.999 replaced with your ip address

```
docker run --rm -it -p 31415:31415 -v ${PWD}:/workspace -e DISPLAY=999.999.999.999:0 -v /tmp/.X11-unix:/tmp/.X11-unix gbaydin/anglican-infcomp
```

In the docker window then run

```
lein gorilla :port 31415 :ip 0.0.0.0
```


Then open a browser and go to

```
http://127.0.0.1:31415/worksheet.html
```

On Windows the physics example program will not display X-windows contents correctly with these settings.  

------------------------------------------


This will have started a new Docker container using the `anglican-infcomp` image that you pulled in the previous step.

In this command `--rm` indicates that the container will be removed when it exists, `-it` attaches an interactive terminal to the container, `-p 31415:31415` sets up a port mapping for port `31415` that is used for the Gorilla REPL for Clojure, and `-v $PWD:/workspace` mounts your current folder `CSCS-summer-school-2017` as `/workspace` within the container. The flags `-e DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix` set up the X server access that is needed for one of the exercises using GUI.

If you want to run with GPU support, replace `docker` with `nvidia-docker` in the above command.

# Exercises

## Exercise 1: Probabilistic programming in Anglican

In the Docker instance change directory into the first programming project

```
cd exercises/exercise-1-probprog
```

then start a Clojure browser-based repl (read evaluate print loop)

```
lein gorilla :port 31415 :ip 0.0.0.0
```

Open a web browser and browse to the first, introduction-to-functional-programming-and-Clojure, exercise workbook [01-clojure-overview.clj](http://0.0.0.0:31415/worksheet.html?filename=worksheets/intro-to-clojure/01-clojure-overview.clj)

We recommend going through the following 4 exercises in order.  We recommend not completing the first exercise in its entirety, instead, just completing a reasonable percentage of it, enough to get the basics, and then to move to the Anglican [hello world example](http://0.0.0.0:31415/worksheet.html?filename=worksheets/intro-to-anglican/01-hello-world.clj).  Use `shift-enter` to evaluate the current cell.  Additional commands and help are available in the GUI tab in the upper right hand corner.

  1. [02-clojure-exercises.clj](http://0.0.0.0:31415/worksheet.html?filename=worksheets/intro-to-clojure/02-clojure-exercises.clj)
  2. [01-hello-world.clj](http://0.0.0.0:31415/worksheet.html?filename=worksheets/intro-to-anglican/01-hello-world.clj)
  3. [02-gaussian.clj](http://0.0.0.0:31415/worksheet.html?filename=worksheets/intro-to-anglican/02-gaussian.clj)
  4. [03-physics.clj](http://0.0.0.0:31415/worksheet.html?filename=worksheets/intro-to-anglican/03-physics.clj)

There are more exercises available and also accessible through the workbook GUI tab in the upper right hand corner.

Note that these exercise links will not work if you choose a different port than `31415`, however, regardless of port choice the exercise workbooks will be available by name from the GUI tab.  If they do not work at all this suggests a problem in your setup and help should be sought from the presenters in the first instance.  

#### Important: Online [Anglican](http://www.robots.ox.ac.uk/~fwood/anglican/) documentation

Anglican [documentation](http://www.robots.ox.ac.uk/~fwood/anglican/language/index.html) is available online, as are a number of helpful and simple [example programs](http://www.robots.ox.ac.uk/~fwood/anglican/examples/index.html).

## Exercise 2: Deep learning in Pytorch

We will run a [Jupyter](http://jupyter.org/) Python notebook using the Docker container. Start by running (for Linux and Mac):

```
docker run --rm -it -p 8888:8888 -v $PWD:/workspace gbaydin/anglican-infcomp jupyter notebook --ip 0.0.0.0 --no-browser --allow-root
```

On Windows, please replace `-v $PWD:/workspace` with `-v %cd%:/workspace` if you are using the command line (cmd) or with `-v ${PWD}:/workspace` if you are using the PowerShell.

This will start a Jupyter server inside the Docker container. In the terminal, you should see a line such as the following, telling you that the server is running and ready to accept connections:

```
[I 20:09:17.270 NotebookApp] The Jupyter Notebook is running at: http://0.0.0.0:8888/

```

Depending on the terminal you use, you can either right click on the link `http://0.0.0.0:8888/` to open it in a web browser or just directly type it into the address bar a web browser. On Windows, please use the link `http://localhost:8888/` instead.

You should now see a web browser window such as this:

![](/resources/Screenshot_2017-09-04_22-09-44.png?raw=true)

Enter `cscs` as the password to authenticate.

Now please navigate to the exercise worksheets by clicking on `exercises` and then `exercise-2-pytorch`.

![](/resources/Screenshot_2017-09-04_21-32-12.png?raw=true)

Now click on `CSCS-summer-school-2017-exercise-2.ipynb` to start the exercise notebook.

## Exercise 3: Inference Compilation

In the Docker instance change directory into the third programming project

```
cd exercises/exercise-3-inference-compilation
```

#### tmux
Then start a [tmux](https://github.com/tmux/tmux/wiki) session which will let you switch between several programs in the same terminal (this will be needed to run the probabilistic program and the neural network at the same time):

```
tmux
```

Once inside a tmux session, you can:
- Split panes: `Control + b` and `"`
- Switch between panes: `Control + b` and `<arrow key>`
- Exit using `exit` command
- Detach the current session: `Control + b` and `d`

Once detached, you can re-attach to a detached session using `tmux attach`.

#### The Captcha worksheet

Create a new tmux session from the `exercises/exercise-3-inference-compilation` folder by running `tmux` and split the session to two panes using `Control + b` and `"`.
In one of the panes, start a Clojure browser-based repl

```
lein gorilla :port 31415 :ip 0.0.0.0
```

Open a web browser and browse to the Captcha exercise in [src/worksheets/captcha.clj](http://0.0.0.0:31415/worksheet.html?filename=src/worksheets/captcha.clj) (solutions are [here](http://0.0.0.0:31415/worksheet.html?filename=src/worksheets/captcha-solutions.clj) and follow the instructions.

When you work through the notebook, there will be a point in which you will switch to the other tmux pane using `Control + b` and `<arrow key>` to start training the neural network.

#### Downloading the pre-trained compilation artifact

For the solutions worksheet to work, you will need to download a pre-trained artifact for the Captcha probabilistic program from [here](http://www.robots.ox.ac.uk/~tuananh/infcomp-artifacts/pyprob-artifact-20170904-180331) into the `exercises/exercise-3-inference-compilation/resources/` folder.
