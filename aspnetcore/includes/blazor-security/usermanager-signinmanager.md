## <a name="usermanager-and-signinmanager"></a><span data-ttu-id="1253e-101">Gerente de usuário e Gerente de Inscrição</span><span class="sxs-lookup"><span data-stu-id="1253e-101">UserManager and SignInManager</span></span>

<span data-ttu-id="1253e-102">Defina o tipo de reclamação do identificador de usuário quando um aplicativo do Servidor exigir:</span><span class="sxs-lookup"><span data-stu-id="1253e-102">Set the user identifier claim type when a Server app requires:</span></span>

* <span data-ttu-id="1253e-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601>ou <xref:Microsoft.AspNetCore.Identity.SignInManager%601> em um ponto final da API.</span><span class="sxs-lookup"><span data-stu-id="1253e-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> or <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in an API endpoint.</span></span>
* <span data-ttu-id="1253e-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser>detalhes, como o nome do usuário, endereço de e-mail ou hora de término do bloqueio.</span><span class="sxs-lookup"><span data-stu-id="1253e-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> details, such as the user's name, email address, or lockout end time.</span></span>

<span data-ttu-id="1253e-105">Em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1253e-105">In `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

<span data-ttu-id="1253e-106">Os `WeatherForecastController` registros a <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> seguir `Get` registram quando o método é chamado:</span><span class="sxs-lookup"><span data-stu-id="1253e-106">The following `WeatherForecastController` logs the <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> when the `Get` method is called:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using BlazorAppIdentityServer.Server.Models;
using BlazorAppIdentityServer.Shared;

namespace BlazorAppIdentityServer.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> _userManager;

        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", 
            "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger, 
            UserManager<ApplicationUser> userManager)
        {
            this.logger = logger;
            _userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await _userManager.GetUserAsync(User);

            if (user != null)
            {
                logger.LogInformation($"User.Identity.Name: {user.UserName}");
            }

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
}
```
