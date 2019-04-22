---
title: Usar vários ambientes no ASP.NET Core
author: rick-anderson
description: Aprenda a controlar o comportamento do aplicativo em vários ambientes em aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/10/2019
uid: fundamentals/environments
ms.openlocfilehash: f14c29030d96b530ea4af054cfa195d44db43679
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59516346"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="6ba35-103">Usar vários ambientes no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ba35-103">Use multiple environments in ASP.NET Core</span></span>

<span data-ttu-id="6ba35-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6ba35-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6ba35-105">O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de tempo de execução usando uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="6ba35-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="6ba35-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6ba35-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="6ba35-107">Ambientes</span><span class="sxs-lookup"><span data-stu-id="6ba35-107">Environments</span></span>

<span data-ttu-id="6ba35-108">O ASP.NET Core lê a variável de ambiente `ASPNETCORE_ENVIRONMENT` na inicialização do aplicativo e armazena o valor em [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).</span><span class="sxs-lookup"><span data-stu-id="6ba35-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).</span></span> <span data-ttu-id="6ba35-109">Você pode definir `ASPNETCORE_ENVIRONMENT` como qualquer valor, mas há suporte para [três valores](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) na estrutura: [Desenvolvimento](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Preparo](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) e [Produção](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span><span class="sxs-lookup"><span data-stu-id="6ba35-109">You can set `ASPNETCORE_ENVIRONMENT` to any value, but [three values](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) are supported by the framework: [Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging), and [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span></span> <span data-ttu-id="6ba35-110">Se `ASPNETCORE_ENVIRONMENT` não estiver definido, o padrão será `Production`.</span><span class="sxs-lookup"><span data-stu-id="6ba35-110">If `ASPNETCORE_ENVIRONMENT` isn't set, it defaults to `Production`.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="6ba35-111">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="6ba35-111">The preceding code:</span></span>

