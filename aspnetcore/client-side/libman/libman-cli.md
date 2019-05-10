---
title: Use a interface de linha de comando LibMan (CLI) com o ASP.NET Core
author: scottaddie
description: Saiba como usar a interface de linha de comando LibMan (CLI) em um projeto ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/30/2018
uid: client-side/libman/libman-cli
ms.openlocfilehash: 5667f79648a60b8fd9496f8041ef08891ab766af
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894753"
---
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a><span data-ttu-id="40d15-103">Use a interface de linha de comando LibMan (CLI) com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40d15-103">Use the LibMan command-line interface (CLI) with ASP.NET Core</span></span>

<span data-ttu-id="40d15-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="40d15-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="40d15-105">O [LibMan](xref:client-side/libman/index) CLI é uma ferramenta de plataforma cruzada que tem suporte em qualquer lugar, há suporte para .NET Core.</span><span class="sxs-lookup"><span data-stu-id="40d15-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="40d15-106">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="40d15-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="40d15-107">Instalação</span><span class="sxs-lookup"><span data-stu-id="40d15-107">Installation</span></span>

<span data-ttu-id="40d15-108">Para instalar a CLI LibMan:</span><span class="sxs-lookup"><span data-stu-id="40d15-108">To install the LibMan CLI:</span></span>

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="40d15-109">Um [ferramenta Global do .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) for instalado a partir de [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="40d15-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="40d15-110">Para instalar a CLI LibMan de uma fonte específica de pacote do NuGet:</span><span class="sxs-lookup"><span data-stu-id="40d15-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="40d15-111">No exemplo anterior, uma ferramenta Global do .NET Core é instalada do computador Windows local *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* arquivo.</span><span class="sxs-lookup"><span data-stu-id="40d15-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="40d15-112">Uso</span><span class="sxs-lookup"><span data-stu-id="40d15-112">Usage</span></span>

<span data-ttu-id="40d15-113">Após a instalação bem-sucedida da CLI, o comando a seguir pode ser usado:</span><span class="sxs-lookup"><span data-stu-id="40d15-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="40d15-114">Para exibir a versão da CLI instalada:</span><span class="sxs-lookup"><span data-stu-id="40d15-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="40d15-115">Para exibir os comandos da CLI disponíveis:</span><span class="sxs-lookup"><span data-stu-id="40d15-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="40d15-116">O comando anterior exibe uma saída semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="40d15-116">The preceding command displays output similar to the following:</span></span>

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

<span data-ttu-id="40d15-117">As seções a seguir descrevem os comandos da CLI disponíveis.</span><span class="sxs-lookup"><span data-stu-id="40d15-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="40d15-118">Inicializar LibMan no projeto</span><span class="sxs-lookup"><span data-stu-id="40d15-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="40d15-119">O `libman init` comando cria um *libman.json* arquivo se ele não existir.</span><span class="sxs-lookup"><span data-stu-id="40d15-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="40d15-120">O arquivo é criado com o conteúdo do modelo de item padrão.</span><span class="sxs-lookup"><span data-stu-id="40d15-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="40d15-121">Sinopse</span><span class="sxs-lookup"><span data-stu-id="40d15-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="40d15-122">Opções</span><span class="sxs-lookup"><span data-stu-id="40d15-122">Options</span></span>

<span data-ttu-id="40d15-123">As seguintes opções estão disponíveis para o `libman init` comando:</span><span class="sxs-lookup"><span data-stu-id="40d15-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="40d15-124">Um caminho relativo à pasta atual.</span><span class="sxs-lookup"><span data-stu-id="40d15-124">A path relative to the current folder.</span></span> <span data-ttu-id="40d15-125">Arquivos de biblioteca são instalados nesse local, se nenhum `destination` propriedade está definida para uma biblioteca no *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="40d15-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="40d15-126">O `<PATH>` valor é gravado para o `defaultDestination` propriedade de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="40d15-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="40d15-127">O provedor a ser usado se nenhum provedor é definido para uma determinada biblioteca.</span><span class="sxs-lookup"><span data-stu-id="40d15-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="40d15-128">O `<PROVIDER>` valor é gravado para o `defaultProvider` propriedade de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="40d15-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="40d15-129">Substitua `<PROVIDER>` com um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="40d15-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="40d15-130">Exemplos</span><span class="sxs-lookup"><span data-stu-id="40d15-130">Examples</span></span>

<span data-ttu-id="40d15-131">Para criar uma *libman.json* arquivo em um projeto ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="40d15-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="40d15-132">Navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="40d15-132">Navigate to the project root.</span></span>
* <span data-ttu-id="40d15-133">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="40d15-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="40d15-134">Digite o nome do provedor padrão, ou pressione `Enter` para usar o provedor do CDNJS padrão.</span><span class="sxs-lookup"><span data-stu-id="40d15-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="40d15-135">Os valores válidos incluem:</span><span class="sxs-lookup"><span data-stu-id="40d15-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![comando de inicialização libman – provedor padrão](_static/libman-init-provider.png)

<span data-ttu-id="40d15-137">Um *libman.json* arquivo é adicionado à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="40d15-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="40d15-138">Adicione arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="40d15-138">Add library files</span></span>

<span data-ttu-id="40d15-139">O `libman install` comando baixa e instala os arquivos de biblioteca no projeto.</span><span class="sxs-lookup"><span data-stu-id="40d15-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="40d15-140">Um *libman.json* arquivo for adicionado, se ele existir.</span><span class="sxs-lookup"><span data-stu-id="40d15-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="40d15-141">O *libman.json* arquivo é modificado para armazenar detalhes de configuração para os arquivos de biblioteca.</span><span class="sxs-lookup"><span data-stu-id="40d15-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="40d15-142">Sinopse</span><span class="sxs-lookup"><span data-stu-id="40d15-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="40d15-143">Arguments</span><span class="sxs-lookup"><span data-stu-id="40d15-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="40d15-144">O nome da biblioteca para instalar.</span><span class="sxs-lookup"><span data-stu-id="40d15-144">The name of the library to install.</span></span> <span data-ttu-id="40d15-145">Esse nome pode incluir a notação de número de versão (por exemplo, `@1.2.0`).</span><span class="sxs-lookup"><span data-stu-id="40d15-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="40d15-146">Opções</span><span class="sxs-lookup"><span data-stu-id="40d15-146">Options</span></span>

<span data-ttu-id="40d15-147">As seguintes opções estão disponíveis para o `libman install` comando:</span><span class="sxs-lookup"><span data-stu-id="40d15-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="40d15-148">O local para instalar a biblioteca.</span><span class="sxs-lookup"><span data-stu-id="40d15-148">The location to install the library.</span></span> <span data-ttu-id="40d15-149">Se não for especificado, o local padrão é usado.</span><span class="sxs-lookup"><span data-stu-id="40d15-149">If not specified, the default location is used.</span></span> <span data-ttu-id="40d15-150">Se nenhum `defaultDestination` propriedade é especificada em *libman.json*, essa opção é necessária.</span><span class="sxs-lookup"><span data-stu-id="40d15-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="40d15-151">Especifique o nome do arquivo para instalá-lo da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="40d15-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="40d15-152">Se não for especificado, todos os arquivos da biblioteca são instalados.</span><span class="sxs-lookup"><span data-stu-id="40d15-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="40d15-153">Forneça um `--files` opção por arquivo para ser instalado.</span><span class="sxs-lookup"><span data-stu-id="40d15-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="40d15-154">Caminhos relativos são suportados também.</span><span class="sxs-lookup"><span data-stu-id="40d15-154">Relative paths are supported too.</span></span> <span data-ttu-id="40d15-155">Por exemplo: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="40d15-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="40d15-156">O nome do provedor a ser usado para a aquisição de biblioteca.</span><span class="sxs-lookup"><span data-stu-id="40d15-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="40d15-157">Substitua `<PROVIDER>` com um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="40d15-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="40d15-158">Se não for especificado, o `defaultProvider` propriedade na *libman.json* é usado.</span><span class="sxs-lookup"><span data-stu-id="40d15-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="40d15-159">Se nenhum `defaultProvider` propriedade é especificada em *libman.json*, essa opção é necessária.</span><span class="sxs-lookup"><span data-stu-id="40d15-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="40d15-160">Exemplos</span><span class="sxs-lookup"><span data-stu-id="40d15-160">Examples</span></span>

<span data-ttu-id="40d15-161">Considere o seguinte *libman.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="40d15-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="40d15-162">Para instalar a versão do jQuery 3.2.1 *jQuery* do arquivo para o *wwwroot/scripts/jquery* pasta usando o provedor do CDNJS:</span><span class="sxs-lookup"><span data-stu-id="40d15-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="40d15-163">O *libman.json* arquivo semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="40d15-163">The *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

<span data-ttu-id="40d15-164">Para instalar o *calendar.js* e *calendar.css* arquivos da *c:\\temp\\contosoCalendar\\*  usando o sistema de arquivos provedor:</span><span class="sxs-lookup"><span data-stu-id="40d15-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="40d15-165">O seguinte aviso é exibido por dois motivos:</span><span class="sxs-lookup"><span data-stu-id="40d15-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="40d15-166">O *libman.json* arquivo não contém um `defaultDestination` propriedade.</span><span class="sxs-lookup"><span data-stu-id="40d15-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="40d15-167">O `libman install` comando não contém o `-d|--destination` opção.</span><span class="sxs-lookup"><span data-stu-id="40d15-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman instalar comando - destino](_static/libman-install-destination.png)

