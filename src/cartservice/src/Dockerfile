# Builder stage
FROM mcr.microsoft.com/dotnet/sdk:8.0.204@sha256:7861b15f318949cf9214d9ad5382b680a0e22e3b8673180707aa0c594ab75656 as builder
WORKDIR /app
COPY src/cartservice/src/cartservice.csproj .
RUN dotnet restore src/cartservice/src/cartservice.csproj -r linux-musl-x64
COPY . .
RUN dotnet publish src/cartservice/src/cartservice.csproj \
    -p:PublishSingleFile=true \
    -r linux-musl-x64 \
    --self-contained true \
    -p:PublishTrimmed=True \
    -p:TrimMode=Full \
    -c release \
    -o /cartservice

# Final runtime image
FROM mcr.microsoft.com/dotnet/runtime-deps:8.0.4-alpine3.18-amd64@sha256:1fd5e6015ca8b6778a19339a921363370c2ab5cf91dc3421df5f29f4caa07440
WORKDIR /app
COPY --from=builder /cartservice .
EXPOSE 7070
ENTRYPOINT ["/app/cartservice"]
