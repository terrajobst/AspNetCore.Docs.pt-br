---
title: LibMan de uso com o ASP.NET Core no Visual Studio
author: scottaddie
description: Saiba como usar LibMan em um projeto do ASP.NET Core com o Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: ebfb405516d968bf5d5b8cff956a9892457027f2
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813466"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="d0c4c-103">LibMan de uso com o ASP.NET Core no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0c4c-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="d0c4c-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d0c4c-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d0c4c-105">O Visual Studio tem suporte interno para [LibMan](xref:client-side/libman/index) em projetos do ASP.NET Core, incluindo:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="d0c4c-106">Suporte para configurar e executar operações de restauração LibMan no build.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="d0c4c-107">Itens de menu para o disparo de operações de limpeza e LibMan restauração.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="d0c4c-108">Caixa de diálogo de pesquisa para localizar bibliotecas e adicionar os arquivos a um projeto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="d0c4c-109">Suporte de edição para *libman.json*&mdash;o arquivo de manifesto LibMan.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="d0c4c-110">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(como fazer o download)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d0c4c-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d0c4c-111">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="d0c4c-111">Prerequisites</span></span>

* <span data-ttu-id="d0c4c-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com carga de trabalho **ASP.NET e desenvolvimento Web**</span><span class="sxs-lookup"><span data-stu-id="d0c4c-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="d0c4c-113">Adicione arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="d0c4c-113">Add library files</span></span>

<span data-ttu-id="d0c4c-114">Arquivos de biblioteca podem ser adicionados a um projeto ASP.NET Core de duas maneiras diferentes:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="d0c4c-115">Use a caixa de diálogo Adicionar biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="d0c4c-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="d0c4c-116">Configurar manualmente as entradas do arquivo de manifesto LibMan</span><span class="sxs-lookup"><span data-stu-id="d0c4c-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="d0c4c-117">Use a caixa de diálogo Adicionar biblioteca de cliente</span><span class="sxs-lookup"><span data-stu-id="d0c4c-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="d0c4c-118">Siga estas etapas para instalar uma biblioteca de cliente:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="d0c4c-119">Na **Gerenciador de soluções**, clique na pasta de projeto no qual os arquivos devem ser adicionados.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="d0c4c-120">Escolher **adicione** > **biblioteca de cliente**.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="d0c4c-121">O **adicionar a biblioteca do lado do cliente** caixa de diálogo aparece:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Adicionar caixa de diálogo de biblioteca do lado do cliente](_static/add-library-dialog.png)