<span data-ttu-id="40d15-169">Depois de aceitar o destino padrão, o *libman.json* arquivo semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="40d15-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a><span data-ttu-id="40d15-170">Restaurar arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="40d15-170">Restore library files</span></span>

<span data-ttu-id="40d15-171">O `libman restore` comando instala arquivos de biblioteca definidos nas *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="40d15-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="40d15-172">As seguintes regras se aplicam:</span><span class="sxs-lookup"><span data-stu-id="40d15-172">The following rules apply:</span></span>

* <span data-ttu-id="40d15-173">Se nenhum *libman.json* arquivo existir na raiz do projeto, um erro será retornado.</span><span class="sxs-lookup"><span data-stu-id="40d15-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="40d15-174">Se uma biblioteca Especifica um provedor, o `defaultProvider` propriedade na *libman.json* será ignorado.</span><span class="sxs-lookup"><span data-stu-id="40d15-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="40d15-175">Se uma biblioteca Especifica um destino, o `defaultDestination` propriedade na *libman.json* será ignorado.</span><span class="sxs-lookup"><span data-stu-id="40d15-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="40d15-176">Sinopse</span><span class="sxs-lookup"><span data-stu-id="40d15-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="40d15-177">Opções</span><span class="sxs-lookup"><span data-stu-id="40d15-177">Options</span></span>

