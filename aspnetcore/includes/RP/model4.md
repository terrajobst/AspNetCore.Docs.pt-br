<a name="codegenerator"></a> A tabela a seguir detalha os parâmetros do gerador de código do ASP.NET Core:

| Parâmetro               | Descrição|
| ----------------- | ------------ |
| -m  | O nome do modelo. |
| -dc  | A classe `DbContext` a ser usada. |
| -udl | Use o layout padrão. |
| -outDir | O caminho da pasta de saída relativa para criar as exibições. |
| --referenceScriptLibraries | Adiciona `_ValidationScriptsPartial` para editar e criar páginas |

Use a opção `h` para obter ajuda sobre o comando `aspnet-codegenerator razorpage`:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Para saber mais, confira [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) 