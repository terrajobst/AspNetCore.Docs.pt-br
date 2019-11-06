---
title: Usar vários ambientes no ASP.NET Core
author: rick-anderson
description: Aprenda a controlar o comportamento do aplicativo em vários ambientes em aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/05/2019
uid: fundamentals/environments
ms.openlocfilehash: 91fa2a78e62dff65704a3dda826f45f27bad6064
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634087"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="47cd8-103">Usar vários ambientes no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47cd8-103">Use multiple environments in ASP.NET Core</span></span>

<span data-ttu-id="47cd8-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="47cd8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="47cd8-105">O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="47cd8-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="47cd8-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47cd8-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="47cd8-107">Ambientes</span><span class="sxs-lookup"><span data-stu-id="47cd8-107">Environments</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47cd8-108">ASP.NET Core lê a variável de ambiente `ASPNETCORE_ENVIRONMENT` na inicialização do aplicativo e armazena o valor em [IWebHostEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="47cd8-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="47cd8-109">`ASPNETCORE_ENVIRONMENT` pode ser definido como qualquer valor, mas três valores são fornecidos pela estrutura:</span><span class="sxs-lookup"><span data-stu-id="47cd8-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="47cd8-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (padrão)</span><span class="sxs-lookup"><span data-stu-id="47cd8-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="47cd8-111">O ASP.NET Core lê a variável de ambiente `ASPNETCORE_ENVIRONMENT` na inicialização do aplicativo e armazena o valor em [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="47cd8-111">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="47cd8-112">`ASPNETCORE_ENVIRONMENT` pode ser definido como qualquer valor, mas três valores são fornecidos pela estrutura:</span><span class="sxs-lookup"><span data-stu-id="47cd8-112">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="47cd8-113"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (padrão)</span><span class="sxs-lookup"><span data-stu-id="47cd8-113"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

::: moniker-end

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="47cd8-114">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="47cd8-114">The preceding code:</span></span>

