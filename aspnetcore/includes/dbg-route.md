## <a name="debug-diagnostics"></a>Diagnóstico de depuração

Para obter uma saída de diagnóstico de roteamento detalhada, defina `Logging:LogLevel:Microsoft` como `Debug` . No ambiente de desenvolvimento, defina o nível de log em *appsettings.Development.jsem*:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
