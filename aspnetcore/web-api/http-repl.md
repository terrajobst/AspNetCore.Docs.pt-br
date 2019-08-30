---
title: Testar APIs Web com o HTTP REPL
author: scottaddie
description: Saiba como usar a ferramenta global HTTP REPL do .NET Core para navegar e testar uma API Web do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/25/2019
uid: web-api/http-repl
ms.openlocfilehash: d2c5f774595e7a2223e84cc76eecdb9baa04adfe
ms.sourcegitcommit: 776598f71da0d1e4c9e923b3b395d3c3b5825796
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70024798"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="ff215-103">Testar APIs Web com o HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="ff215-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="ff215-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="ff215-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="ff215-105">O HTTP REPL (Read-Eval-Print Loop) é:</span><span class="sxs-lookup"><span data-stu-id="ff215-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="ff215-106">Uma ferramenta de linha de comando leve e multiplataforma compatível com todos os recursos compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff215-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="ff215-107">Usado para fazer solicitações HTTP a fim de testar as APIs Web do ASP.NET Core (e as APIs Web não pertencentes ao ASP.NET Core) e exibir os resultados.</span><span class="sxs-lookup"><span data-stu-id="ff215-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="ff215-108">Capaz de testar APIs Web hospedadas em qualquer ambiente, inclusive no localhost e no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="ff215-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="ff215-109">Os [verbos HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) a seguir são compatíveis:</span><span class="sxs-lookup"><span data-stu-id="ff215-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="ff215-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="ff215-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="ff215-111">GET</span><span class="sxs-lookup"><span data-stu-id="ff215-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="ff215-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="ff215-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="ff215-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="ff215-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="ff215-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="ff215-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="ff215-115">POST</span><span class="sxs-lookup"><span data-stu-id="ff215-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="ff215-116">PUT</span><span class="sxs-lookup"><span data-stu-id="ff215-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="ff215-117">Para acompanhar, [exiba ou baixe a API Web de exemplo do ASP.NET Core](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ff215-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ff215-118">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ff215-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="ff215-119">Instalação</span><span class="sxs-lookup"><span data-stu-id="ff215-119">Installation</span></span>

<span data-ttu-id="ff215-120">Para instalar o HTTP REPL, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff215-120">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version "3.0.0-*"
```

<span data-ttu-id="ff215-121">Uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) é instalada pelo pacote do NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).</span><span class="sxs-lookup"><span data-stu-id="ff215-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="ff215-122">Uso</span><span class="sxs-lookup"><span data-stu-id="ff215-122">Usage</span></span>

<span data-ttu-id="ff215-123">Após a instalação da ferramenta, execute o seguinte comando para iniciar o HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="ff215-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="ff215-124">Para exibir os comandos HTTP REPL disponíveis, execute um dos seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ff215-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="ff215-125">É exibida a saída a seguir:</span><span class="sxs-lookup"><span data-stu-id="ff215-125">The following output is displayed:</span></span>

```console
Usage:
  dotnet httprepl [<BASE_ADDRESS>] [options]

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

<span data-ttu-id="ff215-126">O HTTP REPL oferece conclusão de comando.</span><span class="sxs-lookup"><span data-stu-id="ff215-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="ff215-127">Pressionar a tecla <kbd>Tab</kbd> itera na lista de comandos que completam os caracteres ou o ponto de extremidade da API que você digitou.</span><span class="sxs-lookup"><span data-stu-id="ff215-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="ff215-128">As seções a seguir descrevem os comandos da CLI disponíveis.</span><span class="sxs-lookup"><span data-stu-id="ff215-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="ff215-129">Conectar-se à API Web</span><span class="sxs-lookup"><span data-stu-id="ff215-129">Connect to the web API</span></span>

