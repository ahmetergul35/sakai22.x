# Tomcat SSL:443 Port Redirect
```sh
vim /opt/apache-tomcat-8.0.32/conf/web.xml
```
---------------------------------------------
```sh
4690   <!-- ==================== Default Welcome File List ===================== -->
4691   <!-- When a request URI refers to a directory, the default servlet looks  -->
4692   <!-- for a "welcome file" within that directory and, if present, to the   -->
4693   <!-- corresponding resource URI for display.                              -->
4694   <!-- If no welcome files are present, the default servlet either serves a -->
4695   <!-- directory listing (see default servlet configuration on how to       -->
4696   <!-- customize) or returns a 404 status, depending on the value of the    -->
4697   <!-- listings setting.                                                    -->
4698   <!--                                                                      -->
4699   <!-- If you define welcome files in your own application's web.xml        -->
4700   <!-- deployment descriptor, that list *replaces* the list configured      -->
4701   <!-- here, so be sure to include any of the default values that you wish  -->
4702   <!-- to use within your application.                                       -->
4703 
4704     <welcome-file-list>
4705         <welcome-file>index.html</welcome-file>
4706         <welcome-file>index.htm</welcome-file>
4707         <welcome-file>index.jsp</welcome-file>
4708     </welcome-file-list>
4709 
4710 <!-- Force HTTPS, required for HTTP redirect! -->
4711 <security-constraint>
4712 <web-resource-collection>
4713 <web-resource-name>Protected Context</web-resource-name>
4714 <url-pattern>/*</url-pattern>
4715 </web-resource-collection>
4716 
4717 <!-- auth-constraint goes here if you require authentication -->
4718 <user-data-constraint>
4719 <transport-guarantee>CONFIDENTIAL</transport-guarantee>
4720 </user-data-constraint>
4721 </security-constraint>
4722 </web-app>
```
