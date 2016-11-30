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
 1. Log in to your Bluemix organization and space using the following command:

 ```
 cf login -o <org name> -a api.ng.bluemix.net
 cf ic login
 ```
**Important:** If your organization has multiple spaces, be sure to select the correct space.
 1. Download the sample script files that you'll need to deploy the voice gateway. From the   [sample.voice.gateway.for.watson repository on Github](https://github.com/WASdev/sample.voice.gateway.for.watson), click  **Clone or Download** and then **Download ZIP**. After the download completes, extract the ZIP file. If you prefer you can clone the repository instead.

 1. Go to the **bluemix\** subdirectory of the directory where you extracted the ZIP file or cloned the repository. This directory contains the **pull_tag_push.sh** script file, which is used to pull the voice gateway images from Docker Hub, tag them for your Bluemix repository, and then push them into your Bluemix repository. Note that you must be logged in to both Docker Hub and Bluemix for the script to work.

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
 1. In the same directory, copy one of these files to a file named **docker.env** based on which type of configuration you wish to deploy:
 ```
 docker-self-service.env
 docker-agent-assist.env
 ```

 1. Now open the **docker.env** file, which is where you configure the voice gateway by setting Docker environment variables. For example, specify your user name and password for the Watson Speech to Text, Text to Speech, and Conversation services. For a complete listing of all variables, see [Configuration environment variables](config.md).

 **Note:** Each docker.env includes a minimal configuration required to test the voice gateway. Variables that must be set to run will be left blank in the .env file.

 1. Deploy the containers for the voice gateway to your Bluemix space by running the following command:

 ```
 ./deploy.sh
 ```
 After this operation completes,  you'll have a fully operational voice gateway running on IBM Containers for Bluemix. You can verify that both containers are up and running by running the following command:

 ```
 cf ic ps
 ```
The command outputs a list of the containers in your Bluemix space:

 ```
1b69404e-c2e   registry.ng.bluemix.net/myrepo/voice-gateway-mr:beta.latest 2 weeks ago    Running 16 days ago   169.46.147.48:8080->8080/tcp, 169.46.147.48:16384-16484->16384-16484/udp  voice-gateway-mr
2435372b-8a9   registry.ng.bluemix.net/myrepo/voice-gateway-so:beta.latest 4 weeks ago    Running 16 days ago   169.46.147.46:5060->5060/tcp, 169.46.147.46:8080->8080/tcp, 169.46.147.46:5060->5060/udp  voice-gateway-so
```

#### What to do next

 At this point you'll likely want to set up a SIP trunk to connect to your new voice gateway instance running in Bluemix.
