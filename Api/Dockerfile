FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /app
COPY . ./
RUN dotnet restore
RUN dotnet publish -c Release -o out

# Build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:7.0-jammy-amd64
WORKDIR /app
COPY --from=build /app/out .

RUN apt-get update && \
    apt-get -y upgrade && \
    apt-get -y install wget && \
    wget -q https://github.com/DataDog/dd-trace-dotnet/releases/download/v2.35.0/datadog-dotnet-apm_2.35.0_amd64.deb && \
    dpkg -i ./datadog-dotnet-apm_2.35.0_amd64.deb && \
    /opt/datadog/createLogPath.sh && \
    rm -f ./datadog-dotnet-apm_2.35.0_amd64.deb

# Setup Datadog Tracing
ENV CORECLR_ENABLE_PROFILING=1
ENV CORECLR_PROFILER={846F5F1C-F9AE-4B07-969E-05C26BC060D8}
ENV CORECLR_PROFILER_PATH=/opt/datadog/Datadog.Trace.ClrProfiler.Native.so
ENV DD_DOTNET_TRACER_HOME=/opt/datadog

ENTRYPOINT ["dotnet", "Api.dll"]