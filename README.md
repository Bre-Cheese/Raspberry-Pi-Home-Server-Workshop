# Raspberry Pi Home Server Workshop

## Prerequisites
- Raspberry Pi with monitor and keyboard connected
- Raspbian OS installed

## Workshop Flow
1. **Setup**: Boot up your Raspberry Pi and open the terminal.
2. **Docker Installation**: The installation can be done directly through the Terminal. Run the following commands:
    - Update your package index:
      ```bash
      sudo apt update
      ```
    - Install required packages:
      ```bash
      sudo apt install apt-transport-https ca-certificates curl software-properties-common
      ```
    - Add Docker’s official GPG key:
      ```bash
      curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
      ```
    - Add the Docker repository:
      ```bash
      echo "deb [arch=armhf] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
      ```
    - Install Docker:
      ```bash
      sudo apt update
      sudo apt install docker-ce
      ```
3. **Portainer Installation**: Once Docker is successfully installed, run the following to set up Portainer:
    ```bash
    sudo docker volume create portainer_data
    sudo docker run -d -p 9000:9000 --restart always --name portainer --volume /var/run/docker.sock:/var/run/docker.sock --volume portainer_data:/data portainer/portainer-ce
    ```
4. **Creating Your First Stack**: Open the Portainer interface using your Raspberry Pi's IP address on port 9000.
5. **Mini-Game**: Install a mini-game as a Docker container for practice.
6. **Scaling Applications**: Use Docker's scaling features to adjust the number of running instances.
7. **Troubleshooting**: Refer to logs and command outputs if issues arise.
8. **Safety**: Ensure your Raspberry Pi is secured and routinely backed up.
9. **Provided Sources**: Utilize the repositories and links provided in the original workshop docs for further reading and assistance.

## Conclusion
This workshop aims to provide a comprehensive guide to setting up a home server directly from your Raspberry Pi. Follow each step precisely to ensure a smooth installation and configuration process.