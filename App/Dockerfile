FROM mcr.microsoft.com/dotnet/sdk:9.0@sha256:3fcf6f1e809c0553f9feb222369f58749af314af6f063f389cbd2f913b4ad556 AS build
ARG SONAR_TOKEN

RUN dotnet tool install dotnet-sonarscanner --tool-path /tools
WORKDIR /App

# Copy everything
COPY . ./

RUN --mount=type=secret,id=sonar_token,env=SONAR_TOKEN ./tools/dotnet-sonarscanner begin /k:"denis-troller_docker-test" /o:"denis-troller" /d:sonar.token=${SONAR_TOKEN}
# Restore as distinct layers
RUN dotnet restore
# Build and publish a release
RUN dotnet publish -o out
RUN --mount=type=secret,id=sonar_token,env=SONAR_TOKEN ./tools/dotnet-sonarscanner end /d:sonar.token=${SONAR_TOKEN} 

# Build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:9.0@sha256:b4bea3a52a0a77317fa93c5bbdb076623f81e3e2f201078d89914da71318b5d8
WORKDIR /App
COPY --from=build /App/out .
ENTRYPOINT ["dotnet", "DotNet.Docker.dll"]