* <span data-ttu-id="d0c4c-123">Selecione o provedor de biblioteca do **provedor** lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="d0c4c-124">CDNJS é o provedor padrão.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="d0c4c-125">Digite o nome da biblioteca para buscar na **biblioteca** caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="d0c4c-126">IntelliSense fornece uma lista de bibliotecas, começando com o texto fornecido.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="d0c4c-127">Selecione a biblioteca da lista do IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="d0c4c-128">Observe que o nome da biblioteca é sufixado com o `@` símbolo e a versão estável mais recente conhecido para o provedor selecionado.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="d0c4c-129">Decida quais arquivos a serem incluídos:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-129">Decide which files to include:</span></span>
  * <span data-ttu-id="d0c4c-130">Selecione o **incluir todos os arquivos de biblioteca** botão de opção para incluir todos os arquivos da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="d0c4c-131">Selecione o **escolher arquivos específicos** botão de opção para incluir um subconjunto dos arquivos da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="d0c4c-132">Quando o botão de opção é selecionado, a árvore do seletor de arquivos está habilitada.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="d0c4c-133">Marque as caixas à esquerda dos nomes de arquivo para baixar.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="d0c4c-134">Especifique a pasta de projeto para armazenar os arquivos a **local de destino** caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="d0c4c-135">Como uma recomendação, armazene cada biblioteca em uma pasta separada.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="d0c4c-136">Sugeridas **local de destino** pasta baseia-se no local a partir do qual a caixa de diálogo iniciado:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="d0c4c-137">Se iniciado a partir da raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-137">If launched from the project root:</span></span>
    * <span data-ttu-id="d0c4c-138">*wwwroot/lib* será usado se *wwwroot* existe.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="d0c4c-139">*lib* será usado se *wwwroot* não existe.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="d0c4c-140">Se iniciado em uma pasta de projeto, o nome da pasta correspondente será usado.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="d0c4c-141">A sugestão de pasta é sufixada com o nome da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="d0c4c-142">A tabela a seguir ilustra as sugestões de pasta durante a instalação do jQuery em um projeto de páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="d0c4c-143">Inicie o local</span><span class="sxs-lookup"><span data-stu-id="d0c4c-143">Launch location</span></span>                           |<span data-ttu-id="d0c4c-144">Pasta sugerida</span><span class="sxs-lookup"><span data-stu-id="d0c4c-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="d0c4c-145">raiz do projeto (se *wwwroot* existe)</span><span class="sxs-lookup"><span data-stu-id="d0c4c-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="d0c4c-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="d0c4c-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="d0c4c-147">raiz do projeto (se *wwwroot* não existe)</span><span class="sxs-lookup"><span data-stu-id="d0c4c-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="d0c4c-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="d0c4c-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="d0c4c-149">*Páginas* pasta do projeto</span><span class="sxs-lookup"><span data-stu-id="d0c4c-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="d0c4c-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="d0c4c-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="d0c4c-151">Clique o **instale** botão para baixar os arquivos, de acordo com a configuração no *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="d0c4c-152">Examine a **Gerenciador de biblioteca** feed da **saída** janela para obter detalhes de instalação.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="d0c4c-153">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="d0c4c-154">Configurar manualmente as entradas do arquivo de manifesto LibMan</span><span class="sxs-lookup"><span data-stu-id="d0c4c-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="d0c4c-155">Todas as operações de LibMan no Visual Studio são baseadas no conteúdo do manifesto de LibMan da raiz do projeto (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="d0c4c-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="d0c4c-156">Você pode editar manualmente *libman.json* para configurar arquivos de biblioteca do projeto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="d0c4c-157">O Visual Studio restaura todos os arquivos de biblioteca uma vez *libman.json* é salvo.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="d0c4c-158">Para abrir *libman.json* para edição, existem as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="d0c4c-159">Clique duas vezes o *libman.json* arquivo no **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="d0c4c-160">Clique com botão direito no projeto no **Gerenciador de soluções** e selecione **gerenciar bibliotecas de cliente**.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="d0c4c-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="d0c4c-161">**&#8224;**</span></span>
* <span data-ttu-id="d0c4c-162">Selecione **gerenciar bibliotecas do lado do cliente** do Visual Studio **projeto** menu.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="d0c4c-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="d0c4c-163">**&#8224;**</span></span>

<span data-ttu-id="d0c4c-164">**&#8224;** Se o *libman.json* arquivo ainda não existir na raiz do projeto, ele será criado com o conteúdo do modelo de item padrão.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="d0c4c-165">Visual Studio oferece um rico JSON, suporte, como colorização de edição, formatação, IntelliSense e validação de esquema.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="d0c4c-166">Esquema JSON do manifesto LibMan é encontrada em [ https://json.schemastore.org/libman ](https://json.schemastore.org/libman).</span><span class="sxs-lookup"><span data-stu-id="d0c4c-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="d0c4c-167">Com o seguinte arquivo de manifesto, LibMan recupera arquivos de acordo com a configuração definida no `libraries` propriedade.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="d0c4c-168">Obter uma explicação dos literais de objeto definidos dentro `libraries` segue:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="d0c4c-169">Um subconjunto de [jQuery](https://jquery.com/) versão 3.3.1 é recuperado do provedor CDNJS.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="d0c4c-170">O subconjunto é definido na `files` propriedade&mdash;*jQuery*, *obtive*, e *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="d0c4c-171">Os arquivos são colocados no projeto do *wwwroot/lib/jquery* pasta.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="d0c4c-172">Na íntegra [Bootstrap](https://getbootstrap.com/) versão 4.1.3 é recuperado e colocado em um *wwwroot/lib/bootstrap* pasta.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="d0c4c-173">O literal de objeto `provider` substituições de propriedades de `defaultProvider` valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="d0c4c-174">LibMan recupera os arquivos de inicialização do provedor de unpkg.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="d0c4c-175">Um subconjunto de [Lodash](https://lodash.com/) foi aprovada por um corpo regulador dentro da organização.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="d0c4c-176">O *lodash.js* e *lodash.min.js* arquivos são recuperados do sistema de arquivos local no *c:\\temp\\lodash\\* .</span><span class="sxs-lookup"><span data-stu-id="d0c4c-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="d0c4c-177">Os arquivos são copiados para o projeto *wwwroot/lib/lodash* pasta.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="d0c4c-178">LibMan só dá suporte a uma versão de cada biblioteca de cada provedor.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="d0c4c-179">O *libman.json* arquivo Falha na validação de esquema, se ele contiver duas bibliotecas com o mesmo nome de biblioteca para determinado provedor.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="d0c4c-180">Restaurar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="d0c4c-180">Restore library files</span></span>