<span data-ttu-id="ff215-130">Conecte-se à uma API Web executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff215-130">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <ROOT URI>
```

<span data-ttu-id="ff215-131">`<ROOT URI>` é o URI de base para a API Web.</span><span class="sxs-lookup"><span data-stu-id="ff215-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="ff215-132">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-132">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="ff215-133">Outra opção é executar o seguinte comando a qualquer momento quando o HTTP REPL estiver em execução:</span><span class="sxs-lookup"><span data-stu-id="ff215-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="ff215-134">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="ff215-135">Apontar manualmente para o documento Swagger da API Web</span><span class="sxs-lookup"><span data-stu-id="ff215-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="ff215-136">O comando connect acima tentará localizar automaticamente o documento do Swagger.</span><span class="sxs-lookup"><span data-stu-id="ff215-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="ff215-137">Se isso não for possível por algum motivo, você poderá especificar o URI do documento do Swagger para a API Web usando a opção `--swagger`:</span><span class="sxs-lookup"><span data-stu-id="ff215-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="ff215-138">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="ff215-139">Navegar na API Web</span><span class="sxs-lookup"><span data-stu-id="ff215-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="ff215-140">Exibir os pontos de extremidade disponíveis</span><span class="sxs-lookup"><span data-stu-id="ff215-140">View available endpoints</span></span>

<span data-ttu-id="ff215-141">Para listar os diferentes pontos de extremidade (controladores) no caminho atual do endereço da API Web, execute os comandos `ls` ou `dir`:</span><span class="sxs-lookup"><span data-stu-id="ff215-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="ff215-142">O seguinte formato de saída será exibido:</span><span class="sxs-lookup"><span data-stu-id="ff215-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="ff215-143">A saída anterior indica que há dois controladores disponíveis: `Fruits` e `People`.</span><span class="sxs-lookup"><span data-stu-id="ff215-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="ff215-144">Ambos os controladores são compatíveis com operações HTTP GET e POST sem parâmetro.</span><span class="sxs-lookup"><span data-stu-id="ff215-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="ff215-145">Navegar em um controlador específico revela mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="ff215-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="ff215-146">Por exemplo, a saída do seguinte comando mostra que o controlador `Fruits` também é compatível com as operações HTTP GET, PUT e DELETE.</span><span class="sxs-lookup"><span data-stu-id="ff215-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="ff215-147">Cada uma dessas operações espera um parâmetro `id` na rota:</span><span class="sxs-lookup"><span data-stu-id="ff215-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="ff215-148">Outra opção é executar o comando `ui` para abrir a página da interface do usuário do Swagger da API Web em um navegador.</span><span class="sxs-lookup"><span data-stu-id="ff215-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="ff215-149">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="ff215-150">Navegar até um ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="ff215-150">Navigate to an endpoint</span></span>

<span data-ttu-id="ff215-151">Para navegar até um ponto de extremidade diferente na API Web, execute o comando `cd`:</span><span class="sxs-lookup"><span data-stu-id="ff215-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="ff215-152">O caminho após o comando `cd` não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ff215-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="ff215-153">O seguinte formato de saída será exibido:</span><span class="sxs-lookup"><span data-stu-id="ff215-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="ff215-154">Personalizar o HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="ff215-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="ff215-155">As [cores](#set-color-preferences) padrão do HTTP REPL podem ser personalizadas.</span><span class="sxs-lookup"><span data-stu-id="ff215-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="ff215-156">Além disso, um [editor de texto padrão](#set-the-default-text-editor) pode ser definido.</span><span class="sxs-lookup"><span data-stu-id="ff215-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="ff215-157">As preferências de HTTP REPL são persistidas em toda a sessão atual e serão respeitadas nas sessões futuras.</span><span class="sxs-lookup"><span data-stu-id="ff215-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="ff215-158">Depois de modificadas, as preferências são armazenadas no seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="ff215-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="ff215-159">Linux</span><span class="sxs-lookup"><span data-stu-id="ff215-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="ff215-160">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="ff215-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="ff215-161">macOS</span><span class="sxs-lookup"><span data-stu-id="ff215-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="ff215-162">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="ff215-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="ff215-163">Windows</span><span class="sxs-lookup"><span data-stu-id="ff215-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="ff215-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="ff215-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="ff215-165">O arquivo *.httpreplprefs* é carregado na inicialização e suas alterações não são monitoradas no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ff215-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="ff215-166">As modificações manuais no arquivo entram em vigor somente após a reinicialização da ferramenta.</span><span class="sxs-lookup"><span data-stu-id="ff215-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="ff215-167">Exibir as configurações</span><span class="sxs-lookup"><span data-stu-id="ff215-167">View the settings</span></span>

<span data-ttu-id="ff215-168">Para exibir as configurações disponíveis, execute o comando `pref get`.</span><span class="sxs-lookup"><span data-stu-id="ff215-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="ff215-169">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="ff215-170">O comando anterior exibe os pares chave-valor disponíveis:</span><span class="sxs-lookup"><span data-stu-id="ff215-170">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="ff215-171">Definir preferências de cores</span><span class="sxs-lookup"><span data-stu-id="ff215-171">Set color preferences</span></span>

<span data-ttu-id="ff215-172">No momento, as cores das respostas só são compatíveis com JSON.</span><span class="sxs-lookup"><span data-stu-id="ff215-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="ff215-173">Para personalizar a cor padrão da ferramenta HTTP REPL, localize a chave correspondente à cor a ser alterada.</span><span class="sxs-lookup"><span data-stu-id="ff215-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="ff215-174">Para ver instruções sobre como localizar as chaves, confira a seção [Exibir as configurações](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="ff215-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="ff215-175">Por exemplo, altere o valor da chave `colors.json` de `Green` para `White`, desta forma:</span><span class="sxs-lookup"><span data-stu-id="ff215-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="ff215-176">Somente as [cores permitidas](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) podem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="ff215-176">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="ff215-177">As solicitações HTTP subsequentes exibem a saída com a nova cor.</span><span class="sxs-lookup"><span data-stu-id="ff215-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="ff215-178">Quando chaves de cor específicas não estão definidas, mais chaves genéricas são consideradas.</span><span class="sxs-lookup"><span data-stu-id="ff215-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="ff215-179">Para demonstrar esse comportamento de fallback, considere o seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="ff215-180">Se `colors.json.name` não tiver um valor, `colors.json.string` será usado.</span><span class="sxs-lookup"><span data-stu-id="ff215-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="ff215-181">Se `colors.json.string` não tiver um valor, `colors.json.literal` será usado.</span><span class="sxs-lookup"><span data-stu-id="ff215-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="ff215-182">Se `colors.json.literal` não tiver um valor, `colors.json` será usado.</span><span class="sxs-lookup"><span data-stu-id="ff215-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="ff215-183">Se `colors.json` não tiver um valor, a cor de texto padrão do shell de comando (`AllowedColors.None`) será usada.</span><span class="sxs-lookup"><span data-stu-id="ff215-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="ff215-184">Definir o tamanho do recuo</span><span class="sxs-lookup"><span data-stu-id="ff215-184">Set indentation size</span></span>

<span data-ttu-id="ff215-185">A personalização do tamanho do recuo da resposta só é compatível com JSON.</span><span class="sxs-lookup"><span data-stu-id="ff215-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="ff215-186">O tamanho padrão é dois espaços.</span><span class="sxs-lookup"><span data-stu-id="ff215-186">The default size is two spaces.</span></span> <span data-ttu-id="ff215-187">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-187">For example:</span></span>

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

<span data-ttu-id="ff215-188">Para alterar o tamanho padrão, defina a chave `formatting.json.indentSize`.</span><span class="sxs-lookup"><span data-stu-id="ff215-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="ff215-189">Por exemplo, para sempre usar quatro espaços:</span><span class="sxs-lookup"><span data-stu-id="ff215-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="ff215-190">As respostas subsequentes respeitarão a configuração de quatro espaços:</span><span class="sxs-lookup"><span data-stu-id="ff215-190">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-indentation-size"></a><span data-ttu-id="ff215-191">Definir o tamanho do recuo</span><span class="sxs-lookup"><span data-stu-id="ff215-191">Set indentation size</span></span>

<span data-ttu-id="ff215-192">A personalização do tamanho do recuo da resposta só é compatível com JSON.</span><span class="sxs-lookup"><span data-stu-id="ff215-192">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="ff215-193">O tamanho padrão é dois espaços.</span><span class="sxs-lookup"><span data-stu-id="ff215-193">The default size is two spaces.</span></span> <span data-ttu-id="ff215-194">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-194">For example:</span></span>

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

<span data-ttu-id="ff215-195">Para alterar o tamanho padrão, defina a chave `formatting.json.indentSize`.</span><span class="sxs-lookup"><span data-stu-id="ff215-195">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="ff215-196">Por exemplo, para sempre usar quatro espaços:</span><span class="sxs-lookup"><span data-stu-id="ff215-196">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="ff215-197">As respostas subsequentes respeitarão a configuração de quatro espaços:</span><span class="sxs-lookup"><span data-stu-id="ff215-197">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="ff215-198">Definir o editor de texto padrão</span><span class="sxs-lookup"><span data-stu-id="ff215-198">Set the default text editor</span></span>

<span data-ttu-id="ff215-199">Por padrão, o HTTP REPL não tem um editor de texto configurado para uso.</span><span class="sxs-lookup"><span data-stu-id="ff215-199">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="ff215-200">Para testar os métodos de API Web que exigem o corpo da solicitação HTTP, é preciso definir um editor de texto padrão.</span><span class="sxs-lookup"><span data-stu-id="ff215-200">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="ff215-201">A ferramenta HTTP REPL inicia o editor de texto configurado somente para escrever o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="ff215-201">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="ff215-202">Execute o seguinte comando para definir o editor de texto preferido como padrão:</span><span class="sxs-lookup"><span data-stu-id="ff215-202">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="ff215-203">No comando anterior, `<EXECUTABLE>` é o caminho completo para o arquivo executável do editor de texto.</span><span class="sxs-lookup"><span data-stu-id="ff215-203">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="ff215-204">Por exemplo, execute o seguinte comando para definir o Visual Studio Code como o editor de texto padrão:</span><span class="sxs-lookup"><span data-stu-id="ff215-204">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="ff215-205">Linux</span><span class="sxs-lookup"><span data-stu-id="ff215-205">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="ff215-206">macOS</span><span class="sxs-lookup"><span data-stu-id="ff215-206">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="ff215-207">Windows</span><span class="sxs-lookup"><span data-stu-id="ff215-207">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="ff215-208">Para iniciar o editor de texto padrão com argumentos específicos da CLI, defina a chave `editor.command.default.arguments`.</span><span class="sxs-lookup"><span data-stu-id="ff215-208">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="ff215-209">Por exemplo, imagine que o Visual Studio Code é o editor de texto padrão e que você quer que o HTTP REPL sempre o abra em uma nova sessão com as extensões desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="ff215-209">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="ff215-210">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ff215-210">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="ff215-211">Definir os caminhos de pesquisa do Swagger</span><span class="sxs-lookup"><span data-stu-id="ff215-211">Set the Swagger search paths</span></span>

<span data-ttu-id="ff215-212">Por padrão, HTTP REPL tem um conjunto de caminhos relativos que ele usa para localizar o documento do Swagger ao executar o comando `connect` sem a opção `--swagger`.</span><span class="sxs-lookup"><span data-stu-id="ff215-212">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="ff215-213">Esses caminhos relativos são combinados com os caminhos raiz e base especificados no comando `connect`.</span><span class="sxs-lookup"><span data-stu-id="ff215-213">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="ff215-214">Os caminhos relativos padrão são:</span><span class="sxs-lookup"><span data-stu-id="ff215-214">The default relative paths are:</span></span>

- <span data-ttu-id="ff215-215">*swagger.json*</span><span class="sxs-lookup"><span data-stu-id="ff215-215">*swagger.json*</span></span>
- <span data-ttu-id="ff215-216">*swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="ff215-216">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="ff215-217">*/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="ff215-217">*/swagger.json*</span></span>
- <span data-ttu-id="ff215-218">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="ff215-218">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="ff215-219">Para usar um conjunto diferente de caminhos de pesquisa em seu ambiente, defina a preferência `swagger.searchPaths`.</span><span class="sxs-lookup"><span data-stu-id="ff215-219">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="ff215-220">O valor precisa ser uma lista delimitada por pipes de caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="ff215-220">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="ff215-221">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-221">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json
```