<span data-ttu-id="40d15-178">As seguintes opções estão disponíveis para o `libman restore` comando:</span><span class="sxs-lookup"><span data-stu-id="40d15-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="40d15-179">Exemplos</span><span class="sxs-lookup"><span data-stu-id="40d15-179">Examples</span></span>

<span data-ttu-id="40d15-180">Para restaurar os arquivos de biblioteca definidos em *libman.json*:</span><span class="sxs-lookup"><span data-stu-id="40d15-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="40d15-181">Excluir arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="40d15-181">Delete library files</span></span>

<span data-ttu-id="40d15-182">O `libman clean` comando exclui os arquivos de biblioteca restaurados anteriormente por meio de LibMan.</span><span class="sxs-lookup"><span data-stu-id="40d15-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="40d15-183">Pastas se tornar vazias depois dessa operação são excluídas.</span><span class="sxs-lookup"><span data-stu-id="40d15-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="40d15-184">Os arquivos de biblioteca associados configurações na `libraries` propriedade de *libman.json* não são removidos.</span><span class="sxs-lookup"><span data-stu-id="40d15-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="40d15-185">Sinopse</span><span class="sxs-lookup"><span data-stu-id="40d15-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="40d15-186">Opções</span><span class="sxs-lookup"><span data-stu-id="40d15-186">Options</span></span>

<span data-ttu-id="40d15-187">As seguintes opções estão disponíveis para o `libman clean` comando:</span><span class="sxs-lookup"><span data-stu-id="40d15-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="40d15-188">Exemplos</span><span class="sxs-lookup"><span data-stu-id="40d15-188">Examples</span></span>

