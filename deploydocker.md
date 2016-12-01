# Deploying Voice Gateway for Watson on Docker Engine

These instructions are for setting up IBM&reg; WebSphere&reg; Connect Voice Gateway for Watson&trade on your own Docker engine or an on-premises installation. For cloud deployments, see [Deploying the voice gateway on IBM Containers for Bluemix](deploybmix.md).

**Using Docker Machine?** Configuring the gateway on [Docker Machine](https://docs.docker.com/machine/overview/) is slightly different, as noted in the following steps. Windows users should note that Docker for Windows requires 64-bit Windows 10 Pro, so on earlier version of Windows, you must install Docker Machine.

#### Prerequisites

* [Install Docker Engine](https://docs.docker.com/engine/installation/) on the host where you plan to run the gateway. Docker Engine provides a lightweight runtime for the containers that make up the voice gateway, the SIP Orchestrator and Media Relay.

* Create a [Docker Hub](https://hub.docker.com/) account so that you can access the Docker images of the SIP Orchestrator and the Media Relay.

* Sign up for IBM Bluemix and create the following Watson services:
 * [Watson Speech to Text](https://console.ng.bluemix.net/catalog/services/speech-to-text)
 * [Watson Text to Speech (self-service only)](https://console.ng.bluemix.net/catalog/services/text-to-speech)
 * [Watson Conversation (self-service only)](https://console.ng.bluemix.net/catalog/services/conversation)

 **Important:** Be sure to [program your Conversation service](https://www.ibm.com/watson/developercloud/doc/conversation/t_dialog_build.shtml) with at least a catch-all response so that you can test the gateway.

* If you plan to deploy the voice gateway behind a firewall and you want to connect to that gateway through a SIP trunk or SIP client that is outside your firewall, see [Firewall considerations](#firewall-considerations).

#### Installing and running the voice gateway

 1. **Docker Machine only:** Before you can log in to Docker, run this command from the command line to set the shell to point to the Docker Engine:

    ```@FOR /f "tokens=*" %i IN ('docker-machine env default --shell cmd') DO @%i```

 1. Open a terminal window and log in to Docker Hub:

    ```
    docker login
    ```
 1. Pull the latest Docker images of the SIP Orchestrator (`voice-gateway-so`) and Media Relay (`voice-gateway-mr`):

    ```
  docker pull ibmcom/voice-gateway-so:beta.latest

  docker pull ibmcom/voice-gateway-mr:beta.latest

    ```

 1. Go to the [sample.voice.gateway.for.watson Github repository](https://github.com/WASdev/sample.voice.gateway.for.watson) and clone the repository. In the cloned repository on your machine, go to **docker** directory, where you will find the following two files:
  ```
  docker-compose-self-service.yml
  docker-compose-agent-assist.yml
  ```

  These sample files are preconfigured for each implementation and point to the latest beta images of the voice gateway. You'll need to modify the file for your implementation type to include your Watson service credentials.

  1. Copy the file related to your type of implementation to a new file named **docker-compose.yml**. Keep the file in the same directory.
  1. In the new **docker-compose.yml** file, add your credentials for your Watson services. Each Docker environment variable that you need to fill in is blank.

      **Remember:** Agents assistants require only the Speech to Text service. Self-service agents require that service, plus the Text to Speech and Conversation services.

      No other configuration is required to get a basic voice gateway running.

  **Important:** If you are deploying to a public cloud, it is highly recommended that you also define the whitelist variables to prevent Denial of Service attacks. By default, these variables are commented out in the Compose files.

 1. In the directory where you modified the docker-compose.yml file, create a **.env** file and set the `EXTERNAL_IP` to localhost using the following commands. These commands assume you are using **vi** for editing, but you can use any editor:

   ```
 touch .env
 vi .env
   ```
    At the top of the .env file, add this line:

    `EXTERNAL_IP=<IP address of Docker Engine>`

    **Docker Machine only**: Because the voice gateway will be running in a virtual machine, you'll need to determine the IP address of the VM and set it in the .env file. Set it to the IP returned from the `docker-machine ip` command.

 1. Create and start up the containers by running the following command:

    ```
 docker-compose up
    ```
  If you see the following error on startup, you likely have another application listening on port 5060:
  ```Bind for 0.0.0.0:5060 failed: port is already allocated```

  Shut down any conflicting applications, and try the command again.

#### Firewall considerations

To deploy the voice gateway behind a firewall and then connect to that gateway through a SIP trunk or SIP client that is outside your firewall, you must turn on several ports to access the Docker containers that are hosting the voice gateway. You can view the ports and IP addresses exported from the voice gateway Docker container by running the following command:

   ```
    docker ps
   ```
Remember that the voice gateway is processing SIP and RTP media streams on one side and connecting out to Watson services on the other. You must open all of the following IPs and ports in your firewall:

| Purpose | IP value | Port value | Direction | Protocol |
| -------------- | ------ | ----------- | ---------- | -----------|
| Audio to Voice Gateway | IP address of CMR | 16384-16394 (or however configured)| Inbound | RTP over UDP |
| SIP to Voice Gateway | IP address of CSO | 5060| Inbound | UDP or TCP |
| SIP from Voice Gateway (if UDP) | IP address of SIP Trunk | 5060| Outbound | UDP only |
| Audio from Voice Gateway | IP address of SIP Trunk | Defined by SIP Trunk | Outbound | RTP over UDP |
| Connect to Watson services | Configured Watson endpoints | 443| Outbound | TCP (Web Sockets and REST) |

Note that typically you only have to configure inbound access. Outbound is typically open.
