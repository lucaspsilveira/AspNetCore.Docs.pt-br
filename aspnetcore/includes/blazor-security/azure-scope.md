> [!NOTE]
> Se o portal do Azure fornecer o URI de escopo para o aplicativo e o aplicativo lançar uma exceção sem tratamento quando receber uma resposta de *401 não autorizado* da API, tente usar um URI de escopo que não inclua o esquema e o host. Por exemplo, a portal do Azure pode fornecer um dos seguintes formatos de URI de escopo:
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Tente fornecer o URI do escopo sem o esquema e o host:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
>
> Por exemplo:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
> ```
