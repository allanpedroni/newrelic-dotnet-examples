FROM mcr.microsoft.com/dotnet/aspnet:9.0-alpine AS base
WORKDIR /app
EXPOSE 80
ENV ASPNETCORE_URLS=http://+:80

FROM mcr.microsoft.com/dotnet/sdk:9.0-alpine AS build

WORKDIR /src
COPY . ./
RUN dotnet restore
RUN dotnet publish -c Release -o /app/publish

FROM base AS final

# Download and install the New Relic .NET Agent using the tarball for the latest agent release. 
#
# To use a specific version instead, modify the wget and tar commands to reference the full path to the 
# dotnet agent version tarball you want to use instead of the wildcard specification
# 
# For example, to use v10.21.1 of the .NET agent, you would run the following command:
# RUN apk update && apk add --no-cache wget tar \
#     && wget https://download.newrelic.com/dot_net_agent/previous_releases/10.21.1/newrelic-dotnet-agent-10.21.1.amd64.tar.gz \
#     && tar -xzf -i download.newrelic.com/dot_net_agent/previous_releases/10.21.1/newrelic-dotnet-agent-10.21.1.amd64.tar.gz -C /usr/local \
#     && rm -rf download.newrelic.com
# 
# the agent is installed at /usr/local/new-relic-dotnet-agent
RUN apk update && apk add --no-cache wget tar \
    && wget https://download.newrelic.com/dot_net_agent/latest_release/newrelic-dotnet-agent_amd64.tar.gz -r \
    && tar -xzf download.newrelic.com/dot_net_agent/latest_release/newrelic-dotnet-agent_amd64.tar.gz -C /usr/local \ 
    && rm -rf download.newrelic.com

# Enable the agent
ENV CORECLR_ENABLE_PROFILING=1 \
CORECLR_PROFILER={36032161-FFC0-4B61-B559-F6C5D41BAE5A} \
CORECLR_NEWRELIC_HOME=/usr/local/newrelic-dotnet-agent \
CORECLR_PROFILER_PATH=/usr/local/newrelic-dotnet-agent/libNewRelicProfiler.so \
NEW_RELIC_LICENSE_KEY=<your-new-relic-license-key> \
NEW_RELIC_APP_NAME=weather-forecast-alpine \
NEWRELIC_LOG_DIRECTORY=/app/logs

WORKDIR /app
COPY --from=build /app/publish .

ENTRYPOINT ["dotnet", "WeatherForecast.dll"]
