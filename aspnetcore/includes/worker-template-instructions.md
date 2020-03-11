# <a name="visual-studio"></a>[<span data-ttu-id="e8406-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8406-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e8406-102">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="e8406-102">Create a new project.</span></span>
1. <span data-ttu-id="e8406-103">Selecione **serviço de trabalho**.</span><span class="sxs-lookup"><span data-stu-id="e8406-103">Select **Worker Service**.</span></span> <span data-ttu-id="e8406-104">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e8406-104">Select **Next**.</span></span>
1. <span data-ttu-id="e8406-105">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="e8406-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="e8406-106">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e8406-106">Select **Create**.</span></span>
1. <span data-ttu-id="e8406-107">Na caixa de diálogo **criar um novo serviço de trabalho** , selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="e8406-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e8406-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e8406-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e8406-109">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="e8406-109">Create a new project.</span></span>
1. <span data-ttu-id="e8406-110">Selecione **aplicativo** em **.NET Core** na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="e8406-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="e8406-111">Selecione **trabalhador** em **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e8406-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="e8406-112">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e8406-112">Select **Next**.</span></span>
1. <span data-ttu-id="e8406-113">Selecione **.NET Core 3,0** ou posterior para a **estrutura de destino**.</span><span class="sxs-lookup"><span data-stu-id="e8406-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="e8406-114">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="e8406-114">Select **Next**.</span></span>
1. <span data-ttu-id="e8406-115">Forneça um nome no campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="e8406-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="e8406-116">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="e8406-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e8406-117">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e8406-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e8406-118">Use o modelo de Serviço de Trabalho (`worker`) com o comando [dotnet novo](/dotnet/core/tools/dotnet-new) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="e8406-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="e8406-119">No exemplo a seguir, um aplicativo de Serviço de Trabalho é criado com o nome `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="e8406-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="e8406-120">Uma pasta para o aplicativo `ContosoWorker` é criada automaticamente quando o comando é executado.</span><span class="sxs-lookup"><span data-stu-id="e8406-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