## <a name="test-http-get-requests"></a><span data-ttu-id="ff215-222">Testar solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="ff215-222">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="ff215-223">Sinopse</span><span class="sxs-lookup"><span data-stu-id="ff215-223">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="ff215-224">Arguments</span><span class="sxs-lookup"><span data-stu-id="ff215-224">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="ff215-225">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="ff215-225">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="ff215-226">Opções</span><span class="sxs-lookup"><span data-stu-id="ff215-226">Options</span></span>

<span data-ttu-id="ff215-227">As opções a seguir estão disponíveis para o comando `get`:</span><span class="sxs-lookup"><span data-stu-id="ff215-227">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="ff215-228">Exemplo</span><span class="sxs-lookup"><span data-stu-id="ff215-228">Example</span></span>

<span data-ttu-id="ff215-229">Para emitir uma solicitação HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="ff215-229">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="ff215-230">Execute o comando `get` em um ponto de extremidade compatível:</span><span class="sxs-lookup"><span data-stu-id="ff215-230">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="ff215-231">O comando anterior exibirá o seguinte formato de saída:</span><span class="sxs-lookup"><span data-stu-id="ff215-231">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="ff215-232">Recupere um registro específico passando um parâmetro para o comando `get`:</span><span class="sxs-lookup"><span data-stu-id="ff215-232">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="ff215-233">O comando anterior exibirá o seguinte formato de saída:</span><span class="sxs-lookup"><span data-stu-id="ff215-233">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="ff215-234">Testar solicitações HTTP POST</span><span class="sxs-lookup"><span data-stu-id="ff215-234">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="ff215-235">Sinopse</span><span class="sxs-lookup"><span data-stu-id="ff215-235">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="ff215-236">Arguments</span><span class="sxs-lookup"><span data-stu-id="ff215-236">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="ff215-237">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="ff215-237">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="ff215-238">Opções</span><span class="sxs-lookup"><span data-stu-id="ff215-238">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="ff215-239">Exemplo</span><span class="sxs-lookup"><span data-stu-id="ff215-239">Example</span></span>

