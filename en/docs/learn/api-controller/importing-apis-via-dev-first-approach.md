# Importing APIs Via Dev First Approach

WSO2 API Controller, **apictl** allows to create and deploy APIs without using WSO2 API Publisher Portal. You can use this feature to create an API **from scratch** or **using an existing Swagger or Open API specification** and then deploy it to the desired API Manager environment.

!!! info
    **Before you begin...** 

    -   Make sure WSO2 API Manager CTL Tool is initialized and running, if not, follow the steps in [Download and Initialize the CTL Tool]({{base_path}}/learn/api-controller/getting-started-with-wso2-api-controller/#download-and-initialize-the-ctl-tool).

    -   Make sure you already have added an environment using the CTL tool for the API Manager environment you plan to import the API to. 

        If not, follow the steps in [Add an Environment]({{base_path}}/learn/api-controller/getting-started-with-wso2-api-controller#add-an-environment).

## Initialize an API Project

1. Open a terminal window and navigate to the path you need to create the project.

2. You can follow either of the following ways to generate the project.

    1.   **From Scratch**
        -   Command
            ```bash
            apictl init <Project Path>   
            ```
            ```bash
            apictl init <Project Path> --definition <API definition template file>  --force=<force create project>
            ```

            !!! example
                ```bash
                apictl init SampleAPI                
                ```
                ```bash 
                apictl init SampleAPI --definition definition.yaml --force=true                
                ```

        -   Response    
            ```go
            Initializing a new WSO2 API Manager project in /home/user/work/SampleAPI
            Project initialized
            Open README file to learn more
            ```
        
            !!! info
                In this case, the project artifacts are generated according to a set of predefined templates. Therefore, after initializing the project, you need to go and edit the project artifacts manually.     


    2.   **From OpenAPI/Swagger Specification**  
        You can use a Swagger2 and OpenAPI3 specification to generate an API. The file format should be YAML or JSON.

        -   Command
            ```bash
            apictl init <Project Path> --oas <Path to API specification>
            ```
            ```bash
            apictl init <Project Path> --oas <Path to API specification> --definition <API definition template file> --force=<force create project>
            ```

            !!! example
                ```bash
                apictl init Petstore --oas petstore.yaml
                ```
                ```bash
                apictl init Petstore --oas https://petstore.swagger.io/v2/swagger.json
                ```
                ```go
                apictl init Petstore --oas petstore.yaml --definition definition.yaml --force=true
                ```

        -   Response

            ```go
            Initializing a new WSO2 API Manager project in /home/user/work/PetstoreAPI
            Project initialized
            Open README file to learn more
            ```

            !!! info
                When you initialize an API project using an OpenAPI specification, the CTL Tool will automatically read the OpenAPI definition and populate a certain set of configs in the API definition file, `api.yaml`.     

            !!! info
                **Flags:**  
                    
                -   Optional :  
                        `--definition` or `-d` : Provide a YAML definition of API  
                        `--oas` : Provide an OpenAPI specification file/URL for the API   
                        `--force` or `-f` : To overwrite directory and create project 


     A project folder with the following default structure will be created in the given directory.

    ``` java
    ├── api_params.yaml
    ├── Docs
    │    └── FileContents
    ├── Image
    ├── Meta-information
    │    ├── api.yaml
    │    └── swagger.yaml
    ├── README.md
    └── Sequences
        ├── fault-sequence
        ├── in-sequence
        └── out-sequence
    ```

    <table>
        <thead>
            <tr class="header">
                <th>Sub Directory/File</th>
                <th>Description</th>
            </tr>
        </thead>
        <tbody>
            <tr class="odd">
                <td><code>api.yaml</code></td>
                <td>The specification of the created API.</td>
            </tr>
            <tr class="even">
                <td><code>swagger.yaml</code></td>
                <td>The Swagger file generated when the API is created.</td>
            </tr>
            <tr class="odd">
                <td><code>api_params.yaml</code></td>
                <td>Contains environment-specific details.</td>
            </tr>
        <tr class="even">
            <td><pre><code>Sequences
        ├── fault-sequence
        ├── in-sequence
        └── out-sequence</code></pre>
            </td>
            <td>To add custom sequences, save them in XML format and add them to the corresponding folder. For example, to add a custom in-sequence, save the custom sequence as <code>       SampleSequence.xml</code> and add it to the <code>Sequences/in-sequence/</code>directory.</td>
        </tr>
        <tr class="even">
        <td><pre><code>Docs
      |── FileContents</code></pre>
        </td>
        <td>Contains the documents. To add a documentation, add them to the <code>Docs/FileContents/</code>directory.</td>
        </tr>
        </tbody>
    </table>

    !!! note 
        **Changing the Default API Template**

        When you create an API Project, APIs are generated using the default template specified in `<USER_HOME>/.wso2apictl/default_api.yaml` file. Following is the default template used to generate API Projects.  

        ```bash
        id:
            providerName: admin
            version: 1.0.0
            apiName:
        context:
        type: HTTP
        availableTiers:
            - name: Unlimited
        status: CREATED
        visibility: public
        transports: http,https
        productionUrl: http://localhost:8080
        sandboxUrl: http://localhost:8081
        ```

        This file contains the same notation as the `<API-Project>/Meta-information/api.yaml`. Organization-specific common API related details can be put into this template file and shared across developers.

        To further finetune API creation, a custom API Definition file can be used. If you need to use a specific definition file when generating a certain API project, use the `--definition` or `-d` flag along with `apictl init` command. The custom definition file should be in YAML format only.

        **Generate APIs with Dynamic Data**

        When initializing an API Project, the CTL is capable of detecting environment variables in the default definition file or in the provided custom definition file and create the `api.yaml` with the dynamic data. A sample custom definition file is shown below.

        ```bash
        id:
            providerName: admin
            apiName: $APINAME
            version: $APIVERSION
        ```

        When executing the `apictl init` command, the CLI tool automatically injects the values to the API definition. If an environment variable is not set, the tool will fail and request a set of required environment variables on the system.

        In runtime, the CTL tool will automatically inject the environment variable values to the artifacts in the API project.

        !!! tip
            To make this work you will need to set up required environment variables according to your OS. In a Linux/Unix environment, it can be done using
            ```bash
            export APINAME=MyAPI
            export APIVERSION=1.0.0
            ```      

4. Open the `<API Project>/Meta-information/api.yaml` file. You can edit the mandatory configurations listed below.

    | Field                                        | Description                                             |
    |----------------------------------------------|---------------------------------------------------------|
    | `apiName`| The name of API without spaces.                         |
    | `context`| Context of the API in API Manager with a leading slash. |
    | `productionUrl` | Production endpoint for API.                            |
    | `sandboxUrl`| Sandbox endpoint for API.                               |

    For more information about the configurations, see the [api.yaml](https://gist.github.com/kasvith/01e704611b6c301f470ab0e3b5cb0607).

    **api.yaml**

    ``` bash
    id:
        providerName: admin
        apiName: "SampleAPI"
        version: 1.0.0
    type: HTTP
    context: "/samplecontext"
    availableTiers:
        - name: Unlimited
    status: PUBLISHED
    visibility: public
    transports: http,https
    productionUrl: http://localhost:8080
    sandboxUrl: http://localhost:8081
    ```


## Import an API Project

!!! info
    **Before you begin...** 

    -   Make sure you have already created an environment to which you are planning to import the API. If not, follow steps in [Add an Environment]({{base_path}}/learn/api-controller/getting-started-with-wso2-api-controller#add-an-environment).
    
    -   Make sure you have logged-in to the importing environment. If not, follow steps in [Login to an Environment]({{base_path}}/learn/api-controller/getting-started-with-wso2-api-controller#login-to-an-environment). 


!!! warning
    -   A user with `admin` role is allowed to import APIs.
    -   A user with a role [`custom_role`] with BOTH `API Create` and `API Publish` permissions (along with `Login` permission) is allowed to import APIs by following the steps below.
            1. Sign in to the API-M management console as a tenant admin user. 
                 `https://localhost:9443/carbon`
            2. Click **Main > Resources > Browse**
            3. Enter `/_system/config/apimgt/applicationdata/tenant-conf.json` as the location and click **Go** to browse the registry and locate the required resource.
            4. Update the `RESTAPIScopes` JSON field with the following.
                ```bash
                {...
                    "Name": "apim:api_import_export",
                    "Roles": "admin, custom_role"
                ...},
                ``` 
            4. Restart the server or wait for 15 mins until the registry cache expires.
    -   If the `custom_role` ONLY has the `API Create` permission, then the user with the `custom_role` can import APIs ONLY that are in `CREATED` state.
    -   To import an API by updating/changing the lifecycle state, the user with the `custom_role` should have BOTH `API Create` and `API Publish` permissions.
    -   A user that has the `custom_role` with ONLY `API Publish` permission cannot import an API.    

After editing the mandatory fields in the API Project, you can import the API to an environment using any of the following commands.  

-   **Command**
    ``` bash
    apictl import-api -f <path to API Project> -e <environment> -k
    ```
    ``` bash
    apictl import-api --file <path to API Project> --environment <environment> -k
    ```
    ``` bash
    apictl import-api --file <path to API Project> --environment <environment> --params=<environment params file> -k
    ```

    !!! info
        **Flags:**  
           
        -   Required :  
            `--file` or `-f` : The file path of the API project to import.  
            `--environment` or `-e` : Environment to which the API should be imported.   
        -   Optional :  
            `--preserve-provider` : Preserve the existing provider of API after importing. Default value is `true`. 
            `--update` : Update an existing API or create a new API in the importing environment.  
            `--params` : Provide a API Manager environment params file (default "api_params.yaml").   
            For more information, see [Configure Environment Specific Details](#configure-environment-specific-details).  
            `--skipCleanup` : Leave all temporary files created in the CTL during import process. Default value is `false`.  

    !!! example
        ```bash
        apictl import-api -f ~/myapi -e production -k
        ```
        ```bash
        apictl import-api --file ~/myapi --environment production -k
        ```    
        ``` go
        apictl import-api --file ~/myapi --environment production --params prod/custom_api_params.yaml -k 
        ```
        
    !!! tip
        When using `--update` flag with `import-api` command, the CTL tool will check if the given API exists in the targeted environment. If the API exists, it will update the existing API. If not, it will create a new API in the imported environment. 

       
-   **Response**
    ``` bash
    Successfully imported API!
    ```

#### Configure Environment Specific Details
When there are multiple environments, to allow easily configuring environment-specific details, apictl supports an additional parameter file named `api_params.yaml`. It is recommended to store the parameter file with the API Project; however, it can be stored anywhere as required. 

After the file is placed in the project directory, the tool will auto-detect the parameters file upon running the `import-api` command and create an environment-based artifact for API Manager. If the `api_params.yaml` is not found in the project directory, the tool will lookup in the project’s base path and the current working directory. 

The following is the structure of the parameter file.

```go
environments:
    - name: <environment_name>
      endpoints:
          production:
              url: <production_endpoint_url>
              config:
                  retryTimeOut: <no_of_retries_before_suspension>
                  retryDelay: <retry_delay_in_ms>
                  factor: <suspension_factor>
          sandbox:
              url: <sandbox_endpoint_url>
              config:
                  retryTimeOut: <no_of_retries_before_suspension>
                  retryDelay: <retry_delay_in_ms>
                  factor: <suspension_factor>
      security:
        - enabled: <whether_security_is_enabled>
          type: <endpoint_authentication_type_basic_or_digest>
          username: <endpoint_username>
          password: <endpoint_password>
      gatewayEnvironments:
        - <gateway_environment_name>           
      certs:
        - hostName: <endpoint_url>
          alias: <certificate_alias>
          path: <certificate_file_path>
```
The following code snippet contains sample configuration of the parameter file.

!!! example
    ```go
    environments:
        - name: dev
          endpoints:
            production:
              url: 'https://dev.wso2.com'
          security:
           - enabled: true
             type: basic
             username: admin
             password: admin
          certs:
            - hostName: 'https://dev.wso2.com'
              alias: Dev
              path: ~/.certs/dev.pem 
          gatewayEnvironments:
            - Production and Sandbox    
        - name: test
          endpoints:
            production:
              url: 'https://test.wso2.com'
          security:
           - enabled: true
             type: digest
             username: admin
             password: admin
              config:
                retryTimeOut: $RETRY
            sandbox:
              url: 'https://test.sandbox.wso2.com'
    ```
-   You can also provide a custom path for the parameter file using the `--params` flag.
-   Production/Sandbox backends for each environment can be specified in the parameter file with additional configurations, such as timeouts.
-   Under the security field, if the `enabled` attribute is `true`, you must specify the `username`, `password` and the `type` (can be either only `basic` or `digest`). If the `enabled` attribute is `false`, then non of the security parameters will be set. If the `enabled` attribute is not set (blank), then the security parameters in api.yaml file will be considered.
-   Certificates for each URL can be configured in the parameter file. For certificates, a valid path to the certificate file is required. 
-   The parameter file supports detecting environment variables during the API import process. You can use the usual notation. For example, `url: $DEV_PROD_URL`.  If an environment variable is not set, the tool will fail. In addition, the system will also request for a set of required environment variables.   
