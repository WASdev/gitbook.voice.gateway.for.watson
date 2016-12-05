# Recording call audio

You can configure IBM&reg; WebSphere&reg; Connect Voice Gateway for Watson&trade; to record call audio to a WAV file. The recordings capture audio from both the customer caller and the Watson Text to Speech service.

**Note:** Audio recording is available only for self-service agents. Calls to agent assistants cannot be recorded.

### Configuring audio recording

When audio recording is configured, all recordings are saved onto the `voice-gateway-mr` container under the **/cgw-media-relay/recordings/** directory. Because the recordings are saved to the container, recordings are erased when the container is redeployed. To save your recordings, mount the directory to either a local directory on your machine or to a [Docker Volume](https://docs.docker.com/engine/tutorials/dockervolumes/).

How you configure audio recording depends on where you deployed the voice gateway as described in the following sections.

#### Configuring recording for Docker Engine

1. Open the **docker/docker-compose.yml** file that you created when you initially [deployed the voice gateway on Docker Engine](deploydocker.md).

1. Uncomment the following configuration in the `docker-compose.yml` file. This configuration enables recording and mounts a local directory where the recordings will be saved.
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
1. Redeploy the voice gateway so that your changes take effect.

As an alternative to mounting the local directory, you can manually copy recordings to a local path by running the following command:

  ```bash
  docker cp voice-gateway-mr:/cgw-media-relay/recordings .
  ```

#### Configuring recording for IBM Containers for Bluemix&reg;

1. Open the **bluemix/docker.env** file that you created when you initially [deployed the voice gateway on IBM Containers for Bluemix](deploybmix.md).
1. To enable recording, uncomment the  `RTP_RELAY_RECORD` environment variable.

```env
# =====================================================
# media.relay config
# =====================================================
...
# Uncomment to turn on recording. All call recordings are stored in the CMR \recordings directory.
RTP_RELAY_RECORD=true
```
1. Redeploy the voice gateway.

To copy the recordings to a local directory, run the `cf ic` command tools:
  ```bash
  cf ic cp voice-gateway-mr:/cgw-media-relay/recordings .
  ```

When the voice gateway is deployed to IBM Containers for Bluemix, the **deploy.sh** script creates a Docker Volume on IBM Containers called `recordingvol`, which stores your recordings so that they persist when you redeploy the gateway.

To view the volume, run the following `cf ic` command:
```bash
cf ic volume list
```

To change the volume name, set the `CF_VOLUME_NAME` variable in your **docker.env** file:
  ```env
  # Set to a name of your choosing
  CF_VOLUME_NAME=recordingvol

  ```

#### Recording file format

Recordings are saved as WAV files in the following format, which is supported by most default audio players:

- File name: `[session-id]-audio.wav`. For example, `1234-55678-audio.wav`
- Sample rate: 8000 Hz
- Channels: Mono (1 Audio Channel)
- Encoding: PCM

The `session-id` used for the file name maps to the SIP Call ID. To modify the `session id`, set the `CUSTOM_SIP_SESSION_HEADER` configuration to point to different SIP header that defines the session ID to use. For more information about configuration, see [SIP Orchestrator configuration environment variables](config.md#sip-orchestrator-environment-variables).
