# Recording

Voice Gateway for Watson provides the ability to record the calls from a *Self Service* session.

**Note:** _Agent Assist_ recording is not available.

### Configure

Depending on how you deployed the Voice Gateway there will be two ways to configure it for recording and how to retrieve the recordings: **[Docker Engine](#docker-engine)** vs **[Bluemix](#bluemix)**

All the recordings are saved onto the `voice-gateway-mr` container under the **/cgw-media-relay/recordings/** directory. Unless you mount the directory to a local directory on your machine or a [Docker Volume](https://docs.docker.com/engine/tutorials/dockervolumes/) the recordings are erased when the container is re-deployed.

##### Docker Engine

Following the sample from [Getting Started: Deploy the voice gateway on Docker Engine](deploydocker.md), you can uncomment the configuration in the `docker-compose.yml` file:
  ```yaml
  media.relay:
    image: ibmcom/voice-gateway-mr:beta.latest
    ...
    environment:
      ...

      # Uncomment the following three lines to enable call recording
      - RTP_RELAY_RECORD=true
    volumes:
      - $PWD/recordings:/cgw-media-relay/recordings
  ```

This will enable recording and mount a local directory where the recordings will be saved. You will need to re-deploy the Voice Gateway in order for the changes to take place.

You can also copy the recordings to a local path:

  ```bash
  docker cp voice-gateway-mr:/cgw-media-relay/recordings .
  ```

#### Bluemix

Continuing the sample from [Getting Started: Deploy the voice gateway on IBM Containers for Bluemix](deploybmix.md).

To enable recordings in Bluemix you need to uncomment the environment variable `RTP_RELAY_RECORD` found in the `docker.env` file and re-deploy the Voice Gateway

```env
# =====================================================
# media.relay config
# =====================================================
...
# Uncomment to turn on recording. All call recordings are stored in the CMR \recordings directory.
RTP_RELAY_RECORD=true
```
In your Bluemix deployment you will need to copy the recordings to a local directory using the `cf ic` command tools:
  ```bash
  cf ic cp voice-gateway-mr:/cgw-media-relay/recordings .
  ```

When deploying to Bluemix, the `deploy.sh` script creates a Docker Volume on IBM Bluemix Containers called `recordingvol` to store your recordings. Therefore, the recordings will remain persistent across multiple deployments. You can see the volume using the `cf ic` command:
```bash
cf ic volume list
```
You can also set the volume name in `docker.env`
  ```env
  # Set to a name of your choosing
  CF_VOLUME_NAME=recordingvol

  ```
#### Recordings File Format
  Recordings will be saved as Wav files under the following format:

- File name: `[session-id]-audio.wav`. For example `1234-55678-audio.wav`
- 8000 Sample rate
- Mono (1 Audio Channel)
- Audio Format: **PCM**

Most default audio players support that audio format making it easy to listen to the recording.

The `session-id` used for the file name maps to the SIP Call ID. To modify the *session id* you need to set the `CUSTOM_SIP_SESSION_HEADER` configuration to point to different SIP header which defines the session id to use. [More info on configuration](config.md#sip-orchestrator-environment-variables).