* <span data-ttu-id="6ba35-112">Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.</span><span class="sxs-lookup"><span data-stu-id="6ba35-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="6ba35-113">Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="6ba35-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="6ba35-114">O [Auxiliar de Marcação de Ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa o valor de `IHostingEnvironment.EnvironmentName` para incluir ou excluir a marcação no elemento:</span><span class="sxs-lookup"><span data-stu-id="6ba35-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="6ba35-115">No Windows e no macOS, valores e variáveis de ambiente não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6ba35-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="6ba35-116">Valores e variáveis de ambiente do Linux **diferenciam maiúsculas de minúsculas** por padrão.</span><span class="sxs-lookup"><span data-stu-id="6ba35-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="6ba35-117">Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="6ba35-117">Development</span></span>

<span data-ttu-id="6ba35-118">O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção.</span><span class="sxs-lookup"><span data-stu-id="6ba35-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="6ba35-119">Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="6ba35-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="6ba35-120">O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto.</span><span class="sxs-lookup"><span data-stu-id="6ba35-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="6ba35-121">Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="6ba35-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="6ba35-122">O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6ba35-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="6ba35-123">A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores.</span><span class="sxs-lookup"><span data-stu-id="6ba35-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="6ba35-124">Use um ponto e vírgula entre as URLs na lista:</span><span class="sxs-lookup"><span data-stu-id="6ba35-124">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="6ba35-125">Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado.</span><span class="sxs-lookup"><span data-stu-id="6ba35-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="6ba35-126">O valor de `commandName` especifica o servidor Web a ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="6ba35-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="6ba35-127">`commandName` pode ser qualquer um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="6ba35-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="6ba35-128">`Project` (que inicia o Kestrel)</span><span class="sxs-lookup"><span data-stu-id="6ba35-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="6ba35-129">Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="6ba35-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="6ba35-130">*launchSettings.json* é lido, se está disponível.</span><span class="sxs-lookup"><span data-stu-id="6ba35-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="6ba35-131">As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="6ba35-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="6ba35-132">O ambiente de hospedagem é exibido.</span><span class="sxs-lookup"><span data-stu-id="6ba35-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="6ba35-133">A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="6ba35-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="6ba35-134">A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6ba35-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

<span data-ttu-id="6ba35-136">As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="6ba35-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="6ba35-137">O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.</span><span class="sxs-lookup"><span data-stu-id="6ba35-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="6ba35-138">*launchSettings.json* não deve armazenar segredos.</span><span class="sxs-lookup"><span data-stu-id="6ba35-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="6ba35-139">A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="6ba35-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="6ba35-140">Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="6ba35-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="6ba35-141">O exemplo a seguir define o ambiente como `Development`:</span><span class="sxs-lookup"><span data-stu-id="6ba35-141">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="6ba35-142">Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6ba35-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="6ba35-143">Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="6ba35-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="6ba35-144">Produção</span><span class="sxs-lookup"><span data-stu-id="6ba35-144">Production</span></span>

<span data-ttu-id="6ba35-145">O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ba35-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="6ba35-146">Algumas configurações comuns que são diferentes do desenvolvimento incluem:</span><span class="sxs-lookup"><span data-stu-id="6ba35-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="6ba35-147">Cache.</span><span class="sxs-lookup"><span data-stu-id="6ba35-147">Caching.</span></span>
* <span data-ttu-id="6ba35-148">Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="6ba35-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="6ba35-149">Páginas de erro de diagnóstico desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="6ba35-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="6ba35-150">Páginas de erro amigáveis habilitadas.</span><span class="sxs-lookup"><span data-stu-id="6ba35-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="6ba35-151">Log de produção e monitoramento habilitados.</span><span class="sxs-lookup"><span data-stu-id="6ba35-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="6ba35-152">Por exemplo, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="6ba35-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="6ba35-153">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="6ba35-153">Set the environment</span></span>

<span data-ttu-id="6ba35-154">Geralmente, é útil definir um ambiente específico para teste.</span><span class="sxs-lookup"><span data-stu-id="6ba35-154">It's often useful to set a specific environment for testing.</span></span> <span data-ttu-id="6ba35-155">Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração.</span><span class="sxs-lookup"><span data-stu-id="6ba35-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="6ba35-156">O método para configurar o ambiente depende do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="6ba35-156">The method for setting the environment depends on the operating system.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="6ba35-157">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="6ba35-157">Azure App Service</span></span>

<span data-ttu-id="6ba35-158">Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="6ba35-158">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="6ba35-159">Selecione o aplicativo na folha **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="6ba35-159">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="6ba35-160">No grupo **CONFIGURAÇÕES**, selecione a folha **Configurações do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6ba35-160">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="6ba35-161">Na área **Configurações do aplicativo**, selecione **Adicionar nova configuração**.</span><span class="sxs-lookup"><span data-stu-id="6ba35-161">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="6ba35-162">Para **Inserir um nome**, forneça `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="6ba35-162">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="6ba35-163">Para **Inserir um valor**, fornecer o ambiente (por exemplo, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="6ba35-163">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="6ba35-164">Marque a caixa de seleção **Configuração do Slot** se desejar que a configuração do ambiente permaneça no slot atual quando os slots de implantação forem trocados.</span><span class="sxs-lookup"><span data-stu-id="6ba35-164">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="6ba35-165">Para obter mais informações, confira [Documentação do Azure: que configurações são trocadas?](/azure/app-service/web-sites-staged-publishing).</span><span class="sxs-lookup"><span data-stu-id="6ba35-165">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="6ba35-166">Selecione **Salvar** na parte superior da folha.</span><span class="sxs-lookup"><span data-stu-id="6ba35-166">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="6ba35-167">O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="6ba35-167">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="6ba35-168">Windows</span><span class="sxs-lookup"><span data-stu-id="6ba35-168">Windows</span></span>

<span data-ttu-id="6ba35-169">Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:</span><span class="sxs-lookup"><span data-stu-id="6ba35-169">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="6ba35-170">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="6ba35-170">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="6ba35-171">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="6ba35-171">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="6ba35-172">Esses comandos somente têm efeito para a janela atual.</span><span class="sxs-lookup"><span data-stu-id="6ba35-172">These commands only take effect for the current window.</span></span> <span data-ttu-id="6ba35-173">Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.</span><span class="sxs-lookup"><span data-stu-id="6ba35-173">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="6ba35-174">Para definir o valor globalmente no Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="6ba35-174">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="6ba35-175">Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="6ba35-175">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="6ba35-178">Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="6ba35-178">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="6ba35-179">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="6ba35-179">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="6ba35-180">O comutador `/M` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="6ba35-180">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="6ba35-181">Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="6ba35-181">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="6ba35-182">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="6ba35-182">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="6ba35-183">O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="6ba35-183">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="6ba35-184">Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="6ba35-184">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="6ba35-185">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.</span><span class="sxs-lookup"><span data-stu-id="6ba35-185">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="6ba35-186">**web.config**</span><span class="sxs-lookup"><span data-stu-id="6ba35-186">**web.config**</span></span>

<span data-ttu-id="6ba35-187">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="6ba35-187">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6ba35-188">**Arquivo de projeto ou perfil de publicação**</span><span class="sxs-lookup"><span data-stu-id="6ba35-188">**Project file or publish profile**</span></span>

<span data-ttu-id="6ba35-189">**Para implantações do Windows IIS:** Inclua a propriedade `<EnvironmentName>` no perfil de publicação (*.pubxml*) ou no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="6ba35-189">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="6ba35-190">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="6ba35-190">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

<span data-ttu-id="6ba35-191">**Por pool de aplicativos do IIS**</span><span class="sxs-lookup"><span data-stu-id="6ba35-191">**Per IIS Application Pool**</span></span>

<span data-ttu-id="6ba35-192">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="6ba35-192">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="6ba35-193">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="6ba35-193">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6ba35-194">Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:</span><span class="sxs-lookup"><span data-stu-id="6ba35-194">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="6ba35-195">Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="6ba35-195">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="6ba35-196">Reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="6ba35-196">Restart the server.</span></span>

### <a name="macos"></a><span data-ttu-id="6ba35-197">macOS</span><span class="sxs-lookup"><span data-stu-id="6ba35-197">macOS</span></span>

<span data-ttu-id="6ba35-198">A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="6ba35-198">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="6ba35-199">Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="6ba35-199">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="6ba35-200">As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="6ba35-200">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="6ba35-201">Edite o arquivo usando qualquer editor de texto.</span><span class="sxs-lookup"><span data-stu-id="6ba35-201">Edit the file using any text editor.</span></span> <span data-ttu-id="6ba35-202">Adicione a seguinte instrução:</span><span class="sxs-lookup"><span data-stu-id="6ba35-202">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a><span data-ttu-id="6ba35-203">Linux</span><span class="sxs-lookup"><span data-stu-id="6ba35-203">Linux</span></span>

<span data-ttu-id="6ba35-204">Para distribuições Linux, use o comando `export` no prompt de comando para as configurações de variável baseadas na sessão e o arquivo *bash_profile* para as configurações de ambiente no nível do computador.</span><span class="sxs-lookup"><span data-stu-id="6ba35-204">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="6ba35-205">Configuração por ambiente</span><span class="sxs-lookup"><span data-stu-id="6ba35-205">Configuration by environment</span></span>

<span data-ttu-id="6ba35-206">Para carregar a configuração por ambiente, recomendamos:</span><span class="sxs-lookup"><span data-stu-id="6ba35-206">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="6ba35-207">Arquivos *appsettings* (*appsettings.\<Environment>.json*).</span><span class="sxs-lookup"><span data-stu-id="6ba35-207">*appsettings* files (*appsettings.\<Environment>.json*).</span></span> <span data-ttu-id="6ba35-208">Confira [Configuração: provedor de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="6ba35-208">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider).</span></span>
* <span data-ttu-id="6ba35-209">Variáveis de ambiente (definidas em cada sistema em que o aplicativo está hospedado).</span><span class="sxs-lookup"><span data-stu-id="6ba35-209">environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="6ba35-210">Confira [Configuração: provedor de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider) e [Armazenamento seguro de segredos do aplicativo em desenvolvimento: variáveis de ambiente](xref:security/app-secrets#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="6ba35-210">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider) and [Safe storage of app secrets in development: Environment variables](xref:security/app-secrets#environment-variables).</span></span>
* <span data-ttu-id="6ba35-211">Gerenciador de Segredo (somente no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="6ba35-211">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="6ba35-212">Consulte <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="6ba35-212">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="6ba35-213">Métodos e classe Startup baseados no ambiente</span><span class="sxs-lookup"><span data-stu-id="6ba35-213">Environment-based Startup class and methods</span></span>

### <a name="startup-class-conventions"></a><span data-ttu-id="6ba35-214">Convenções da classe Startup</span><span class="sxs-lookup"><span data-stu-id="6ba35-214">Startup class conventions</span></span>

<span data-ttu-id="6ba35-215">Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6ba35-215">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="6ba35-216">O aplicativo pode definir classes `Startup` separadas para ambientes diferentes (por exemplo, `StartupDevelopment`), e a classe `Startup` adequada é selecionada em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="6ba35-216">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`), and the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="6ba35-217">A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada.</span><span class="sxs-lookup"><span data-stu-id="6ba35-217">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="6ba35-218">Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada.</span><span class="sxs-lookup"><span data-stu-id="6ba35-218">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span>

<span data-ttu-id="6ba35-219">Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:</span><span class="sxs-lookup"><span data-stu-id="6ba35-219">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}
```

<span data-ttu-id="6ba35-220">Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:</span><span class="sxs-lookup"><span data-stu-id="6ba35-220">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="6ba35-221">Convenções do método Startup</span><span class="sxs-lookup"><span data-stu-id="6ba35-221">Startup method conventions</span></span>

<span data-ttu-id="6ba35-222">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) são compatíveis com versões específicas do ambiente dos formatos `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services`:</span><span class="sxs-lookup"><span data-stu-id="6ba35-222">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`:</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="6ba35-223">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6ba35-223">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="6ba35-224">IHostingEnvironment.EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="6ba35-224">IHostingEnvironment.EnvironmentName</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)
