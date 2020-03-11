::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e4b06-101">Execute o scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="e4b06-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e4b06-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e4b06-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e4b06-103">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="e4b06-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e4b06-104">No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **identidade** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e4b06-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="e4b06-105">Na caixa de diálogo **Adicionar identidade** , selecione as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="e4b06-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="e4b06-106">Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta.</span><span class="sxs-lookup"><span data-stu-id="e4b06-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="e4b06-107">Quando um arquivo *layout. cshtml de\_* existente é selecionado, ele **não** é substituído.</span><span class="sxs-lookup"><span data-stu-id="e4b06-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="e4b06-108">Por exemplo: `~/Pages/Shared/_Layout.cshtml` para Razor Pages `~/Views/Shared/_Layout.cshtml` para projetos MVC</span><span class="sxs-lookup"><span data-stu-id="e4b06-108">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="e4b06-109">Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="e4b06-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="e4b06-110">Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="e4b06-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="e4b06-111">Selecione sua classe de contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="e4b06-111">Select your data context class.</span></span>
  * <span data-ttu-id="e4b06-112">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e4b06-112">Select **Add**.</span></span>
* <span data-ttu-id="e4b06-113">Para criar um novo contexto de usuário e possivelmente criar uma classe de usuário personalizada para identidade:</span><span class="sxs-lookup"><span data-stu-id="e4b06-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="e4b06-114">Selecione o botão **+** para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="e4b06-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="e4b06-115">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e4b06-115">Select **Add**.</span></span>

<span data-ttu-id="e4b06-116">Observação: se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="e4b06-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e4b06-117">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e4b06-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e4b06-118">Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="e4b06-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="e4b06-119">Adicione as referências de pacote NuGet necessárias ao arquivo de projeto (\*. csproj).</span><span class="sxs-lookup"><span data-stu-id="e4b06-119">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="e4b06-120">Execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="e4b06-120">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="e4b06-121">Execute o seguinte comando para listar as opções de scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="e4b06-121">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="e4b06-122">Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="e4b06-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="e4b06-123">Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="e4b06-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="e4b06-124">Use o nome totalmente qualificado correto para seu contexto de BD:</span><span class="sxs-lookup"><span data-stu-id="e4b06-124">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="e4b06-125">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="e4b06-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="e4b06-126">Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas.</span><span class="sxs-lookup"><span data-stu-id="e4b06-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="e4b06-127">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e4b06-127">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="e4b06-128">Se você executar o scaffolder de identidade sem especificar o sinalizador de `--files` ou o sinalizador de `--useDefaultUI`, todas as páginas de interface do usuário de identidade disponíveis serão criadas em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="e4b06-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e4b06-129">Execute o scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="e4b06-129">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e4b06-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e4b06-130">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e4b06-131">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="e4b06-131">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e4b06-132">No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **identidade** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e4b06-132">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="e4b06-133">Na caixa de diálogo **Adicionar identidade** , selecione as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="e4b06-133">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="e4b06-134">Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta.</span><span class="sxs-lookup"><span data-stu-id="e4b06-134">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="e4b06-135">Quando um arquivo *layout. cshtml de\_* existente é selecionado, ele **não** é substituído.</span><span class="sxs-lookup"><span data-stu-id="e4b06-135">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="e4b06-136">Por exemplo: `~/Pages/Shared/_Layout.cshtml` para Razor Pages `~/Views/Shared/_Layout.cshtml` para projetos MVC</span><span class="sxs-lookup"><span data-stu-id="e4b06-136">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="e4b06-137">Para usar o contexto de dados existente, selecione pelo menos um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="e4b06-137">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="e4b06-138">Você deve selecionar pelo menos um arquivo para adicionar o contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="e4b06-138">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="e4b06-139">Selecione sua classe de contexto de dados.</span><span class="sxs-lookup"><span data-stu-id="e4b06-139">Select your data context class.</span></span>
  * <span data-ttu-id="e4b06-140">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e4b06-140">Select **Add**.</span></span>
* <span data-ttu-id="e4b06-141">Para criar um novo contexto de usuário e possivelmente criar uma classe de usuário personalizada para identidade:</span><span class="sxs-lookup"><span data-stu-id="e4b06-141">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="e4b06-142">Selecione o botão **+** para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="e4b06-142">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="e4b06-143">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="e4b06-143">Select **Add**.</span></span>

<span data-ttu-id="e4b06-144">Observação: se você estiver criando um novo contexto de usuário, não precisará selecionar um arquivo para substituir.</span><span class="sxs-lookup"><span data-stu-id="e4b06-144">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e4b06-145">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e4b06-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e4b06-146">Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="e4b06-146">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="e4b06-147">Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (\*. csproj).</span><span class="sxs-lookup"><span data-stu-id="e4b06-147">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="e4b06-148">Execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="e4b06-148">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="e4b06-149">Execute o seguinte comando para listar as opções de scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="e4b06-149">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="e4b06-150">Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="e4b06-150">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="e4b06-151">Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="e4b06-151">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="e4b06-152">Use o nome totalmente qualificado correto para seu contexto de BD:</span><span class="sxs-lookup"><span data-stu-id="e4b06-152">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="e4b06-153">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="e4b06-153">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="e4b06-154">Ao usar o PowerShell, escape os ponto-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas.</span><span class="sxs-lookup"><span data-stu-id="e4b06-154">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="e4b06-155">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e4b06-155">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="e4b06-156">Se você executar o scaffolder de identidade sem especificar o sinalizador de `--files` ou o sinalizador de `--useDefaultUI`, todas as páginas de interface do usuário de identidade disponíveis serão criadas em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="e4b06-156">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end