<span data-ttu-id="ff215-240">Para emitir uma solicitação HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="ff215-240">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="ff215-241">Execute o comando `post` em um ponto de extremidade compatível:</span><span class="sxs-lookup"><span data-stu-id="ff215-241">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="ff215-242">No comando anterior, o cabeçalho da solicitação HTTP `Content-Type` está configurado para indicar um tipo de mídia de corpo da solicitação do JSON.</span><span class="sxs-lookup"><span data-stu-id="ff215-242">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="ff215-243">O editor de texto padrão abrirá um arquivo *.tmp* com um modelo JSON que representa o corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="ff215-243">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="ff215-244">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-244">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="ff215-245">Para definir o editor de texto padrão, confira a seção [Definir o editor de texto padrão](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="ff215-245">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="ff215-246">Modifique o modelo JSON para satisfazer os requisitos de validação de modelo:</span><span class="sxs-lookup"><span data-stu-id="ff215-246">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="ff215-247">Salve o arquivo *.tmp* e feche o editor de texto.</span><span class="sxs-lookup"><span data-stu-id="ff215-247">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="ff215-248">A seguinte saída será exibida no shell de comando:</span><span class="sxs-lookup"><span data-stu-id="ff215-248">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="ff215-249">Testar solicitações HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="ff215-249">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="ff215-250">Sinopse</span><span class="sxs-lookup"><span data-stu-id="ff215-250">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="ff215-251">Arguments</span><span class="sxs-lookup"><span data-stu-id="ff215-251">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="ff215-252">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="ff215-252">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="ff215-253">Opções</span><span class="sxs-lookup"><span data-stu-id="ff215-253">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="ff215-254">Exemplo</span><span class="sxs-lookup"><span data-stu-id="ff215-254">Example</span></span>

