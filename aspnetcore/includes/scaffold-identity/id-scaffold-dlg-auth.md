Execute o scaffolder de identidade:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Partir **Gerenciador de soluções**, clique com botão direito no projeto > **Add** > **New Scaffolded Item**.
* No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **identidade** > **Adicionar**.
* Na caixa de diálogo **Adicionar identidade** , selecione as opções desejadas.
  * Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta. Quando um arquivo  *\_layout. cshtml* existente é selecionado, ele **não** é substituído.

 Por exemplo: `~/Pages/Shared/_Layout.cshtml` para Razor Pages `~/Views/Shared/_Layout.cshtml` para projetos MVC
* Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir. Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.
  * Selecione sua classe de contexto de dados.
  * Selecione **Adicionar**.
* Para criar um novo contexto de usuário e possivelmente criar uma classe de usuário personalizada para identidade:
  * Selecione o **+** botão para criar um novo **classe de contexto de dados**.
  * Selecione **Adicionar**.

Observação: Se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de\*projeto (. csproj). Execute o seguinte comando no diretório do projeto:

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Execute o seguinte comando para listar as opções de scaffolder de identidade:

```console
dotnet aspnet-codegenerator identity -h
```

Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas. Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir. Use o nome totalmente qualificado correto para seu contexto de BD:

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

O PowerShell usa ponto e vírgula como um separador de comando. Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas. Por exemplo:

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Se você executar o scaffolder de identidade sem especificar `--files` o sinalizador ou `--useDefaultUI` o sinalizador, todas as páginas de interface do usuário de identidade disponíveis serão criadas em seu projeto.

---
