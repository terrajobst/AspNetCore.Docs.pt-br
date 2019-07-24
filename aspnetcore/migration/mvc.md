---
title: Migrar do ASP.NET MVC para o ASP.NET Core MVC
author: ardalis
description: Saiba como começar a migrar um projeto MVC do ASP.NET para ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: 6c9449fb43960d05db8aa6dcba64d3d830834cdb
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371878"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="e845f-103">Migrar do ASP.NET MVC para o ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="e845f-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="e845f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="e845f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="e845f-105">Este artigo mostra como começar a migração de um projeto MVC ASP.NET para [ASP.NET Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="e845f-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="e845f-106">No processo, ele realça muitas das coisas que foram alteradas do ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="e845f-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="e845f-107">A migração do ASP.NET MVC é um processo de várias etapas e este artigo aborda a configuração inicial, os controladores básicos e as exibições, o conteúdo estático e as dependências do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="e845f-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="e845f-108">Artigos adicionais abordam a migração de configuração e o código de identidade encontrados em muitos projetos do ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="e845f-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="e845f-109">Os números de versão nos exemplos podem não ser atuais.</span><span class="sxs-lookup"><span data-stu-id="e845f-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="e845f-110">Talvez seja necessário atualizar seus projetos adequadamente.</span><span class="sxs-lookup"><span data-stu-id="e845f-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="e845f-111">Criar o projeto do MVC do ASP.NET do início</span><span class="sxs-lookup"><span data-stu-id="e845f-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="e845f-112">Para demonstrar a atualização, começaremos criando um aplicativo MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e845f-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="e845f-113">Crie-o com o nome *WebApp1* para que o namespace corresponda ao projeto ASP.NET Core que criamos na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="e845f-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Caixa de diálogo novo projeto do Visual Studio](mvc/_static/new-project.png)

