---
title: Migrar do ASP.NET MVC para ASP.NET Core MVC
author: ardalis
description: Saiba como começar a migrar um projeto ASP.NET MVC para ASP.NET Core MVC.
ms.author: riande
ms.date: 02/13/2019
uid: migration/mvc
ms.openlocfilehash: 2ca51a145243444722ad8081fd8cdbb65d72b53a
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248037"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="40751-103">Migrar do ASP.NET MVC para ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="40751-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="40751-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="40751-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="40751-105">Este artigo mostra como começar a migrar um projeto ASP.NET MVC para [ASP.NET Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="40751-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="40751-106">No processo, ele destaca muitas das coisas que mudaram do ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="40751-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="40751-107">Migrando do ASP.NET MVC é um processo de várias etapas e este artigo aborda a configuração inicial, básicas controladores e modos de exibição, conteúdo estático e as dependências do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="40751-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="40751-108">Artigos adicionais abrangem a migração de configuração e o código de identidade encontrados em muitos projetos do ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="40751-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="40751-109">Os números de versão nos exemplos podem não ser atuais.</span><span class="sxs-lookup"><span data-stu-id="40751-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="40751-110">Talvez você precise atualizar seus projetos de acordo.</span><span class="sxs-lookup"><span data-stu-id="40751-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="40751-111">Criar o projeto do MVC do ASP.NET starter</span><span class="sxs-lookup"><span data-stu-id="40751-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="40751-112">Para demonstrar a atualização, vamos começar criando um aplicativo ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="40751-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="40751-113">Criá-lo com o nome *WebApp1* para o namespace coincida com o projeto do ASP.NET Core que criamos na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="40751-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Caixa de diálogo Novo projeto do Studio Visual](mvc/_static/new-project.png)

![Nova caixa de diálogo do aplicativo Web: Modelo de projeto do MVC selecionado no painel de modelos do ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="40751-116">*Opcional:* Altere o nome da solução do *WebApp1* à *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="40751-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="40751-117">O Visual Studio exibe o novo nome da solução (*Mvc5*), que torna mais fácil de informar deste projeto no próximo projeto.</span><span class="sxs-lookup"><span data-stu-id="40751-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="40751-118">Criar o projeto do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40751-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="40751-119">Criar um novo *vazio* aplicativo de web do ASP.NET Core com o mesmo nome que o projeto anterior (*WebApp1*) para que os namespaces em dois projetos sejam correspondentes.</span><span class="sxs-lookup"><span data-stu-id="40751-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="40751-120">Ter o mesmo namespace torna mais fácil copiar o código entre os dois projetos.</span><span class="sxs-lookup"><span data-stu-id="40751-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="40751-121">Você precisará criar esse projeto em um diretório diferente do projeto anterior para usar o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="40751-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Caixa de diálogo Novo Projeto](mvc/_static/new_core.png)

![Nova caixa de diálogo de aplicativo Web ASP.NET: Modelo de projeto vazio selecionado no painel de modelos do ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="40751-124">*Opcional:* Criar um novo aplicativo de ASP.NET Core usando o *aplicativo Web* modelo de projeto.</span><span class="sxs-lookup"><span data-stu-id="40751-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="40751-125">Nomeie o projeto *WebApp1*e selecione uma opção de autenticação do **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="40751-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="40751-126">Renomear este aplicativo seja *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="40751-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="40751-127">Isso poupa de projeto tempo criando a conversão.</span><span class="sxs-lookup"><span data-stu-id="40751-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="40751-128">Você pode examinar o código gerado pelo modelo para ver o resultado final ou copiar o código para o projeto de conversão.</span><span class="sxs-lookup"><span data-stu-id="40751-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="40751-129">Também é útil quando você ficar preso em uma etapa de conversão a ser comparado com o projeto de modelo gerado.</span><span class="sxs-lookup"><span data-stu-id="40751-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="40751-130">Configurar o site para usar o MVC</span><span class="sxs-lookup"><span data-stu-id="40751-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="40751-131">Ao direcionar o .NET Core, o [metapacote Microsoft](xref:fundamentals/metapackage-app) é referenciado por padrão.</span><span class="sxs-lookup"><span data-stu-id="40751-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="40751-132">Este pacote contém pacotes de pacotes usados pelos aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="40751-132">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="40751-133">Se o destino do .NET Framework, referências de pacote devem estar listadas individualmente no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="40751-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="40751-134">Ao direcionar o .NET Core, o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage) é referenciado por padrão.</span><span class="sxs-lookup"><span data-stu-id="40751-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="40751-135">Este pacote contém pacotes de pacotes usados pelos aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="40751-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="40751-136">Se o destino do .NET Framework, referências de pacote devem estar listadas individualmente no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="40751-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="40751-137">Ao direcionar o .NET Core ou .NET Framework, pacotes de pacotes usados pelos aplicativos MVC estão listados individualmente no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="40751-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="40751-138">`Microsoft.AspNetCore.Mvc` é a estrutura do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="40751-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="40751-139">`Microsoft.AspNetCore.StaticFiles` é o manipulador de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="40751-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="40751-140">O tempo de execução do ASP.NET Core é modular e você deve explicitamente optar por fornecer arquivos estáticos (consulte [arquivos estáticos](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="40751-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="40751-141">Abra o *Startup.cs* de arquivo e altere o código para corresponder ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="40751-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="40751-142">O `UseStaticFiles` método de extensão adiciona o manipulador de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="40751-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="40751-143">Conforme mencionado anteriormente, o tempo de execução do ASP.NET é modular e você deve explicitamente optar por fornecer arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="40751-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="40751-144">O `UseMvc` adiciona o método de extensão roteamento.</span><span class="sxs-lookup"><span data-stu-id="40751-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="40751-145">Para obter mais informações, consulte [inicialização do aplicativo](xref:fundamentals/startup) e [roteamento](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="40751-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="40751-146">Adicionar um controlador e o modo de exibição</span><span class="sxs-lookup"><span data-stu-id="40751-146">Add a controller and view</span></span>

<span data-ttu-id="40751-147">Nesta seção, você adicionará um controlador de mínimo e o modo de exibição para servir como espaços reservados para o controlador do ASP.NET MVC e exibições que na próxima seção, você migrará.</span><span class="sxs-lookup"><span data-stu-id="40751-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="40751-148">Adicionar um *controladores* pasta.</span><span class="sxs-lookup"><span data-stu-id="40751-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="40751-149">Adicionar um **classe Controller** denominado *HomeController.cs* para o *controladores* pasta.</span><span class="sxs-lookup"><span data-stu-id="40751-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Caixa de diálogo Adicionar Novo Item](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="40751-151">Adicionar um *modos de exibição* pasta.</span><span class="sxs-lookup"><span data-stu-id="40751-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="40751-152">Adicionar um *exibições/inicial* pasta.</span><span class="sxs-lookup"><span data-stu-id="40751-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="40751-153">Adicionar um **modo de exibição do Razor** denominado *index. cshtml* para o *exibições/inicial* pasta.</span><span class="sxs-lookup"><span data-stu-id="40751-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Caixa de diálogo Adicionar Novo Item](mvc/_static/view.png)

<span data-ttu-id="40751-155">A estrutura do projeto é mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="40751-155">The project structure is shown below:</span></span>

![Gerenciador de soluções mostrando arquivos e pastas do WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="40751-157">Substitua o conteúdo do *Views/Home/Index.cshtml* arquivo com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="40751-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="40751-158">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40751-158">Run the app.</span></span>

![Aplicativo Web aberto no Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="40751-160">Ver [controladores](xref:mvc/controllers/actions) e [modos de exibição](xref:mvc/views/overview) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="40751-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="40751-161">Agora que temos um projeto de núcleo do ASP.NET mínimo do trabalho, podemos começar a migrar a funcionalidade do projeto ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="40751-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="40751-162">É preciso mover o seguinte:</span><span class="sxs-lookup"><span data-stu-id="40751-162">We need to move the following:</span></span>

* <span data-ttu-id="40751-163">conteúdo do lado do cliente (CSS, fontes e scripts)</span><span class="sxs-lookup"><span data-stu-id="40751-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="40751-164">controladores</span><span class="sxs-lookup"><span data-stu-id="40751-164">controllers</span></span>

* <span data-ttu-id="40751-165">modos de exibição</span><span class="sxs-lookup"><span data-stu-id="40751-165">views</span></span>

* <span data-ttu-id="40751-166">modelos</span><span class="sxs-lookup"><span data-stu-id="40751-166">models</span></span>

* <span data-ttu-id="40751-167">Agrupamento</span><span class="sxs-lookup"><span data-stu-id="40751-167">bundling</span></span>

* <span data-ttu-id="40751-168">filtros</span><span class="sxs-lookup"><span data-stu-id="40751-168">filters</span></span>

* <span data-ttu-id="40751-169">Log de entrada/saída, a identidade (Isso é feito no próximo tutorial.)</span><span class="sxs-lookup"><span data-stu-id="40751-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="40751-170">Controladores e exibições</span><span class="sxs-lookup"><span data-stu-id="40751-170">Controllers and views</span></span>

* <span data-ttu-id="40751-171">Copie cada um dos métodos do ASP.NET MVC `HomeController` para o novo `HomeController`.</span><span class="sxs-lookup"><span data-stu-id="40751-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="40751-172">Observe que, no ASP.NET MVC, tipo de método de ação retorno controlador interno do modelo é [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); no ASP.NET Core MVC, os retorno de métodos de ação `IActionResult` em vez disso.</span><span class="sxs-lookup"><span data-stu-id="40751-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="40751-173">`ActionResult` implementa `IActionResult`, portanto, não é necessário alterar o tipo de retorno de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="40751-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="40751-174">Cópia de *About. cshtml*, *Contact. cshtml*, e *index. cshtml* arquivos de exibição do Razor do projeto ASP.NET MVC para o projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40751-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="40751-175">Execute o aplicativo ASP.NET Core e cada método de teste.</span><span class="sxs-lookup"><span data-stu-id="40751-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="40751-176">Ainda não migramos o arquivo de layout ou estilos ainda, portanto, os modos de exibição renderizados contêm apenas o conteúdo nos arquivos de exibição.</span><span class="sxs-lookup"><span data-stu-id="40751-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="40751-177">Você não terá os links de arquivo gerado de layout para o `About` e `Contact` modos de exibição, portanto, você precisará invocá-los a partir do navegador (substitua **4492** com o número da porta usado em seu projeto).</span><span class="sxs-lookup"><span data-stu-id="40751-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Página de contato](mvc/_static/contact-page.png)

<span data-ttu-id="40751-179">Observe a falta de estilo e itens de menu.</span><span class="sxs-lookup"><span data-stu-id="40751-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="40751-180">Corrigiremos isso na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="40751-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="40751-181">Conteúdo estático</span><span class="sxs-lookup"><span data-stu-id="40751-181">Static content</span></span>

<span data-ttu-id="40751-182">Nas versões anteriores do ASP.NET MVC, o conteúdo estático foi hospedado da raiz do projeto da web e foi Intercalado com arquivos do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="40751-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="40751-183">No ASP.NET Core, conteúdo estático é hospedado na *wwwroot* pasta.</span><span class="sxs-lookup"><span data-stu-id="40751-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="40751-184">Você desejará copiar o conteúdo estático de seu aplicativo ASP.NET MVC antigo para o *wwwroot* pasta em seu projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40751-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="40751-185">Nessa conversão de exemplo:</span><span class="sxs-lookup"><span data-stu-id="40751-185">In this sample conversion:</span></span>

* <span data-ttu-id="40751-186">Cópia de */favicon.ico* arquivo de projeto MVC antigo para o *wwwroot* pasta no projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40751-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="40751-187">O ASP.NET MVC antigo projeto usa [Bootstrap](https://getbootstrap.com/) para seu estilo e armazena a inicialização de arquivos no *conteúdo* e *Scripts* pastas.</span><span class="sxs-lookup"><span data-stu-id="40751-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="40751-188">O modelo, que gerou o antigo projeto do ASP.NET MVC, faz referência a inicialização no arquivo de layout (*Views/Shared/_Layout.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="40751-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="40751-189">Você pode copiar o *bootstrap. js* e *Bootstrap* arquivos do ASP.NET MVC de projeto para o *wwwroot* pasta no novo projeto.</span><span class="sxs-lookup"><span data-stu-id="40751-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="40751-190">Em vez disso, vamos adicionar suporte para o Bootstrap (e outras bibliotecas do lado do cliente) usando as CDNs na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="40751-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="40751-191">Migrar o arquivo de layout</span><span class="sxs-lookup"><span data-stu-id="40751-191">Migrate the layout file</span></span>

* <span data-ttu-id="40751-192">Cópia de *viewstart* arquivo do antigo do projeto ASP.NET MVC *exibições* pasta para do projeto ASP.NET Core *modos de exibição* pasta.</span><span class="sxs-lookup"><span data-stu-id="40751-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="40751-193">O *viewstart* arquivo não foi alterado no ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="40751-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="40751-194">Criar uma *Views/Shared* pasta.</span><span class="sxs-lookup"><span data-stu-id="40751-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="40751-195">*Opcional:* Cópia *viewimports. cshtml* da *FullAspNetCore* do projeto MVC *modos de exibição* pasta para do projeto ASP.NET Core *exibições* pasta.</span><span class="sxs-lookup"><span data-stu-id="40751-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="40751-196">Remover qualquer declaração de namespace na *viewimports. cshtml* arquivo.</span><span class="sxs-lookup"><span data-stu-id="40751-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="40751-197">O *viewimports. cshtml* fornece os namespaces para todos os arquivos de exibição de arquivo e traz [auxiliares de marca](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="40751-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="40751-198">Os auxiliares de marca são usados no novo arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="40751-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="40751-199">O *viewimports. cshtml* arquivo é novo para o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40751-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="40751-200">Cópia de *layout. cshtml* arquivo do antigo do projeto ASP.NET MVC *Views/Shared* pasta para do projeto ASP.NET Core *Views/Shared* pasta.</span><span class="sxs-lookup"><span data-stu-id="40751-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="40751-201">Abra *layout. cshtml* de arquivo e faça as seguintes alterações (o código completo é mostrado abaixo):</span><span class="sxs-lookup"><span data-stu-id="40751-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="40751-202">Substitua `@Styles.Render("~/Content/css")` com um `<link>` elemento do qual carregar *Bootstrap* (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="40751-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="40751-203">Remova `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="40751-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="40751-204">Comente a `@Html.Partial("_LoginPartial")` linha (envolvem a linha com `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="40751-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="40751-205">Para obter mais informações, consulte [migrar autenticação e identidade para o ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="40751-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="40751-206">Substitua `@Scripts.Render("~/bundles/jquery")` com um `<script>` elemento (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="40751-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="40751-207">Substitua `@Scripts.Render("~/bundles/bootstrap")` com um `<script>` elemento (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="40751-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="40751-208">A marcação de substituição para inclusão de CSS do Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="40751-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="40751-209">A marcação de substituição para o jQuery e Bootstrap JavaScript inclusão:</span><span class="sxs-lookup"><span data-stu-id="40751-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="40751-210">Atualizada *layout. cshtml* arquivo é mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="40751-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="40751-211">Exiba o site no navegador.</span><span class="sxs-lookup"><span data-stu-id="40751-211">View the site in the browser.</span></span> <span data-ttu-id="40751-212">Ele agora deve carregar corretamente, com os estilos esperados em vigor.</span><span class="sxs-lookup"><span data-stu-id="40751-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="40751-213">*Opcional:* Você talvez queira tentar usar o novo arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="40751-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="40751-214">Para este projeto, você pode copiar o arquivo de layout do *FullAspNetCore* projeto.</span><span class="sxs-lookup"><span data-stu-id="40751-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="40751-215">O novo arquivo de layout utiliza [auxiliares de marca](xref:mvc/views/tag-helpers/intro) e tem outras melhorias.</span><span class="sxs-lookup"><span data-stu-id="40751-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="40751-216">Configurar o agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="40751-216">Configure bundling and minification</span></span>

<span data-ttu-id="40751-217">Para obter informações sobre como configurar o agrupamento e minificação, consulte [agrupamento e Minificação](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="40751-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="40751-218">Resolver erros HTTP 500</span><span class="sxs-lookup"><span data-stu-id="40751-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="40751-219">Há muitos problemas que podem causar uma mensagem de erro HTTP 500 que não contêm informações sobre a origem do problema.</span><span class="sxs-lookup"><span data-stu-id="40751-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="40751-220">Por exemplo, se o *viewimports* arquivo contém um namespace que não existe em seu projeto, você obterá um erro HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="40751-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="40751-221">Por padrão em aplicativos ASP.NET Core, o `UseDeveloperExceptionPage` extensão é adicionada para o `IApplicationBuilder` e executados quando a configuração é *desenvolvimento*.</span><span class="sxs-lookup"><span data-stu-id="40751-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="40751-222">Isso é detalhado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="40751-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="40751-223">ASP.NET Core converte as exceções sem tratamento em um aplicativo web em respostas de erro HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="40751-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="40751-224">Normalmente, os detalhes do erro não estão incluídos nessas respostas para evitar a divulgação de informações possivelmente confidenciais sobre o servidor.</span><span class="sxs-lookup"><span data-stu-id="40751-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="40751-225">Ver **usando a página de exceção do desenvolvedor** na [tratar erros](../fundamentals/error-handling.md) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="40751-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40751-226">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="40751-226">Additional resources</span></span>

* <xref:razor-components/index>
* <xref:mvc/views/tag-helpers/intro>