* <span data-ttu-id="47cd8-115">Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.</span><span class="sxs-lookup"><span data-stu-id="47cd8-115">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="47cd8-116">Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="47cd8-116">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="47cd8-117">O [Auxiliar de Marcação de Ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa o valor de `IHostingEnvironment.EnvironmentName` para incluir ou excluir a marcação no elemento:</span><span class="sxs-lookup"><span data-stu-id="47cd8-117">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="47cd8-118">No Windows e no macOS, valores e variáveis de ambiente não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="47cd8-118">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="47cd8-119">Valores e variáveis de ambiente do Linux **diferenciam maiúsculas de minúsculas** por padrão.</span><span class="sxs-lookup"><span data-stu-id="47cd8-119">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="47cd8-120">Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="47cd8-120">Development</span></span>

<span data-ttu-id="47cd8-121">O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção.</span><span class="sxs-lookup"><span data-stu-id="47cd8-121">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="47cd8-122">Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="47cd8-122">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="47cd8-123">O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto.</span><span class="sxs-lookup"><span data-stu-id="47cd8-123">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="47cd8-124">Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="47cd8-124">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="47cd8-125">O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="47cd8-125">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="47cd8-126">A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores.</span><span class="sxs-lookup"><span data-stu-id="47cd8-126">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="47cd8-127">Use um ponto e vírgula entre as URLs na lista:</span><span class="sxs-lookup"><span data-stu-id="47cd8-127">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="47cd8-128">Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado.</span><span class="sxs-lookup"><span data-stu-id="47cd8-128">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="47cd8-129">O valor de `commandName` especifica o servidor Web a ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="47cd8-129">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="47cd8-130">`commandName` pode ser qualquer um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="47cd8-130">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="47cd8-131">`Project` (que inicia o Kestrel)</span><span class="sxs-lookup"><span data-stu-id="47cd8-131">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="47cd8-132">Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="47cd8-132">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="47cd8-133">*launchSettings.json* é lido, se está disponível.</span><span class="sxs-lookup"><span data-stu-id="47cd8-133">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="47cd8-134">As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="47cd8-134">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="47cd8-135">O ambiente de hospedagem é exibido.</span><span class="sxs-lookup"><span data-stu-id="47cd8-135">The hosting environment is displayed.</span></span>

<span data-ttu-id="47cd8-136">A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="47cd8-136">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="47cd8-137">A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="47cd8-137">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

<span data-ttu-id="47cd8-139">As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="47cd8-139">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="47cd8-140">O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.</span><span class="sxs-lookup"><span data-stu-id="47cd8-140">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="47cd8-141">*launchSettings.json* não deve armazenar segredos.</span><span class="sxs-lookup"><span data-stu-id="47cd8-141">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="47cd8-142">A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="47cd8-142">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="47cd8-143">Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="47cd8-143">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="47cd8-144">O exemplo a seguir define o ambiente como `Development`:</span><span class="sxs-lookup"><span data-stu-id="47cd8-144">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="47cd8-145">Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="47cd8-145">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="47cd8-146">Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="47cd8-146">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="47cd8-147">Produção</span><span class="sxs-lookup"><span data-stu-id="47cd8-147">Production</span></span>

<span data-ttu-id="47cd8-148">O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47cd8-148">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="47cd8-149">Algumas configurações comuns que são diferentes do desenvolvimento incluem:</span><span class="sxs-lookup"><span data-stu-id="47cd8-149">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="47cd8-150">Cache.</span><span class="sxs-lookup"><span data-stu-id="47cd8-150">Caching.</span></span>
* <span data-ttu-id="47cd8-151">Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="47cd8-151">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="47cd8-152">Páginas de erro de diagnóstico desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="47cd8-152">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="47cd8-153">Páginas de erro amigáveis habilitadas.</span><span class="sxs-lookup"><span data-stu-id="47cd8-153">Friendly error pages enabled.</span></span>
* <span data-ttu-id="47cd8-154">Log de produção e monitoramento habilitados.</span><span class="sxs-lookup"><span data-stu-id="47cd8-154">Production logging and monitoring enabled.</span></span> <span data-ttu-id="47cd8-155">Por exemplo, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="47cd8-155">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="47cd8-156">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="47cd8-156">Set the environment</span></span>

<span data-ttu-id="47cd8-157">Geralmente, é útil definir um ambiente específico para teste com uma variável de ambiente ou configuração de plataforma.</span><span class="sxs-lookup"><span data-stu-id="47cd8-157">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="47cd8-158">Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração.</span><span class="sxs-lookup"><span data-stu-id="47cd8-158">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="47cd8-159">O método para configurar o ambiente depende do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="47cd8-159">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="47cd8-160">Quando o host é criado, a última configuração de ambiente lida pelo aplicativo determina o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47cd8-160">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="47cd8-161">O ambiente do aplicativo não pode ser alterado enquanto o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="47cd8-161">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="47cd8-162">Configuração de plataforma ou variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="47cd8-162">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="47cd8-163">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="47cd8-163">Azure App Service</span></span>

<span data-ttu-id="47cd8-164">Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="47cd8-164">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="47cd8-165">Selecione o aplicativo na folha **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="47cd8-165">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="47cd8-166">No grupo **CONFIGURAÇÕES**, selecione a folha **Configurações do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="47cd8-166">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="47cd8-167">Na área **Configurações do aplicativo**, selecione **Adicionar nova configuração**.</span><span class="sxs-lookup"><span data-stu-id="47cd8-167">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="47cd8-168">Para **Inserir um nome**, forneça `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="47cd8-168">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="47cd8-169">Para **Inserir um valor**, fornecer o ambiente (por exemplo, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="47cd8-169">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="47cd8-170">Marque a caixa de seleção **Configuração do Slot** se desejar que a configuração do ambiente permaneça no slot atual quando os slots de implantação forem trocados.</span><span class="sxs-lookup"><span data-stu-id="47cd8-170">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="47cd8-171">Para obter mais informações, veja [Documentação do Azure: que configurações são trocadas?](/azure/app-service/web-sites-staged-publishing).</span><span class="sxs-lookup"><span data-stu-id="47cd8-171">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="47cd8-172">Selecione **Salvar** na parte superior da folha.</span><span class="sxs-lookup"><span data-stu-id="47cd8-172">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="47cd8-173">O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="47cd8-173">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="47cd8-174">Windows</span><span class="sxs-lookup"><span data-stu-id="47cd8-174">Windows</span></span>

<span data-ttu-id="47cd8-175">Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:</span><span class="sxs-lookup"><span data-stu-id="47cd8-175">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="47cd8-176">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="47cd8-176">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="47cd8-177">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="47cd8-177">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="47cd8-178">Esses comandos somente têm efeito para a janela atual.</span><span class="sxs-lookup"><span data-stu-id="47cd8-178">These commands only take effect for the current window.</span></span> <span data-ttu-id="47cd8-179">Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.</span><span class="sxs-lookup"><span data-stu-id="47cd8-179">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="47cd8-180">Para definir o valor globalmente no Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="47cd8-180">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="47cd8-181">Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="47cd8-181">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="47cd8-184">Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="47cd8-184">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="47cd8-185">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="47cd8-185">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="47cd8-186">O comutador `/M` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="47cd8-186">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="47cd8-187">Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="47cd8-187">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="47cd8-188">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="47cd8-188">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="47cd8-189">O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="47cd8-189">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="47cd8-190">Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="47cd8-190">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="47cd8-191">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.</span><span class="sxs-lookup"><span data-stu-id="47cd8-191">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="47cd8-192">**web.config**</span><span class="sxs-lookup"><span data-stu-id="47cd8-192">**web.config**</span></span>

<span data-ttu-id="47cd8-193">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="47cd8-193">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="47cd8-194">**Arquivo de projeto ou perfil de publicação**</span><span class="sxs-lookup"><span data-stu-id="47cd8-194">**Project file or publish profile**</span></span>

<span data-ttu-id="47cd8-195">**Para implantações do Windows IIS:** Inclua a propriedade `<EnvironmentName>` no perfil de publicação ( *. pubxml*) ou no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="47cd8-195">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="47cd8-196">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="47cd8-196">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

<span data-ttu-id="47cd8-197">**Por pool de aplicativos do IIS**</span><span class="sxs-lookup"><span data-stu-id="47cd8-197">**Per IIS Application Pool**</span></span>

<span data-ttu-id="47cd8-198">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="47cd8-198">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="47cd8-199">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="47cd8-199">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="47cd8-200">Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:</span><span class="sxs-lookup"><span data-stu-id="47cd8-200">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="47cd8-201">Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="47cd8-201">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="47cd8-202">Reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="47cd8-202">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="47cd8-203">macOS</span><span class="sxs-lookup"><span data-stu-id="47cd8-203">macOS</span></span>

<span data-ttu-id="47cd8-204">A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="47cd8-204">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="47cd8-205">Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="47cd8-205">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="47cd8-206">As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="47cd8-206">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="47cd8-207">Edite o arquivo usando qualquer editor de texto.</span><span class="sxs-lookup"><span data-stu-id="47cd8-207">Edit the file using any text editor.</span></span> <span data-ttu-id="47cd8-208">Adicione a seguinte instrução:</span><span class="sxs-lookup"><span data-stu-id="47cd8-208">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="47cd8-209">Linux</span><span class="sxs-lookup"><span data-stu-id="47cd8-209">Linux</span></span>

<span data-ttu-id="47cd8-210">Para distribuições Linux, use o comando `export` no prompt de comando para as configurações de variável baseadas na sessão e o arquivo *bash_profile* para as configurações de ambiente no nível do computador.</span><span class="sxs-lookup"><span data-stu-id="47cd8-210">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="47cd8-211">Definir o ambiente no código</span><span class="sxs-lookup"><span data-stu-id="47cd8-211">Set the environment in code</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47cd8-212">Chame <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> ao compilar o host.</span><span class="sxs-lookup"><span data-stu-id="47cd8-212">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="47cd8-213">Consulte <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="47cd8-213">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="47cd8-214">Chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> ao compilar o host.</span><span class="sxs-lookup"><span data-stu-id="47cd8-214">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="47cd8-215">Consulte <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="47cd8-215">See <xref:fundamentals/host/web-host#environment>.</span></span>

::: moniker-end

### <a name="configuration-by-environment"></a><span data-ttu-id="47cd8-216">Configuração por ambiente</span><span class="sxs-lookup"><span data-stu-id="47cd8-216">Configuration by environment</span></span>

<span data-ttu-id="47cd8-217">Para carregar a configuração por ambiente, recomendamos:</span><span class="sxs-lookup"><span data-stu-id="47cd8-217">To load configuration by environment, we recommend:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="47cd8-218">*appSettings* arquivos (*appSettings. { Ambiente}. JSON*).</span><span class="sxs-lookup"><span data-stu-id="47cd8-218">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="47cd8-219">Consulte <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="47cd8-219">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="47cd8-220">Variáveis de ambiente (definidas em cada sistema em que o aplicativo está hospedado).</span><span class="sxs-lookup"><span data-stu-id="47cd8-220">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="47cd8-221">Consulte <xref:fundamentals/host/generic-host#environmentname> e <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="47cd8-221">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="47cd8-222">Gerenciador de Segredo (somente no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="47cd8-222">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="47cd8-223">Consulte <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="47cd8-223">See <xref:security/app-secrets>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="47cd8-224">*appSettings* arquivos (*appSettings. { Ambiente}. JSON*).</span><span class="sxs-lookup"><span data-stu-id="47cd8-224">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="47cd8-225">Consulte <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="47cd8-225">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="47cd8-226">Variáveis de ambiente (definidas em cada sistema em que o aplicativo está hospedado).</span><span class="sxs-lookup"><span data-stu-id="47cd8-226">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="47cd8-227">Consulte <xref:fundamentals/host/web-host#environment> e <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="47cd8-227">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="47cd8-228">Gerenciador de Segredo (somente no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="47cd8-228">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="47cd8-229">Consulte <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="47cd8-229">See <xref:security/app-secrets>.</span></span>

::: moniker-end

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="47cd8-230">Métodos e classe Startup baseados no ambiente</span><span class="sxs-lookup"><span data-stu-id="47cd8-230">Environment-based Startup class and methods</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="47cd8-231">Injetar IWebHostEnvironment na inicialização. configurar</span><span class="sxs-lookup"><span data-stu-id="47cd8-231">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="47cd8-232">Injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="47cd8-232">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="47cd8-233">Essa abordagem é útil quando o aplicativo requer apenas o ajuste de `Startup.Configure` para alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="47cd8-233">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="47cd8-234">Injetar IWebHostEnvironment na classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="47cd8-234">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="47cd8-235">Injeta <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no construtor de `Startup`.</span><span class="sxs-lookup"><span data-stu-id="47cd8-235">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="47cd8-236">Essa abordagem é útil quando o aplicativo requer a configuração de `Startup` apenas para alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="47cd8-236">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="47cd8-237">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="47cd8-237">In the following example:</span></span>

* <span data-ttu-id="47cd8-238">O ambiente é mantido no campo `_env`.</span><span class="sxs-lookup"><span data-stu-id="47cd8-238">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="47cd8-239">`_env` é usado em `ConfigureServices` e `Configure` para aplicar a configuração de inicialização com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47cd8-239">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="47cd8-240">Injetar IHostingEnvironment na inicialização. configurar</span><span class="sxs-lookup"><span data-stu-id="47cd8-240">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="47cd8-241">Injetar <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="47cd8-241">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="47cd8-242">Essa abordagem é útil quando o aplicativo requer apenas a configuração de `Startup.Configure` para apenas alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="47cd8-242">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="47cd8-243">Injetar IHostingEnvironment na classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="47cd8-243">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="47cd8-244">Insira <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> no Construtor `Startup` e atribua o serviço a um campo para uso em toda a classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="47cd8-244">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="47cd8-245">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para apenas alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="47cd8-245">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="47cd8-246">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="47cd8-246">In the following example:</span></span>

* <span data-ttu-id="47cd8-247">O ambiente é mantido no campo `_env`.</span><span class="sxs-lookup"><span data-stu-id="47cd8-247">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="47cd8-248">`_env` é usado em `ConfigureServices` e `Configure` para aplicar a configuração de inicialização com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47cd8-248">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

::: moniker-end

### <a name="startup-class-conventions"></a><span data-ttu-id="47cd8-249">Convenções da classe Startup</span><span class="sxs-lookup"><span data-stu-id="47cd8-249">Startup class conventions</span></span>

<span data-ttu-id="47cd8-250">Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="47cd8-250">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="47cd8-251">O aplicativo pode definir classes `Startup` separadas para ambientes diferentes (por exemplo, `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="47cd8-251">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="47cd8-252">A classe de `Startup` apropriada está selecionada em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="47cd8-252">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="47cd8-253">A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada.</span><span class="sxs-lookup"><span data-stu-id="47cd8-253">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="47cd8-254">Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada.</span><span class="sxs-lookup"><span data-stu-id="47cd8-254">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="47cd8-255">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="47cd8-255">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="47cd8-256">Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:</span><span class="sxs-lookup"><span data-stu-id="47cd8-256">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="47cd8-257">Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:</span><span class="sxs-lookup"><span data-stu-id="47cd8-257">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="47cd8-258">Convenções do método Startup</span><span class="sxs-lookup"><span data-stu-id="47cd8-258">Startup method conventions</span></span>

<span data-ttu-id="47cd8-259">[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [configuraservices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) oferecem suporte a versões específicas do ambiente do formulário `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="47cd8-259">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="47cd8-260">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="47cd8-260">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="47cd8-261">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="47cd8-261">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
