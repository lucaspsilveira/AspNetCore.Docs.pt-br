> [!WARNING]
> <span data-ttu-id="471a5-101">Por motivos de segurança, você deve aceitar associar os dados da solicitação `GET` às propriedades do modelo de página.</span><span class="sxs-lookup"><span data-stu-id="471a5-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="471a5-102">Verifique a entrada do usuário antes de mapeá-la para as propriedades.</span><span class="sxs-lookup"><span data-stu-id="471a5-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="471a5-103">Optar pela `GET` vinculação é útil ao abordar cenários que dependem de seqüência de consultas ou valores de rota.</span><span class="sxs-lookup"><span data-stu-id="471a5-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="471a5-104">Para vincular uma `GET` propriedade nas [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) solicitações, defina a propriedade do `SupportsGet` atributo como: `true`</span><span class="sxs-lookup"><span data-stu-id="471a5-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="471a5-105">Para obter mais informações, consulte [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="471a5-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
