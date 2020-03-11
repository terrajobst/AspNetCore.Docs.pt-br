---
title: Usar vários ambientes no ASP.NET Core
author: rick-anderson
description: Aprenda a controlar o comportamento do aplicativo em vários ambientes em aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656215"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="e2d53-103">Usar vários ambientes no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2d53-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e2d53-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e2d53-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e2d53-105">O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="e2d53-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2d53-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="e2d53-107">Ambientes</span><span class="sxs-lookup"><span data-stu-id="e2d53-107">Environments</span></span>

<span data-ttu-id="e2d53-108">ASP.NET Core lê a variável de ambiente `ASPNETCORE_ENVIRONMENT` na inicialização do aplicativo e armazena o valor em [IWebHostEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="e2d53-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="e2d53-109">`ASPNETCORE_ENVIRONMENT` pode ser definido como qualquer valor, mas três valores são fornecidos pela estrutura:</span><span class="sxs-lookup"><span data-stu-id="e2d53-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="e2d53-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (padrão)</span><span class="sxs-lookup"><span data-stu-id="e2d53-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="e2d53-111">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="e2d53-111">The preceding code:</span></span>

* <span data-ttu-id="e2d53-112">Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="e2d53-113">Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="e2d53-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="e2d53-114">O [Auxiliar de Marcação de Ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa o valor de `IHostingEnvironment.EnvironmentName` para incluir ou excluir a marcação no elemento:</span><span class="sxs-lookup"><span data-stu-id="e2d53-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="e2d53-115">No Windows e no macOS, valores e variáveis de ambiente não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="e2d53-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="e2d53-116">Valores e variáveis de ambiente do Linux **diferenciam maiúsculas de minúsculas** por padrão.</span><span class="sxs-lookup"><span data-stu-id="e2d53-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="e2d53-117">Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="e2d53-117">Development</span></span>

<span data-ttu-id="e2d53-118">O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção.</span><span class="sxs-lookup"><span data-stu-id="e2d53-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="e2d53-119">Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e2d53-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="e2d53-120">O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto.</span><span class="sxs-lookup"><span data-stu-id="e2d53-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="e2d53-121">Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="e2d53-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="e2d53-122">O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e2d53-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="e2d53-123">A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores.</span><span class="sxs-lookup"><span data-stu-id="e2d53-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="e2d53-124">Use um ponto e vírgula entre as URLs na lista:</span><span class="sxs-lookup"><span data-stu-id="e2d53-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="e2d53-125">Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado.</span><span class="sxs-lookup"><span data-stu-id="e2d53-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="e2d53-126">O valor de `commandName` especifica o servidor Web a ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="e2d53-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="e2d53-127">`commandName` pode ser qualquer um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="e2d53-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="e2d53-128">`Project` (que inicia o Kestrel)</span><span class="sxs-lookup"><span data-stu-id="e2d53-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="e2d53-129">Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="e2d53-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="e2d53-130">*launchSettings.json* é lido, se está disponível.</span><span class="sxs-lookup"><span data-stu-id="e2d53-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="e2d53-131">As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="e2d53-132">O ambiente de hospedagem é exibido.</span><span class="sxs-lookup"><span data-stu-id="e2d53-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="e2d53-133">A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="e2d53-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="e2d53-134">A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e2d53-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

<span data-ttu-id="e2d53-136">As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="e2d53-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="e2d53-137">O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="e2d53-138">*launchSettings.json* não deve armazenar segredos.</span><span class="sxs-lookup"><span data-stu-id="e2d53-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="e2d53-139">A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="e2d53-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="e2d53-140">Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="e2d53-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="e2d53-141">O exemplo a seguir define o ambiente como `Development`:</span><span class="sxs-lookup"><span data-stu-id="e2d53-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="e2d53-142">Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e2d53-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="e2d53-143">Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="e2d53-144">Production</span><span class="sxs-lookup"><span data-stu-id="e2d53-144">Production</span></span>

<span data-ttu-id="e2d53-145">O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2d53-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="e2d53-146">Algumas configurações comuns que são diferentes do desenvolvimento incluem:</span><span class="sxs-lookup"><span data-stu-id="e2d53-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="e2d53-147">Cache.</span><span class="sxs-lookup"><span data-stu-id="e2d53-147">Caching.</span></span>
* <span data-ttu-id="e2d53-148">Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="e2d53-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="e2d53-149">Páginas de erro de diagnóstico desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="e2d53-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="e2d53-150">Páginas de erro amigáveis habilitadas.</span><span class="sxs-lookup"><span data-stu-id="e2d53-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="e2d53-151">Log de produção e monitoramento habilitados.</span><span class="sxs-lookup"><span data-stu-id="e2d53-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="e2d53-152">Por exemplo, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="e2d53-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="e2d53-153">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="e2d53-153">Set the environment</span></span>

<span data-ttu-id="e2d53-154">Geralmente, é útil definir um ambiente específico para teste com uma variável de ambiente ou configuração de plataforma.</span><span class="sxs-lookup"><span data-stu-id="e2d53-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="e2d53-155">Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração.</span><span class="sxs-lookup"><span data-stu-id="e2d53-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="e2d53-156">O método para configurar o ambiente depende do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="e2d53-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="e2d53-157">Quando o host é criado, a última configuração de ambiente lida pelo aplicativo determina o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2d53-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="e2d53-158">O ambiente do aplicativo não pode ser alterado enquanto o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="e2d53-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="e2d53-159">Configuração de plataforma ou variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="e2d53-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="e2d53-160">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="e2d53-160">Azure App Service</span></span>

<span data-ttu-id="e2d53-161">Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="e2d53-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="e2d53-162">Selecione o aplicativo na folha **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="e2d53-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="e2d53-163">No grupo **configurações** , selecione a folha **configuração** .</span><span class="sxs-lookup"><span data-stu-id="e2d53-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="e2d53-164">Na guia **configurações do aplicativo** , selecione **nova configuração de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="e2d53-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="e2d53-165">Na janela **Adicionar/Editar configuração de aplicativo** , forneça `ASPNETCORE_ENVIRONMENT` para o **nome**.</span><span class="sxs-lookup"><span data-stu-id="e2d53-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="e2d53-166">Para **valor**, forneça o ambiente (por exemplo, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="e2d53-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="e2d53-167">Marque a caixa de seleção **configuração do slot de implantação** se desejar que a configuração do ambiente permaneça com o slot atual quando os slots de implantação forem trocados.</span><span class="sxs-lookup"><span data-stu-id="e2d53-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="e2d53-168">Para obter mais informações, consulte [configurar ambientes de preparo no serviço de Azure app](/azure/app-service/web-sites-staged-publishing) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="e2d53-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="e2d53-169">Selecione **OK** para fechar a janela **Adicionar/Editar configuração de aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="e2d53-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="e2d53-170">Selecione **salvar** na parte superior da folha de **configuração** .</span><span class="sxs-lookup"><span data-stu-id="e2d53-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="e2d53-171">O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="e2d53-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="e2d53-172">Portal</span><span class="sxs-lookup"><span data-stu-id="e2d53-172">Windows</span></span>

<span data-ttu-id="e2d53-173">Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:</span><span class="sxs-lookup"><span data-stu-id="e2d53-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="e2d53-174">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="e2d53-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="e2d53-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="e2d53-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="e2d53-176">Esses comandos somente têm efeito para a janela atual.</span><span class="sxs-lookup"><span data-stu-id="e2d53-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="e2d53-177">Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.</span><span class="sxs-lookup"><span data-stu-id="e2d53-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="e2d53-178">Para definir o valor globalmente no Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e2d53-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="e2d53-179">Abra o **painel de controle** > **sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="e2d53-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="e2d53-182">Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="e2d53-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="e2d53-183">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="e2d53-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="e2d53-184">O comutador `/M` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="e2d53-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="e2d53-185">Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="e2d53-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="e2d53-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="e2d53-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="e2d53-187">O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="e2d53-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="e2d53-188">Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="e2d53-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="e2d53-189">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.</span><span class="sxs-lookup"><span data-stu-id="e2d53-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="e2d53-190">**web.config**</span><span class="sxs-lookup"><span data-stu-id="e2d53-190">**web.config**</span></span>

<span data-ttu-id="e2d53-191">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="e2d53-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="e2d53-192">**Arquivo de projeto ou perfil de publicação**</span><span class="sxs-lookup"><span data-stu-id="e2d53-192">**Project file or publish profile**</span></span>

<span data-ttu-id="e2d53-193">**Para implantações do Windows IIS:** Inclua a propriedade `<EnvironmentName>` no [perfil de publicação (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="e2d53-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="e2d53-194">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="e2d53-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="e2d53-195">**Por pool de aplicativos do IIS**</span><span class="sxs-lookup"><span data-stu-id="e2d53-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="e2d53-196">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="e2d53-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="e2d53-197">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="e2d53-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e2d53-198">Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:</span><span class="sxs-lookup"><span data-stu-id="e2d53-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="e2d53-199">Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="e2d53-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="e2d53-200">Reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="e2d53-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="e2d53-201">macOS</span><span class="sxs-lookup"><span data-stu-id="e2d53-201">macOS</span></span>

<span data-ttu-id="e2d53-202">A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e2d53-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="e2d53-203">Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e2d53-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="e2d53-204">As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="e2d53-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="e2d53-205">Edite o arquivo usando qualquer editor de texto.</span><span class="sxs-lookup"><span data-stu-id="e2d53-205">Edit the file using any text editor.</span></span> <span data-ttu-id="e2d53-206">Adicione a seguinte instrução:</span><span class="sxs-lookup"><span data-stu-id="e2d53-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="e2d53-207">Linux</span><span class="sxs-lookup"><span data-stu-id="e2d53-207">Linux</span></span>

<span data-ttu-id="e2d53-208">Para distribuições Linux, use o comando `export` no prompt de comando para as configurações de variável baseadas na sessão e o arquivo *bash_profile* para as configurações de ambiente no nível do computador.</span><span class="sxs-lookup"><span data-stu-id="e2d53-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="e2d53-209">Definir o ambiente no código</span><span class="sxs-lookup"><span data-stu-id="e2d53-209">Set the environment in code</span></span>

<span data-ttu-id="e2d53-210">Chame <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> ao compilar o host.</span><span class="sxs-lookup"><span data-stu-id="e2d53-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="e2d53-211">Consulte <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="e2d53-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="e2d53-212">Configuração por ambiente</span><span class="sxs-lookup"><span data-stu-id="e2d53-212">Configuration by environment</span></span>

<span data-ttu-id="e2d53-213">Para carregar a configuração por ambiente, recomendamos:</span><span class="sxs-lookup"><span data-stu-id="e2d53-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="e2d53-214">*appSettings* arquivos (*appSettings. { Ambiente}. JSON*).</span><span class="sxs-lookup"><span data-stu-id="e2d53-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="e2d53-215">Consulte <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="e2d53-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="e2d53-216">Variáveis de ambiente (definidas em cada sistema em que o aplicativo está hospedado).</span><span class="sxs-lookup"><span data-stu-id="e2d53-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="e2d53-217">Consulte <xref:fundamentals/host/generic-host#environmentname> e <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="e2d53-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="e2d53-218">Gerenciador de Segredo (somente no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="e2d53-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="e2d53-219">Consulte <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="e2d53-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="e2d53-220">Métodos e classe Startup baseados no ambiente</span><span class="sxs-lookup"><span data-stu-id="e2d53-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="e2d53-221">Injetar IWebHostEnvironment na inicialização. configurar</span><span class="sxs-lookup"><span data-stu-id="e2d53-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="e2d53-222">Injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="e2d53-223">Essa abordagem é útil quando o aplicativo requer apenas o ajuste de `Startup.Configure` para alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="e2d53-224">Injetar IWebHostEnvironment na classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="e2d53-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="e2d53-225">Injeta <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no construtor de `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="e2d53-226">Essa abordagem é útil quando o aplicativo requer a configuração de `Startup` apenas para alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="e2d53-227">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e2d53-227">In the following example:</span></span>

* <span data-ttu-id="e2d53-228">O ambiente é mantido no campo `_env`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="e2d53-229">`_env` é usado em `ConfigureServices` e `Configure` para aplicar a configuração de inicialização com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2d53-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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
### <a name="startup-class-conventions"></a><span data-ttu-id="e2d53-230">Convenções da classe Startup</span><span class="sxs-lookup"><span data-stu-id="e2d53-230">Startup class conventions</span></span>

<span data-ttu-id="e2d53-231">Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2d53-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="e2d53-232">O aplicativo pode definir classes `Startup` separadas para ambientes diferentes (por exemplo, `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="e2d53-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="e2d53-233">A classe de `Startup` apropriada está selecionada em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="e2d53-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="e2d53-234">A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada.</span><span class="sxs-lookup"><span data-stu-id="e2d53-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="e2d53-235">Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada.</span><span class="sxs-lookup"><span data-stu-id="e2d53-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="e2d53-236">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="e2d53-237">Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:</span><span class="sxs-lookup"><span data-stu-id="e2d53-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e2d53-238">Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:</span><span class="sxs-lookup"><span data-stu-id="e2d53-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="e2d53-239">Convenções do método Startup</span><span class="sxs-lookup"><span data-stu-id="e2d53-239">Startup method conventions</span></span>

<span data-ttu-id="e2d53-240">[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [configuraservices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) oferecem suporte a versões específicas do ambiente do formulário `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="e2d53-241">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="e2d53-242">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e2d53-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e2d53-243">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e2d53-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e2d53-244">O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="e2d53-245">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2d53-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="e2d53-246">Ambientes</span><span class="sxs-lookup"><span data-stu-id="e2d53-246">Environments</span></span>

<span data-ttu-id="e2d53-247">O ASP.NET Core lê a variável de ambiente `ASPNETCORE_ENVIRONMENT` na inicialização do aplicativo e armazena o valor em [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="e2d53-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="e2d53-248">`ASPNETCORE_ENVIRONMENT` pode ser definido como qualquer valor, mas três valores são fornecidos pela estrutura:</span><span class="sxs-lookup"><span data-stu-id="e2d53-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="e2d53-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (padrão)</span><span class="sxs-lookup"><span data-stu-id="e2d53-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="e2d53-250">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="e2d53-250">The preceding code:</span></span>

* <span data-ttu-id="e2d53-251">Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="e2d53-252">Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="e2d53-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="e2d53-253">O [Auxiliar de Marcação de Ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa o valor de `IHostingEnvironment.EnvironmentName` para incluir ou excluir a marcação no elemento:</span><span class="sxs-lookup"><span data-stu-id="e2d53-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="e2d53-254">No Windows e no macOS, valores e variáveis de ambiente não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="e2d53-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="e2d53-255">Valores e variáveis de ambiente do Linux **diferenciam maiúsculas de minúsculas** por padrão.</span><span class="sxs-lookup"><span data-stu-id="e2d53-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="e2d53-256">Desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="e2d53-256">Development</span></span>

<span data-ttu-id="e2d53-257">O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção.</span><span class="sxs-lookup"><span data-stu-id="e2d53-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="e2d53-258">Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e2d53-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="e2d53-259">O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto.</span><span class="sxs-lookup"><span data-stu-id="e2d53-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="e2d53-260">Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="e2d53-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="e2d53-261">O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e2d53-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="e2d53-262">A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores.</span><span class="sxs-lookup"><span data-stu-id="e2d53-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="e2d53-263">Use um ponto e vírgula entre as URLs na lista:</span><span class="sxs-lookup"><span data-stu-id="e2d53-263">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="e2d53-264">Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado.</span><span class="sxs-lookup"><span data-stu-id="e2d53-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="e2d53-265">O valor de `commandName` especifica o servidor Web a ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="e2d53-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="e2d53-266">`commandName` pode ser qualquer um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="e2d53-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="e2d53-267">`Project` (que inicia o Kestrel)</span><span class="sxs-lookup"><span data-stu-id="e2d53-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="e2d53-268">Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="e2d53-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="e2d53-269">*launchSettings.json* é lido, se está disponível.</span><span class="sxs-lookup"><span data-stu-id="e2d53-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="e2d53-270">As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="e2d53-271">O ambiente de hospedagem é exibido.</span><span class="sxs-lookup"><span data-stu-id="e2d53-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="e2d53-272">A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="e2d53-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="e2d53-273">A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e2d53-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

<span data-ttu-id="e2d53-275">As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="e2d53-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="e2d53-276">O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="e2d53-277">*launchSettings.json* não deve armazenar segredos.</span><span class="sxs-lookup"><span data-stu-id="e2d53-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="e2d53-278">A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="e2d53-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="e2d53-279">Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="e2d53-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="e2d53-280">O exemplo a seguir define o ambiente como `Development`:</span><span class="sxs-lookup"><span data-stu-id="e2d53-280">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="e2d53-281">Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e2d53-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="e2d53-282">Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="e2d53-283">Production</span><span class="sxs-lookup"><span data-stu-id="e2d53-283">Production</span></span>

<span data-ttu-id="e2d53-284">O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2d53-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="e2d53-285">Algumas configurações comuns que são diferentes do desenvolvimento incluem:</span><span class="sxs-lookup"><span data-stu-id="e2d53-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="e2d53-286">Cache.</span><span class="sxs-lookup"><span data-stu-id="e2d53-286">Caching.</span></span>
* <span data-ttu-id="e2d53-287">Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="e2d53-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="e2d53-288">Páginas de erro de diagnóstico desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="e2d53-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="e2d53-289">Páginas de erro amigáveis habilitadas.</span><span class="sxs-lookup"><span data-stu-id="e2d53-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="e2d53-290">Log de produção e monitoramento habilitados.</span><span class="sxs-lookup"><span data-stu-id="e2d53-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="e2d53-291">Por exemplo, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="e2d53-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="e2d53-292">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="e2d53-292">Set the environment</span></span>

<span data-ttu-id="e2d53-293">Geralmente, é útil definir um ambiente específico para teste com uma variável de ambiente ou configuração de plataforma.</span><span class="sxs-lookup"><span data-stu-id="e2d53-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="e2d53-294">Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração.</span><span class="sxs-lookup"><span data-stu-id="e2d53-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="e2d53-295">O método para configurar o ambiente depende do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="e2d53-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="e2d53-296">Quando o host é criado, a última configuração de ambiente lida pelo aplicativo determina o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2d53-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="e2d53-297">O ambiente do aplicativo não pode ser alterado enquanto o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="e2d53-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="e2d53-298">Configuração de plataforma ou variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="e2d53-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="e2d53-299">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="e2d53-299">Azure App Service</span></span>

<span data-ttu-id="e2d53-300">Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="e2d53-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="e2d53-301">Selecione o aplicativo na folha **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="e2d53-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="e2d53-302">No grupo **configurações** , selecione a folha **configuração** .</span><span class="sxs-lookup"><span data-stu-id="e2d53-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="e2d53-303">Na guia **configurações do aplicativo** , selecione **nova configuração de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="e2d53-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="e2d53-304">Na janela **Adicionar/Editar configuração de aplicativo** , forneça `ASPNETCORE_ENVIRONMENT` para o **nome**.</span><span class="sxs-lookup"><span data-stu-id="e2d53-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="e2d53-305">Para **valor**, forneça o ambiente (por exemplo, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="e2d53-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="e2d53-306">Marque a caixa de seleção **configuração do slot de implantação** se desejar que a configuração do ambiente permaneça com o slot atual quando os slots de implantação forem trocados.</span><span class="sxs-lookup"><span data-stu-id="e2d53-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="e2d53-307">Para obter mais informações, consulte [configurar ambientes de preparo no serviço de Azure app](/azure/app-service/web-sites-staged-publishing) na documentação do Azure.</span><span class="sxs-lookup"><span data-stu-id="e2d53-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="e2d53-308">Selecione **OK** para fechar a janela **Adicionar/Editar configuração de aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="e2d53-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="e2d53-309">Selecione **salvar** na parte superior da folha de **configuração** .</span><span class="sxs-lookup"><span data-stu-id="e2d53-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="e2d53-310">O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="e2d53-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="e2d53-311">Portal</span><span class="sxs-lookup"><span data-stu-id="e2d53-311">Windows</span></span>

<span data-ttu-id="e2d53-312">Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:</span><span class="sxs-lookup"><span data-stu-id="e2d53-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="e2d53-313">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="e2d53-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="e2d53-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="e2d53-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="e2d53-315">Esses comandos somente têm efeito para a janela atual.</span><span class="sxs-lookup"><span data-stu-id="e2d53-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="e2d53-316">Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.</span><span class="sxs-lookup"><span data-stu-id="e2d53-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="e2d53-317">Para definir o valor globalmente no Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e2d53-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="e2d53-318">Abra o **painel de controle** > **sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="e2d53-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="e2d53-321">Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="e2d53-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="e2d53-322">**Prompt de comando**</span><span class="sxs-lookup"><span data-stu-id="e2d53-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="e2d53-323">O comutador `/M` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="e2d53-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="e2d53-324">Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="e2d53-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="e2d53-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="e2d53-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="e2d53-326">O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="e2d53-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="e2d53-327">Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="e2d53-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="e2d53-328">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.</span><span class="sxs-lookup"><span data-stu-id="e2d53-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="e2d53-329">**web.config**</span><span class="sxs-lookup"><span data-stu-id="e2d53-329">**web.config**</span></span>

<span data-ttu-id="e2d53-330">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="e2d53-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="e2d53-331">**Arquivo de projeto ou perfil de publicação**</span><span class="sxs-lookup"><span data-stu-id="e2d53-331">**Project file or publish profile**</span></span>

<span data-ttu-id="e2d53-332">**Para implantações do Windows IIS:** Inclua a propriedade `<EnvironmentName>` no [perfil de publicação (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="e2d53-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="e2d53-333">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="e2d53-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="e2d53-334">**Por pool de aplicativos do IIS**</span><span class="sxs-lookup"><span data-stu-id="e2d53-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="e2d53-335">Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="e2d53-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="e2d53-336">Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.</span><span class="sxs-lookup"><span data-stu-id="e2d53-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e2d53-337">Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:</span><span class="sxs-lookup"><span data-stu-id="e2d53-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="e2d53-338">Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="e2d53-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="e2d53-339">Reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="e2d53-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="e2d53-340">macOS</span><span class="sxs-lookup"><span data-stu-id="e2d53-340">macOS</span></span>

<span data-ttu-id="e2d53-341">A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e2d53-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="e2d53-342">Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e2d53-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="e2d53-343">As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="e2d53-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="e2d53-344">Edite o arquivo usando qualquer editor de texto.</span><span class="sxs-lookup"><span data-stu-id="e2d53-344">Edit the file using any text editor.</span></span> <span data-ttu-id="e2d53-345">Adicione a seguinte instrução:</span><span class="sxs-lookup"><span data-stu-id="e2d53-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="e2d53-346">Linux</span><span class="sxs-lookup"><span data-stu-id="e2d53-346">Linux</span></span>

<span data-ttu-id="e2d53-347">Para distribuições Linux, use o comando `export` no prompt de comando para as configurações de variável baseadas na sessão e o arquivo *bash_profile* para as configurações de ambiente no nível do computador.</span><span class="sxs-lookup"><span data-stu-id="e2d53-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="e2d53-348">Definir o ambiente no código</span><span class="sxs-lookup"><span data-stu-id="e2d53-348">Set the environment in code</span></span>

<span data-ttu-id="e2d53-349">Chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> ao compilar o host.</span><span class="sxs-lookup"><span data-stu-id="e2d53-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="e2d53-350">Consulte <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="e2d53-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="e2d53-351">Configuração por ambiente</span><span class="sxs-lookup"><span data-stu-id="e2d53-351">Configuration by environment</span></span>

<span data-ttu-id="e2d53-352">Para carregar a configuração por ambiente, recomendamos:</span><span class="sxs-lookup"><span data-stu-id="e2d53-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="e2d53-353">*appSettings* arquivos (*appSettings. { Ambiente}. JSON*).</span><span class="sxs-lookup"><span data-stu-id="e2d53-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="e2d53-354">Consulte <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="e2d53-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="e2d53-355">Variáveis de ambiente (definidas em cada sistema em que o aplicativo está hospedado).</span><span class="sxs-lookup"><span data-stu-id="e2d53-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="e2d53-356">Consulte <xref:fundamentals/host/web-host#environment> e <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="e2d53-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="e2d53-357">Gerenciador de Segredo (somente no ambiente de desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="e2d53-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="e2d53-358">Consulte <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="e2d53-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="e2d53-359">Métodos e classe Startup baseados no ambiente</span><span class="sxs-lookup"><span data-stu-id="e2d53-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="e2d53-360">Injetar IHostingEnvironment na inicialização. configurar</span><span class="sxs-lookup"><span data-stu-id="e2d53-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="e2d53-361">Injetar <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="e2d53-362">Essa abordagem é útil quando o aplicativo requer apenas a configuração de `Startup.Configure` para apenas alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="e2d53-363">Injetar IHostingEnvironment na classe de inicialização</span><span class="sxs-lookup"><span data-stu-id="e2d53-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="e2d53-364">Insira <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> no Construtor `Startup` e atribua o serviço a um campo para uso em toda a classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="e2d53-365">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para apenas alguns ambientes com diferenças mínimas de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="e2d53-366">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e2d53-366">In the following example:</span></span>

* <span data-ttu-id="e2d53-367">O ambiente é mantido no campo `_env`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="e2d53-368">`_env` é usado em `ConfigureServices` e `Configure` para aplicar a configuração de inicialização com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2d53-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="e2d53-369">Convenções da classe Startup</span><span class="sxs-lookup"><span data-stu-id="e2d53-369">Startup class conventions</span></span>

<span data-ttu-id="e2d53-370">Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2d53-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="e2d53-371">O aplicativo pode definir classes `Startup` separadas para ambientes diferentes (por exemplo, `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="e2d53-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="e2d53-372">A classe de `Startup` apropriada está selecionada em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="e2d53-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="e2d53-373">A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada.</span><span class="sxs-lookup"><span data-stu-id="e2d53-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="e2d53-374">Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada.</span><span class="sxs-lookup"><span data-stu-id="e2d53-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="e2d53-375">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="e2d53-376">Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:</span><span class="sxs-lookup"><span data-stu-id="e2d53-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="e2d53-377">Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:</span><span class="sxs-lookup"><span data-stu-id="e2d53-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="e2d53-378">Convenções do método Startup</span><span class="sxs-lookup"><span data-stu-id="e2d53-378">Startup method conventions</span></span>

<span data-ttu-id="e2d53-379">[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [configuraservices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) oferecem suporte a versões específicas do ambiente do formulário `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="e2d53-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="e2d53-380">Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2d53-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="e2d53-381">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e2d53-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
