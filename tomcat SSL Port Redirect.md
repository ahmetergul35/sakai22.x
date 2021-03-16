# Tomcat SSL:443 Port Redirect
```sh
vim /opt/apache-tomcat-8.0.32/conf/web.xml
```
---------------------------------------------
```sh
  <!-- ==================== Default Welcome File List ===================== -->
  <!-- When a request URI refers to a directory, the default servlet looks  -->
  <!-- for a "welcome file" within that directory and, if present, to the   -->
  <!-- corresponding resource URI for display.                              -->
  <!-- If no welcome files are present, the default servlet either serves a -->
  <!-- directory listing (see default servlet configuration on how to       -->
  <!-- customize) or returns a 404 status, depending on the value of the    -->
  <!-- listings setting.                                                    -->
  <!--                                                                      -->
  <!-- If you define welcome files in your own application's web.xml        -->
  <!-- deployment descriptor, that list *replaces* the list configured      -->
  <!-- here, so be sure to include any of the default values that you wish  -->
  <!-- to use within your application.                                       -->

    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>

<!-- Force HTTPS, required for HTTP redirect! -->
<security-constraint>
<web-resource-collection>
<web-resource-name>Protected Context</web-resource-name>
<url-pattern>/*</url-pattern>
</web-resource-collection>

<!-- auth-constraint goes here if you require authentication -->
<user-data-constraint>
<transport-guarantee>CONFIDENTIAL</transport-guarantee>
</user-data-constraint>
</security-constraint>
</web-app>
```
