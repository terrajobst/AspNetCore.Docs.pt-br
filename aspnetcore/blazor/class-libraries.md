---
title: ASP.NET Core bibliotecas de classes de componentes Razor
author: guardrex
description: Descubra como os componentes podem ser incluídos em aplicativos mais podestas de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/class-libraries
ms.openlocfilehash: 2e042b43c6db24e0ecac727be100575fe1275e17
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999781"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="f222a-103">ASP.NET Core bibliotecas de classes de componentes Razor</span><span class="sxs-lookup"><span data-stu-id="f222a-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="f222a-104">Por [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="f222a-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="f222a-105">Os componentes podem ser compartilhados em uma [biblioteca de classes Razor (RCL)](xref:razor-pages/ui-class) em projetos.</span><span class="sxs-lookup"><span data-stu-id="f222a-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="f222a-106">Uma *biblioteca de classes de componentes Razor* pode ser incluída em:</span><span class="sxs-lookup"><span data-stu-id="f222a-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="f222a-107">Outro projeto na solução.</span><span class="sxs-lookup"><span data-stu-id="f222a-107">Another project in the solution.</span></span>
* <span data-ttu-id="f222a-108">Um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="f222a-108">A NuGet package.</span></span>
* <span data-ttu-id="f222a-109">Uma biblioteca .NET referenciada.</span><span class="sxs-lookup"><span data-stu-id="f222a-109">A referenced .NET library.</span></span>

<span data-ttu-id="f222a-110">Assim como os componentes são tipos .NET regulares, os componentes fornecidos por um RCL são assemblies normais do .NET.</span><span class="sxs-lookup"><span data-stu-id="f222a-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="f222a-111">Criar um RCL</span><span class="sxs-lookup"><span data-stu-id="f222a-111">Create an RCL</span></span>

<span data-ttu-id="f222a-112">Siga as orientações no artigo <xref:blazor/get-started> para configurar seu ambiente para um mais alto.</span><span class="sxs-lookup"><span data-stu-id="f222a-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f222a-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f222a-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f222a-114">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="f222a-114">Create a new project.</span></span>
1. <span data-ttu-id="f222a-115">Selecione **biblioteca de classes Razor**.</span><span class="sxs-lookup"><span data-stu-id="f222a-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="f222a-116">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f222a-116">Select **Next**.</span></span>
1. <span data-ttu-id="f222a-117">Na caixa de diálogo **criar uma nova biblioteca de classes Razor** , selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="f222a-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="f222a-118">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="f222a-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f222a-119">Os exemplos neste tópico usam o nome do projeto `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="f222a-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="f222a-120">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f222a-120">Select **Create**.</span></span>
1. <span data-ttu-id="f222a-121">Adicione o RCL a uma solução:</span><span class="sxs-lookup"><span data-stu-id="f222a-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="f222a-122">Clique com o botão direito do mouse na solução.</span><span class="sxs-lookup"><span data-stu-id="f222a-122">Right-click the solution.</span></span> <span data-ttu-id="f222a-123">Selecione **adicionar** > **projeto existente**.</span><span class="sxs-lookup"><span data-stu-id="f222a-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="f222a-124">Navegue até o arquivo de projeto do RCL.</span><span class="sxs-lookup"><span data-stu-id="f222a-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="f222a-125">Selecione o arquivo de projeto do RCL ( *. csproj*).</span><span class="sxs-lookup"><span data-stu-id="f222a-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="f222a-126">Adicione uma referência ao RCL do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f222a-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="f222a-127">Clique com o botão direito do mouse no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f222a-127">Right-click the app project.</span></span> <span data-ttu-id="f222a-128">Selecione **Adicionar** **referência**de  > .</span><span class="sxs-lookup"><span data-stu-id="f222a-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="f222a-129">Selecione o projeto RCL.</span><span class="sxs-lookup"><span data-stu-id="f222a-129">Select the RCL project.</span></span> <span data-ttu-id="f222a-130">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f222a-130">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f222a-131">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f222a-131">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="f222a-132">Use o modelo de **biblioteca de classes Razor** (`razorclasslib`) com o [novo comando dotnet](/dotnet/core/tools/dotnet-new) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f222a-132">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="f222a-133">No exemplo a seguir, um RCL é criado com o nome `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="f222a-133">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="f222a-134">A pasta que mantém `MyComponentLib1` é criada automaticamente quando o comando é executado:</span><span class="sxs-lookup"><span data-stu-id="f222a-134">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="f222a-135">Para adicionar a biblioteca a um projeto existente, use o comando [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f222a-135">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="f222a-136">No exemplo a seguir, o RCL é adicionado ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f222a-136">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="f222a-137">Execute o seguinte comando na pasta do projeto do aplicativo com o caminho para a biblioteca:</span><span class="sxs-lookup"><span data-stu-id="f222a-137">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="f222a-138">Consumir um componente de biblioteca</span><span class="sxs-lookup"><span data-stu-id="f222a-138">Consume a library component</span></span>

<span data-ttu-id="f222a-139">Para consumir os componentes definidos em uma biblioteca em outro projeto, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="f222a-139">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="f222a-140">Use o nome completo do tipo com o namespace.</span><span class="sxs-lookup"><span data-stu-id="f222a-140">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="f222a-141">Use a diretiva [\@using](xref:mvc/views/razor#using) do Razor.</span><span class="sxs-lookup"><span data-stu-id="f222a-141">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="f222a-142">Componentes individuais podem ser adicionados por nome.</span><span class="sxs-lookup"><span data-stu-id="f222a-142">Individual components can be added by name.</span></span>

<span data-ttu-id="f222a-143">Nos exemplos a seguir, `MyComponentLib1` é uma biblioteca de componentes que contém um componente `SalesReport`.</span><span class="sxs-lookup"><span data-stu-id="f222a-143">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="f222a-144">O componente `SalesReport` pode ser referenciado usando seu nome de tipo completo com namespace:</span><span class="sxs-lookup"><span data-stu-id="f222a-144">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="f222a-145">O componente também poderá ser referenciado se a biblioteca for colocada no escopo com uma diretiva `@using`:</span><span class="sxs-lookup"><span data-stu-id="f222a-145">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="f222a-146">Inclua a diretiva `@using MyComponentLib1` no arquivo *_Import. Razor* de nível superior para disponibilizar os componentes da biblioteca para um projeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="f222a-146">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="f222a-147">Adicione a diretiva a um arquivo *_Import. Razor* em qualquer nível para aplicar o namespace a uma única página ou a um conjunto de páginas dentro de uma pasta.</span><span class="sxs-lookup"><span data-stu-id="f222a-147">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="f222a-148">Compilar, empacotar e enviar para o NuGet</span><span class="sxs-lookup"><span data-stu-id="f222a-148">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="f222a-149">Como as bibliotecas de componentes são bibliotecas padrão do .NET, o empacotamento e o envio deles para o NuGet não é diferente do empacotamento e do envio de nenhuma biblioteca para o NuGet.</span><span class="sxs-lookup"><span data-stu-id="f222a-149">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="f222a-150">O empacotamento é executado usando o comando [dotnet Pack](/dotnet/core/tools/dotnet-pack) em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f222a-150">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="f222a-151">Carregue o pacote para o NuGet usando o comando [dotnet NuGet Push](/dotnet/core/tools/dotnet-nuget-push) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f222a-151">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="f222a-152">Criar uma biblioteca de classes de componentes Razor com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="f222a-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="f222a-153">Um RCL pode incluir ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="f222a-153">An RCL can include static assets.</span></span> <span data-ttu-id="f222a-154">Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f222a-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="f222a-155">Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="f222a-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f222a-156">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f222a-156">Additional resources</span></span>

* <xref:razor-pages/ui-class>
