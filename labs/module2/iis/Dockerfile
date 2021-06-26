FROM mcr.microsoft.com/windows/servercore:2004

LABEL owner="cloudarchitectureteam@contoso.com"

#Install IIS
RUN powershell -Command Add-WindowsFeature Web-Server

ADD ServiceMonitor.exe /ServiceMonitor.exe

EXPOSE 80

ENTRYPOINT ["C:\\ServiceMonitor.exe", "w3svc"]