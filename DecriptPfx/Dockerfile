FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
#EXPOSE 8075
EXPOSE 8080

#ENV ASPNETCORE_URLS=http://+:8075

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG configuration=Release
WORKDIR /DecriptPfx
COPY ["DecriptPfx/DecriptPfx.csproj", "DecriptPfx/"]
#COPY ["ApiPfx/ApiPfx.csproj", "ApiPfx/"]
RUN dotnet restore "./DecriptPfx/DecriptPfx.csproj"

COPY . .
WORKDIR "/DecriptPfx"
RUN dotnet build "./DecriptPfx.csproj" -c $configuration -o /app/build

FROM build AS publish
ARG configuration=Release
RUN dotnet publish "./DecriptPfx.csproj" -c $configuration -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "DecriptPfx.dll"]
#ENTRYPOINT ["./ApiPfx.dll"]