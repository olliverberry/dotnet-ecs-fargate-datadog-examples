FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /app
COPY . ./
RUN dotnet restore
RUN dotnet publish -c Release -o out

# Build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:7.0-jammy-amd64
WORKDIR /app
COPY --from=build /app/out .

# Setup Datadog Tracing
ENV COR_ENABLE_PROFILING=1
ENV COR_PROFILER={846F5F1C-F9AE-4B07-969E-05C26BC060D8}
ENV COR_PROFILER_PATH=/app/datadog/linux-x64/Datadog.Trace.ClrProfiler.Native.so
ENV DD_DOTNET_TRACER_HOME=/app/datadog
RUN /app/datadog/createLogPath.sh

ENTRYPOINT ["dotnet", "BundleApi.dll"]