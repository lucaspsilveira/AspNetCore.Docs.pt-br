## <a name="troubleshoot"></a><span data-ttu-id="345f7-101">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="345f7-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="345f7-102">Cookies e dados do site</span><span class="sxs-lookup"><span data-stu-id="345f7-102">Cookies and site data</span></span>

<span data-ttu-id="345f7-103">Cookies e dados do site podem persistir em atualizações de aplicativos e interferir em testes e solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="345f7-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="345f7-104">Limpe o seguinte ao fazer alterações no código do aplicativo, alterações na conta do usuário com o provedor ou alterações na configuração do aplicativo do provedor:</span><span class="sxs-lookup"><span data-stu-id="345f7-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="345f7-105">Cookies de login do usuário</span><span class="sxs-lookup"><span data-stu-id="345f7-105">User sign-in cookies</span></span>
* <span data-ttu-id="345f7-106">Cookies de aplicativos</span><span class="sxs-lookup"><span data-stu-id="345f7-106">App cookies</span></span>
* <span data-ttu-id="345f7-107">Dados do site armazenados em cache e armazenados</span><span class="sxs-lookup"><span data-stu-id="345f7-107">Cached and stored site data</span></span>

<span data-ttu-id="345f7-108">Uma abordagem para evitar que cookies e dados de sites persistentes interfidem com testes e solução de problemas é:</span><span class="sxs-lookup"><span data-stu-id="345f7-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="345f7-109">Use um navegador para testar que você pode configurar para excluir todos os dados de cookies e sites cada vez que o navegador estiver fechado.</span><span class="sxs-lookup"><span data-stu-id="345f7-109">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
* <span data-ttu-id="345f7-110">Feche o navegador entre qualquer alteração na configuração do aplicativo, do usuário de teste ou do provedor.</span><span class="sxs-lookup"><span data-stu-id="345f7-110">Close the browser between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="345f7-111">Execute o aplicativo Server</span><span class="sxs-lookup"><span data-stu-id="345f7-111">Run the Server app</span></span>

<span data-ttu-id="345f7-112">Ao testar e solucionar problemas de um aplicativo Blazor hospedado, certifique-se de que você está executando o aplicativo a partir do projeto **Server.**</span><span class="sxs-lookup"><span data-stu-id="345f7-112">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="345f7-113">Por exemplo, no Visual Studio, confirme se o projeto Server está destacado no **Solution Explorer** antes de iniciar o aplicativo com qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="345f7-113">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="345f7-114">Selecione o botão **Executar**.</span><span class="sxs-lookup"><span data-stu-id="345f7-114">Select the **Run** button.</span></span>
* <span data-ttu-id="345f7-115">Use **Debug** > **Start Debugging** do menu.</span><span class="sxs-lookup"><span data-stu-id="345f7-115">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="345f7-116">Pressione <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="345f7-116">Press <kbd>F5</kbd>.</span></span>
