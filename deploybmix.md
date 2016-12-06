# Deploying Voice Gateway for Watson on IBM Containers for Bluemix

These instructions are for setting up IBM&reg; WebSphere&reg; Connect Voice Gateway for Watson&trade; on IBM&reg; Containers for Bluemix&reg;. To deploy the voice gateway on your own Docker engine or an on-premises installation, see [Deploying the voice gateway on Docker Engine](deploydocker.md).

#### Prerequisites

* [Install Docker Engine](https://docs.docker.com/engine/installation/) on the host where you plan to deploy the voice gateway from. Docker Engine provides a local repository that will hold the voice gateway images before being retagged and pushed to Bluemix.

 Also install the [Cloud Foundry tools](https://console.ng.bluemix.net/docs/containers/container_cli_cfic_install.html), which enable you to run native Docker CLI commands to manage your containers on Bluemix.

* Create a [Docker Hub](https://hub.docker.com/) account so that you can access the Docker images of the SIP Orchestrator and the Media Relay.

* Sign up for IBM Bluemix and create the following Watson services:
 * [Watson Speech to Text](https://console.ng.bluemix.net/catalog/services/speech-to-text)
 * [Watson Text to Speech (self-service only)](https://console.ng.bluemix.net/catalog/services/text-to-speech)
 * [Watson Conversation (self-service only)](https://console.ng.bluemix.net/catalog/services/conversation)

 **Important:** Be sure to [program your Conversation service](https://www.ibm.com/watson/developercloud/doc/conversation/t_dialog_build.shtml) with at least a catch-all response so that you can test the gateway.

#### Installing and running the voice gateway

 1. Open a terminal window and log in to Docker Hub:

 ```
  docker login
 ```
 1. Log in to your Bluemix account space using the following command, which will prompt you for your organization and space:

 ```
 cf login -a api.ng.bluemix.net
 cf ic login
 ```

 1. Clone or download sample script files from the [sample.voice.gateway.for.watson repository on GitHub](https://github.com/WASdev/sample.voice.gateway.for.watson). You'll need these files to deploy the voice gateway.

 1. In the cloned or downloaded repository, go to the **bluemix** subdirectory. This directory contains the **pull_tag_push.sh** script file, which is used to pull the voice gateway images from Docker Hub, tag them for your Bluemix repository, and then push them into your Bluemix repository. Note that you must be logged in to both Docker Hub and Bluemix for the script to work.

 Run the script by running the following command:

 ```
 ./pull_tag_push.sh
 ```
You can verify that the images now exist in your Bluemix repository by running this command:

 ```
 cf ic images
 ```
The command outputs a list of images similar to the following example:

 ```
 registry.ng.bluemix.net/myrepo/voice-gateway-mr beta.latest  d2c2c3f446e8   2 days ago  212.4 MB
 registry.ng.bluemix.net/myrepo/voice-gateway-so beta.latest  01269ea75e08   3 days ago  341.3 MB
 ```
 1. In the same directory, copy one of the following sample files to a new file named **docker.env**. These sample files are preconfigured with the minimum configuration for each implementation:
 ```
 docker-self-service.env
 docker-agent-assist.env
 ```

 1. Open the **docker.env** file that you just created, which is where you configure the voice gateway by setting Docker environment variables. Fill in any blank configuration variables, such as the user name and password for your Watson services on Bluemix. For agent assistants, also configure the [MQTT broker and topic path](rttconfig.md).

 Each sample file includes the minimum configuration required to test the voice gateway. For a complete listing of all variables, see [Configuration environment variables](config.md).

 1. Deploy the containers for the voice gateway to your Bluemix space by running the following command:

 ```
 ./deploy.sh
 ```
 After this operation completes,  your voice gateway is fully operational on IBM Containers for Bluemix. You can verify that both containers are up and running by running the following command:

 ```
 cf ic ps
 ```
The command outputs a list of the containers in your Bluemix space:

 ```
1b69404e-c2e   registry.ng.bluemix.net/myrepo/voice-gateway-mr:beta.latest 2 weeks ago    Running 16 days ago   169.46.147.48:8080->8080/tcp, 169.46.147.48:16384-16484->16384-16484/udp  voice-gateway-mr
2435372b-8a9   registry.ng.bluemix.net/myrepo/voice-gateway-so:beta.latest 4 weeks ago    Running 16 days ago   169.46.147.46:5060->5060/tcp, 169.46.147.46:8080->8080/tcp, 169.46.147.46:5060->5060/udp  voice-gateway-so
```
