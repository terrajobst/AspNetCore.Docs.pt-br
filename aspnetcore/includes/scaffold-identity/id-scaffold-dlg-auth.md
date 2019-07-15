<span data-ttu-id="bed65-101">Execute o scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="bed65-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bed65-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bed65-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bed65-103">Partir **Gerenciador de soluções**, clique com botão direito no projeto > **Add** > **New Scaffolded Item**.</span><span class="sxs-lookup"><span data-stu-id="bed65-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="bed65-104">No painel à esquerda do **adicionar Scaffold** caixa de diálogo, selecione **identidade** > **adicionar**.</span><span class="sxs-lookup"><span data-stu-id="bed65-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="bed65-105">No **identidade de adição** caixa de diálogo, selecione as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="bed65-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="bed65-106">Selecione a página de layout existente ou seu arquivo de layout será substituído pela marcação incorreta.</span><span class="sxs-lookup"><span data-stu-id="bed65-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="bed65-107">Quando um existente  *\_layout. cshtml* arquivo for selecionado, ele é **não** substituídos.</span><span class="sxs-lookup"><span data-stu-id="bed65-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="bed65-108">Por exemplo `~/Pages/Shared/_Layout.cshtml` para as páginas Razor `~/Views/Shared/_Layout.cshtml` para projetos MVC</span><span class="sxs-lookup"><span data-stu-id="bed65-108">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="bed65-109">Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="bed65-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="bed65-110">Você deve selecionar pelo menos um arquivo para adicionar seu contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="bed65-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="bed65-111">Selecione sua classe de contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="bed65-111">Select your data context class.</span></span>
  * <span data-ttu-id="bed65-112">Selecione **adicionar**.</span><span class="sxs-lookup"><span data-stu-id="bed65-112">Select **ADD**.</span></span>
* <span data-ttu-id="bed65-113">Para criar um novo contexto de usuário e, possivelmente, crie uma classe de usuário personalizada para a identidade:</span><span class="sxs-lookup"><span data-stu-id="bed65-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="bed65-114">Selecione o **+** botão para criar um novo **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="bed65-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="bed65-115">Selecione **adicionar**.</span><span class="sxs-lookup"><span data-stu-id="bed65-115">Select **ADD**.</span></span>

<span data-ttu-id="bed65-116">Observação: Se você estiver criando um novo contexto de usuário, você não precisa selecionar um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="bed65-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bed65-117">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="bed65-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bed65-118">Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="bed65-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="bed65-119">Adicione uma referência de pacote ao [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao projeto (\*. csproj) arquivos.</span><span class="sxs-lookup"><span data-stu-id="bed65-119">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="bed65-120">Execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="bed65-120">Run the following command in the project directory:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="bed65-121">Execute o seguinte comando para listar as opções de scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="bed65-121">Run the following command to list the Identity scaffolder options:</span></span>

```console
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="bed65-122">Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="bed65-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="bed65-123">Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="bed65-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="bed65-124">Use o nome totalmente qualificado correto para o contexto de banco de dados:</span><span class="sxs-lookup"><span data-stu-id="bed65-124">Use the correct fully qualified name for your DB context:</span></span>

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

<span data-ttu-id="bed65-125">O PowerShell usa o ponto e vírgula como separador de comando.</span><span class="sxs-lookup"><span data-stu-id="bed65-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="bed65-126">Ao usar o PowerShell, a ponto e vírgula na lista de arquivos de escape ou coloque a lista de arquivos entre aspas duplas.</span><span class="sxs-lookup"><span data-stu-id="bed65-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="bed65-127">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bed65-127">For example:</span></span>

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="bed65-128">Se você executar o scaffolder de identidade sem especificar o `--files` sinalizador ou a `--useDefaultUI` sinalizar, todas as páginas de identidade da interface do usuário disponíveis em seu projeto serão criadas.</span><span class="sxs-lookup"><span data-stu-id="bed65-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---
