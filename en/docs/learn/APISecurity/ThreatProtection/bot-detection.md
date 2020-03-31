# Bot Detection

After a Publisher publishes APIs in the API Developer Portal, hackers can invoke the APIs without an access token by scanning the open ports of a system. Therefore, WSO2 API Manager has a bot detection mechanism in place to prevent such attacks by identifying who tried to enter and invoke resources without proper authorization. WSO2 API Manager's bot detection mechanism traces and logs details of such unauthorized API calls and sends notifications in this regard via emails. Thereby this helps Publishers to protect their data from bot attackers and improve the security of their data.  

If hackers (e.g., bot attackers) tries to invoke open service APIs, WSO2 API Manager will log all unauthorized API calls in the `<API-M_HOME>/repository/logs/wso2-BotDetectedData.log` file. The following is a sample log record.
 
```
INFO BotDetectionMediator MessageId : urn:uuid:535437f1-a178-4722-a232-164e4a7e0207 | Request Method : POST | Message Body : <soapenv:Body xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"><jsonObject/></soapenv:Body> | client Ip : 127.0.0.1 | Headers set : [Accept=*/*, activityid=4b932127-d07e-43c3-bee2-4f5344074185, Content-Length=2, Content-Type=application/json, Host=localhost:8243, User-Agent=curl/7.58.0]  
```

If you enable WSO2 API Manager Analytics with WSO2 API Manager, you can enable email notifications for all unauthorized API calls that you receive and also view the bot detection data easily via the Admin Portal.


  <html>
  <div class="admonition note">
  <p class="admonition-title">Note</p>
  <p>If you wish to work with a third-party monitoring tool, then you can use the  details in the <code><API-M_HOME>/repository/logs/wso2-BotDetectedData.log</code> trace log and build an alert mechanism to receive alerts. </p>
  </div> 
  </html>


## Enabling email notifications for bot detection

Follow the instructions below to enable email notifications for bot detection:

1. Enable WSO2 API Manager Analytics.

    Follow steps 1, 2, and 3 of the quick setup in [Configuring API Manager Analytics](../../../../Learn/Analytics/configuring-apim-analytics/).
    
2. Share your API-M database (`AM_DB`).

     Modify the `<API-M_ANALYTICS_HOME>/conf/worker/deployment.yaml` file as follows. 

     ```
     - name: WSO2AM_DB
        description: "The datasource used for APIM MGW analytics data."
        jndiConfig:
          name: jdbc/WSO2AM_DB
        definition:
          type: RDBMS
          configuration:
            jdbcUrl: 'jdbc:mysql://localhost:3306/apimgt_database'
            username: username
            password: password
            driverClassName: com.mysql.jdbc.Driver
            maxPoolSize: 50
            idleTimeout: 60000
            connectionTestQuery: SELECT 1
            validationTimeout: 30000
            isAutoCommit: false
     ```

3. Start the WSO2 API Manager Analytcs server.
   
     Navigate to the `<API-M_ANALYTICS_HOME>/bin` directory in your console and execute one of the following scripts based on your OS.

    - On Windows:  `worker.bat --run`
    - On Linux/Mac OS:  `sh worker.sh`
    

4. Start the WSO2 API Manager server.
  
    Navigate to the  `<API-M_HOME>/bin` directory in your console and execute one of the following scripts based on your OS.

    - On Windows:  `wso2server.bat --run`
    - On Linux/Mac OS:  `sh wso2server.sh`
    

5. Sign in to the Admin Portal.

     `https://<IP_Address>:9443/admin`

6. Click **BOT DETECTION**.

7. Click **CONFIGURE EMAILS**.
  
    ![Add email recipients](../../../assets/img/Learn/bot-email-notification.png)

8. Add the recipient's email address and click **Add**.

    If a hacker (e.g., bot attacker) tries to invoke an open service API, WSO2 API Manager will send emails to the email alert recipients. The following is a sample email notification.

    ![Sample email notification for unauthorized API call](../../../assets/img/Learn/sample-alert-email.png)
 
## Viewing bot detection data via the Admin Portal

Follow the instructions below to view the bot detection data for the unauthorized API calls via the Admin Portal.

  <html>
  <div class="admonition note">
  <p class="admonition-title">Note</p>
  <p>Skip steps 1, 2, 3, 4, and 5 if you have already enabled API Manager Analytics, configured the AM_DB database, started the WSO2 API Analytics and WSO2 API Manager servers, and signed in to the Admin Portal.</p>
  </div> 
  </html>

1. Enable WSO2 API Manager Analytics.

    Follow steps 1, 2, and 3 of the quick setup in [Configuring API Manager Analytics](../../../../Learn/Analytics/configuring-apim-analytics/).
    
2. Share your API-M database (`AM_DB`) by modifying the `<API-M_ANALYTICS_HOME>/conf/worker/deployment.yaml` file as follows. 

     ```
     - name: WSO2AM_DB
        description: "The datasource used for APIM MGW analytics data."
        jndiConfig:
          name: jdbc/WSO2AM_DB
        definition:
          type: RDBMS
          configuration:
            jdbcUrl: 'jdbc:mysql://localhost:3306/apimgt_database'
            username: username
            password: password
            driverClassName: com.mysql.jdbc.Driver
            maxPoolSize: 50
            idleTimeout: 60000
            connectionTestQuery: SELECT 1
            validationTimeout: 30000
            isAutoCommit: false
     ```

3. Start the WSO2 API Manager Analytcs server.
   
     Navigate to the `<API-M_ANALYTICS_HOME>/bin` directory in your console and execute one of the following scripts based on your OS.

    - On Windows:  `worker.bat --run`
    - On Linux/Mac OS:  `sh worker.sh`


4. Start the WSO2 API Manager server.
  
    Navigate to the `<API-M_HOME>/bin` directory in your console and execute one of the following scripts based on your OS.

    - On Windows:  `wso2server.bat --run`
    - On Linux/Mac OS:  `sh wso2server.sh`

5. Sign in to the Admin Portal.

     `https://<IP_Address>:9443/admin/`

6. Click **BOT DETECTION DATA**.

     ![Bot detection data details for unauthorized API calls](../../../assets/img/Learn/bot-data.png)

    If a hacker (e.g., bot attacker) tries to invoke an open service API, the Bot detection data details, which appear in the `<API-M_HOME>/repository/logs/wso2-BotDetectedData.log` trace log, will appear.
