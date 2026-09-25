# --------------------
# -      build       -
# --------------------

FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build

WORKDIR /app

COPY ./src/TaskList.Api/TaskList.Api.csproj ./src/TaskList.Api/
RUN dotnet restore ./src/TaskList.Api/TaskList.Api.csproj

COPY ./src/TaskList.Api/ ./src/TaskList.Api/
RUN dotnet publish ./src/TaskList.Api/TaskList.Api.csproj -c Release -o out

# --------------------
# -     finale       -
# --------------------

FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS finale

WORKDIR /app/out

ENV ASPNETCORE_URLS=http://+:8080
EXPOSE 8080

RUN <<EOF
apt-get update
apt-get install -y curl
EOF

HEALTHCHECK --interval=5m --timeout=3s \
    CMD curl -f http://localhost:8080 || exit 1

COPY --from=build /app/out .
USER $APP_UID

ENTRYPOINT ["dotnet", "TaskList.Api.dll"]