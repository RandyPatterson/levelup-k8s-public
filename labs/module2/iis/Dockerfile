FROM mcr.microsoft.com/windows/servercore:2004

LABEL owner="cloudarchitectureteam@contoso.com"

RUN powershell -Command Add-WindowsFeature Web-Server

COPY index.html /inetpub/wwwroot/

COPY ServiceMonitor.exe /ServiceMonitor.exe

EXPOSE 80

ENTRYPOINT ["C:\\ServiceMonitor.exe", "w3svc"]