<span data-ttu-id="ff215-255">Para emitir uma solicitação HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="ff215-255">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="ff215-256">*Opcional*: Execute o comando `get` para exibir os dados antes de modificá-los:</span><span class="sxs-lookup"><span data-stu-id="ff215-256">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. Run the `put` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="ff215-257">No comando anterior, o cabeçalho da solicitação HTTP `Content-Type` está configurado para indicar um tipo de mídia de corpo da solicitação do JSON.</span><span class="sxs-lookup"><span data-stu-id="ff215-257">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="ff215-258">O editor de texto padrão abrirá um arquivo *.tmp* com um modelo JSON que representa o corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="ff215-258">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="ff215-259">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-259">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="ff215-260">Para definir o editor de texto padrão, confira a seção [Definir o editor de texto padrão](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="ff215-260">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="ff215-261">Modifique o modelo JSON para satisfazer os requisitos de validação de modelo:</span><span class="sxs-lookup"><span data-stu-id="ff215-261">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="ff215-262">Salve o arquivo *.tmp* e feche o editor de texto.</span><span class="sxs-lookup"><span data-stu-id="ff215-262">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="ff215-263">A seguinte saída será exibida no shell de comando:</span><span class="sxs-lookup"><span data-stu-id="ff215-263">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="ff215-264">*Opcional*: Emita um comando `get` para ver as modificações.</span><span class="sxs-lookup"><span data-stu-id="ff215-264">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="ff215-265">Por exemplo, se você digitou "Cereja" no editor de texto, um `get` retornará o seguinte:</span><span class="sxs-lookup"><span data-stu-id="ff215-265">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="ff215-266">Testar solicitações HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="ff215-266">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="ff215-267">Sinopse</span><span class="sxs-lookup"><span data-stu-id="ff215-267">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="ff215-268">Arguments</span><span class="sxs-lookup"><span data-stu-id="ff215-268">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="ff215-269">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="ff215-269">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="ff215-270">Opções</span><span class="sxs-lookup"><span data-stu-id="ff215-270">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="ff215-271">Exemplo</span><span class="sxs-lookup"><span data-stu-id="ff215-271">Example</span></span>

