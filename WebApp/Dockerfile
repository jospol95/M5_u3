# Build stage
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /app

# Copiar carpetas y restore projects
COPY ./WebApp/WebApp.csproj ./WebApp/
RUN dotnet restore ./WebApp/WebApp.csproj

COPY ./WebApp.UnitTests/WebApp.UnitTests.csproj ./WebApp.UnitTests/
RUN dotnet restore ./WebApp.UnitTests/WebApp.UnitTests.csproj

COPY ./WebApp.IntegrationTests/WebApp.IntegrationTests.csproj ./WebApp.IntegrationTests/
RUN dotnet restore ./WebApp.IntegrationTests/WebApp.IntegrationTests.csproj

# Copiar el resto del codigo y run build
COPY . .
RUN dotnet build ./WebApp/WebApp.csproj -c Release
RUN dotnet test ./WebApp.UnitTests/WebApp.UnitTests.csproj -c Release
RUN dotnet test ./WebApp.IntegrationTests/WebApp.IntegrationTests.csproj -c Release

# Publish stage
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS publish
WORKDIR /app
COPY --from=build /app/WebApp/bin/Release/net5.0/publish ./binaries

# Final stage
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS final
WORKDIR /app
COPY --from=publish /app/binaries ./
ENV ASPNETCORE_URLS=http://+:5000
EXPOSE 5000
ENTRYPOINT ["dotnet", "WebApp.dll"]