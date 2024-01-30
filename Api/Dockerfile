FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /app
COPY . ./
RUN dotnet restore
RUN dotnet publish -c Release -o out

# build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:7.0-jammy-amd64
WORKDIR /app
COPY --from=build /app/out .

# run updates and install the datadog dotnet tracing client.
# the download page for the tracing client can be found here: https://github.com/DataDog/dd-trace-dotnet/releases
# be sure to pay attention to the bitness and processor architecture indicated by each download package.
# more information can be found on datadog's documentation here: https://docs.datadoghq.com/tracing/trace_collection/automatic_instrumentation/dd_libraries/dotnet-core/?tab=linux#install-the-tracer
RUN apt-get update && \
    apt-get -y upgrade && \
    apt-get -y install wget && \
    wget -q https://github.com/DataDog/dd-trace-dotnet/releases/download/v2.46.0/datadog-dotnet-apm_2.46.0_amd64.deb && \
    dpkg -i ./datadog-dotnet-apm_2.46.0_amd64.deb && \
    /opt/datadog/createLogPath.sh && \
    rm -f ./datadog-dotnet-apm_2.46.0_amd64.deb

# setup datadog tracing
# refer to documentation found here: https://docs.datadoghq.com/tracing/trace_collection/automatic_instrumentation/dd_libraries/dotnet-core?tab=windows#linux-docker-container
ENV CORECLR_ENABLE_PROFILING=1
ENV CORECLR_PROFILER={846F5F1C-F9AE-4B07-969E-05C26BC060D8}
ENV CORECLR_PROFILER_PATH=/opt/datadog/Datadog.Trace.ClrProfiler.Native.so
ENV DD_DOTNET_TRACER_HOME=/opt/datadog

ENTRYPOINT ["dotnet", "Api.dll"]