<span data-ttu-id="f01f3-101">Execute o scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="f01f3-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f01f3-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f01f3-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f01f3-103">Partir **Gerenciador de soluções**, clique com botão direito no projeto > **Add** > **New Scaffolded Item**.</span><span class="sxs-lookup"><span data-stu-id="f01f3-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f01f3-104">No painel à esquerda do **adicionar Scaffold** caixa de diálogo, selecione **identidade** > **adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f01f3-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="f01f3-105">Na caixa de diálogo **Adicionar identidade** , selecione as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="f01f3-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="f01f3-106">Selecione a página de layout existente ou o arquivo de layout será substituído por marcação incorreta.</span><span class="sxs-lookup"><span data-stu-id="f01f3-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="f01f3-107">Por exemplo `~/Pages/Shared/_Layout.cshtml` para Razor Pages `~/Views/Shared/_Layout.cshtml` para projetos MVC</span><span class="sxs-lookup"><span data-stu-id="f01f3-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="f01f3-108">Selecione o **+** botão para criar um novo **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="f01f3-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="f01f3-109">Selecione **adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f01f3-109">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f01f3-110">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f01f3-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f01f3-111">Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="f01f3-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="f01f3-112">Adicione as referências de pacote NuGet necessárias ao arquivo de projeto (\*. csproj).</span><span class="sxs-lookup"><span data-stu-id="f01f3-112">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="f01f3-113">Execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="f01f3-113">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="f01f3-114">Execute o seguinte comando para listar as opções de scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="f01f3-114">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="f01f3-115">Na pasta do projeto, execute o scaffolder de identidade com as opções desejadas.</span><span class="sxs-lookup"><span data-stu-id="f01f3-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="f01f3-116">Por exemplo, para configurar a identidade com a interface do usuário padrão e o número mínimo de arquivos, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f01f3-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
