A tabela a seguir detalha os parâmetros do gerador de código do ASP.NET Core:

| Parâmetro               | Descrição|
| ----------------- | ------------ |
| -m  | O nome do modelo. |
| -dc  | O contexto de dados. |
| -udl | Use o layout padrão. |
| --relativeFolderPath | O caminho da pasta de saída relativa para criar as exibições. |
| --useDefaultLayout | O layout padrão deve ser usado para as exibições. |
| --referenceScriptLibraries | Adiciona `_ValidationScriptsPartial` para editar e criar páginas |

Use a opção `h` para obter ajuda sobre o comando `aspnet-codegenerator controller`:

```console
dotnet aspnet-codegenerator controller -h
```