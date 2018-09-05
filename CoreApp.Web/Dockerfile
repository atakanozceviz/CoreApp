FROM microsoft/dotnet:2.1-sdk AS build
WORKDIR /app
 
 
COPY *.csproj ./
RUN dotnet restore
 
 
COPY . ./
RUN dotnet publish -c Release -o output
 
 
FROM microsoft/dotnet:2.1-aspnetcore-runtime
WORKDIR /app
COPY --from=build /app/output .
 
EXPOSE 5000
ENV ASPNETCORE_URLS http://*:5000
ENV ASPNETCORE_ENVIRONMENT docker
 
ENTRYPOINT ["dotnet", "CoreApp.Web.dll"]