## <a name="troubleshoot"></a>Solucionar problemas

### <a name="cookies-and-site-data"></a>Cookies e dados do site

Cookies e dados do site podem persistir em atualizações de aplicativos e interferir em testes e solução de problemas. Limpe o seguinte ao fazer alterações no código do aplicativo, alterações na conta do usuário com o provedor ou alterações na configuração do aplicativo do provedor:

* Cookies de login do usuário
* Cookies de aplicativos
* Dados do site armazenados em cache e armazenados

Uma abordagem para evitar que cookies e dados de sites persistentes interfidem com testes e solução de problemas é:

* Use um navegador para testar que você pode configurar para excluir todos os dados de cookies e sites cada vez que o navegador estiver fechado.
* Feche o navegador entre qualquer alteração na configuração do aplicativo, do usuário de teste ou do provedor.

### <a name="run-the-server-app"></a>Execute o aplicativo Server

Ao testar e solucionar problemas de um aplicativo Blazor hospedado, certifique-se de que você está executando o aplicativo a partir do projeto **Server.** Por exemplo, no Visual Studio, confirme se o projeto Server está destacado no **Solution Explorer** antes de iniciar o aplicativo com qualquer uma das seguintes abordagens:

* Selecione o botão **Executar**.
* Use **Debug** > **Start Debugging** do menu.
* Pressione <kbd>F5</kbd>.
