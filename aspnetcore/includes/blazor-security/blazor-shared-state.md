Aplicativos de servidor mais poseriais residem na memória do servidor. Isso significa que há vários aplicativos hospedados no mesmo processo. Para cada sessão de aplicativo, o mais incrivelmente inicia um circuito com seu próprio escopo de contêiner de DI. Isso significa que os serviços com escopo são exclusivos por sessão mais incrivelmente.

> [!WARNING]
> Não recomendamos aplicativos no mesmo estado de compartilhamento do servidor usando serviços singleton, a menos que seja feito um tratamento extremo, pois isso pode introduzir vulnerabilidades de segurança, como o vazamento do estado do usuário entre circuitos.

Você pode usar serviços singleton com estado em aplicativos mais incrivelmente se eles forem projetados especificamente para ele. Por exemplo, é possível usar um cache de memória como um singleton porque ele requer uma chave para acessar uma determinada entrada, supondo que os usuários não tenham controle de quais chaves de cache são usadas.

**Além disso, por motivos de segurança, você não deve usar em aplicativos mais potentes <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> .** Os aplicativos mais bem executados fora do contexto do pipeline de ASP.NET Core e <xref:Microsoft.AspNetCore.Http.HttpContext> não há garantia de que estejam disponíveis no <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , nem é garantido que esteja mantendo o contexto que iniciou o aplicativo mais incrivelmente.

A maneira recomendada de passar o estado da solicitação para o aplicativo mais fácil é por meio de parâmetros para o componente raiz na renderização inicial do aplicativo:

* Defina uma classe com todos os dados que você deseja passar para o aplicativo mais incrivelmente.
* Preencha esses dados da página Razor usando o <xref:Microsoft.AspNetCore.Http.HttpContext> disponível naquele momento.
* Passe os dados para o aplicativo mais incrivelmente como um parâmetro para o componente raiz (aplicativo).
* Defina um parâmetro no componente raiz para manter os dados que estão sendo passados para o aplicativo.
* Usar os dados específicos do usuário dentro do aplicativo; ou, como alternativa, copie esses dados em um serviço com escopo dentro <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> para que ele possa ser usado em todo o aplicativo.

Para obter mais informações e um código de exemplo, consulte <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.