![Caixa de diálogo novo aplicativo Web: Modelo de projeto MVC selecionado no painel modelos de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="e845f-116">*Adicional* Altere o nome da solução de *WebApp1* para *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="e845f-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="e845f-117">O Visual Studio exibe o nome da nova solução (*Mvc5*), o que torna mais fácil dizer a esse projeto do próximo projeto.</span><span class="sxs-lookup"><span data-stu-id="e845f-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="e845f-118">Criar o projeto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e845f-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="e845f-119">Crie um novo aplicativo Web *vazio* ASP.NET Core com o mesmo nome do projeto anterior (*WebApp1*) para que os namespaces nos dois projetos correspondam.</span><span class="sxs-lookup"><span data-stu-id="e845f-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="e845f-120">Ter o mesmo namespace torna mais fácil copiar o código entre os dois projetos.</span><span class="sxs-lookup"><span data-stu-id="e845f-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="e845f-121">Você precisará criar esse projeto em um diretório diferente do projeto anterior para usar o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="e845f-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Caixa de diálogo Novo Projeto](mvc/_static/new_core.png)

![Caixa de diálogo novo aplicativo Web ASP.NET: Modelo de projeto vazio selecionado no painel modelos de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="e845f-124">*Adicional* Crie um novo aplicativo ASP.NET Core usando o modelo de projeto de *aplicativo Web* .</span><span class="sxs-lookup"><span data-stu-id="e845f-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="e845f-125">Nomeie o projeto *WebApp1*e selecione uma opção de autenticação de **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="e845f-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="e845f-126">Renomeie este aplicativo como *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="e845f-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="e845f-127">A criação desse projeto economiza tempo na conversão.</span><span class="sxs-lookup"><span data-stu-id="e845f-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="e845f-128">Você pode examinar o código gerado por modelo para ver o resultado final ou copiar o código para o projeto de conversão.</span><span class="sxs-lookup"><span data-stu-id="e845f-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="e845f-129">Também é útil quando você fica preso em uma etapa de conversão para comparar com o projeto gerado por modelo.</span><span class="sxs-lookup"><span data-stu-id="e845f-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="e845f-130">Configurar o site para usar o MVC</span><span class="sxs-lookup"><span data-stu-id="e845f-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="e845f-131">Ao direcionar o .NET Core, o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) é referenciado por padrão.</span><span class="sxs-lookup"><span data-stu-id="e845f-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="e845f-132">Este pacote contém pacotes comumente usados por aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="e845f-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="e845f-133">Se estiver direcionando .NET Framework, as referências de pacote deverão ser listadas individualmente no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="e845f-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="e845f-134">Ao direcionar o .NET Core, o [metapacote Microsoft. AspNetCore. All](xref:fundamentals/metapackage) é referenciado por padrão.</span><span class="sxs-lookup"><span data-stu-id="e845f-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="e845f-135">Este pacote contém pacotes comumente usados por aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="e845f-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="e845f-136">Se estiver direcionando .NET Framework, as referências de pacote deverão ser listadas individualmente no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="e845f-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="e845f-137">Ao direcionar .NET Core ou .NET Framework, pacotes usados por aplicativos MVC são listados individualmente no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="e845f-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="e845f-138">`Microsoft.AspNetCore.Mvc`é o ASP.NET Core MVC Framework.</span><span class="sxs-lookup"><span data-stu-id="e845f-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="e845f-139">`Microsoft.AspNetCore.StaticFiles`é o manipulador de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="e845f-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="e845f-140">O tempo de execução do ASP.NET Core é modular e você deve optar explicitamente por fornecer arquivos estáticos (consulte [arquivos estáticos](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="e845f-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="e845f-141">Abra o arquivo *Startup.cs* e altere o código para corresponder ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="e845f-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="e845f-142">O `UseStaticFiles` método de extensão adiciona o manipulador de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="e845f-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="e845f-143">Conforme mencionado anteriormente, o tempo de execução do ASP.NET é modular e você deve optar explicitamente por fornecer arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="e845f-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="e845f-144">O `UseMvc` método de extensão adiciona o roteamento.</span><span class="sxs-lookup"><span data-stu-id="e845f-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="e845f-145">Para obter mais informações, consulte inicialização e [Roteamento](xref:fundamentals/routing)de [aplicativos](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="e845f-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="e845f-146">Adicionar um controlador e uma exibição</span><span class="sxs-lookup"><span data-stu-id="e845f-146">Add a controller and view</span></span>

<span data-ttu-id="e845f-147">Nesta seção, você adicionará um controlador e uma exibição mínima para servir como espaços reservados para o controlador MVC ASP.NET e exibições que serão migradas na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="e845f-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="e845f-148">Adicione uma pasta de *controladores* .</span><span class="sxs-lookup"><span data-stu-id="e845f-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="e845f-149">Adicione uma **classe de controlador** denominada *HomeController.cs* à pasta Controllers.</span><span class="sxs-lookup"><span data-stu-id="e845f-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Caixa de diálogo Adicionar Novo Item](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="e845f-151">Adicione uma  pasta views.</span><span class="sxs-lookup"><span data-stu-id="e845f-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="e845f-152">Adicione uma pasta *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="e845f-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="e845f-153">Adicione uma **exibição do Razor** denominada *index. cshtml* à pasta *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="e845f-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Caixa de diálogo Adicionar Novo Item](mvc/_static/view.png)

<span data-ttu-id="e845f-155">A estrutura do projeto é mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="e845f-155">The project structure is shown below:</span></span>

![Gerenciador de Soluções mostrando arquivos e pastas do WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="e845f-157">Substitua o conteúdo do arquivo *views/home/index. cshtml* pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="e845f-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="e845f-158">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e845f-158">Run the app.</span></span>

![Aplicativo Web aberto no Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="e845f-160">Consulte [controladores](xref:mvc/controllers/actions) e [exibições](xref:mvc/views/overview) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="e845f-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="e845f-161">Agora que temos um projeto de ASP.NET Core de trabalho mínimo, podemos começar a migrar a funcionalidade do projeto ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="e845f-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="e845f-162">Precisamos mover o seguinte:</span><span class="sxs-lookup"><span data-stu-id="e845f-162">We need to move the following:</span></span>

* <span data-ttu-id="e845f-163">conteúdo do lado do cliente (CSS, fontes e scripts)</span><span class="sxs-lookup"><span data-stu-id="e845f-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="e845f-164">controladores</span><span class="sxs-lookup"><span data-stu-id="e845f-164">controllers</span></span>

* <span data-ttu-id="e845f-165">modos de exibição</span><span class="sxs-lookup"><span data-stu-id="e845f-165">views</span></span>

* <span data-ttu-id="e845f-166">modelos</span><span class="sxs-lookup"><span data-stu-id="e845f-166">models</span></span>

* <span data-ttu-id="e845f-167">reagrupamento</span><span class="sxs-lookup"><span data-stu-id="e845f-167">bundling</span></span>

* <span data-ttu-id="e845f-168">filtros</span><span class="sxs-lookup"><span data-stu-id="e845f-168">filters</span></span>

* <span data-ttu-id="e845f-169">Logon/saída, identidade (isso é feito no próximo tutorial.)</span><span class="sxs-lookup"><span data-stu-id="e845f-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="e845f-170">Controladores e exibições</span><span class="sxs-lookup"><span data-stu-id="e845f-170">Controllers and views</span></span>

* <span data-ttu-id="e845f-171">Copie cada um dos métodos do ASP.NET MVC `HomeController` para o novo. `HomeController`</span><span class="sxs-lookup"><span data-stu-id="e845f-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="e845f-172">Observe que no ASP.NET MVC, o tipo de retorno do método de ação do controlador interno do modelo é [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); no ASP.NET Core MVC, os métodos de ação `IActionResult` retornam.</span><span class="sxs-lookup"><span data-stu-id="e845f-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="e845f-173">`ActionResult`implementa `IActionResult`, portanto, não é necessário alterar o tipo de retorno de seus métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="e845f-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="e845f-174">Copie os arquivos de exibição *about. cshtml*, *Contact. cshtml*e *index. cshtml* Razor do projeto ASP.NET MVC para o projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e845f-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="e845f-175">Execute o aplicativo ASP.NET Core e teste cada método.</span><span class="sxs-lookup"><span data-stu-id="e845f-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="e845f-176">Ainda não migramos o arquivo de layout ou os estilos, portanto, as exibições renderizadas contêm apenas o conteúdo nos arquivos de exibição.</span><span class="sxs-lookup"><span data-stu-id="e845f-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="e845f-177">Você não terá os links gerados pelo arquivo de layout `About` para `Contact` as exibições e, portanto, precisará chamá-los do navegador (substitua **4492** pelo número da porta usada em seu projeto).</span><span class="sxs-lookup"><span data-stu-id="e845f-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Página de contato](mvc/_static/contact-page.png)

<span data-ttu-id="e845f-179">Observe a falta de estilos e itens de menu.</span><span class="sxs-lookup"><span data-stu-id="e845f-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="e845f-180">Corrigiremos isso na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="e845f-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="e845f-181">Conteúdo estático</span><span class="sxs-lookup"><span data-stu-id="e845f-181">Static content</span></span>

<span data-ttu-id="e845f-182">Nas versões anteriores do ASP.NET MVC, o conteúdo estático era hospedado da raiz do projeto Web e foi misturado com arquivos do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="e845f-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="e845f-183">No ASP.NET Core, o conteúdo estático é hospedado na pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="e845f-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="e845f-184">Você desejará copiar o conteúdo estático do seu aplicativo MVC ASP.NET antigo para a pasta *wwwroot* em seu projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e845f-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="e845f-185">Nesta conversão de exemplo:</span><span class="sxs-lookup"><span data-stu-id="e845f-185">In this sample conversion:</span></span>

* <span data-ttu-id="e845f-186">Copie o arquivo *favicon. ico* do projeto MVC antigo para a pasta *wwwroot* no projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e845f-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="e845f-187">O antigo projeto MVC ASP.NET usa a [inicialização](https://getbootstrap.com/) para seu estilo e armazena os arquivos de inicialização nas pastas *conteúdo* e *scripts* .</span><span class="sxs-lookup"><span data-stu-id="e845f-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="e845f-188">O modelo, que gerou o antigo projeto MVC do ASP.NET, faz referência à inicialização no arquivo de layout (*views/Shared/_ layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="e845f-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="e845f-189">Você pode copiar os arquivos *bootstrap. js* e *bootstrap. css* do projeto MVC ASP.net para a pasta *wwwroot* no novo projeto.</span><span class="sxs-lookup"><span data-stu-id="e845f-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="e845f-190">Em vez disso, adicionaremos suporte para bootstrap (e outras bibliotecas do lado do cliente) usando CDNs na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="e845f-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="e845f-191">Migrar o arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="e845f-191">Migrate the layout file</span></span>

* <span data-ttu-id="e845f-192">Copie o arquivo *_ViewStart. cshtml* da pasta views do projeto MVC  ASP.net antigo para a pasta views  do ASP.NET Core Project.</span><span class="sxs-lookup"><span data-stu-id="e845f-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="e845f-193">O arquivo *_ViewStart. cshtml* não foi alterado no ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="e845f-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="e845f-194">Crie uma pasta *views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="e845f-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="e845f-195">*Adicional* Copie *_ViewImports. cshtml* da pasta views do projeto  MVC *FullAspNetCore* para a pasta views  do ASP.NET Core Project.</span><span class="sxs-lookup"><span data-stu-id="e845f-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="e845f-196">Remova qualquer declaração de namespace no arquivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e845f-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="e845f-197">O arquivo *_ViewImports. cshtml* fornece namespaces para todos os arquivos de exibição e apresenta [auxiliares de marca](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e845f-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e845f-198">Os auxiliares de marca são usados no novo arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="e845f-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="e845f-199">O arquivo *_ViewImports. cshtml* é novo para ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e845f-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="e845f-200">Copie o arquivo *_ layout. cshtml* da pasta *views/Shared* do projeto do ASP.net antigo para a pasta *compartilhada/exibições* do projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e845f-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="e845f-201">Abra o arquivo *_ layout. cshtml* e faça as seguintes alterações (o código completo é mostrado abaixo):</span><span class="sxs-lookup"><span data-stu-id="e845f-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="e845f-202">Substituir `@Styles.Render("~/Content/css")` por um `<link>` elemento para carregar *bootstrap. css* (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="e845f-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="e845f-203">Remova `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="e845f-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="e845f-204">Comente a `@Html.Partial("_LoginPartial")` linha (circundando a linha com `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="e845f-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="e845f-205">Para obter mais informações, consulte [migrar autenticação e identidade para ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="e845f-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="e845f-206">Substituir `@Scripts.Render("~/bundles/jquery")` por um `<script>` elemento (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="e845f-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="e845f-207">Substituir `@Scripts.Render("~/bundles/bootstrap")` por um `<script>` elemento (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="e845f-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="e845f-208">A marcação de substituição para inclusão de CSS de Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="e845f-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="e845f-209">A marcação de substituição para a inclusão do jQuery e do JavaScript de Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="e845f-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="e845f-210">O arquivo *_ layout. cshtml* atualizado é mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="e845f-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="e845f-211">Exibir o site no navegador.</span><span class="sxs-lookup"><span data-stu-id="e845f-211">View the site in the browser.</span></span> <span data-ttu-id="e845f-212">Agora ele deve ser carregado corretamente, com os estilos esperados em vigor.</span><span class="sxs-lookup"><span data-stu-id="e845f-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="e845f-213">*Adicional* Talvez você queira tentar usar o novo arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="e845f-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="e845f-214">Para este projeto, você pode copiar o arquivo de layout do projeto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="e845f-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="e845f-215">O novo arquivo de layout usa auxiliares de [marca](xref:mvc/views/tag-helpers/intro) e tem outras melhorias.</span><span class="sxs-lookup"><span data-stu-id="e845f-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="e845f-216">Configurar agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="e845f-216">Configure bundling and minification</span></span>

<span data-ttu-id="e845f-217">Para obter informações sobre como configurar o agrupamento e o minificação, consulte [agrupamento e minificação](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="e845f-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="e845f-218">Resolver erros HTTP 500</span><span class="sxs-lookup"><span data-stu-id="e845f-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="e845f-219">Há muitos problemas que podem causar uma mensagem de erro HTTP 500 que não contém informações sobre a origem do problema.</span><span class="sxs-lookup"><span data-stu-id="e845f-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="e845f-220">Por exemplo, se o arquivo *views/_ViewImports. cshtml* contiver um namespace que não exista em seu projeto, você receberá um erro http 500.</span><span class="sxs-lookup"><span data-stu-id="e845f-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="e845f-221">Por padrão, em aplicativos ASP.NET Core, `UseDeveloperExceptionPage` a extensão é adicionada `IApplicationBuilder` ao e executada quando a configuração está em *desenvolvimento*.</span><span class="sxs-lookup"><span data-stu-id="e845f-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="e845f-222">Isso é detalhado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="e845f-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="e845f-223">ASP.NET Core converte exceções sem tratamento em um aplicativo Web em respostas de erro HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="e845f-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="e845f-224">Normalmente, os detalhes do erro não são incluídos nessas respostas para evitar a divulgação de informações potencialmente confidenciais sobre o servidor.</span><span class="sxs-lookup"><span data-stu-id="e845f-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="e845f-225">Consulte **usando a página de exceção do desenvolvedor** em [manipular erros](../fundamentals/error-handling.md) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="e845f-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e845f-226">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e845f-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
