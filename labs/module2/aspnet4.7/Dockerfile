FROM mcr.microsoft.com/dotnet/framework/sdk:4.7.2-windowsservercore-ltsc2019 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY WebAppLegacy/*.csproj ./WebAppLegacy/
COPY WebAppLegacy/*.config ./WebAppLegacy/
RUN nuget restore

# copy everything else and build app
COPY WebAppLegacy/. ./WebAppLegacy/
WORKDIR /app/WebAppLegacy
RUN msbuild /p:Configuration=Release


FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019 AS runtime
WORKDIR /inetpub/wwwroot
COPY --from=build /app/WebAppLegacy/. ./