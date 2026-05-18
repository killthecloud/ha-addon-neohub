# Build stage
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

COPY ["NeoHub/NeoHub/NeoHub.csproj", "NeoHub/NeoHub/"]
COPY ["NeoHub/TLink/DSC.TLink.csproj", "NeoHub/TLink/"]

RUN dotnet restore "NeoHub/NeoHub/NeoHub.csproj"

COPY . .

ARG GITHUB_SHA=local
ARG GITHUB_RUN_NUMBER=0

WORKDIR "/src/NeoHub/NeoHub"
RUN dotnet publish "NeoHub.csproj" -c Release -o /app/publish /p:UseAppHost=false /p:GITHUB_SHA=$GITHUB_SHA /p:GITHUB_RUN_NUMBER=$GITHUB_RUN_NUMBER

# Runtime stage
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS runtime
WORKDIR /app

EXPOSE 8080
EXPOSE 8443
EXPOSE 3072

COPY --from=build /app/publish .

RUN mkdir -p /config

ENV NEOHUB_PERSIST_PATH=/config \
    ASPNETCORE_URLS=http://+:8080

ENTRYPOINT ["dotnet", "NeoHub.dll"]
