::: moniker range=">= aspnetcore-3.0"

Execute o scaffolder de identidade:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.
* No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **identidade** > **Adicionar**.
* Na caixa de diálogo **Adicionar identidade** , selecione as opções desejadas.
  * Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta. Quando um arquivo *layout. cshtml de\_* existente é selecionado, ele **não** é substituído.

 Por exemplo: `~/Pages/Shared/_Layout.cshtml` para Razor Pages `~/Views/Shared/_Layout.cshtml` para projetos MVC
* Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir. Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.
  * Selecione sua classe de contexto de dados.
  * Selecione **Adicionar**.
* Para criar um novo contexto de usuário e possivelmente criar uma classe de usuário personalizada para identidade:
  * Selecione o botão **+** para criar uma nova **classe de contexto de dados**.
  * Selecione **Adicionar**.

Observação: se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione as referências de pacote NuGet necessárias ao arquivo de projeto (\*. csproj). Execute o seguinte comando no diretório do projeto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Execute o seguinte comando para listar as opções de scaffolder de identidade:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas. Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir. Use o nome totalmente qualificado correto para seu contexto de BD:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

O PowerShell usa ponto e vírgula como um separador de comando. Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas. Por exemplo:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Se você executar o scaffolder de identidade sem especificar o sinalizador de `--files` ou o sinalizador de `--useDefaultUI`, todas as páginas de interface do usuário de identidade disponíveis serão criadas em seu projeto.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Execute o scaffolder de identidade:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.
* No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **identidade** > **Adicionar**.
* Na caixa de diálogo **Adicionar identidade** , selecione as opções desejadas.
  * Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta. Quando um arquivo *layout. cshtml de\_* existente é selecionado, ele **não** é substituído.

 Por exemplo: `~/Pages/Shared/_Layout.cshtml` para Razor Pages `~/Views/Shared/_Layout.cshtml` para projetos MVC
* Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir. Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.
  * Selecione sua classe de contexto de dados.
  * Selecione **Adicionar**.
* Para criar um novo contexto de usuário e possivelmente criar uma classe de usuário personalizada para identidade:
  * Selecione o botão **+** para criar uma nova **classe de contexto de dados**.
  * Selecione **Adicionar**.

Observação: se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (\*. csproj). Execute o seguinte comando no diretório do projeto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Execute o seguinte comando para listar as opções de scaffolder de identidade:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas. Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir. Use o nome totalmente qualificado correto para seu contexto de BD:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

O PowerShell usa ponto e vírgula como um separador de comando. Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas. Por exemplo:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Se você executar o scaffolder de identidade sem especificar o sinalizador de `--files` ou o sinalizador de `--useDefaultUI`, todas as páginas de interface do usuário de identidade disponíveis serão criadas em seu projeto.

---

::: moniker-end