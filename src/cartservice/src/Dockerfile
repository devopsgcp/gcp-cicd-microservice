# Step 1: Build Stage (SDK image)
FROM mcr.microsoft.com/dotnet/sdk:9.0.100-noble@sha256:bd0365368f46274500ebb086f491703052b8ce23e3d52d3233a23b2020730057 AS builder

WORKDIR /app

# Copy the .csproj file and restore dependencies (via dotnet restore)
COPY src/cartservice/cartservice.csproj ./  # Ensure the path is correct based on your build context
RUN dotnet restore cartservice.csproj -r linux-x64

# Copy all source files to the container
COPY src/cartservice/ .  # Copy the entire src/cartservice directory, adjust according to your project structure

# Publish the application
RUN dotnet publish cartservice.csproj \
    -p:PublishSingleFile=true \
    -r linux-x64 \
    --self-contained true \
    -p:PublishTrimmed=true \
    -p:TrimMode=full \
    -c release \
    -o /cartservice

# Step 2: Runtime Stage (Runtime image)
FROM mcr.microsoft.com/dotnet/runtime-deps:9.0.0-noble-chiseled@sha256:5cc893809e2d2869e1a98c1eecc4c6ff6978d53bc3e5342014eff28a058867a4

WORKDIR /app

# Copy the published files from the builder image to the runtime image
COPY --from=builder /cartservice .

# Expose the application port
EXPOSE 7070

# Set environment variables
ENV DOTNET_EnableDiagnostics=0 \
    ASPNETCORE_HTTP_PORTS=7070

# Run as a non-root user
USER 1000

# Set the entrypoint for the application
ENTRYPOINT ["/app/cartservice"]
