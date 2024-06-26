# ROS2 Container

[![version-image]][release-url]
[![release-date-image]][release-url]
[![license-image]][license-url]

ROS2 Container is a Docker container for ROS2 development and deployment.

- GitHub: [https://github.com/entelecheia/ros2-container][repo-url]

## Building a ROS2 Execution Environment with Docker

This tutorial explains how to build a ROS2 execution environment using Docker. We will use the [dotfiles](https://dotfiles.entelecheia.ai/) and [ros2-container](https://github.com/entelecheia/ros2-container) projects to set up the development environment, and learn how to run GUI applications from a remote computer using SSH and X11 Forwarding with MobaXterm (Windows) and XQuartz (macOS).

### Setting up the ROS2 Development Environment with dotfiles

The [Dotfiles project](https://dotfiles.entelecheia.ai/) is a tool that helps maintain and manage a consistent development environment. Follow these steps to install and initialize dotfiles:

#### Installing dotfiles

Use the `wget` or `curl` command to download and run the installation script.

- Using `wget`:

  ```sh
  sh -c "$(wget -qO- https://dotfiles.entelecheia.ai/install)"
  ```

- Using `curl`:
  ```sh
  sh -c "$(curl -fsSL https://dotfiles.entelecheia.ai/install)"
  ```

When the installation script runs, the dotfiles repository will be cloned to your computer and the necessary settings will be made.

#### Initializing dotfiles

After the installation is complete, the initialization process will start automatically. During this process, the settings defined in dotfiles will be applied to your system to create a consistent development environment.

If you need to reinitialize manually, run the following command:

```sh
dotu init
```

This command updates your system with the latest settings from dotfiles, keeping your changes and environment synchronized.

### Building a ROS2 Execution Environment with ros2-container

The [ros2-container](https://github.com/entelecheia/ros2-container) project helps build a ROS2 execution environment using Docker. Follow these steps to clone the project, build the Docker image, and run the container.

#### Cloning the ros2-container repository

```bash
cd ~/workspace/projects
git clone https://github.com/entelecheia/ros2-container.git
```

#### Building and Running the Docker Image

1. Build the Docker image:

   ```bash
   make docker-build
   ```

   The `docker.foxy-dsr.env` file includes various configuration options and environment variables. The `docker-compose.foxy-dsr.yaml` file uses these variables to customize the behavior of the services. This is a common practice when you want to set different configurations for development, testing, and production environments. The `Dockerfile.foxy-dsr` file uses these variables to customize the Docker build. These files are automatically generated by Copier when you run the `copier copy` command.

2. Run the Docker container:

   ```bash
   make docker-run
   ```

   This command starts a Docker container using the image built in the previous step. Inside the container, a bash script that starts the application will be executed.

### Using SSH and X11 Forwarding with MobaXterm on Windows

[MobaXterm](https://mobaxterm.mobatek.net/download.html) is a cool terminal emulator with a ton of features. The Home Edition is free for use. MobaXterm is available to download.

After installation, open MobaXterm. It may be a little slow on the first launch. If you get a prompt for firewall access, select Allow access.

Start a new session by selecting Session.

In the MobaXterm window, select SSH and enter the computer you want to connect to in the Remote host field. You can check the Specify username box and enter your username as well. Leave the port set to 22.

Select the Advanced SSH settings tab and make sure the X11-Forwarding checkbox is selected. Leave the rest of the settings at their defaults. Select OK.

MobaXterm will start a new SSH session in a tab and prompt for your password. Once authenticated, MobaXterm will display information about the session and the connected computer. You can now run commands and open programs normally. MobaXterm will add "@computername" to the title bar of any windows that pop up to indicate that they are running from an X session.

From here, you can quickly use the saved user session to reconnect again. The tabs on the left have useful tools, games, and more. The SFTP tab also has an SFTP file browser for quick and easy file transfers between your Michigan Tech home drive and the computer running MobaXterm.

Now you can use MobaXterm to run ROS2 GUI applications from a remote computer using SSH and X11 Forwarding. Follow these steps to connect to a Docker container in MobaXterm and run GUI applications using X11 Forwarding.

1. Start a new session in MobaXterm:
   Open MobaXterm and select Session to start a new session. Select SSH and enter the IP address or hostname of the remote host where the Docker container is running. Leave the port set to 22.

2. Configure X11 Forwarding:
   Select the Advanced SSH settings tab and check the X11-Forwarding checkbox. Leave the rest of the settings at their defaults. Select OK.

3. Start the SSH session:
   MobaXterm will start a new SSH session and prompt for your password. Once authenticated, you will be connected to the terminal of the remote host.

4. Run the Docker container:
   In the SSH session, run the following command to start the Docker container:

   ```bash
   docker run -it --rm \
     -e DISPLAY=$DISPLAY \
     -v /tmp/.X11-unix:/tmp/.X11-unix \
     ghcr.io/entelecheia/ros2:latest-foxy-dsr \
     bash
   ```

   This command maps the DISPLAY environment variable and the X11 socket to run the Docker container.

5. Run GUI applications:
   Inside the Docker container, run GUI applications. For example, to run RViz, enter the following command:

   ```bash
   rviz2
   ```

   The RViz window will appear in MobaXterm. The title bar will include "@computername" to indicate that it is running from an X session.

### Using SSH and X11 Forwarding with XQuartz on macOS

To use X11 Forwarding from a remote Docker container on macOS, you need to install and configure XQuartz. Follow these steps to install XQuartz and run ROS2 GUI applications using SSH and X11 Forwarding.

1. Install XQuartz:
   XQuartz provides the X11 server for macOS. Download and install XQuartz from https://www.xquartz.org/.

   ```bash
   brew install --cask xquartz
   ```

2. Launch XQuartz:
   After installation, open XQuartz from the Applications folder.

3. Allow network connections:
   In the XQuartz preferences, go to the Security tab and check the option "Allow connections from network clients".

4. Run the xhost command:
   Open a terminal and run the following command to allow connections to the X server from any host:

   ```bash
   xhost +
   ```

   To allow connections from a specific IP address, run:

   ```bash
   xhost + [IP_ADDRESS]
   ```

5. Start the SSH session:
   In the terminal, run the following command to connect to the remote host via SSH:

   ```bash
   ssh -X username@remote_host
   ```

   The `-X` option enables X11 Forwarding. `username` is the username on the remote host, and `remote_host` is the IP address or hostname of the remote host.

6. Run the Docker container:
   In the SSH session, run the following command to start the Docker container:

   ```bash
   docker run -it --rm \
     -e DISPLAY=host.docker.internal:0 \
     -v /tmp/.X11-unix:/tmp/.X11-unix \
     ghcr.io/entelecheia/ros2:latest-foxy-dsr \
     bash
   ```

   The `-e DISPLAY=host.docker.internal:0` sets the DISPLAY environment variable inside the container to point to the host's X11 server. The `-v /tmp/.X11-unix:/tmp/.X11-unix` maps the X11 unix socket from the host to the container.

7. Run GUI applications:
   Inside the Docker container, run GUI applications. For example, to run RViz, enter the following command:

   ```bash
   rviz2
   ```

   The RViz window will appear on macOS.

You can now use XQuartz to run ROS2 GUI applications from a remote Docker container on macOS using SSH and X11 Forwarding.

## Docker Usage

### Prerequisites

- Docker
- Docker Compose
- NVIDIA Docker (for GPU support, Optional)

### Volumes

The `docker-compose.foxy-dsr.yaml` file specifies several volumes that bind mount directories on the host to directories in the container. These include the cache, the workspace directory, and the scripts directory. Changes made in these directories will persist across container restarts.

### Troubleshooting

If you encounter any issues with this setup, please check the following:

- Make sure Docker and Docker Compose are installed correctly.
- Make sure NVIDIA Docker is installed if you're planning to use GPU acceleration.
- Ensure the environment variables in the `docker.foxy-dsr.env` file are correctly set.
- Check the Docker and Docker Compose logs for any error messages.

### Environment Variables

In Docker, environment variables can be used in the `docker-compose.foxy-dsr.yaml` file to customize the behavior of the services.

The `docker-compose` command has an `--env-file` argument, but it's used to set the environment variables for the services defined in the `docker-compose.yaml` file, not for the `docker-compose` command itself. The variables defined in the `--env-file` will not be substituted into the `docker-compose.yaml` file.

However, the environment variables we set in the `.docker/docker.foxy-dsr.env` file are used in the `docker-compose.app.yaml` file. For example, the `$BUILD_FROM` variable is used to set the base image for the Docker build. Therefore, we need to export these variables to the shell environment before running the `docker-compose` command.

This method also allows us to use shell commands in the variable definitions, like `"$(whoami)"` for the `USERNAME` variable, which wouldn't be possible if we used the `--env-file` argument. Shell commands in the `.env` file are not evaluated.

#### Files for Environment Variables

- `.docker/docker.common.env`: Common environment variables for all Docker images.
- `.docker/docker.foxy-dsr.env`: Environment variables for the Docker image.
- `.env.secret`: Secret environment variables that are not committed to the repository.

## Changelog

See the [CHANGELOG] for more information.

## Contributing

Contributions are welcome! Please see the [contributing guidelines] for more information.

## License

This project is released under the [MIT License][license-url].

<!-- Links: -->

[license-image]: https://img.shields.io/github/license/entelecheia/ros2-container
[license-url]: https://github.com/entelecheia/ros2-container/blob/main/LICENSE
[version-image]: https://img.shields.io/github/v/release/entelecheia/ros2-container?sort=semver
[release-date-image]: https://img.shields.io/github/release-date/entelecheia/ros2-container
[release-url]: https://github.com/entelecheia/ros2-container/releases
[repo-url]: https://github.com/entelecheia/ros2-container
[changelog]: https://github.com/entelecheia/ros2-container/blob/main/CHANGELOG.md
[contributing guidelines]: https://github.com/entelecheia/ros2-container/blob/main/CONTRIBUTING.md

<!-- Links: -->
