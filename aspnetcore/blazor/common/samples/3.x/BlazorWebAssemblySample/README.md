# <a name="blazor-webassembly-sample-app"></a>Aplicativo de amostragem Blazor WebAssembly

Esta amostra ilustra o uso dos cenários blazor descritos na documentação blazor.

## <a name="call-web-api-example"></a>Chamar exemplo de API web

O exemplo da API web requer uma API web em execução baseada no aplicativo de exemplo para a <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">API criar uma Web Com ASP.NET</a> tópico Core, que por padrão usa a mesma porta HTTPS (5001) que o aplicativo de amostra Blazor. Para usar ambos os aplicativos na mesma máquina ao mesmo tempo, altere a porta da API web (por exemplo, use a porta 10000). O aplicativo de exemplo faz solicitações à API web em `https://localhost:10000/api/TodoItems`. Se um endereço De PiPI web `ServiceEndpoint` diferente for usado, `@code` atualize o valor constante no bloco do componente Razor.</p>

O aplicativo de exemplo faz uma solicitação <a href="https://docs.microsoft.com/aspnet/core/security/cors">de compartilhamento de recursos de origem cruzada (CORS)</a> a partir `http://localhost:5000` ou `https://localhost:5001` para a API web. Credenciais (cookies/cabeçalhos de autorização) são permitidas. Adicione a seguinte configuração de middleware CORS ao método da API web: `Startup.Configure`</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Ajuste os domínios `WithOrigins` e portas conforme necessário para o aplicativo Blazor.

A API da Web está configurada para o CORS permitir cookies/cabeçalhos de autorização e solicitações do código do cliente, mas a API web criada pelo tutorial não autoriza solicitações. Consulte os <a href="https://docs.microsoft.com/aspnet/core/security/">artigos de segurança e identidade do ASP.NET para</a> obter orientação de implementação.
