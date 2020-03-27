## <a name="debug-diagnostics"></a>Diagnóstico de depuração

Para obter uma saída de diagnóstico de roteamento detalhada, defina `Logging:LogLevel:Microsoft` como `Debug`. Por exemplo, no ambiente de desenvolvimento, defina *appSettings. Development. JSON*:

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