<span data-ttu-id="ff215-272">Para emitir uma solicitação HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="ff215-272">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="ff215-273">*Opcional*: Execute o comando `get` para exibir os dados antes de modificá-los:</span><span class="sxs-lookup"><span data-stu-id="ff215-273">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. Run the `delete` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="ff215-274">O comando anterior exibirá o seguinte formato de saída:</span><span class="sxs-lookup"><span data-stu-id="ff215-274">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="ff215-275">*Opcional*: Emita um comando `get` para ver as modificações.</span><span class="sxs-lookup"><span data-stu-id="ff215-275">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="ff215-276">Neste exemplo, o `get` retornará o seguinte:</span><span class="sxs-lookup"><span data-stu-id="ff215-276">In this example, a `get` returns the following:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="ff215-277">Testar solicitações HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="ff215-277">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="ff215-278">Sinopse</span><span class="sxs-lookup"><span data-stu-id="ff215-278">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="ff215-279">Arguments</span><span class="sxs-lookup"><span data-stu-id="ff215-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="ff215-280">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="ff215-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="ff215-281">Opções</span><span class="sxs-lookup"><span data-stu-id="ff215-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="ff215-282">Testar solicitações HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="ff215-282">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="ff215-283">Sinopse</span><span class="sxs-lookup"><span data-stu-id="ff215-283">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="ff215-284">Arguments</span><span class="sxs-lookup"><span data-stu-id="ff215-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="ff215-285">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="ff215-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="ff215-286">Opções</span><span class="sxs-lookup"><span data-stu-id="ff215-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="ff215-287">Testar solicitações HTTP OPTIONS</span><span class="sxs-lookup"><span data-stu-id="ff215-287">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="ff215-288">Sinopse</span><span class="sxs-lookup"><span data-stu-id="ff215-288">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="ff215-289">Arguments</span><span class="sxs-lookup"><span data-stu-id="ff215-289">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="ff215-290">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="ff215-290">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="ff215-291">Opções</span><span class="sxs-lookup"><span data-stu-id="ff215-291">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="ff215-292">Configurar cabeçalhos de solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="ff215-292">Set HTTP request headers</span></span>