<span data-ttu-id="40d15-189">Para excluir arquivos de biblioteca instalados por meio de LibMan:</span><span class="sxs-lookup"><span data-stu-id="40d15-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="40d15-190">Desinstalar os arquivos de biblioteca</span><span class="sxs-lookup"><span data-stu-id="40d15-190">Uninstall library files</span></span>

<span data-ttu-id="40d15-191">O `libman uninstall` comando:</span><span class="sxs-lookup"><span data-stu-id="40d15-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="40d15-192">Exclui todos os arquivos associados a biblioteca especificada do destino no *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="40d15-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="40d15-193">Remove a configuração de biblioteca associado de *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="40d15-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="40d15-194">Ocorre um erro quando:</span><span class="sxs-lookup"><span data-stu-id="40d15-194">An error occurs when:</span></span>

* <span data-ttu-id="40d15-195">Não *libman.json* arquivo existe na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="40d15-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="40d15-196">A biblioteca especificada não existe.</span><span class="sxs-lookup"><span data-stu-id="40d15-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="40d15-197">Se mais de uma biblioteca com o mesmo nome estiver instalada, solicitado para escolher um.</span><span class="sxs-lookup"><span data-stu-id="40d15-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="40d15-198">Sinopse</span><span class="sxs-lookup"><span data-stu-id="40d15-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="40d15-199">Arguments</span><span class="sxs-lookup"><span data-stu-id="40d15-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="40d15-200">O nome da biblioteca para desinstalar.</span><span class="sxs-lookup"><span data-stu-id="40d15-200">The name of the library to uninstall.</span></span> <span data-ttu-id="40d15-201">Esse nome pode incluir a notação de número de versão (por exemplo, `@1.2.0`).</span><span class="sxs-lookup"><span data-stu-id="40d15-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="40d15-202">Opções</span><span class="sxs-lookup"><span data-stu-id="40d15-202">Options</span></span>

<span data-ttu-id="40d15-203">As seguintes opções estão disponíveis para o `libman uninstall` comando:</span><span class="sxs-lookup"><span data-stu-id="40d15-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="40d15-204">Exemplos</span><span class="sxs-lookup"><span data-stu-id="40d15-204">Examples</span></span>

<span data-ttu-id="40d15-205">Considere o seguinte *libman.json* arquivo:</span><span class="sxs-lookup"><span data-stu-id="40d15-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="40d15-206">Para desinstalar o jQuery, qualquer um dos comandos a seguir bem-sucedida:</span><span class="sxs-lookup"><span data-stu-id="40d15-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="40d15-207">Para desinstalar os arquivos de Lodash instalados por meio de `filesystem` provedor:</span><span class="sxs-lookup"><span data-stu-id="40d15-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="40d15-208">Atualizar a versão da biblioteca</span><span class="sxs-lookup"><span data-stu-id="40d15-208">Update library version</span></span>

<span data-ttu-id="40d15-209">O `libman update` comando atualiza uma biblioteca instalada via LibMan à versão especificada.</span><span class="sxs-lookup"><span data-stu-id="40d15-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="40d15-210">Ocorre um erro quando:</span><span class="sxs-lookup"><span data-stu-id="40d15-210">An error occurs when:</span></span>

* <span data-ttu-id="40d15-211">Não *libman.json* arquivo existe na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="40d15-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="40d15-212">A biblioteca especificada não existe.</span><span class="sxs-lookup"><span data-stu-id="40d15-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="40d15-213">Se mais de uma biblioteca com o mesmo nome estiver instalada, solicitado para escolher um.</span><span class="sxs-lookup"><span data-stu-id="40d15-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="40d15-214">Sinopse</span><span class="sxs-lookup"><span data-stu-id="40d15-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="40d15-215">Arguments</span><span class="sxs-lookup"><span data-stu-id="40d15-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="40d15-216">O nome da biblioteca para atualizar.</span><span class="sxs-lookup"><span data-stu-id="40d15-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="40d15-217">Opções</span><span class="sxs-lookup"><span data-stu-id="40d15-217">Options</span></span>

<span data-ttu-id="40d15-218">As seguintes opções estão disponíveis para o `libman update` comando:</span><span class="sxs-lookup"><span data-stu-id="40d15-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="40d15-219">Obter a versão de pré-lançamento mais recente da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="40d15-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="40d15-220">Obtenha uma versão específica da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="40d15-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="40d15-221">Exemplos</span><span class="sxs-lookup"><span data-stu-id="40d15-221">Examples</span></span>

