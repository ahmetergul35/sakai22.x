Create ROOT directory;
```sh
mkdir /opt/tomcat/webapps/ROOT
vim /opt/tomcat/webapps/ROOT/index.html
```
---------------------------------------------
Add;
```sh
<html>
<head>
<title>Redirecting to /portal</title>
<meta http-equiv="Refresh" content="0:URL=/portal">
</head>
<body bgcolor="#ffffff" onLoad="javascript:window.location='/portal';">
<div style="margin:18px;width:288px;background-color:#cccc99;padding:18px;border:thin solid #666600;text-align:justify">
<p style="margin-top:0px">
You are being redirected to the Sakai portal.  If you are not automatically redirected, use the link below to continue:<br/>
<a href="/portal">Take me to the Sakai portal</a>
</p>
</body>
</html>
```