<span data-ttu-id="ff215-293">Para configurar um cabeçalho de solicitação HTTP, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ff215-293">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="ff215-294">Configure embutido com a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="ff215-294">Set inline with the HTTP request.</span></span> <span data-ttu-id="ff215-295">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-295">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="ff215-296">Com a abordagem anterior, cada cabeçalho de solicitação HTTP diferente exige sua própria opção `-h`.</span><span class="sxs-lookup"><span data-stu-id="ff215-296">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="ff215-297">Configure antes de enviar a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="ff215-297">Set before sending the HTTP request.</span></span> <span data-ttu-id="ff215-298">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-298">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="ff215-299">Ao configurar o cabeçalho antes de enviar a solicitação, ele permanecerá configurado durante toda a sessão do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="ff215-299">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="ff215-300">Para limpar o cabeçalho, forneça um valor vazio.</span><span class="sxs-lookup"><span data-stu-id="ff215-300">To clear the header, provide an empty value.</span></span> <span data-ttu-id="ff215-301">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-301">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="ff215-302">Alternar exibição da solicitação HTTP</span><span class="sxs-lookup"><span data-stu-id="ff215-302">Toggle HTTP request display</span></span>

<span data-ttu-id="ff215-303">Por padrão, a exibição da solicitação HTTP que está sendo enviada é suprimida.</span><span class="sxs-lookup"><span data-stu-id="ff215-303">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="ff215-304">É possível alterar a configuração correspondente durante a sessão do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="ff215-304">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="ff215-305">Habilitar a exibição da solicitação</span><span class="sxs-lookup"><span data-stu-id="ff215-305">Enable request display</span></span>

<span data-ttu-id="ff215-306">Exiba a solicitação HTTP que está sendo enviada executando o comando `echo on`.</span><span class="sxs-lookup"><span data-stu-id="ff215-306">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="ff215-307">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-307">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="ff215-308">As solicitações HTTP subsequentes na sessão atual exibem os cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="ff215-308">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="ff215-309">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-309">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="ff215-310">Desabilitar a exibição da solicitação</span><span class="sxs-lookup"><span data-stu-id="ff215-310">Disable request display</span></span>

<span data-ttu-id="ff215-311">Suprima a exibição da solicitação HTTP que está sendo enviada executando o comando `echo off`.</span><span class="sxs-lookup"><span data-stu-id="ff215-311">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="ff215-312">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-312">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="ff215-313">Executar um script</span><span class="sxs-lookup"><span data-stu-id="ff215-313">Run a script</span></span>

<span data-ttu-id="ff215-314">Se você executar com frequência o mesmo conjunto de comandos HTTP REPL, considere armazená-los em um arquivo de texto.</span><span class="sxs-lookup"><span data-stu-id="ff215-314">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="ff215-315">Os comandos no arquivo assumem a mesma forma que os executados manualmente na linha de comando.</span><span class="sxs-lookup"><span data-stu-id="ff215-315">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="ff215-316">Os comandos podem ser executados em um modo em lote usando o comando `run`.</span><span class="sxs-lookup"><span data-stu-id="ff215-316">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="ff215-317">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-317">For example:</span></span>

1. <span data-ttu-id="ff215-318">Crie um arquivo de texto com um conjunto de comandos delimitados por nova linha.</span><span class="sxs-lookup"><span data-stu-id="ff215-318">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="ff215-319">Para ilustrar, considere um arquivo *people-script.txt* com os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="ff215-319">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="ff215-320">Execute o comando `run`, passando o caminho do arquivo de texto.</span><span class="sxs-lookup"><span data-stu-id="ff215-320">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="ff215-321">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="ff215-321">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="ff215-322">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="ff215-322">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="ff215-323">Limpar a saída</span><span class="sxs-lookup"><span data-stu-id="ff215-323">Clear the output</span></span>

<span data-ttu-id="ff215-324">Para remover toda a saída gravada no shell de comando pela ferramenta HTTP REPL, execute os comandos `clear` ou `cls`.</span><span class="sxs-lookup"><span data-stu-id="ff215-324">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="ff215-325">Para ilustrar, imagine que o shell de comando contém a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="ff215-325">To illustrate, imagine the command shell contains the following output:</span></span>

```console
dotnet httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="ff215-326">Execute o comando a seguir para limpar a saída:</span><span class="sxs-lookup"><span data-stu-id="ff215-326">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="ff215-327">Após a execução o comando anterior, o shell de comando conterá somente a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="ff215-327">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="ff215-328">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ff215-328">Additional resources</span></span>

* [<span data-ttu-id="ff215-329">Solicitações da API REST</span><span class="sxs-lookup"><span data-stu-id="ff215-329">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="ff215-330">Repositório do GitHub do HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="ff215-330">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
