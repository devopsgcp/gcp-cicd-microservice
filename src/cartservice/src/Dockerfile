# Use .NET SDK for build
FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:9.0.101-noble AS builder
ARG TARGETARCH
WORKDIR /app

# Copy only the main project file first for caching
COPY cartservice/src/cartservice.csproj .

# Restore dependencies
RUN dotnet restore cartservice.csproj

# Copy the entire source code (but exclude test files)
COPY cartservice/src/ ./src/

# Ensure only the service project is built, avoiding tests
WORKDIR /app/src
RUN dotnet publish cartservice.csproj \
    -p:PublishSingleFile=true \
    -p:PublishTrimmed=true \
    -p:TrimMode=full \
    --self-contained true \
    -c release \
    -o /cartservice

# Use a lightweight runtime image for final deployment
FROM mcr.microsoft.com/dotnet/runtime-deps:9.0.1-noble-chiseled

WORKDIR /app
COPY --from=builder /cartservice .
EXPOSE 7070

# Environment variables
ENV DOTNET_EnableDiagnostics=0 \
    ASPNETCORE_HTTP_PORTS=7070

USER 1000
ENTRYPOINT ["/app/cartservice"]