<span data-ttu-id="d0c4c-181">Para restaurar os arquivos de biblioteca de dentro do Visual Studio, deve haver um válido *libman.json* arquivo na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="d0c4c-182">Arquivos restaurados são colocados no projeto no local especificado para cada biblioteca.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="d0c4c-183">Arquivos de biblioteca podem ser restaurados em um projeto do ASP.NET Core de duas maneiras:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="d0c4c-184">Restaurar os arquivos durante a compilação</span><span class="sxs-lookup"><span data-stu-id="d0c4c-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="d0c4c-185">Restaurar os arquivos manualmente</span><span class="sxs-lookup"><span data-stu-id="d0c4c-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="d0c4c-186">Restaurar os arquivos durante a compilação</span><span class="sxs-lookup"><span data-stu-id="d0c4c-186">Restore files during build</span></span>

<span data-ttu-id="d0c4c-187">LibMan pode restaurar os arquivos de biblioteca definidas como parte do processo de compilação.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="d0c4c-188">Por padrão, o *restauração no build* comportamento está desabilitado.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="d0c4c-189">Para ativar e testar o comportamento de restauração no build:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="d0c4c-190">Clique com botão direito *libman.json* na **Gerenciador de soluções** e selecione **habilitar bibliotecas de cliente restaurar no Build** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="d0c4c-191">Clique o **Sim** botão quando for solicitado a instalar um pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="d0c4c-192">O [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) pacote do NuGet é adicionado ao projeto:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="d0c4c-193">Compile o projeto para confirmar a restauração de arquivo LibMan ocorre.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="d0c4c-194">O `Microsoft.Web.LibraryManager.Build` pacote injeta um destino do MSBuild que executa LibMan durante a operação de compilação do projeto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="d0c4c-195">Examine a **compilar** feed da **saída** janela para um log de atividades LibMan:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="d0c4c-196">Quando o comportamento de restauração no build está habilitado, o *libman.json* menu de contexto exibe um **desabilitar bibliotecas de cliente restaurar no Build** opção.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="d0c4c-197">Esta opção remove o `Microsoft.Web.LibraryManager.Build` referência do arquivo de projeto de pacote.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="d0c4c-198">Consequentemente, as bibliotecas do lado do cliente não são restauradas em cada compilação.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="d0c4c-199">Independentemente da configuração de restauração no build, você pode restaurar manualmente a qualquer momento do *libman.json* menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="d0c4c-200">Para obter mais informações, consulte [restaurar os arquivos manualmente](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="d0c4c-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="d0c4c-201">Restaurar os arquivos manualmente</span><span class="sxs-lookup"><span data-stu-id="d0c4c-201">Restore files manually</span></span>

<span data-ttu-id="d0c4c-202">Para restaurar manualmente os arquivos de biblioteca:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-202">To manually restore library files:</span></span>

* <span data-ttu-id="d0c4c-203">Para todos os projetos na solução:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="d0c4c-204">Clique com botão direito no nome da solução no **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="d0c4c-205">Selecione o **bibliotecas de cliente restaurar** opção.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="d0c4c-206">Para um projeto específico:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-206">For a specific project:</span></span>
  * <span data-ttu-id="d0c4c-207">Clique com botão direito do *libman.json* arquivo no **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="d0c4c-208">Selecione o **bibliotecas de cliente restaurar** opção.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="d0c4c-209">Enquanto a operação de restauração está em execução:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-209">While the restore operation is running:</span></span>

* <span data-ttu-id="d0c4c-210">O ícone do Centro de Status da tarefa (TSC) na barra de status do Visual Studio será animado e lerá *iniciada a operação de restauração*.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="d0c4c-211">Clicando no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidos.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="d0c4c-212">As mensagens serão enviadas à barra de status e o **Gerenciador de biblioteca** feed da **saída** janela.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="d0c4c-213">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="d0c4c-214">Excluir arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="d0c4c-214">Delete library files</span></span>

<span data-ttu-id="d0c4c-215">Para executar o *limpa* operação, que exclui os arquivos de biblioteca restaurados anteriormente no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="d0c4c-216">Clique com botão direito do *libman.json* arquivo no **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="d0c4c-217">Selecione o **bibliotecas de cliente limpa** opção.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="d0c4c-218">Para evitar a remoção não intencional de arquivos não seja de biblioteca, a operação de limpeza não exclui diretórios inteiros.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="d0c4c-219">Ela remove somente os arquivos que foram incluídos na restauração anterior.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="d0c4c-220">Enquanto a operação de limpeza é executado:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-220">While the clean operation is running:</span></span>

* <span data-ttu-id="d0c4c-221">O ícone TSC na barra de status do Visual Studio será animado e lerá *operação de bibliotecas de cliente iniciada*.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="d0c4c-222">Clicando no ícone abre uma dica de ferramenta listando as tarefas em segundo plano conhecidos.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="d0c4c-223">As mensagens são enviadas para a barra de status e o **Gerenciador de biblioteca** feed da **saída** janela.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="d0c4c-224">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="d0c4c-225">A operação de limpeza exclui somente os arquivos do projeto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="d0c4c-226">Arquivos de biblioteca permanecem no cache para uma recuperação mais rápida em operações de restauração futuras.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="d0c4c-227">Para gerenciar arquivos de biblioteca armazenados no cache do computador local, use o [LibMan CLI](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="d0c4c-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="d0c4c-228">Desinstalar os arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="d0c4c-228">Uninstall library files</span></span>

<span data-ttu-id="d0c4c-229">Para desinstalar os arquivos de biblioteca:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-229">To uninstall library files:</span></span>

* <span data-ttu-id="d0c4c-230">Abra *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-230">Open *libman.json*.</span></span>
* <span data-ttu-id="d0c4c-231">Posicione o cursor dentro correspondente `libraries` literal de objeto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="d0c4c-232">Clique no ícone de lâmpada que aparece na margem esquerda e selecione **Uninstall \<nome_da_biblioteca > @\<library_version >** :</span><span class="sxs-lookup"><span data-stu-id="d0c4c-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Desinstalar a opção de menu de contexto de biblioteca](_static/uninstall-menu-option.png)

<span data-ttu-id="d0c4c-234">Como alternativa, você pode editar e salvar o manifesto LibMan manualmente (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="d0c4c-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="d0c4c-235">O [operação de restauração](#restore-library-files) é executado quando o arquivo é salvo.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="d0c4c-236">Arquivos de biblioteca que não estão definidos na *libman.json* são removidas do projeto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="d0c4c-237">Atualizar a versão da biblioteca</span><span class="sxs-lookup"><span data-stu-id="d0c4c-237">Update library version</span></span>

<span data-ttu-id="d0c4c-238">Para verificar se há uma versão atualizada de biblioteca:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-238">To check for an updated library version:</span></span>

* <span data-ttu-id="d0c4c-239">Abra *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-239">Open *libman.json*.</span></span>
* <span data-ttu-id="d0c4c-240">Posicione o cursor dentro correspondente `libraries` literal de objeto.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="d0c4c-241">Clique no ícone de lâmpada que aparece na margem esquerda.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="d0c4c-242">Passe o mouse sobre **verificar se há atualizações**.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="d0c4c-243">LibMan verifica se há uma versão mais recente do que a versão instalada da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="d0c4c-244">Podem ocorrer os seguintes resultados:</span><span class="sxs-lookup"><span data-stu-id="d0c4c-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="d0c4c-245">Um **nenhuma atualização encontrada** mensagem será exibida se a versão mais recente já está instalada.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="d0c4c-246">A versão estável mais recente é exibida se não estiver instalada.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-246">The latest stable version is displayed if not already installed.</span></span>

  ![Procure a opção de menu de contexto de atualizações](_static/update-menu-option.png)

* <span data-ttu-id="d0c4c-248">Se houver uma versão de pré-lançamento mais recente do que a versão instalada, a versão de pré-lançamento é exibida.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="d0c4c-249">Para fazer o downgrade para uma versão mais antiga da biblioteca, edite manualmente o *libman.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="d0c4c-250">Quando o arquivo é salvo, o LibMan [operação de restauração](#restore-library-files):</span><span class="sxs-lookup"><span data-stu-id="d0c4c-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="d0c4c-251">Remove arquivos redundantes da versão anterior.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="d0c4c-252">Adiciona arquivos novos e atualizados da nova versão.</span><span class="sxs-lookup"><span data-stu-id="d0c4c-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0c4c-253">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d0c4c-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="d0c4c-254">Repositório do GitHub do LibMan</span><span class="sxs-lookup"><span data-stu-id="d0c4c-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
