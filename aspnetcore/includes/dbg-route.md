## <a name="debug-diagnostics"></a>Diagnóstico sinuoso de depuração

Para obter uma saída `Logging:LogLevel:Microsoft` de `Debug`diagnóstico de roteamento detalhada, defina-se como . Por exemplo, no ambiente de desenvolvimento, defina *as configurações. Desenvolvimento.json:*

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