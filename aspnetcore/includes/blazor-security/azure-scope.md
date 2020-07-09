> [!NOTE]
> <span data-ttu-id="1d4f2-101">Se o portal do Azure fornecer o URI de escopo para o aplicativo e o aplicativo lançar uma exceção sem tratamento quando receber uma resposta de *401 não autorizado* da API, tente usar um URI de escopo que não inclua o esquema e o host.</span><span class="sxs-lookup"><span data-stu-id="1d4f2-101">If the Azure portal provides the scope URI for the app and the app throws an unhandled exception when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="1d4f2-102">Por exemplo, a portal do Azure pode fornecer um dos seguintes formatos de URI de escopo:</span><span class="sxs-lookup"><span data-stu-id="1d4f2-102">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="1d4f2-103">Tente fornecer o URI do escopo sem o esquema e o host:</span><span class="sxs-lookup"><span data-stu-id="1d4f2-103">Try supplying the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
>
> <span data-ttu-id="1d4f2-104">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="1d4f2-104">For example:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
> ```
