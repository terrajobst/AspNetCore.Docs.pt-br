---
title: Testar APIs Web com o HTTP REPL
author: scottaddie
description: Saiba como usar a ferramenta global HTTP REPL do .NET Core para navegar e testar uma API Web do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/11/2019
uid: web-api/http-repl
ms.openlocfilehash: 15899917826fb6559244998766d99d00f56e0521
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294725"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="280f8-103">Testar APIs Web com o HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="280f8-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="280f8-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="280f8-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="280f8-105">O HTTP REPL (Read-Eval-Print Loop) é:</span><span class="sxs-lookup"><span data-stu-id="280f8-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="280f8-106">Uma ferramenta de linha de comando leve e multiplataforma compatível com todos os recursos compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="280f8-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="280f8-107">Usado para fazer solicitações HTTP a fim de testar as APIs Web do ASP.NET Core (e as APIs Web não pertencentes ao ASP.NET Core) e exibir os resultados.</span><span class="sxs-lookup"><span data-stu-id="280f8-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="280f8-108">Capaz de testar APIs Web hospedadas em qualquer ambiente, inclusive no localhost e no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="280f8-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="280f8-109">Os [verbos HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) a seguir são compatíveis:</span><span class="sxs-lookup"><span data-stu-id="280f8-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="280f8-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="280f8-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="280f8-111">GET</span><span class="sxs-lookup"><span data-stu-id="280f8-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="280f8-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="280f8-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="280f8-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="280f8-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="280f8-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="280f8-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="280f8-115">POST</span><span class="sxs-lookup"><span data-stu-id="280f8-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="280f8-116">PUT</span><span class="sxs-lookup"><span data-stu-id="280f8-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="280f8-117">Para acompanhar, [exiba ou baixe a API Web de exemplo do ASP.NET Core](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="280f8-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="280f8-118">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="280f8-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="280f8-119">Instalação</span><span class="sxs-lookup"><span data-stu-id="280f8-119">Installation</span></span>

<span data-ttu-id="280f8-120">Para instalar o HTTP REPL, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="280f8-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="280f8-121">Uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) é instalada pelo pacote do NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).</span><span class="sxs-lookup"><span data-stu-id="280f8-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="280f8-122">Medição de</span><span class="sxs-lookup"><span data-stu-id="280f8-122">Usage</span></span>

<span data-ttu-id="280f8-123">Após a instalação da ferramenta, execute o seguinte comando para iniciar o HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="280f8-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="280f8-124">Para exibir os comandos HTTP REPL disponíveis, execute um dos seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="280f8-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="280f8-125">É exibida a saída a seguir:</span><span class="sxs-lookup"><span data-stu-id="280f8-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="280f8-126">O HTTP REPL oferece conclusão de comando.</span><span class="sxs-lookup"><span data-stu-id="280f8-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="280f8-127">Pressionar a tecla <kbd>Tab</kbd> itera na lista de comandos que completam os caracteres ou o ponto de extremidade da API que você digitou.</span><span class="sxs-lookup"><span data-stu-id="280f8-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="280f8-128">As seções a seguir descrevem os comandos da CLI disponíveis.</span><span class="sxs-lookup"><span data-stu-id="280f8-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="280f8-129">Conectar-se à API Web</span><span class="sxs-lookup"><span data-stu-id="280f8-129">Connect to the web API</span></span>

<span data-ttu-id="280f8-130">Conecte-se à uma API Web executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="280f8-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="280f8-131">`<ROOT URI>` é o URI de base para a API Web.</span><span class="sxs-lookup"><span data-stu-id="280f8-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="280f8-132">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="280f8-133">Outra opção é executar o seguinte comando a qualquer momento quando o HTTP REPL estiver em execução:</span><span class="sxs-lookup"><span data-stu-id="280f8-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="280f8-134">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="280f8-135">Apontar manualmente para o documento Swagger da API Web</span><span class="sxs-lookup"><span data-stu-id="280f8-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="280f8-136">O comando connect acima tentará localizar automaticamente o documento do Swagger.</span><span class="sxs-lookup"><span data-stu-id="280f8-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="280f8-137">Se isso não for possível por algum motivo, você poderá especificar o URI do documento do Swagger para a API Web usando a opção `--swagger`:</span><span class="sxs-lookup"><span data-stu-id="280f8-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="280f8-138">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="280f8-139">Navegar na API Web</span><span class="sxs-lookup"><span data-stu-id="280f8-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="280f8-140">Exibir os pontos de extremidade disponíveis</span><span class="sxs-lookup"><span data-stu-id="280f8-140">View available endpoints</span></span>

<span data-ttu-id="280f8-141">Para listar os diferentes pontos de extremidade (controladores) no caminho atual do endereço da API Web, execute os comandos `ls` ou `dir`:</span><span class="sxs-lookup"><span data-stu-id="280f8-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="280f8-142">O seguinte formato de saída será exibido:</span><span class="sxs-lookup"><span data-stu-id="280f8-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="280f8-143">A saída anterior indica que há dois controladores disponíveis: `Fruits` e `People`.</span><span class="sxs-lookup"><span data-stu-id="280f8-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="280f8-144">Ambos os controladores são compatíveis com operações HTTP GET e POST sem parâmetro.</span><span class="sxs-lookup"><span data-stu-id="280f8-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="280f8-145">Navegar em um controlador específico revela mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="280f8-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="280f8-146">Por exemplo, a saída do seguinte comando mostra que o controlador `Fruits` também é compatível com as operações HTTP GET, PUT e DELETE.</span><span class="sxs-lookup"><span data-stu-id="280f8-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="280f8-147">Cada uma dessas operações espera um parâmetro `id` na rota:</span><span class="sxs-lookup"><span data-stu-id="280f8-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="280f8-148">Outra opção é executar o comando `ui` para abrir a página da interface do usuário do Swagger da API Web em um navegador.</span><span class="sxs-lookup"><span data-stu-id="280f8-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="280f8-149">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="280f8-150">Navegar até um ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="280f8-150">Navigate to an endpoint</span></span>

<span data-ttu-id="280f8-151">Para navegar até um ponto de extremidade diferente na API Web, execute o comando `cd`:</span><span class="sxs-lookup"><span data-stu-id="280f8-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="280f8-152">O caminho após o comando `cd` não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="280f8-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="280f8-153">O seguinte formato de saída será exibido:</span><span class="sxs-lookup"><span data-stu-id="280f8-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="280f8-154">Personalizar o HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="280f8-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="280f8-155">As [cores](#set-color-preferences) padrão do HTTP REPL podem ser personalizadas.</span><span class="sxs-lookup"><span data-stu-id="280f8-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="280f8-156">Além disso, um [editor de texto padrão](#set-the-default-text-editor) pode ser definido.</span><span class="sxs-lookup"><span data-stu-id="280f8-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="280f8-157">As preferências de HTTP REPL são persistidas em toda a sessão atual e serão respeitadas nas sessões futuras.</span><span class="sxs-lookup"><span data-stu-id="280f8-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="280f8-158">Depois de modificadas, as preferências são armazenadas no seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="280f8-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="280f8-159">Linux</span><span class="sxs-lookup"><span data-stu-id="280f8-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="280f8-160">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="280f8-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="280f8-161">macOS</span><span class="sxs-lookup"><span data-stu-id="280f8-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="280f8-162">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="280f8-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="280f8-163">Windows</span><span class="sxs-lookup"><span data-stu-id="280f8-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="280f8-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="280f8-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="280f8-165">O arquivo *.httpreplprefs* é carregado na inicialização e suas alterações não são monitoradas no runtime.</span><span class="sxs-lookup"><span data-stu-id="280f8-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="280f8-166">As modificações manuais no arquivo entram em vigor somente após a reinicialização da ferramenta.</span><span class="sxs-lookup"><span data-stu-id="280f8-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="280f8-167">Exibir as configurações</span><span class="sxs-lookup"><span data-stu-id="280f8-167">View the settings</span></span>

<span data-ttu-id="280f8-168">Para exibir as configurações disponíveis, execute o comando `pref get`.</span><span class="sxs-lookup"><span data-stu-id="280f8-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="280f8-169">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="280f8-170">O comando anterior exibe os pares chave-valor disponíveis:</span><span class="sxs-lookup"><span data-stu-id="280f8-170">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="280f8-171">Definir preferências de cores</span><span class="sxs-lookup"><span data-stu-id="280f8-171">Set color preferences</span></span>

<span data-ttu-id="280f8-172">No momento, as cores das respostas só são compatíveis com JSON.</span><span class="sxs-lookup"><span data-stu-id="280f8-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="280f8-173">Para personalizar a cor padrão da ferramenta HTTP REPL, localize a chave correspondente à cor a ser alterada.</span><span class="sxs-lookup"><span data-stu-id="280f8-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="280f8-174">Para ver instruções sobre como localizar as chaves, confira a seção [Exibir as configurações](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="280f8-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="280f8-175">Por exemplo, altere o valor da chave `colors.json` de `Green` para `White`, desta forma:</span><span class="sxs-lookup"><span data-stu-id="280f8-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="280f8-176">Somente as [cores permitidas](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) podem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="280f8-176">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="280f8-177">As solicitações HTTP subsequentes exibem a saída com a nova cor.</span><span class="sxs-lookup"><span data-stu-id="280f8-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="280f8-178">Quando chaves de cor específicas não estão definidas, mais chaves genéricas são consideradas.</span><span class="sxs-lookup"><span data-stu-id="280f8-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="280f8-179">Para demonstrar esse comportamento de fallback, considere o seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="280f8-180">Se `colors.json.name` não tiver um valor, `colors.json.string` será usado.</span><span class="sxs-lookup"><span data-stu-id="280f8-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="280f8-181">Se `colors.json.string` não tiver um valor, `colors.json.literal` será usado.</span><span class="sxs-lookup"><span data-stu-id="280f8-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="280f8-182">Se `colors.json.literal` não tiver um valor, `colors.json` será usado.</span><span class="sxs-lookup"><span data-stu-id="280f8-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="280f8-183">Se `colors.json` não tiver um valor, a cor de texto padrão do shell de comando (`AllowedColors.None`) será usada.</span><span class="sxs-lookup"><span data-stu-id="280f8-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="280f8-184">Definir o tamanho do recuo</span><span class="sxs-lookup"><span data-stu-id="280f8-184">Set indentation size</span></span>

<span data-ttu-id="280f8-185">A personalização do tamanho do recuo da resposta só é compatível com JSON.</span><span class="sxs-lookup"><span data-stu-id="280f8-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="280f8-186">O tamanho padrão é dois espaços.</span><span class="sxs-lookup"><span data-stu-id="280f8-186">The default size is two spaces.</span></span> <span data-ttu-id="280f8-187">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-187">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="280f8-188">Para alterar o tamanho padrão, defina a chave `formatting.json.indentSize`.</span><span class="sxs-lookup"><span data-stu-id="280f8-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="280f8-189">Por exemplo, para sempre usar quatro espaços:</span><span class="sxs-lookup"><span data-stu-id="280f8-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="280f8-190">As respostas subsequentes respeitarão a configuração de quatro espaços:</span><span class="sxs-lookup"><span data-stu-id="280f8-190">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="280f8-191">Definir o editor de texto padrão</span><span class="sxs-lookup"><span data-stu-id="280f8-191">Set the default text editor</span></span>

<span data-ttu-id="280f8-192">Por padrão, o HTTP REPL não tem um editor de texto configurado para uso.</span><span class="sxs-lookup"><span data-stu-id="280f8-192">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="280f8-193">Para testar os métodos de API Web que exigem o corpo da solicitação HTTP, é preciso definir um editor de texto padrão.</span><span class="sxs-lookup"><span data-stu-id="280f8-193">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="280f8-194">A ferramenta HTTP REPL inicia o editor de texto configurado somente para escrever o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="280f8-194">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="280f8-195">Execute o seguinte comando para definir o editor de texto preferido como padrão:</span><span class="sxs-lookup"><span data-stu-id="280f8-195">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="280f8-196">No comando anterior, `<EXECUTABLE>` é o caminho completo para o arquivo executável do editor de texto.</span><span class="sxs-lookup"><span data-stu-id="280f8-196">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="280f8-197">Por exemplo, execute o seguinte comando para definir o Visual Studio Code como o editor de texto padrão:</span><span class="sxs-lookup"><span data-stu-id="280f8-197">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="280f8-198">Linux</span><span class="sxs-lookup"><span data-stu-id="280f8-198">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="280f8-199">macOS</span><span class="sxs-lookup"><span data-stu-id="280f8-199">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="280f8-200">Windows</span><span class="sxs-lookup"><span data-stu-id="280f8-200">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="280f8-201">Para iniciar o editor de texto padrão com argumentos específicos da CLI, defina a chave `editor.command.default.arguments`.</span><span class="sxs-lookup"><span data-stu-id="280f8-201">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="280f8-202">Por exemplo, imagine que o Visual Studio Code é o editor de texto padrão e que você quer que o HTTP REPL sempre o abra em uma nova sessão com as extensões desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="280f8-202">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="280f8-203">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="280f8-203">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="280f8-204">Definir os caminhos de pesquisa do Swagger</span><span class="sxs-lookup"><span data-stu-id="280f8-204">Set the Swagger search paths</span></span>

<span data-ttu-id="280f8-205">Por padrão, HTTP REPL tem um conjunto de caminhos relativos que ele usa para localizar o documento do Swagger ao executar o comando `connect` sem a opção `--swagger`.</span><span class="sxs-lookup"><span data-stu-id="280f8-205">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="280f8-206">Esses caminhos relativos são combinados com os caminhos raiz e base especificados no comando `connect`.</span><span class="sxs-lookup"><span data-stu-id="280f8-206">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="280f8-207">Os caminhos relativos padrão são:</span><span class="sxs-lookup"><span data-stu-id="280f8-207">The default relative paths are:</span></span>

- <span data-ttu-id="280f8-208">*swagger.json*</span><span class="sxs-lookup"><span data-stu-id="280f8-208">*swagger.json*</span></span>
- <span data-ttu-id="280f8-209">*swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="280f8-209">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="280f8-210">*/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="280f8-210">*/swagger.json*</span></span>
- <span data-ttu-id="280f8-211">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="280f8-211">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="280f8-212">Para usar um conjunto diferente de caminhos de pesquisa em seu ambiente, defina a preferência `swagger.searchPaths`.</span><span class="sxs-lookup"><span data-stu-id="280f8-212">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="280f8-213">O valor precisa ser uma lista delimitada por pipes de caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="280f8-213">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="280f8-214">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-214">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="280f8-215">Testar solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="280f8-215">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="280f8-216">Sinopse</span><span class="sxs-lookup"><span data-stu-id="280f8-216">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="280f8-217">Arguments</span><span class="sxs-lookup"><span data-stu-id="280f8-217">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="280f8-218">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="280f8-218">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="280f8-219">Opções</span><span class="sxs-lookup"><span data-stu-id="280f8-219">Options</span></span>

<span data-ttu-id="280f8-220">As opções a seguir estão disponíveis para o comando `get`:</span><span class="sxs-lookup"><span data-stu-id="280f8-220">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="280f8-221">Exemplo</span><span class="sxs-lookup"><span data-stu-id="280f8-221">Example</span></span>

<span data-ttu-id="280f8-222">Para emitir uma solicitação HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="280f8-222">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="280f8-223">Execute o comando `get` em um ponto de extremidade compatível:</span><span class="sxs-lookup"><span data-stu-id="280f8-223">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="280f8-224">O comando anterior exibirá o seguinte formato de saída:</span><span class="sxs-lookup"><span data-stu-id="280f8-224">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. <span data-ttu-id="280f8-225">Recupere um registro específico passando um parâmetro para o comando `get`:</span><span class="sxs-lookup"><span data-stu-id="280f8-225">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="280f8-226">O comando anterior exibirá o seguinte formato de saída:</span><span class="sxs-lookup"><span data-stu-id="280f8-226">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="280f8-227">Testar solicitações HTTP POST</span><span class="sxs-lookup"><span data-stu-id="280f8-227">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="280f8-228">Sinopse</span><span class="sxs-lookup"><span data-stu-id="280f8-228">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="280f8-229">Arguments</span><span class="sxs-lookup"><span data-stu-id="280f8-229">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="280f8-230">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="280f8-230">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="280f8-231">Opções</span><span class="sxs-lookup"><span data-stu-id="280f8-231">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="280f8-232">Exemplo</span><span class="sxs-lookup"><span data-stu-id="280f8-232">Example</span></span>

<span data-ttu-id="280f8-233">Para emitir uma solicitação HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="280f8-233">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="280f8-234">Execute o comando `post` em um ponto de extremidade compatível:</span><span class="sxs-lookup"><span data-stu-id="280f8-234">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="280f8-235">No comando anterior, o cabeçalho da solicitação HTTP `Content-Type` está configurado para indicar um tipo de mídia de corpo da solicitação do JSON.</span><span class="sxs-lookup"><span data-stu-id="280f8-235">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="280f8-236">O editor de texto padrão abrirá um arquivo *.tmp* com um modelo JSON que representa o corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="280f8-236">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="280f8-237">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-237">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="280f8-238">Para definir o editor de texto padrão, confira a seção [Definir o editor de texto padrão](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="280f8-238">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="280f8-239">Modifique o modelo JSON para satisfazer os requisitos de validação de modelo:</span><span class="sxs-lookup"><span data-stu-id="280f8-239">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="280f8-240">Salve o arquivo *.tmp* e feche o editor de texto.</span><span class="sxs-lookup"><span data-stu-id="280f8-240">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="280f8-241">A seguinte saída será exibida no shell de comando:</span><span class="sxs-lookup"><span data-stu-id="280f8-241">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="280f8-242">Testar solicitações HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="280f8-242">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="280f8-243">Sinopse</span><span class="sxs-lookup"><span data-stu-id="280f8-243">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="280f8-244">Arguments</span><span class="sxs-lookup"><span data-stu-id="280f8-244">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="280f8-245">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="280f8-245">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="280f8-246">Opções</span><span class="sxs-lookup"><span data-stu-id="280f8-246">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="280f8-247">Exemplo</span><span class="sxs-lookup"><span data-stu-id="280f8-247">Example</span></span>

<span data-ttu-id="280f8-248">Para emitir uma solicitação HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="280f8-248">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="280f8-249">*Optional*: Run the `get` command to view the data before modifying it:</span><span class="sxs-lookup"><span data-stu-id="280f8-249">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="280f8-250">Execute o comando `put` em um ponto de extremidade compatível:</span><span class="sxs-lookup"><span data-stu-id="280f8-250">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="280f8-251">No comando anterior, o cabeçalho da solicitação HTTP `Content-Type` está configurado para indicar um tipo de mídia de corpo da solicitação do JSON.</span><span class="sxs-lookup"><span data-stu-id="280f8-251">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="280f8-252">O editor de texto padrão abrirá um arquivo *.tmp* com um modelo JSON que representa o corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="280f8-252">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="280f8-253">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-253">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="280f8-254">Para definir o editor de texto padrão, confira a seção [Definir o editor de texto padrão](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="280f8-254">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="280f8-255">Modifique o modelo JSON para satisfazer os requisitos de validação de modelo:</span><span class="sxs-lookup"><span data-stu-id="280f8-255">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="280f8-256">Salve o arquivo *.tmp* e feche o editor de texto.</span><span class="sxs-lookup"><span data-stu-id="280f8-256">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="280f8-257">A seguinte saída será exibida no shell de comando:</span><span class="sxs-lookup"><span data-stu-id="280f8-257">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="280f8-258">*Optional*: Issue a `get` command to see the modifications.</span><span class="sxs-lookup"><span data-stu-id="280f8-258">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="280f8-259">Por exemplo, se você digitou "Cereja" no editor de texto, um `get` retornará o seguinte:</span><span class="sxs-lookup"><span data-stu-id="280f8-259">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="280f8-260">Testar solicitações HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="280f8-260">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="280f8-261">Sinopse</span><span class="sxs-lookup"><span data-stu-id="280f8-261">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="280f8-262">Arguments</span><span class="sxs-lookup"><span data-stu-id="280f8-262">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="280f8-263">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="280f8-263">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="280f8-264">Opções</span><span class="sxs-lookup"><span data-stu-id="280f8-264">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="280f8-265">Exemplo</span><span class="sxs-lookup"><span data-stu-id="280f8-265">Example</span></span>

<span data-ttu-id="280f8-266">Para emitir uma solicitação HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="280f8-266">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="280f8-267">*Optional*: Run the `get` command to view the data before modifying it:</span><span class="sxs-lookup"><span data-stu-id="280f8-267">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="280f8-268">Execute o comando `delete` em um ponto de extremidade compatível:</span><span class="sxs-lookup"><span data-stu-id="280f8-268">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="280f8-269">O comando anterior exibirá o seguinte formato de saída:</span><span class="sxs-lookup"><span data-stu-id="280f8-269">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="280f8-270">*Optional*: Issue a `get` command to see the modifications.</span><span class="sxs-lookup"><span data-stu-id="280f8-270">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="280f8-271">Neste exemplo, o `get` retornará o seguinte:</span><span class="sxs-lookup"><span data-stu-id="280f8-271">In this example, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="280f8-272">Testar solicitações HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="280f8-272">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="280f8-273">Sinopse</span><span class="sxs-lookup"><span data-stu-id="280f8-273">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="280f8-274">Arguments</span><span class="sxs-lookup"><span data-stu-id="280f8-274">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="280f8-275">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="280f8-275">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="280f8-276">Opções</span><span class="sxs-lookup"><span data-stu-id="280f8-276">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="280f8-277">Testar solicitações HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="280f8-277">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="280f8-278">Sinopse</span><span class="sxs-lookup"><span data-stu-id="280f8-278">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="280f8-279">Arguments</span><span class="sxs-lookup"><span data-stu-id="280f8-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="280f8-280">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="280f8-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="280f8-281">Opções</span><span class="sxs-lookup"><span data-stu-id="280f8-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="280f8-282">Testar solicitações HTTP OPTIONS</span><span class="sxs-lookup"><span data-stu-id="280f8-282">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="280f8-283">Sinopse</span><span class="sxs-lookup"><span data-stu-id="280f8-283">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="280f8-284">Arguments</span><span class="sxs-lookup"><span data-stu-id="280f8-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="280f8-285">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="280f8-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="280f8-286">Opções</span><span class="sxs-lookup"><span data-stu-id="280f8-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="280f8-287">Configurar cabeçalhos de solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="280f8-287">Set HTTP request headers</span></span>

<span data-ttu-id="280f8-288">Para configurar um cabeçalho de solicitação HTTP, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="280f8-288">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="280f8-289">Configure embutido com a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="280f8-289">Set inline with the HTTP request.</span></span> <span data-ttu-id="280f8-290">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-290">For example:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="280f8-291">Com a abordagem anterior, cada cabeçalho de solicitação HTTP diferente exige sua própria opção `-h`.</span><span class="sxs-lookup"><span data-stu-id="280f8-291">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="280f8-292">Configure antes de enviar a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="280f8-292">Set before sending the HTTP request.</span></span> <span data-ttu-id="280f8-293">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-293">For example:</span></span>

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    <span data-ttu-id="280f8-294">Ao configurar o cabeçalho antes de enviar a solicitação, ele permanecerá configurado durante toda a sessão do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="280f8-294">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="280f8-295">Para limpar o cabeçalho, forneça um valor vazio.</span><span class="sxs-lookup"><span data-stu-id="280f8-295">To clear the header, provide an empty value.</span></span> <span data-ttu-id="280f8-296">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-296">For example:</span></span>
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="280f8-297">Test secured endpoints</span><span class="sxs-lookup"><span data-stu-id="280f8-297">Test secured endpoints</span></span>

<span data-ttu-id="280f8-298">The HTTP REPL supports the testing of secured endpoints through the use of HTTP request headers.</span><span class="sxs-lookup"><span data-stu-id="280f8-298">The HTTP REPL supports the testing of secured endpoints through the use of HTTP request headers.</span></span> <span data-ttu-id="280f8-299">Examples of supported authentication and authorization schemes include basic authentication, JWT bearer tokens, and digest authentication.</span><span class="sxs-lookup"><span data-stu-id="280f8-299">Examples of supported authentication and authorization schemes include basic authentication, JWT bearer tokens, and digest authentication.</span></span> <span data-ttu-id="280f8-300">For example, you can send a bearer token to an endpoint with the following command:</span><span class="sxs-lookup"><span data-stu-id="280f8-300">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="280f8-301">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span><span class="sxs-lookup"><span data-stu-id="280f8-301">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="280f8-302">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="280f8-302">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="280f8-303">The HTTP REPL sets the bearer token in an HTTP request header and retrieves a list of Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="280f8-303">The HTTP REPL sets the bearer token in an HTTP request header and retrieves a list of Azure App Service Web Apps.</span></span>

1. <span data-ttu-id="280f8-304">Faça logon no Azure:</span><span class="sxs-lookup"><span data-stu-id="280f8-304">Log in to Azure:</span></span>

    ```azcli
    az login
    ```

1. <span data-ttu-id="280f8-305">Get your subscription ID with the following command:</span><span class="sxs-lookup"><span data-stu-id="280f8-305">Get your subscription ID with the following command:</span></span>

    ```azcli
    az account show --query id
    ```

1. <span data-ttu-id="280f8-306">Copy your subscription ID and run the following command:</span><span class="sxs-lookup"><span data-stu-id="280f8-306">Copy your subscription ID and run the following command:</span></span>

    ```azcli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="280f8-307">Get your bearer token with the following command:</span><span class="sxs-lookup"><span data-stu-id="280f8-307">Get your bearer token with the following command:</span></span>

    ```azcli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="280f8-308">Connect to the Azure REST API via the HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="280f8-308">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="280f8-309">Set the `Authorization` HTTP request header:</span><span class="sxs-lookup"><span data-stu-id="280f8-309">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="280f8-310">Navigate to the subscription:</span><span class="sxs-lookup"><span data-stu-id="280f8-310">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="280f8-311">Get a list of your subscription's Azure App Service Web Apps:</span><span class="sxs-lookup"><span data-stu-id="280f8-311">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="280f8-312">The following response is displayed:</span><span class="sxs-lookup"><span data-stu-id="280f8-312">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="280f8-313">Alternar exibição da solicitação HTTP</span><span class="sxs-lookup"><span data-stu-id="280f8-313">Toggle HTTP request display</span></span>

<span data-ttu-id="280f8-314">Por padrão, a exibição da solicitação HTTP que está sendo enviada é suprimida.</span><span class="sxs-lookup"><span data-stu-id="280f8-314">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="280f8-315">É possível alterar a configuração correspondente durante a sessão do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="280f8-315">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="280f8-316">Habilitar a exibição da solicitação</span><span class="sxs-lookup"><span data-stu-id="280f8-316">Enable request display</span></span>

<span data-ttu-id="280f8-317">Exiba a solicitação HTTP que está sendo enviada executando o comando `echo on`.</span><span class="sxs-lookup"><span data-stu-id="280f8-317">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="280f8-318">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-318">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="280f8-319">As solicitações HTTP subsequentes na sessão atual exibem os cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="280f8-319">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="280f8-320">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-320">For example:</span></span>

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a><span data-ttu-id="280f8-321">Desabilitar a exibição da solicitação</span><span class="sxs-lookup"><span data-stu-id="280f8-321">Disable request display</span></span>

<span data-ttu-id="280f8-322">Suprima a exibição da solicitação HTTP que está sendo enviada executando o comando `echo off`.</span><span class="sxs-lookup"><span data-stu-id="280f8-322">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="280f8-323">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-323">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="280f8-324">Executar um script</span><span class="sxs-lookup"><span data-stu-id="280f8-324">Run a script</span></span>

<span data-ttu-id="280f8-325">Se você executar com frequência o mesmo conjunto de comandos HTTP REPL, considere armazená-los em um arquivo de texto.</span><span class="sxs-lookup"><span data-stu-id="280f8-325">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="280f8-326">Os comandos no arquivo assumem a mesma forma que os executados manualmente na linha de comando.</span><span class="sxs-lookup"><span data-stu-id="280f8-326">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="280f8-327">Os comandos podem ser executados em um modo em lote usando o comando `run`.</span><span class="sxs-lookup"><span data-stu-id="280f8-327">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="280f8-328">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-328">For example:</span></span>

1. <span data-ttu-id="280f8-329">Crie um arquivo de texto com um conjunto de comandos delimitados por nova linha.</span><span class="sxs-lookup"><span data-stu-id="280f8-329">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="280f8-330">Para ilustrar, considere um arquivo *people-script.txt* com os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="280f8-330">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="280f8-331">Execute o comando `run`, passando o caminho do arquivo de texto.</span><span class="sxs-lookup"><span data-stu-id="280f8-331">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="280f8-332">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="280f8-332">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="280f8-333">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="280f8-333">The following output appears:</span></span>

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a><span data-ttu-id="280f8-334">Limpar a saída</span><span class="sxs-lookup"><span data-stu-id="280f8-334">Clear the output</span></span>

<span data-ttu-id="280f8-335">Para remover toda a saída gravada no shell de comando pela ferramenta HTTP REPL, execute os comandos `clear` ou `cls`.</span><span class="sxs-lookup"><span data-stu-id="280f8-335">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="280f8-336">Para ilustrar, imagine que o shell de comando contém a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="280f8-336">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="280f8-337">Execute o comando a seguir para limpar a saída:</span><span class="sxs-lookup"><span data-stu-id="280f8-337">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="280f8-338">Após a execução o comando anterior, o shell de comando conterá somente a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="280f8-338">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="280f8-339">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="280f8-339">Additional resources</span></span>

* [<span data-ttu-id="280f8-340">Solicitações da API REST</span><span class="sxs-lookup"><span data-stu-id="280f8-340">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="280f8-341">Repositório do GitHub do HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="280f8-341">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
