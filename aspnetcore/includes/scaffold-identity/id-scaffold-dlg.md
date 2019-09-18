Execute o scaffolder de identidade:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Partir **Gerenciador de soluções**, clique com botão direito no projeto > **Add** > **New Scaffolded Item**.
* No painel à esquerda do **adicionar Scaffold** caixa de diálogo, selecione **identidade** > **adicionar**.
* Na caixa de diálogo **Adicionar identidade** , selecione as opções desejadas.
  * Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta. Por exemplo `~/Pages/Shared/_Layout.cshtml` , para `~/Views/Shared/_Layout.cshtml` Razor Pages para projetos MVC
  * Selecione o **+** botão para criar um novo **classe de contexto de dados**.
* Selecione **adicionar**.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de\*projeto (. csproj). Execute o seguinte comando no diretório do projeto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Execute o seguinte comando para listar as opções de scaffolder de identidade:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas. Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o seguinte comando:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