* <span data-ttu-id="40d15-222">Para atualizar o jQuery para a versão mais recente:</span><span class="sxs-lookup"><span data-stu-id="40d15-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="40d15-223">Para atualizar o jQuery versão 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="40d15-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="40d15-224">Para atualizar o jQuery para a versão de pré-lançamento mais recente:</span><span class="sxs-lookup"><span data-stu-id="40d15-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="40d15-225">Gerenciar o cache de biblioteca</span><span class="sxs-lookup"><span data-stu-id="40d15-225">Manage library cache</span></span>

<span data-ttu-id="40d15-226">O `libman cache` comando gerencia o cache de biblioteca LibMan.</span><span class="sxs-lookup"><span data-stu-id="40d15-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="40d15-227">O `filesystem` provedor não usa o cache de biblioteca.</span><span class="sxs-lookup"><span data-stu-id="40d15-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="40d15-228">Sinopse</span><span class="sxs-lookup"><span data-stu-id="40d15-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="40d15-229">Arguments</span><span class="sxs-lookup"><span data-stu-id="40d15-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="40d15-230">Usado somente com o `clean` comando.</span><span class="sxs-lookup"><span data-stu-id="40d15-230">Only used with the `clean` command.</span></span> <span data-ttu-id="40d15-231">Especifica para limpar o cache do provedor.</span><span class="sxs-lookup"><span data-stu-id="40d15-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="40d15-232">Os valores válidos incluem:</span><span class="sxs-lookup"><span data-stu-id="40d15-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="40d15-233">Opções</span><span class="sxs-lookup"><span data-stu-id="40d15-233">Options</span></span>

<span data-ttu-id="40d15-234">As seguintes opções estão disponíveis para o `libman cache` comando:</span><span class="sxs-lookup"><span data-stu-id="40d15-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="40d15-235">Liste os nomes de arquivos que são armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="40d15-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="40d15-236">Liste os nomes de bibliotecas que são armazenados em cache.</span><span class="sxs-lookup"><span data-stu-id="40d15-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="40d15-237">Exemplos</span><span class="sxs-lookup"><span data-stu-id="40d15-237">Examples</span></span>

* <span data-ttu-id="40d15-238">Para exibir os nomes de bibliotecas em cache por um provedor, use um dos seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="40d15-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="40d15-239">Uma saída semelhante à apresentada a seguir será exibida:</span><span class="sxs-lookup"><span data-stu-id="40d15-239">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* <span data-ttu-id="40d15-240">Para exibir os nomes dos arquivos de biblioteca em cache por provedor:</span><span class="sxs-lookup"><span data-stu-id="40d15-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="40d15-241">Uma saída semelhante à apresentada a seguir será exibida:</span><span class="sxs-lookup"><span data-stu-id="40d15-241">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  <span data-ttu-id="40d15-242">Observe que a saída anterior mostra que o jQuery versões 3.2.1 e 3.3.1 são armazenados em cache no provedor de CDNJS.</span><span class="sxs-lookup"><span data-stu-id="40d15-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="40d15-243">Para esvaziar o cache de biblioteca para o provedor do CDNJS:</span><span class="sxs-lookup"><span data-stu-id="40d15-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="40d15-244">Depois de esvaziar o cache do provedor do CDNJS o `libman cache list` comando exibe o seguinte:</span><span class="sxs-lookup"><span data-stu-id="40d15-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* <span data-ttu-id="40d15-245">Para esvaziar o cache para todos os provedores com suporte:</span><span class="sxs-lookup"><span data-stu-id="40d15-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="40d15-246">Depois de esvaziar todos os caches de provedor, o `libman cache list` comando exibe o seguinte:</span><span class="sxs-lookup"><span data-stu-id="40d15-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="40d15-247">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="40d15-247">Additional resources</span></span>

* [<span data-ttu-id="40d15-248">Instalar uma ferramenta Global</span><span class="sxs-lookup"><span data-stu-id="40d15-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="40d15-249">Repositório do GitHub do LibMan</span><span class="sxs-lookup"><span data-stu-id="40d15-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
