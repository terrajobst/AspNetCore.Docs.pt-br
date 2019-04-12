---
title: Bibliotecas de classes de componentes do Razor
author: guardrex
description: Descubra como os componentes podem ser incluídos em Razor componentes aplicativos de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/14/2019
uid: razor-components/class-libraries
ms.openlocfilehash: 1064ad60d90af15af483ba9bca5ed85fb63c2924
ms.sourcegitcommit: 10e14b85490f064395e9b2f423d21e3c2d39ed8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "59515323"
---
# <a name="razor-components-class-libraries"></a><span data-ttu-id="5aba6-103">Bibliotecas de classes de componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="5aba6-103">Razor Components Class Libraries</span></span>

<span data-ttu-id="5aba6-104">Por [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="5aba6-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="5aba6-105">Componentes podem ser compartilhados em bibliotecas de classes Razor entre projetos.</span><span class="sxs-lookup"><span data-stu-id="5aba6-105">Components can be shared in Razor class libraries across projects.</span></span> <span data-ttu-id="5aba6-106">Componentes podem ser incluídas nela:</span><span class="sxs-lookup"><span data-stu-id="5aba6-106">Components can be included from:</span></span>

* <span data-ttu-id="5aba6-107">Outro projeto na solução.</span><span class="sxs-lookup"><span data-stu-id="5aba6-107">Another project in the solution.</span></span>
* <span data-ttu-id="5aba6-108">Um pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="5aba6-108">A NuGet package.</span></span>
* <span data-ttu-id="5aba6-109">Uma biblioteca referenciada do .NET.</span><span class="sxs-lookup"><span data-stu-id="5aba6-109">A referenced .NET library.</span></span>

<span data-ttu-id="5aba6-110">Como componentes são tipos de .NET regulares, componentes fornecidos pelo bibliotecas de classes Razor são assemblies do .NET normais.</span><span class="sxs-lookup"><span data-stu-id="5aba6-110">Just as components are regular .NET types, components provided by Razor class libraries are normal .NET assemblies.</span></span>

<span data-ttu-id="5aba6-111">Use o `razorclasslib` modelo (biblioteca de classes Razor) com o [dotnet novo](/dotnet/core/tools/dotnet-new) comando:</span><span class="sxs-lookup"><span data-stu-id="5aba6-111">Use the `razorclasslib` (Razor class library) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command:</span></span>

```console
dotnet new razorclasslib -o MyComponentLib1
```

<span data-ttu-id="5aba6-112">Adicionar arquivos de componente do Razor (*.razor*) para a biblioteca de classes Razor.</span><span class="sxs-lookup"><span data-stu-id="5aba6-112">Add Razor Component files (*.razor*) to the Razor class library.</span></span>

<span data-ttu-id="5aba6-113">Para adicionar a biblioteca a um projeto existente, use o [dotnet sln](/dotnet/core/tools/dotnet-sln) comando:</span><span class="sxs-lookup"><span data-stu-id="5aba6-113">To add the library to an existing project, use the [dotnet sln](/dotnet/core/tools/dotnet-sln) command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5aba6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5aba6-114">Visual Studio</span></span>](#tab/visual-studio)

```console
dotnet sln add .\MyComponentLib1
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5aba6-115">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="5aba6-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet add WebApplication1 reference MyComponentLib1
```

---

> [!NOTE]
> <span data-ttu-id="5aba6-116">Bibliotecas de classes Razor não são compatíveis com aplicativos de Blazor no ASP.NET Core da visualização 3.</span><span class="sxs-lookup"><span data-stu-id="5aba6-116">Razor class libraries aren't compatible with Blazor apps in ASP.NET Core Preview 3.</span></span>
>
> <span data-ttu-id="5aba6-117">Para criar componentes em uma biblioteca que pode ser compartilhada com aplicativos Blazor e componentes do Razor, use uma biblioteca de classes Blazor criada pelo `blazorlib` modelo.</span><span class="sxs-lookup"><span data-stu-id="5aba6-117">To create components in a library that can be shared with Blazor and Razor Components apps, use a Blazor class library created by the `blazorlib` template.</span></span>
>
> <span data-ttu-id="5aba6-118">Bibliotecas de classes Razor não dão suporte a ativos estáticos no ASP.NET Core da visualização 3.</span><span class="sxs-lookup"><span data-stu-id="5aba6-118">Razor class libraries don't support static assets in ASP.NET Core Preview 3.</span></span> <span data-ttu-id="5aba6-119">Bibliotecas de componentes usando o `blazorlib` modelo pode incluir arquivos estáticos, como imagens, JavaScript e folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="5aba6-119">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="5aba6-120">No momento da compilação, arquivos estáticos são inseridos no arquivo de assembly compilado (*. dll*), que permite o consumo dos componentes sem precisar se preocupar sobre como incluir seus recursos.</span><span class="sxs-lookup"><span data-stu-id="5aba6-120">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="5aba6-121">Todos os arquivos incluídos no `content` directory são marcados como um recurso inserido.</span><span class="sxs-lookup"><span data-stu-id="5aba6-121">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="5aba6-122">Consumir um componente de biblioteca</span><span class="sxs-lookup"><span data-stu-id="5aba6-122">Consume a library component</span></span>

<span data-ttu-id="5aba6-123">Para consumir componentes definidos em uma biblioteca em outro projeto, o [ @addTagHelper ](xref:mvc/views/tag-helpers/intro#add-helper-label) diretiva deve ser usada.</span><span class="sxs-lookup"><span data-stu-id="5aba6-123">In order to consume components defined in a library in another project, the [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) directive must be used.</span></span> <span data-ttu-id="5aba6-124">Componentes individuais podem ser adicionados por nome.</span><span class="sxs-lookup"><span data-stu-id="5aba6-124">Individual components may be added by name.</span></span>

<span data-ttu-id="5aba6-125">O formato geral da diretiva é:</span><span class="sxs-lookup"><span data-stu-id="5aba6-125">The general format of the directive is:</span></span>

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="5aba6-126">Por exemplo, adiciona a seguinte diretiva `Component1` de `MyComponentLib1`:</span><span class="sxs-lookup"><span data-stu-id="5aba6-126">For example, the following directive adds `Component1` of `MyComponentLib1`:</span></span>

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1
```

<span data-ttu-id="5aba6-127">No entanto, é comum incluir todos os componentes de um assembly usando um caractere curinga (`*`):</span><span class="sxs-lookup"><span data-stu-id="5aba6-127">However, it's common to include all of the components from an assembly using a wildcard (`*`):</span></span>

```cshtml
@addTagHelper *, MyComponentLib1
```

<span data-ttu-id="5aba6-128">O `@addTagHelper` diretiva pode ser incluída no *_ViewImport.cshtml* para tornar os componentes disponíveis para um projeto inteiro ou aplicadas a uma única página ou um conjunto de páginas dentro de uma pasta.</span><span class="sxs-lookup"><span data-stu-id="5aba6-128">The `@addTagHelper` directive can be included in *_ViewImport.cshtml* to make the components available for an entire project or applied to a single page or set of pages within a folder.</span></span> <span data-ttu-id="5aba6-129">Com o `@addTagHelper` diretiva em vigor, os componentes da biblioteca de componentes podem ser consumidos como se estivessem no mesmo assembly que o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5aba6-129">With the `@addTagHelper` directive in place, the components of the component library can be consumed as if they were in the same assembly as the app.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="5aba6-130">Compilação, pacote e entregar para o NuGet</span><span class="sxs-lookup"><span data-stu-id="5aba6-130">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="5aba6-131">Como as bibliotecas de componentes são bibliotecas .NET padrão, empacotamento e enviá-los para o NuGet não é diferente de empacotamento e envio de qualquer biblioteca no NuGet.</span><span class="sxs-lookup"><span data-stu-id="5aba6-131">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="5aba6-132">Empacotamento é realizado usando o [dotnet pack](/dotnet/core/tools/dotnet-pack) comando:</span><span class="sxs-lookup"><span data-stu-id="5aba6-132">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command:</span></span>

```console
dotnet pack
```

<span data-ttu-id="5aba6-133">Carregue o pacote NuGet usando o [dotnet nuget publicar](/dotnet/core/tools/dotnet-nuget-push) comando:</span><span class="sxs-lookup"><span data-stu-id="5aba6-133">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="5aba6-134">Ao usar o `blazorlib` modelo, recursos estáticos são incluídos no pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="5aba6-134">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="5aba6-135">Os clientes de biblioteca recebem automaticamente scripts e folhas de estilo, para que os consumidores não é necessários instalar manualmente os recursos.</span><span class="sxs-lookup"><span data-stu-id="5aba6-135">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>
