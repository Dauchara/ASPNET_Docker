#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY ["ASPNET_Docker/ASPNET_Docker.csproj", "ASPNET_Docker/"]
RUN dotnet restore "ASPNET_Docker/ASPNET_Docker.csproj"
COPY . .
WORKDIR "/src/ASPNET_Docker"
RUN dotnet build "ASPNET_Docker.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "ASPNET_Docker.csproj" -c Release -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENV ASPNETCORE_URLS=http://+:80


# Install PostgreSQL client
RUN apt-get update \
    && apt-get install -y postgresql-client

# Install Redis client
RUN apt-get update \
    && apt-get install -y redis-tools

# Install MongoDB client
RUN apt-get update \
    && apt-get install -y gnupg \
    && wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | apt-key add - \
    && echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-4.4.list \
    && apt-get update \
    && apt-get install -y mongodb-org-tools

ENTRYPOINT ["dotnet", "ASPNET_Docker.dll"]