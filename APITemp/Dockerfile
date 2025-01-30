# Base runtime
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base

# Criar usuário não root
RUN useradd -m appuser

WORKDIR /app
EXPOSE 8080
EXPOSE 8081

# Build stage
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copiar csproj e restaurar dependências
COPY APITemp.csproj ./
RUN dotnet restore "APITemp.csproj" --disable-parallel --no-cache

# Copiar código fonte e compilar
COPY . . 
WORKDIR "/src"
RUN dotnet build "APITemp.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Publish stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "APITemp.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Final stage
FROM base AS final
WORKDIR /app

# Copiar arquivos publicados
COPY --from=publish /app/publish .

# Somente aqui mudamos para usuário não root
USER appuser

# Comando de entrada
ENTRYPOINT ["dotnet", "APITemp.dll"]
