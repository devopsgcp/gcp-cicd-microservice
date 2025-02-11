# Build using the .NET SDK
FROM --platform=linux/amd64 mcr.microsoft.com/dotnet/sdk:9.0.101-noble AS builder
WORKDIR /app

# Copy only the project file and restore dependencies
COPY cartservice/src/cartservice.csproj .
RUN dotnet restore cartservice.csproj

# Copy the rest of the source code
COPY cartservice/src/ ./src/

# Build the service with the specified publish settings
WORKDIR /app/src
RUN dotnet publish cartservice.csproj \
    -p:PublishSingleFile=true \
    -p:PublishTrimmed=true \
    -p:TrimMode=full \
    --self-contained true \
    -c release \
    -o /cartservice

# Use a lightweight runtime-deps image for final deployment
FROM mcr.microsoft.com/dotnet/runtime-deps:9.0.1-noble-chiseled
WORKDIR /app

# Copy the built application from the builder image
COPY --from=builder /cartservice .

# Expose the port the service will listen on
EXPOSE 7070

# Set environment variables for the app
ENV DOTNET_EnableDiagnostics=0 \
    ASPNETCORE_HTTP_PORTS=7070

USER 1000

# Define the entrypoint for the container
ENTRYPOINT ["/app/cartservice"]
