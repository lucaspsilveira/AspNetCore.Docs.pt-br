## <a name="debug-diagnostics"></a><span data-ttu-id="b2269-101">Diagnóstico de depuração</span><span class="sxs-lookup"><span data-stu-id="b2269-101">Debug diagnostics</span></span>

<span data-ttu-id="b2269-102">Para obter uma saída de diagnóstico de `Logging:LogLevel:Microsoft` roteamento `Debug`detalhada, defina como.</span><span class="sxs-lookup"><span data-stu-id="b2269-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="b2269-103">Por exemplo, no ambiente de desenvolvimento, defina *appSettings. Development. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b2269-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

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