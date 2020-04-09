> [!WARNING]
> Por motivos de segurança, você deve aceitar associar os dados da solicitação `GET` às propriedades do modelo de página. Verifique a entrada do usuário antes de mapeá-la para as propriedades. Optar pela `GET` vinculação é útil ao abordar cenários que dependem de seqüência de consultas ou valores de rota.
>
> Para vincular uma `GET` propriedade nas [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) solicitações, defina a propriedade do `SupportsGet` atributo como: `true`
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Para obter mais informações, consulte [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
