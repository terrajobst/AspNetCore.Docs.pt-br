---
title: ASP.NET Core bibliotecas de classes de componentes Razor
author: guardrex
description: Descubra como os componentes podem ser incluídos em aplicativos mais podestas de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/class-libraries
ms.openlocfilehash: 402b7b072554f63f85e7cf5e55336104d235a071
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68948436"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="f9b35-103">ASP.NET Core bibliotecas de classes de componentes Razor</span><span class="sxs-lookup"><span data-stu-id="f9b35-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="f9b35-104">Por [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="f9b35-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="f9b35-105">Os componentes podem ser compartilhados em uma [biblioteca de classes Razor (RCL)](xref:razor-pages/ui-class) em projetos.</span><span class="sxs-lookup"><span data-stu-id="f9b35-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="f9b35-106">Uma *biblioteca de classes de componentes Razor* pode ser incluída em:</span><span class="sxs-lookup"><span data-stu-id="f9b35-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="f9b35-107">Outro projeto na solução.</span><span class="sxs-lookup"><span data-stu-id="f9b35-107">Another project in the solution.</span></span>
* <span data-ttu-id="f9b35-108">Um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="f9b35-108">A NuGet package.</span></span>
* <span data-ttu-id="f9b35-109">Uma biblioteca .NET referenciada.</span><span class="sxs-lookup"><span data-stu-id="f9b35-109">A referenced .NET library.</span></span>

<span data-ttu-id="f9b35-110">Assim como os componentes são tipos .NET regulares, os componentes fornecidos por um RCL são assemblies normais do .NET.</span><span class="sxs-lookup"><span data-stu-id="f9b35-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="f9b35-111">Criar um RCL</span><span class="sxs-lookup"><span data-stu-id="f9b35-111">Create an RCL</span></span>

<span data-ttu-id="f9b35-112">Siga as orientações no <xref:blazor/get-started> artigo para configurar seu ambiente para um mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="f9b35-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9b35-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9b35-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f9b35-114">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="f9b35-114">Create a new project.</span></span>
1. <span data-ttu-id="f9b35-115">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f9b35-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f9b35-116">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f9b35-116">Select **Next**.</span></span>
1. <span data-ttu-id="f9b35-117">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="f9b35-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f9b35-118">Os exemplos neste tópico usam o nome `MyComponentLib1`do projeto.</span><span class="sxs-lookup"><span data-stu-id="f9b35-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="f9b35-119">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f9b35-119">Select **Create**.</span></span>
1. <span data-ttu-id="f9b35-120">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 3.0** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="f9b35-120">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="f9b35-121">Selecione o modelo de **biblioteca de classes Razor** .</span><span class="sxs-lookup"><span data-stu-id="f9b35-121">Select the **Razor Class Library** template.</span></span> <span data-ttu-id="f9b35-122">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f9b35-122">Select **Create**.</span></span>
1. <span data-ttu-id="f9b35-123">Adicione o RCL a uma solução:</span><span class="sxs-lookup"><span data-stu-id="f9b35-123">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="f9b35-124">Clique com o botão direito do mouse na solução.</span><span class="sxs-lookup"><span data-stu-id="f9b35-124">Right-click the solution.</span></span> <span data-ttu-id="f9b35-125">Selecione **Adicionar** > **projeto existente**.</span><span class="sxs-lookup"><span data-stu-id="f9b35-125">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="f9b35-126">Navegue até o arquivo de projeto do RCL.</span><span class="sxs-lookup"><span data-stu-id="f9b35-126">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="f9b35-127">Selecione o arquivo de projeto do RCL ( *. csproj*).</span><span class="sxs-lookup"><span data-stu-id="f9b35-127">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="f9b35-128">Adicione uma referência ao RCL do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f9b35-128">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="f9b35-129">Clique com o botão direito do mouse no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b35-129">Right-click the app project.</span></span> <span data-ttu-id="f9b35-130">Selecione **Adicionar** > **referência**.</span><span class="sxs-lookup"><span data-stu-id="f9b35-130">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="f9b35-131">Selecione o projeto RCL.</span><span class="sxs-lookup"><span data-stu-id="f9b35-131">Select the RCL project.</span></span> <span data-ttu-id="f9b35-132">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="f9b35-132">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f9b35-133">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f9b35-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="f9b35-134">Use o modelo de **biblioteca de classes Razor** (`razorclasslib`) com o comando [dotnet New](/dotnet/core/tools/dotnet-new) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f9b35-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="f9b35-135">No exemplo a seguir, um RCL é criado com `MyComponentLib1`o nome.</span><span class="sxs-lookup"><span data-stu-id="f9b35-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="f9b35-136">A pasta que contém `MyComponentLib1` é criada automaticamente quando o comando é executado:</span><span class="sxs-lookup"><span data-stu-id="f9b35-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="f9b35-137">Para adicionar a biblioteca a um projeto existente, use o comando [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="f9b35-137">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="f9b35-138">No exemplo a seguir, o RCL é adicionado ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9b35-138">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="f9b35-139">Execute o seguinte comando na pasta do projeto do aplicativo com o caminho para a biblioteca:</span><span class="sxs-lookup"><span data-stu-id="f9b35-139">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a><span data-ttu-id="f9b35-140">RCLs não tem suporte para aplicativos do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="f9b35-140">RCLs not supported for client-side apps</span></span>

<span data-ttu-id="f9b35-141">Na versão prévia atual do ASP.NET Core 3,0, as bibliotecas de classes do Razor não são compatíveis com aplicativos de alto nível do cliente.</span><span class="sxs-lookup"><span data-stu-id="f9b35-141">In the current ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span> <span data-ttu-id="f9b35-142">Para aplicativos do lado do cliente mais incrivelmente, use uma biblioteca de componentes mais incrivelmente criada `blazorlib` pelo modelo em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f9b35-142">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template in a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="f9b35-143">As bibliotecas de componentes `blazorlib` que usam o modelo podem incluir arquivos estáticos, como imagens, JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="f9b35-143">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="f9b35-144">No momento da compilação, os arquivos estáticos são inseridos no arquivo de assembly compilado ( *. dll*), que permite o consumo dos componentes sem precisar se preocupar com a inclusão de seus recursos.</span><span class="sxs-lookup"><span data-stu-id="f9b35-144">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="f9b35-145">Todos os arquivos incluídos no `content` diretório são marcados como um recurso incorporado.</span><span class="sxs-lookup"><span data-stu-id="f9b35-145">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="f9b35-146">Consumir um componente de biblioteca</span><span class="sxs-lookup"><span data-stu-id="f9b35-146">Consume a library component</span></span>

<span data-ttu-id="f9b35-147">Para consumir os componentes definidos em uma biblioteca em outro projeto, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="f9b35-147">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="f9b35-148">Use o nome completo do tipo com o namespace.</span><span class="sxs-lookup"><span data-stu-id="f9b35-148">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="f9b35-149">Use a diretiva [ \@using](xref:mvc/views/razor#using) do Razor.</span><span class="sxs-lookup"><span data-stu-id="f9b35-149">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="f9b35-150">Componentes individuais podem ser adicionados por nome.</span><span class="sxs-lookup"><span data-stu-id="f9b35-150">Individual components can be added by name.</span></span>

<span data-ttu-id="f9b35-151">Nos exemplos a seguir, `MyComponentLib1` é uma biblioteca de componentes que `SalesReport` contém um componente.</span><span class="sxs-lookup"><span data-stu-id="f9b35-151">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="f9b35-152">O `SalesReport` componente pode ser referenciado usando seu nome de tipo completo com namespace:</span><span class="sxs-lookup"><span data-stu-id="f9b35-152">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="f9b35-153">O componente também poderá ser referenciado se a biblioteca for colocada no escopo com `@using` uma diretiva:</span><span class="sxs-lookup"><span data-stu-id="f9b35-153">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="f9b35-154">Inclua a `@using MyComponentLib1` diretiva no arquivo *_Import. Razor* de nível superior para disponibilizar os componentes da biblioteca para um projeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="f9b35-154">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="f9b35-155">Adicione a diretiva a um arquivo *_Import. Razor* em qualquer nível para aplicar o namespace a uma única página ou a um conjunto de páginas dentro de uma pasta.</span><span class="sxs-lookup"><span data-stu-id="f9b35-155">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="f9b35-156">Compilar, empacotar e enviar para o NuGet</span><span class="sxs-lookup"><span data-stu-id="f9b35-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="f9b35-157">Como as bibliotecas de componentes são bibliotecas padrão do .NET, o empacotamento e o envio deles para o NuGet não é diferente do empacotamento e do envio de nenhuma biblioteca para o NuGet.</span><span class="sxs-lookup"><span data-stu-id="f9b35-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="f9b35-158">O empacotamento é executado usando o comando [dotnet Pack](/dotnet/core/tools/dotnet-pack) em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f9b35-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="f9b35-159">Carregue o pacote no NuGet usando o comando [dotnet NuGet Publish](/dotnet/core/tools/dotnet-nuget-push) em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="f9b35-159">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="f9b35-160">Ao usar o `blazorlib` modelo, os recursos estáticos são incluídos no pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="f9b35-160">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="f9b35-161">Os consumidores de biblioteca recebem scripts e folhas de estilo automaticamente, para que os consumidores não sejam solicitados a instalar os recursos manualmente.</span><span class="sxs-lookup"><span data-stu-id="f9b35-161">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="f9b35-162">Criar uma biblioteca de classes de componentes Razor com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="f9b35-162">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="f9b35-163">Um RCL pode incluir ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="f9b35-163">An RCL can include static assets.</span></span> <span data-ttu-id="f9b35-164">Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f9b35-164">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="f9b35-165">Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="f9b35-165">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9b35-166">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f9b35-166">Additional resources</span></span>

* <xref:razor-pages/ui-class>
