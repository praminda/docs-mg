# HTTP 2.0

WSO2 API Microgateway is upgraded to support HTTP 2.0 (HTTP/2) together with HTTP/1.1 as the incoming and outgoing transport protocol. WSO2 API Microgateway is able to process requests faster and simpler with HTTP/2 enabled. For more information on HTTP/2 and its benefits, refer to the [HTTP/2 homepage](https://http2.github.io/) .

WSO2 API Microgateway, when configured to communicate on HTTP2, is able to switch protocols from  HTTP/1.1 to HTTP/2 and vice versa.

-   [Identifying a HTTP 2.0 connection](#HTTP2.0-IdentifyingaHTTP2.0connection)
-   [Enabling HTTP 2.0 on API Microgateway](#HTTP2.0-EnablingHTTP2.0onAPIMicrogateway)
-   [How it works](#HTTP2.0-Howitworks)
    -   [User story 1 - Both client and backend supports HTTP 2.0](#HTTP2.0-Userstory1-BothclientandbackendsupportsHTTP2.0)
    -   [User story 2 - The client supports HTTP 2.0 but the backend does not support HTTP 2.0](#HTTP2.0-Userstory2-TheclientsupportsHTTP2.0butthebackenddoesnotsupportHTTP2.0)
    -   [User story 3 - The client does not support HTTP/2 but the backend supports HTTP 2.0](#HTTP2.0-Userstory3-TheclientdoesnotsupportHTTP/2butthebackendsupportsHTTP2.0)

#### Identifying a HTTP 2.0 connection

A HTTP/2 request contains three special headers “ Connection ”, “ Upgrade ” and “ HTTP2-Settings ”.

**Example : HTTP2 Request Headers**

``` java
    upgrade: h2c
    HTTP2-Settings: AAEAABAAAAIAAAABAAN_____AAQAAP__AAUAAEAAAAYAACAA
    connection: HTTP2-Settings,upgrade  
```

    A HTTP/2 response contains 3 special headers “ Connection ”, “ Upgrade ” and  "HTTP/1.1 101 Switching Protocols"

    **Example : HTTP2 Response Headers**

``` java
    HTTP/1.1 101 Switching Protocols
    connection: upgrade
    upgrade: h2c
```

    #### Enabling HTTP 2.0 on API Microgateway

    1.  Create a Microgateway project.

    1.  Navigate to a preferred workspace folder using the command line.
    This location is used to run the Microgateway commands and to generate Microgateway artifacts.
    2.  Create a project using the command given below.

    **Format**

        ``` java
            micro-gw init <project_name> 
        ```

            **Example**

        ``` java
            micro-gw init petstore  
            Project 'petstore' is initialized successfully.
        ```

            2.  Add the API (open API definition) to the project.
            Navigate to the `           /petstore/api_definitions          ` directory and add the API definition(s) to this directory. A sample open API definition can be found [here](https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml) .

            3.  Build the Microgateway distribution for the project using the following command:

            **Format**

    ``` java
        micro-gw build <project_name>
    ```

        **Example**

    ``` java
        micro-gw build petstore
                Build successful  for the project - petstore
    ```

        After the above command is executed, an executable file ( `             /petstore/target/petstore.balx            ` ) is created to expose the API via WSO2 API Microgateway.

        4.  Navigate to the `          <MGW-RUNTIME-HOME>/conf         ` folder and open the `          micro-gw.conf         ` file.
        5.  Locate the **\[http2\]** tag in the file and change the **enabled** value to **true** .

    ``` java
        [http2]
        enable=true
    ```

        6.  Execute the following command to start WSO2 API Microgateway with new configurations.

        -   [**Format**](#format-gateway-start)
        -   [**Example**](#example-gateway-start)

        **Format**

    ``` java
        gateway <path-to-executable-file>
    ```

        **Example**

    ``` java
        gateway /Users/kim/petstore/target/petstore.balx
    ```

        7.  Invoke the petstore API using a valid JWT token.

        #### How it works

        The following user stories explains how the API Microgateway works when HTTP/2 is enabled.

        !!! note
        -   The backend service in the following scenarios communicate on HTTP/2 **without** SSL.
        -   The WSO2 API Microgateway server in the following scenarios communicate on HTTP/2 **with** SSL.
        -   "h2" in the following images refers to HTTP/2 .

##### User story 1 - Both client and backend supports HTTP 2.0

![](https://lh6.googleusercontent.com/ssCDyoDTIp6qUEEjpPNzz8e7MRdb1dT7wvAUjcKOBFpnFCz5d8prd5Vr5FHGOgYyysQc1TZRVDu3T55sUGO4zhhS9dkFNfJtMeJ8Fl3E2biSOjvGF7X7fw6XpuzlpWABYcLcqHp2){width="461" height="171"}

-   The user sends an HTTP/2 request to WSO2 API Microgateway.

-   WSO2 API Microgateway forwards the received HTTP/2 request to the backend  as an HTTP/1.1 request after including the HTTP/2 headers to determine the protocol of the connection between WSO2 API Microgateway and the backend.

-   The backend accepts the request and sends a response back to WSO2 API Microgateway with the connection upgrade header as well as the "HTTP/1.1 101 Switching Protocols" header. This indicates that the backend supports HTTP/2. As a result, a HTTP/2 connection between WSO2 API Microgateway and the backend is established.

-   The backend sends an HTTP/2 response with the payload back WSO2 API Microgateway.

-   The Microgateway forwards that HTTP/2 response back to the client.

##### User story 2 - The client supports HTTP 2.0 but the backend does not support HTTP 2.0

![](https://lh5.googleusercontent.com/re11nNRfMioqa8APJv3QrCoyzHVnyPByc0JXGFhAGkFsT39TPH6m6dxOe9TuN1yK95nBnpvNGnEBqeM8f1s5dVIXVGXjZwhOxSm_2pfdQZ-h55Qhz61Es4e69ImSvUICj4OQDjTC){width="461" height="171"}

-   The user sends an HTTP/2 request to WSO2 API Microgateway.

-   WSO2 API Microgateway forwards the received HTTP/2 request to the backend  as an HTTP/1.1 request after including the HTTP/2 headers to determine the protocol of the connection between the API Microgateway and the backend.

-   Since the backend does not support the HTTP/2, it does not upgrade the connection into HTTP/2.

-   The backend sends an HTTP/1.1 response  with the payload to WSO2 API Microgateway.

-   WSO2 API Microgateway then upgrades it to an HTTP/2 response by adding HTTP/2 headers and sends it to the client.

##### User story 3 - The client does not support HTTP/2 but the backend supports HTTP 2.0

![](https://lh5.googleusercontent.com/qGeWfwIp6fat_ZF8jSGt0AlNaImlqNmuQ9oTHKsjjVRUnMvPYtp4LK1L_Lb672GHSzEOWoGiVtoQFlBkD8hROrTB1WAigU1KWA04vueTFvTjllZFnqWiW36_Dac6Yz5XeOJNbPOe){width="461" height="171"}

-   The user sends an HTTP/1.1 request to WSO2 API Microgateway.

-   WSO2 API Microgateway sends the request as an HTTP/1.1 request after including the HTTP/2 headers to determine the protocol of the connection between WSO2 API Microgateway and the backend.

-   The backend accepts the request and sends an response back to the client with connection upgrade header as well as the "HTTP/1.1 101 Switching Protocols" header. This indicates that the backend supports HTTP/2. As a result, a HTTP/2 connection between WSO2 API Microgateway and the backend is established.

-   The backend sends an HTTP/2 response with the payload to the API Microgateway.

-   WSO2 API Microgateway upon receiving an HTTP/2 response, proceeds to downgrade the response to an HTTP/1.1 response removing the HTTP/2  headers and forwards it to the client.

