# Beta limitations for Voice Gateway for Watson

The beta for IBM&reg; WebSphere&reg; Connect Voice Gateway for Watson&trade; has the following limitations:
* Full orchestrated high availability (HA) is not supported.
* Recording is supported only for self-service agents.
* Multi-tenancy is not supported.
* Security limitations:
    * Only HTTP connections are supported. HTTPS is not supported.
    * Proxy authentication is not supported for the SIP Orchestrator.
    * Known issue: The SIP Orchestrator erroneously displays the CWPKI0033E error when the `SSL_CLIENT_PKCS12_FILE` environment variable is not configured. If `SSL_CLIENT_PKCS12_FILE` is not configured, you can safely ignore this error.

        ```
        CWPKI0033E: The keystore located at ${env.SSL_CLIENT_PKCS12_FILE} did not load
        because of the following error: KeyStore "${env.SSL_CLIENT_PKCS12_FILE}" does not exist.
        ```
