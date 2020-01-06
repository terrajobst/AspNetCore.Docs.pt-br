---
title: Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core
author: rick-anderson
description: Saiba como armazenar e recuperar informações confidenciais como segredos do aplicativo durante o desenvolvimento de um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: security/app-secrets
ms.openlocfilehash: 9b36ae64fbe277cd81ed22ba7b21b0a035082dbd
ms.sourcegitcommit: c815a9465e7b1bab44ce1643ec345b33e6cf1598
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75606786"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="8e3bf-103">Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e3bf-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

<span data-ttu-id="8e3bf-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="8e3bf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="8e3bf-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8e3bf-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8e3bf-106">Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo de ASP.NET Core em um computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="8e3bf-107">Nunca armazene senhas ou outros dados confidenciais no código-fonte.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="8e3bf-108">Os segredos de produção não devem ser usados para desenvolvimento ou teste.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="8e3bf-109">Os segredos não devem ser implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="8e3bf-110">Em vez disso, os segredos devem ser disponibilizados no ambiente de produção por meio de um meio controlado, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger os segredos de produção e de teste do Azure com o [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="8e3bf-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="8e3bf-111">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="8e3bf-111">Environment variables</span></span>

<span data-ttu-id="8e3bf-112">As variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou nos arquivos de configuração locais.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="8e3bf-113">Variáveis de ambiente substituem valores de configuração para todas as fontes de configuração especificadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="8e3bf-114">Configure a leitura de valores de variáveis de ambiente chamando <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> no construtor de `Startup`:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-114">Configure the reading of environment variable values by calling <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

<span data-ttu-id="8e3bf-115">Considere um aplicativo Web ASP.NET Core no qual a segurança **das contas de usuário individuais** está habilitada.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="8e3bf-116">Uma cadeia de conexão de banco de dados padrão é incluída no arquivo *appSettings. JSON* do projeto com a chave `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="8e3bf-117">A cadeia de conexão padrão é para o LocalDB, que é executado no modo de usuário e não requer uma senha.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="8e3bf-118">Durante a implantação do aplicativo, o valor da chave de `DefaultConnection` pode ser substituído por um valor de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="8e3bf-119">A variável de ambiente pode armazenar a cadeia de conexão completa com credenciais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="8e3bf-120">Variáveis de ambiente geralmente são armazenadas em texto simples e não criptografado.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="8e3bf-121">Se o computador ou o processo estiver comprometido, as variáveis de ambiente poderão ser acessadas por partes não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="8e3bf-122">Outras medidas para evitar a divulgação de segredos do usuário podem ser necessárias.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="8e3bf-123">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="8e3bf-123">Secret Manager</span></span>

<span data-ttu-id="8e3bf-124">A ferramenta Gerenciador de segredo armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="8e3bf-125">Nesse contexto, um elemento de dados confidenciais é um segredo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="8e3bf-126">Os segredos do aplicativo são armazenados em um local separado da árvore do projeto.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="8e3bf-127">Os segredos do aplicativo são associados a um projeto específico ou compartilhados entre vários projetos.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="8e3bf-128">Os segredos do aplicativo não são verificados no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="8e3bf-129">A ferramenta Gerenciador de segredo não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="8e3bf-130">É apenas para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-130">It's for development purposes only.</span></span> <span data-ttu-id="8e3bf-131">As chaves e os valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="8e3bf-132">Como funciona a ferramenta Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="8e3bf-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="8e3bf-133">A ferramenta Gerenciador de segredo abstrai os detalhes da implementação, como onde e como os valores são armazenados.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-133">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="8e3bf-134">Você pode usar a ferramenta sem conhecer esses detalhes de implementação.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="8e3bf-135">Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema no computador local:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-135">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="8e3bf-136">Windows</span><span class="sxs-lookup"><span data-stu-id="8e3bf-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="8e3bf-137">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="8e3bf-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="8e3bf-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="8e3bf-139">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="8e3bf-140">Nos caminhos de arquivo anteriores, substitua `<user_secrets_id>` pelo valor de `UserSecretsId` especificado no arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="8e3bf-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="8e3bf-141">Não escreva o código que depende do local ou do formato dos dados salvos com a ferramenta Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="8e3bf-142">Esses detalhes de implementação podem ser alterados.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-142">These implementation details may change.</span></span> <span data-ttu-id="8e3bf-143">Por exemplo, os valores secretos não são criptografados, mas podem estar no futuro.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a><span data-ttu-id="8e3bf-144">Instalar a ferramenta Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="8e3bf-144">Install the Secret Manager tool</span></span>

<span data-ttu-id="8e3bf-145">A ferramenta Gerenciador de segredo é agrupada com o CLI do .NET Core no SDK do .NET Core 2.1.300 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-145">The Secret Manager tool is bundled with the .NET Core CLI in .NET Core SDK 2.1.300 or later.</span></span> <span data-ttu-id="8e3bf-146">Para SDK do .NET Core versões anteriores ao 2.1.300, a instalação da ferramenta é necessária.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-146">For .NET Core SDK versions before 2.1.300, tool installation is necessary.</span></span>

> [!TIP]
> <span data-ttu-id="8e3bf-147">Execute `dotnet --version` de um shell de comando para ver o número da versão do SDK do .NET Core instalado.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-147">Run `dotnet --version` from a command shell to see the installed .NET Core SDK version number.</span></span>

<span data-ttu-id="8e3bf-148">Um aviso será exibido se o SDK do .NET Core que está sendo usado incluir a ferramenta:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-148">A warning is displayed if the .NET Core SDK being used includes the tool:</span></span>

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

<span data-ttu-id="8e3bf-149">Instale o pacote NuGet [Microsoft. Extensions. secretomanager. Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) em seu projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-149">Install the [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet package in your ASP.NET Core project.</span></span> <span data-ttu-id="8e3bf-150">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-150">For example:</span></span>

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

<span data-ttu-id="8e3bf-151">Execute o seguinte comando em um shell de comando para validar a instalação da ferramenta:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-151">Execute the following command in a command shell to validate the tool installation:</span></span>

```dotnetcli
dotnet user-secrets -h
```

<span data-ttu-id="8e3bf-152">A ferramenta Gerenciador de segredo exibe o uso de exemplo, as opções e a ajuda de comando:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-152">The Secret Manager tool displays sample usage, options, and command help:</span></span>

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> <span data-ttu-id="8e3bf-153">Você deve estar no mesmo diretório que o arquivo *. csproj* para executar ferramentas definidas nos elementos de `DotNetCliToolReference` do arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="8e3bf-153">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` elements.</span></span>

::: moniker-end

## <a name="enable-secret-storage"></a><span data-ttu-id="8e3bf-154">Habilitar o armazenamento secreto</span><span class="sxs-lookup"><span data-stu-id="8e3bf-154">Enable secret storage</span></span>

<span data-ttu-id="8e3bf-155">A ferramenta Gerenciador de segredo opera em definições de configuração específicas do projeto armazenadas no seu perfil de usuário.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-155">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e3bf-156">A ferramenta Gerenciador de segredo inclui um comando `init` no SDK do .NET Core 3.0.100 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-156">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="8e3bf-157">Para usar os segredos do usuário, execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-157">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="8e3bf-158">O comando anterior adiciona um elemento `UserSecretsId` dentro de uma `PropertyGroup` do arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="8e3bf-158">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="8e3bf-159">Por padrão, o texto interno de `UserSecretsId` é um GUID.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-159">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="8e3bf-160">O texto interno é arbitrário, mas é exclusivo para o projeto.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-160">The inner text is arbitrary, but is unique to the project.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8e3bf-161">Para usar os segredos do usuário, defina um elemento `UserSecretsId` dentro de uma `PropertyGroup` do arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="8e3bf-161">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="8e3bf-162">O texto interno de `UserSecretsId` é arbitrário, mas é exclusivo para o projeto.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-162">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="8e3bf-163">Os desenvolvedores normalmente geram um GUID para o `UserSecretsId`.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-163">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> <span data-ttu-id="8e3bf-164">No Visual Studio, clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **gerenciar segredos do usuário** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-164">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="8e3bf-165">Esse gesto adiciona um elemento `UserSecretsId`, populado com um GUID, ao arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="8e3bf-165">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="8e3bf-166">Definir um segredo</span><span class="sxs-lookup"><span data-stu-id="8e3bf-166">Set a secret</span></span>

<span data-ttu-id="8e3bf-167">Defina um segredo de aplicativo que consiste em uma chave e seu valor.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-167">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="8e3bf-168">O segredo é associado ao valor de `UserSecretsId` do projeto.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-168">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="8e3bf-169">Por exemplo, execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-169">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="8e3bf-170">No exemplo anterior, os dois pontos indica que `Movies` é um literal de objeto com uma propriedade `ServiceApiKey`.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-170">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="8e3bf-171">A ferramenta Gerenciador de segredo também pode ser usada em outros diretórios.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-171">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="8e3bf-172">Use a opção `--project` para fornecer o caminho do sistema de arquivos no qual o arquivo *. csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-172">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="8e3bf-173">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-173">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="8e3bf-174">Nivelamento de estrutura JSON no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e3bf-174">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="8e3bf-175">O gesto **gerenciar segredos do usuário** do Visual Studio abre um arquivo *segredos. JSON* no editor de texto.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-175">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="8e3bf-176">Substitua o conteúdo de *Secrets. JSON* pelos pares de chave-valor a serem armazenados.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-176">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="8e3bf-177">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-177">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="8e3bf-178">A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-178">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="8e3bf-179">Por exemplo, executar `dotnet user-secrets remove "Movies:ConnectionString"` recolhe o literal do objeto `Movies`.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-179">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="8e3bf-180">O arquivo modificado é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-180">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="8e3bf-181">Definir vários segredos</span><span class="sxs-lookup"><span data-stu-id="8e3bf-181">Set multiple secrets</span></span>

<span data-ttu-id="8e3bf-182">Um lote de segredos pode ser definido por meio de tubulação JSON para o comando `set`.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-182">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="8e3bf-183">No exemplo a seguir, o conteúdo do arquivo *Input. JSON* é canalizado para o comando `set`.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-183">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="8e3bf-184">Windows</span><span class="sxs-lookup"><span data-stu-id="8e3bf-184">Windows</span></span>](#tab/windows)

<span data-ttu-id="8e3bf-185">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-185">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="8e3bf-186">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="8e3bf-186">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="8e3bf-187">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-187">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="8e3bf-188">Acessar um segredo</span><span class="sxs-lookup"><span data-stu-id="8e3bf-188">Access a secret</span></span>

<span data-ttu-id="8e3bf-189">A [API de configuração do ASP.NET Core](xref:fundamentals/configuration/index) fornece acesso aos segredos do Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-189">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="8e3bf-190">Se seu projeto tiver como destino .NET Framework, instale o pacote NuGet [Microsoft. Extensions. Configuration. Usersecretos](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="8e3bf-190">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8e3bf-191">No ASP.NET Core 2,0 ou posterior, a fonte de configuração de segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> para inicializar uma nova instância do host com padrões pré-configurados.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-191">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="8e3bf-192">`CreateDefaultBuilder` chama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando o <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> é <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-192">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8e3bf-193">Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de configuração de segredos do usuário explicitamente chamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> no Construtor `Startup`.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-193">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="8e3bf-194">Chame `AddUserSecrets` somente quando o aplicativo for executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-194">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="8e3bf-195">Instale o pacote NuGet [Microsoft. Extensions. Configuration. Usersecretos](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="8e3bf-195">Install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="8e3bf-196">Adicione a fonte de configuração de segredos do usuário com uma chamada para <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> no construtor de `Startup`:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-196">Add the user secrets configuration source with a call to <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

<span data-ttu-id="8e3bf-197">Os segredos do usuário podem ser recuperados por meio da API `Configuration`:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-197">User secrets can be retrieved via the `Configuration` API:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="8e3bf-198">Mapear segredos para um POCO</span><span class="sxs-lookup"><span data-stu-id="8e3bf-198">Map secrets to a POCO</span></span>

<span data-ttu-id="8e3bf-199">O mapeamento de um literal de objeto inteiro para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-199">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8e3bf-200">Para mapear os segredos anteriores para um POCO, use o recurso de [Associação de gráfico de objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API do `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-200">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="8e3bf-201">O código a seguir é associado a uma `MovieSettings` personalizada POCO e acessa o valor da propriedade `ServiceApiKey`:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-201">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

<span data-ttu-id="8e3bf-202">Os segredos de `Movies:ConnectionString` e `Movies:ServiceApiKey` são mapeados para as respectivas propriedades no `MovieSettings`:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-202">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="8e3bf-203">Substituição de cadeia de caracteres com segredos</span><span class="sxs-lookup"><span data-stu-id="8e3bf-203">String replacement with secrets</span></span>

<span data-ttu-id="8e3bf-204">O armazenamento de senhas em texto sem formatação não é seguro.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-204">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="8e3bf-205">Por exemplo, uma cadeia de conexão de banco de dados armazenada em *appSettings. JSON* pode incluir uma senha para o usuário especificado:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-205">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="8e3bf-206">Uma abordagem mais segura é armazenar a senha como um segredo.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-206">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="8e3bf-207">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-207">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="8e3bf-208">Remova o `Password` par chave-valor da cadeia de conexão em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-208">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="8e3bf-209">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-209">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="8e3bf-210">O valor do segredo pode ser definido em uma propriedade <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> do objeto de <xref:System.Data.SqlClient.SqlConnectionStringBuilder> para concluir a cadeia de conexão:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-210">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a><span data-ttu-id="8e3bf-211">Listar os segredos</span><span class="sxs-lookup"><span data-stu-id="8e3bf-211">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8e3bf-212">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-212">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="8e3bf-213">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-213">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="8e3bf-214">No exemplo anterior, dois-pontos nos nomes de chave denota a hierarquia de objeto dentro de *segredos. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-214">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="8e3bf-215">Remover um único segredo</span><span class="sxs-lookup"><span data-stu-id="8e3bf-215">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8e3bf-216">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-216">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="8e3bf-217">O arquivo *segredos. JSON* do aplicativo foi modificado para remover o par chave-valor associado à chave de `MoviesConnectionString`:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-217">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="8e3bf-218">A execução de `dotnet user-secrets list` exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-218">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="8e3bf-219">Remover todos os segredos</span><span class="sxs-lookup"><span data-stu-id="8e3bf-219">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8e3bf-220">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-220">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="8e3bf-221">Todos os segredos do usuário para o aplicativo foram excluídos do arquivo *segredos. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8e3bf-221">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="8e3bf-222">A execução de `dotnet user-secrets list` exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="8e3bf-222">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="8e3bf-223">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8e3bf-223">Additional resources</span></span>

* <span data-ttu-id="8e3bf-224">Consulte [esse problema](https://github.com/aspnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerenciador de segredo do IIS.</span><span class="sxs-lookup"><span data-stu-id="8e3bf-224">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
