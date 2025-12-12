FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build

WORKDIR /src

COPY . .

RUN dotnet restore  ./src/Worker/Worker.csproj

RUN dotnet publish -c Release -o /out ./src/Worker/Worker.csproj

FROM mcr.microsoft.com/dotnet/core/runtime:3.1

WORKDIR /app

COPY --from=build /out .

CMD ["dotnet", "Worker.dll"]
