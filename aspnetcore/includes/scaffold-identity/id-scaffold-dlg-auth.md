Execute o scaffolder de identidade:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Partir **Gerenciador de soluções**, clique com botão direito no projeto > **Add** > **New Scaffolded Item**.
* No painel à esquerda do **adicionar Scaffold** caixa de diálogo, selecione **identidade** > **adicionar**.
* No **identidade de adição** caixa de diálogo, selecione as opções desejadas.
  * Selecione a página de layout existente ou seu arquivo de layout será substituído pela marcação incorreta. Quando um existente  *\_layout. cshtml* arquivo for selecionado, ele é **não** substituídos.

 Por exemplo `~/Pages/Shared/_Layout.cshtml` para as páginas Razor `~/Views/Shared/_Layout.cshtml` para projetos MVC
* Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir. Você deve selecionar pelo menos um arquivo para adicionar seu contexto de dados.
  * Selecione sua classe de contexto de dados.
  * Selecione **adicionar**.
* Para criar um novo contexto de usuário e, possivelmente, crie uma classe de usuário personalizada para a identidade:
  * Selecione o **+** botão para criar um novo **classe de contexto de dados**.
  * Selecione **adicionar**.

Observação: Se você estiver criando um novo contexto de usuário, você não precisa selecionar um arquivo para substituir.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

Adicione uma referência de pacote ao [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao projeto (\*. csproj) arquivos. Execute o seguinte comando no diretório do projeto:

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Execute o seguinte comando para listar as opções de scaffolder de identidade:

```console
dotnet aspnet-codegenerator identity -h
```

Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas. Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir. Use o nome totalmente qualificado correto para o contexto de banco de dados:

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

O PowerShell usa o ponto e vírgula como separador de comando. Ao usar o PowerShell, a ponto e vírgula na lista de arquivos de escape ou coloque a lista de arquivos entre aspas duplas. Por exemplo:

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Se você executar o scaffolder de identidade sem especificar o `--files` sinalizador ou a `--useDefaultUI` sinalizar, todas as páginas de identidade da interface do usuário disponíveis em seu projeto serão criadas.

---
