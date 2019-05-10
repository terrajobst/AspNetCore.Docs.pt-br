---
title: Gerenciar pacotes do lado do cliente com Bower no ASP.NET Core
author: rick-anderson
description: Gerenciar pacotes do lado do cliente com o Bower.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 08/09/2018
uid: client-side/bower
ms.openlocfilehash: 08e6daa537c6c6f92a1cf80d70745e8ef606f580
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892993"
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a><span data-ttu-id="e18f9-103">Gerenciar pacotes do lado do cliente com Bower no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e18f9-103">Manage client-side packages with Bower in ASP.NET Core</span></span>

<span data-ttu-id="e18f9-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel arroz](https://twitter.com/noelrice1), e [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="e18f9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel Rice](https://twitter.com/noelrice1), and [Scott Addie](https://scottaddie.com)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e18f9-105">Enquanto o Bower é mantido, seus mantenedores recomendam usando uma solução diferente.</span><span class="sxs-lookup"><span data-stu-id="e18f9-105">While Bower is maintained, its maintainers recommend using a different solution.</span></span> <span data-ttu-id="e18f9-106">[Gerenciador de biblioteca](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (LibMan de forma abreviada) é a ferramenta de aquisição de biblioteca do lado do cliente novo do Visual Studio (Visual Studio 15,8 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="e18f9-106">[Library Manager](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (LibMan for short) is Visual Studio's new client-side library acquisition tool (Visual Studio 15.8 or later).</span></span> <span data-ttu-id="e18f9-107">Para obter mais informações, consulte <xref:client-side/libman/index>.</span><span class="sxs-lookup"><span data-stu-id="e18f9-107">For more information, see <xref:client-side/libman/index>.</span></span> <span data-ttu-id="e18f9-108">Bower tem suporte no Visual Studio versão 15.5.</span><span class="sxs-lookup"><span data-stu-id="e18f9-108">Bower is supported in Visual Studio through version 15.5.</span></span>
>
> <span data-ttu-id="e18f9-109">Yarn com Webpack é uma alternativa popular para a qual [instruções de migração](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="e18f9-109">Yarn with Webpack is one popular alternative for which [migration instructions](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) are available.</span></span>

<span data-ttu-id="e18f9-110">[Bower](https://bower.io/) chama a próprio "Um Gerenciador de pacotes para a web".</span><span class="sxs-lookup"><span data-stu-id="e18f9-110">[Bower](https://bower.io/) calls itself "A package manager for the web".</span></span> <span data-ttu-id="e18f9-111">Dentro do ecossistema do .NET, ele preenche essa lacuna deixado pela incapacidade do NuGet para entregar os arquivos de conteúdo estático.</span><span class="sxs-lookup"><span data-stu-id="e18f9-111">Within the .NET ecosystem, it fills the void left by NuGet's inability to deliver static content files.</span></span> <span data-ttu-id="e18f9-112">Para projetos do ASP.NET Core, esses arquivos estáticos são inerentes às bibliotecas do lado do cliente, como [jQuery](http://jquery.com/) e [Bootstrap](http://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="e18f9-112">For ASP.NET Core projects, these static files are inherent to client-side libraries like [jQuery](http://jquery.com/) and [Bootstrap](http://getbootstrap.com/).</span></span> <span data-ttu-id="e18f9-113">Para bibliotecas do .NET, você usar [NuGet](https://www.nuget.org/) Gerenciador de pacotes.</span><span class="sxs-lookup"><span data-stu-id="e18f9-113">For .NET libraries, you still use [NuGet](https://www.nuget.org/) package manager.</span></span>

<span data-ttu-id="e18f9-114">Processo de compilação de novos projetos criados com os modelos de projeto do ASP.NET Core configurar lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="e18f9-114">New projects created with the ASP.NET Core project templates set up the client-side build process.</span></span> <span data-ttu-id="e18f9-115">[jQuery](http://jquery.com/) e [Bootstrap](http://getbootstrap.com/) estiverem instalados, e Bower é suportado.</span><span class="sxs-lookup"><span data-stu-id="e18f9-115">[jQuery](http://jquery.com/) and [Bootstrap](http://getbootstrap.com/) are installed, and Bower is supported.</span></span>

<span data-ttu-id="e18f9-116">Pacotes do lado do cliente são listados na *bower. JSON* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e18f9-116">Client-side packages are listed in the *bower.json* file.</span></span> <span data-ttu-id="e18f9-117">Os modelos de projeto do ASP.NET Core configura *bower. JSON* com o jQuery, validação do jQuery e Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="e18f9-117">The ASP.NET Core project templates configures *bower.json* with jQuery, jQuery validation, and Bootstrap.</span></span>

<span data-ttu-id="e18f9-118">Neste tutorial, vamos adicionar suporte para [Font Awesome](http://fontawesome.io).</span><span class="sxs-lookup"><span data-stu-id="e18f9-118">In this tutorial, we'll add support for [Font Awesome](http://fontawesome.io).</span></span> <span data-ttu-id="e18f9-119">Pacotes do bower podem ser instalados com o **gerenciar pacotes do Bower** interface do usuário ou manualmente na *bower. JSON* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e18f9-119">Bower packages can be installed with the **Manage Bower Packages** UI or manually in the *bower.json* file.</span></span>

### <a name="installation-via-manage-bower-packages-ui"></a><span data-ttu-id="e18f9-120">Instalação por meio de pacotes do Bower gerenciar da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="e18f9-120">Installation via Manage Bower Packages UI</span></span>

* <span data-ttu-id="e18f9-121">Criar um novo aplicativo Web do ASP.NET Core com o **aplicativo Web do ASP.NET Core (.NET Core)** modelo.</span><span class="sxs-lookup"><span data-stu-id="e18f9-121">Create a new ASP.NET Core Web app with the **ASP.NET Core Web Application (.NET Core)** template.</span></span> <span data-ttu-id="e18f9-122">Selecione **aplicativo Web** e **nenhuma autenticação**.</span><span class="sxs-lookup"><span data-stu-id="e18f9-122">Select **Web Application** and **No Authentication**.</span></span>

* <span data-ttu-id="e18f9-123">Clique com botão direito no projeto no Gerenciador de soluções e selecione **gerenciar pacotes do Bower** (como alternativa, no menu principal, **Project** > **gerenciar pacotes Bower**).</span><span class="sxs-lookup"><span data-stu-id="e18f9-123">Right-click the project in Solution Explorer and select **Manage Bower Packages** (alternatively from the main menu, **Project** > **Manage Bower Packages**).</span></span>

* <span data-ttu-id="e18f9-124">No **Bower: \<nome do projeto\>**  janela, clique na guia "Procurar" e, em seguida, filtre a lista de pacotes inserindo `font-awesome` na caixa de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="e18f9-124">In the **Bower: \<project name\>** window, click the "Browse" tab, and then filter the packages list by entering `font-awesome` in the search box:</span></span>

  ![Gerenciar pacotes do bower](bower/_static/manage-bower-packages.png)

* <span data-ttu-id="e18f9-126">Confirme se o "Salvar alterações *bower. JSON*" caixa de seleção está marcada.</span><span class="sxs-lookup"><span data-stu-id="e18f9-126">Confirm that the "Save changes to *bower.json*" check box is checked.</span></span> <span data-ttu-id="e18f9-127">Selecione uma versão na lista suspensa e clique no **instalar** botão.</span><span class="sxs-lookup"><span data-stu-id="e18f9-127">Select a version from the drop-down list and click the **Install** button.</span></span> <span data-ttu-id="e18f9-128">O **saída** janela mostra os detalhes da instalação.</span><span class="sxs-lookup"><span data-stu-id="e18f9-128">The **Output** window shows the installation details.</span></span>

### <a name="manual-installation-in-bowerjson"></a><span data-ttu-id="e18f9-129">Instalação manual no bower. JSON</span><span class="sxs-lookup"><span data-stu-id="e18f9-129">Manual installation in bower.json</span></span>

<span data-ttu-id="e18f9-130">Abra o *bower. JSON* arquivo e adicione "font-incrível" para as dependências.</span><span class="sxs-lookup"><span data-stu-id="e18f9-130">Open the *bower.json* file and add "font-awesome" to the dependencies.</span></span> <span data-ttu-id="e18f9-131">O IntelliSense mostra os pacotes disponíveis.</span><span class="sxs-lookup"><span data-stu-id="e18f9-131">IntelliSense shows the available packages.</span></span> <span data-ttu-id="e18f9-132">Quando um pacote é selecionado, as versões disponíveis são exibidas.</span><span class="sxs-lookup"><span data-stu-id="e18f9-132">When a package is selected, the available versions are displayed.</span></span> <span data-ttu-id="e18f9-133">As imagens abaixo são mais antigas e não corresponder ao que você vê.</span><span class="sxs-lookup"><span data-stu-id="e18f9-133">The images below are older and won't match what you see.</span></span>

![IntelliSense do Explorador de pacotes de bower](bower/_static/add-package.png)

![versão bower IntelliSense](bower/_static/version-intelliSense.png)

<span data-ttu-id="e18f9-136">Usos para bower [controle de versão semântico](http://semver.org/) para organizar as dependências.</span><span class="sxs-lookup"><span data-stu-id="e18f9-136">Bower uses [semantic versioning](http://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="e18f9-137">Controle de versão semântico, também conhecido como SemVer, identifica os pacotes com o esquema de numeração \<principal >.\< secundária >. \<patch >.</span><span class="sxs-lookup"><span data-stu-id="e18f9-137">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="e18f9-138">IntelliSense simplifica o controle de versão semântico, mostrando apenas algumas opções comuns.</span><span class="sxs-lookup"><span data-stu-id="e18f9-138">IntelliSense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="e18f9-139">O item superior na lista do IntelliSense (4.6.3 no exemplo acima) é considerado a versão estável mais recente do pacote.</span><span class="sxs-lookup"><span data-stu-id="e18f9-139">The top item in the IntelliSense list (4.6.3 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="e18f9-140">O símbolo de acento circunflexo (^) corresponde à versão principal mais recente e o til (~) corresponde a versão secundária mais recente.</span><span class="sxs-lookup"><span data-stu-id="e18f9-140">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span>

<span data-ttu-id="e18f9-141">Salvar a *bower. JSON* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e18f9-141">Save the *bower.json* file.</span></span> <span data-ttu-id="e18f9-142">Visual Studio inspeciona as *bower. JSON* arquivo para que as alterações.</span><span class="sxs-lookup"><span data-stu-id="e18f9-142">Visual Studio watches the *bower.json* file for changes.</span></span> <span data-ttu-id="e18f9-143">Ao salvar, o *bower install* comando é executado.</span><span class="sxs-lookup"><span data-stu-id="e18f9-143">Upon saving, the *bower install* command is executed.</span></span> <span data-ttu-id="e18f9-144">Consulte a janela de saída **npm/Bower** modo de exibição para o comando executado.</span><span class="sxs-lookup"><span data-stu-id="e18f9-144">See the Output window's **Bower/npm** view for the exact command executed.</span></span>

<span data-ttu-id="e18f9-145">Abra o *. bowerrc* do arquivo sob *bower. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e18f9-145">Open the *.bowerrc* file under *bower.json*.</span></span> <span data-ttu-id="e18f9-146">O `directory` estiver definida como *wwwroot/lib* que indica o local do Bower instalará os ativos do pacote.</span><span class="sxs-lookup"><span data-stu-id="e18f9-146">The `directory` property is set to *wwwroot/lib* which indicates the location Bower will install the package assets.</span></span>

```json
{
 "directory": "wwwroot/lib"
}
```

<span data-ttu-id="e18f9-147">Você pode usar a caixa de pesquisa no Gerenciador de soluções para localizar e exibir o pacote font awesome.</span><span class="sxs-lookup"><span data-stu-id="e18f9-147">You can use the search box in Solution Explorer to find and display the font-awesome package.</span></span>

<span data-ttu-id="e18f9-148">Abra o *Views\Shared\_layout. cshtml* arquivo e adicione o arquivo CSS font awesome no ambiente [auxiliar de marca](xref:mvc/views/tag-helpers/intro) para `Development`.</span><span class="sxs-lookup"><span data-stu-id="e18f9-148">Open the *Views\Shared\_Layout.cshtml* file and add the font-awesome CSS file to the environment [Tag Helper](xref:mvc/views/tag-helpers/intro) for `Development`.</span></span> <span data-ttu-id="e18f9-149">No Gerenciador de soluções, arraste e solte *fonte awesome.css* dentro de `<environment names="Development">` elemento.</span><span class="sxs-lookup"><span data-stu-id="e18f9-149">From Solution Explorer, drag and drop *font-awesome.css* inside the `<environment names="Development">` element.</span></span>

[!code-html[](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

<span data-ttu-id="e18f9-150">Um aplicativo de produção, você adicionaria *fonte awesome.min.css* para o auxiliar de marca de ambiente para `Staging,Production`.</span><span class="sxs-lookup"><span data-stu-id="e18f9-150">In a production app you would add *font-awesome.min.css* to the environment tag helper for `Staging,Production`.</span></span>

<span data-ttu-id="e18f9-151">Substitua o conteúdo do *Views\Home\About.cshtml* arquivo Razor com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="e18f9-151">Replace the contents of the *Views\Home\About.cshtml* Razor file with the following markup:</span></span>

[!code-html[](bower/sample/About.cshtml)]

<span data-ttu-id="e18f9-152">Execute o aplicativo e navegue até a exibição About sobre para verificar se o pacote font awesome funciona.</span><span class="sxs-lookup"><span data-stu-id="e18f9-152">Run the app and navigate to the About view to verify the font-awesome package works.</span></span>

## <a name="exploring-the-client-side-build-process"></a><span data-ttu-id="e18f9-153">Explorando o processo de compilação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="e18f9-153">Exploring the client-side build process</span></span>

<span data-ttu-id="e18f9-154">A maioria dos modelos de projeto do ASP.NET Core já estão configurados para usar o Bower.</span><span class="sxs-lookup"><span data-stu-id="e18f9-154">Most ASP.NET Core project templates are already configured to use Bower.</span></span> <span data-ttu-id="e18f9-155">Este passo a passo de próximo começa com um projeto vazio do ASP.NET Core e adiciona cada parte manualmente, portanto, você pode ter uma ideia de como o Bower é usado em um projeto.</span><span class="sxs-lookup"><span data-stu-id="e18f9-155">This next walkthrough starts with an empty ASP.NET Core project and adds each piece manually, so you can get a feel for how Bower is used in a project.</span></span> <span data-ttu-id="e18f9-156">Você pode ver o que acontece com a estrutura do projeto e o tempo de execução de saída que cada alteração de configuração é feita.</span><span class="sxs-lookup"><span data-stu-id="e18f9-156">You can see what happens to the project structure and the runtime output as each configuration change is made.</span></span>

<span data-ttu-id="e18f9-157">As etapas gerais para usar o processo de compilação do lado do cliente com o Bower são:</span><span class="sxs-lookup"><span data-stu-id="e18f9-157">The general steps to use the client-side build process with Bower are:</span></span>

* <span data-ttu-id="e18f9-158">Defina pacotes usados em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="e18f9-158">Define packages used in your project.</span></span> <!-- once defined, you don't need to download them, VS does -->
* <span data-ttu-id="e18f9-159">Pacotes de referência de suas páginas da web.</span><span class="sxs-lookup"><span data-stu-id="e18f9-159">Reference packages from your web pages.</span></span>

### <a name="define-packages"></a><span data-ttu-id="e18f9-160">Definir pacotes</span><span class="sxs-lookup"><span data-stu-id="e18f9-160">Define packages</span></span>

<span data-ttu-id="e18f9-161">Depois que você listar pacotes na *bower. JSON* arquivo, o Visual Studio irá baixá-los.</span><span class="sxs-lookup"><span data-stu-id="e18f9-161">Once you list packages in the *bower.json* file, Visual Studio will download them.</span></span> <span data-ttu-id="e18f9-162">O exemplo a seguir usa o Bower para carregar o jQuery e Bootstrap para o *wwwroot* pasta.</span><span class="sxs-lookup"><span data-stu-id="e18f9-162">The following example uses Bower to load jQuery and Bootstrap to the *wwwroot* folder.</span></span>

* <span data-ttu-id="e18f9-163">Criar um novo aplicativo Web do ASP.NET Core com o **aplicativo Web do ASP.NET Core (.NET Core)** modelo.</span><span class="sxs-lookup"><span data-stu-id="e18f9-163">Create a new ASP.NET Core Web app with the **ASP.NET Core Web Application (.NET Core)** template.</span></span> <span data-ttu-id="e18f9-164">Selecione o **vazio** modelo de projeto e clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="e18f9-164">Select the **Empty** project template and click **OK**.</span></span>

* <span data-ttu-id="e18f9-165">No Gerenciador de soluções, clique com botão direito no projeto > **Adicionar Novo Item** e selecione **arquivo de configuração Bower**.</span><span class="sxs-lookup"><span data-stu-id="e18f9-165">In Solution Explorer, right-click the project > **Add New Item** and select **Bower Configuration File**.</span></span> <span data-ttu-id="e18f9-166">Observação: Um *. bowerrc* arquivo também é adicionado.</span><span class="sxs-lookup"><span data-stu-id="e18f9-166">Note: A *.bowerrc* file is also added.</span></span>

* <span data-ttu-id="e18f9-167">Abra *bower. JSON*e adicionar o jquery e bootstrap para o `dependencies` seção.</span><span class="sxs-lookup"><span data-stu-id="e18f9-167">Open *bower.json*, and add jquery and bootstrap to the `dependencies` section.</span></span> <span data-ttu-id="e18f9-168">Resultante *bower. JSON* arquivo se parecerá com o exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="e18f9-168">The resulting *bower.json* file will look like the following example.</span></span> <span data-ttu-id="e18f9-169">As versões serão alterado ao longo do tempo e podem não corresponder a imagem a seguir.</span><span class="sxs-lookup"><span data-stu-id="e18f9-169">The versions will change over time and may not match the image below.</span></span>

[!code-json[](bower/sample/bower.json?highlight=5,6)]

* <span data-ttu-id="e18f9-170">Salvar a *bower. JSON* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e18f9-170">Save the *bower.json* file.</span></span>

  <span data-ttu-id="e18f9-171">Verifique se o projeto inclui o *bootstrap* e *jQuery* diretórios *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="e18f9-171">Verify the project includes the *bootstrap* and *jQuery* directories in *wwwroot/lib*.</span></span> <span data-ttu-id="e18f9-172">Bower usa o *. bowerrc* arquivo para instalar os ativos na *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="e18f9-172">Bower uses the *.bowerrc* file to install the assets in *wwwroot/lib*.</span></span>

  <span data-ttu-id="e18f9-173">Observação: A interface do usuário "Gerenciar pacotes do Bower" fornece uma alternativa à edição do arquivo manual.</span><span class="sxs-lookup"><span data-stu-id="e18f9-173">Note: The "Manage Bower Packages" UI provides an alternative to manual file editing.</span></span>

### <a name="enable-static-files"></a><span data-ttu-id="e18f9-174">Habilitar arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="e18f9-174">Enable static files</span></span>

* <span data-ttu-id="e18f9-175">Adicionar o `Microsoft.AspNetCore.StaticFiles` pacote NuGet ao projeto.</span><span class="sxs-lookup"><span data-stu-id="e18f9-175">Add the `Microsoft.AspNetCore.StaticFiles` NuGet package to the project.</span></span>
* <span data-ttu-id="e18f9-176">Habilitar arquivos estáticos a serem atendidos com o [middleware de arquivo estático](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions).</span><span class="sxs-lookup"><span data-stu-id="e18f9-176">Enable static files to be served with the [Static file middleware](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions).</span></span> <span data-ttu-id="e18f9-177">Adicione uma chamada para [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions) para o `Configure` método `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e18f9-177">Add a call to [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions) to the `Configure` method of `Startup`.</span></span>

[!code-csharp[](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a><span data-ttu-id="e18f9-178">Pacotes de referência</span><span class="sxs-lookup"><span data-stu-id="e18f9-178">Reference packages</span></span>

<span data-ttu-id="e18f9-179">Nesta seção, você criará uma página HTML para verificar se que ele pode acessar os pacotes implantados.</span><span class="sxs-lookup"><span data-stu-id="e18f9-179">In this section, you will create an HTML page to verify it can access the deployed packages.</span></span>

* <span data-ttu-id="e18f9-180">Adicionar uma nova página HTML chamada *index. HTML* para o *wwwroot* pasta.</span><span class="sxs-lookup"><span data-stu-id="e18f9-180">Add a new HTML page named *Index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="e18f9-181">Observação: Você deve adicionar o arquivo HTML para o *wwwroot* pasta.</span><span class="sxs-lookup"><span data-stu-id="e18f9-181">Note: You must add the HTML file to the *wwwroot* folder.</span></span> <span data-ttu-id="e18f9-182">Por padrão, o conteúdo estático não pode ser servido fora *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e18f9-182">By default, static content cannot be served outside *wwwroot*.</span></span> <span data-ttu-id="e18f9-183">Ver [arquivos estáticos](xref:fundamentals/static-files) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="e18f9-183">See [Static files](xref:fundamentals/static-files) for more information.</span></span>

  <span data-ttu-id="e18f9-184">Substitua o conteúdo do *index. HTML* com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="e18f9-184">Replace the contents of *Index.html* with the following markup:</span></span>

[!code-html[](bower/sample/Index.html)]

* <span data-ttu-id="e18f9-185">Execute o aplicativo e navegue até `http://localhost:<port>/Index.html`.</span><span class="sxs-lookup"><span data-stu-id="e18f9-185">Run the app and navigate to `http://localhost:<port>/Index.html`.</span></span> <span data-ttu-id="e18f9-186">Como alternativa, com *index. HTML* aberto, pressione `Ctrl+Shift+W`.</span><span class="sxs-lookup"><span data-stu-id="e18f9-186">Alternatively, with *Index.html* opened, press `Ctrl+Shift+W`.</span></span> <span data-ttu-id="e18f9-187">Verifique se que o estilo de jumbotron é aplicado, o código jQuery responde quando o botão é clicado e que o Bootstrap botão muda de estado.</span><span class="sxs-lookup"><span data-stu-id="e18f9-187">Verify that the jumbotron styling is applied, the jQuery code responds when the button is clicked, and that the Bootstrap button changes state.</span></span>

  ![estilo de jumbotron aplicado](bower/_static/jumbotron.png)
