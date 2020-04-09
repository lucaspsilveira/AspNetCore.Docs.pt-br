## <a name="debug-diagnostics"></a><span data-ttu-id="8604e-101">Diagnóstico sinuoso de depuração</span><span class="sxs-lookup"><span data-stu-id="8604e-101">Debug diagnostics</span></span>

<span data-ttu-id="8604e-102">Para obter uma saída `Logging:LogLevel:Microsoft` de `Debug`diagnóstico de roteamento detalhada, defina-se como .</span><span class="sxs-lookup"><span data-stu-id="8604e-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="8604e-103">Por exemplo, no ambiente de desenvolvimento, defina *as configurações. Desenvolvimento.json:*</span><span class="sxs-lookup"><span data-stu-id="8604e-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

```JSON
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}