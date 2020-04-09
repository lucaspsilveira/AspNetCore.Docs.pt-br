## <a name="grpc-not-supported-on-azure-app-service"></a>gRPC não suportado no Azure App Service

> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index) não é suportado atualmente no Azure App Service ou IIS. A implementação HTTP/2 do Http.Sys não suporta cabeçalhos de resposta HTTP que o gRPC conta. Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/9020).
