---
title: Implantar aplicativos ASP.NET Core no Serviço de Aplicativo do Azure
author: bradygaster
description: Este artigo contém links para o host do Azure e para implantar recursos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/11/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 392868b4fc9105279f8f3b10436a9915123e7070
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190619"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="68f7f-103">Implantar aplicativos ASP.NET Core no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="68f7f-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="68f7f-104">[Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) é um [serviço de plataforma de computação em nuvem da Microsoft](https://azure.microsoft.com/) para hospedar aplicativos Web, incluindo o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68f7f-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="68f7f-105">Recursos úteis</span><span class="sxs-lookup"><span data-stu-id="68f7f-105">Useful resources</span></span>

<span data-ttu-id="68f7f-106">A [Documentação de Serviço de Aplicativo](/azure/app-service/) do Azure é a página inicial para documentação de Aplicativos Azure, tutoriais, exemplos, guias de instruções e outros recursos.</span><span class="sxs-lookup"><span data-stu-id="68f7f-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="68f7f-107">Dois tutoriais importantes que pertencem à hospedagem de aplicativos ASP.NET Core são:</span><span class="sxs-lookup"><span data-stu-id="68f7f-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="68f7f-108">Criar um aplicativo Web ASP.NET Core no Azure</span><span class="sxs-lookup"><span data-stu-id="68f7f-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="68f7f-109">Use o Visual Studio para criar e implantar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure no Windows.</span><span class="sxs-lookup"><span data-stu-id="68f7f-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="68f7f-110">Criar um aplicativo ASP.NET Core no Serviço de Aplicativo no Linux</span><span class="sxs-lookup"><span data-stu-id="68f7f-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="68f7f-111">Use a linha de comando do Visual Studio para criar e implantar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure no Linux.</span><span class="sxs-lookup"><span data-stu-id="68f7f-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="68f7f-112">Consulte o [ASP.NET Core no painel do serviço de aplicativo](https://aspnetcoreon.azurewebsites.net/) para obter a versão do ASP.NET Core disponível no serviço Azure app.</span><span class="sxs-lookup"><span data-stu-id="68f7f-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="68f7f-113">Assine o repositório de [anúncios do serviço de aplicativo](https://github.com/Azure/app-service-announcements/) e monitore os problemas.</span><span class="sxs-lookup"><span data-stu-id="68f7f-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="68f7f-114">A equipe do serviço de aplicativo publica regularmente anúncios e cenários chegando ao serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68f7f-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="68f7f-115">Os artigos a seguir estão disponíveis na documentação do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="68f7f-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="68f7f-116">Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68f7f-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="68f7f-117">Saiba como criar um aplicativo Web ASP.NET Core usando o Visual Studio e implantá-lo no Serviço de Aplicativo do Azure, usando o Git para implantação contínua.</span><span class="sxs-lookup"><span data-stu-id="68f7f-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="68f7f-118">Crie seu primeiro pipeline</span><span class="sxs-lookup"><span data-stu-id="68f7f-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="68f7f-119">Configurar um build de CI para um aplicativo ASP.NET Core e, em seguida, criar uma versão de implantação contínua para o Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="68f7f-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="68f7f-120">Área restrita de aplicativo Web do Azure</span><span class="sxs-lookup"><span data-stu-id="68f7f-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="68f7f-121">Descubra as limitações de runtime do Serviço de Aplicativo do Azure impostas pela plataforma de Aplicativos do Azure.</span><span class="sxs-lookup"><span data-stu-id="68f7f-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="68f7f-122">Compreenda e solucione problemas de avisos e erros com projetos do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68f7f-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="68f7f-123">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="68f7f-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="68f7f-124">Plataforma</span><span class="sxs-lookup"><span data-stu-id="68f7f-124">Platform</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="68f7f-125">Os runtimes para aplicativos de 32 bits (x86) e 64 bits (x64) estão presentes no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="68f7f-125">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="68f7f-126">O [SDK do .NET Core](/dotnet/core/sdk) disponível no Serviço de Aplicativo é de 32 bits, mas é possível implantar aplicativos de 64 bits compilados localmente usando o console do [Kudu](https://github.com/projectkudu/kudu/wiki) ou o processo de publicação do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68f7f-126">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="68f7f-127">Para obter mais informações, consulte a seção [Publicar e implantar o aplicativo](#publish-and-deploy-the-app).</span><span class="sxs-lookup"><span data-stu-id="68f7f-127">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="68f7f-128">Para aplicativos com dependências nativas, os runtimes para aplicativos de 32 bits (x86) estão presentes no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="68f7f-128">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="68f7f-129">O [SDK do .NET Core](/dotnet/core/sdk) disponível no Serviço de Aplicativo é de 32 bits.</span><span class="sxs-lookup"><span data-stu-id="68f7f-129">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="68f7f-130">Para obter mais informações sobre os componentes do .NET Core Framework e os métodos de distribuição, como informações sobre o tempo de execução do .NET Core e o SDK do .NET Core, consulte [sobre o .NET Core: composição](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="68f7f-130">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="68f7f-131">Pacotes</span><span class="sxs-lookup"><span data-stu-id="68f7f-131">Packages</span></span>

<span data-ttu-id="68f7f-132">Incluem os seguintes pacotes NuGet para fornecer recursos de registro automático em log para aplicativos implantados no Serviço de Aplicativo do Azure:</span><span class="sxs-lookup"><span data-stu-id="68f7f-132">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="68f7f-133">O [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) usa [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) para fornecer integração leve do ASP.NET Core com o Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="68f7f-133">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="68f7f-134">Os recursos de registro em log adicionais são fornecidos pelo pacote `Microsoft.AspNetCore.AzureAppServicesIntegration`.</span><span class="sxs-lookup"><span data-stu-id="68f7f-134">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="68f7f-135">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executa [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) para adicionar provedores de log de diagnósticos do Serviço de Aplicativo do Azure no pacote `Microsoft.Extensions.Logging.AzureAppServices`.</span><span class="sxs-lookup"><span data-stu-id="68f7f-135">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="68f7f-136">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) fornece implementações de agente para dar suporte a recursos de streaming de log e logs de diagnóstico do Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="68f7f-136">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="68f7f-137">Os pacotes anteriores não estão disponíveis no [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="68f7f-137">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="68f7f-138">Os aplicativos que são direcionados ao .NET Framework ou fazem referência a um metapacote `Microsoft.AspNetCore.App` precisam referenciar explicitamente os pacotes individuais no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68f7f-138">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="68f7f-139">Substituir a configuração do aplicativo no Portal do Azure</span><span class="sxs-lookup"><span data-stu-id="68f7f-139">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="68f7f-140">As configurações do aplicativo no portal do Azure permitem definir variáveis de ambiente para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68f7f-140">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="68f7f-141">As variáveis de ambiente podem ser consumidas pelo [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="68f7f-141">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="68f7f-142">Quando uma configuração de aplicativo é criada ou modificada no Portal do Azure e o botão **Salvar** é selecionado, o Aplicativo Azure é reiniciado.</span><span class="sxs-lookup"><span data-stu-id="68f7f-142">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="68f7f-143">A variável de ambiente estará disponível para o aplicativo após o serviço ser reiniciado.</span><span class="sxs-lookup"><span data-stu-id="68f7f-143">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68f7f-144">Quando o aplicativo usa o [host genérico](xref:fundamentals/host/generic-host), as variáveis de ambiente não são carregadas na configuração do aplicativo por padrão, e o provedor de configuração deve ser adicionado pelo desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="68f7f-144">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="68f7f-145">O desenvolvedor determina o prefixo da variável de ambiente quando o provedor de configuração é adicionado.</span><span class="sxs-lookup"><span data-stu-id="68f7f-145">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="68f7f-146">Para saber mais, confira <xref:fundamentals/host/generic-host> e o [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="68f7f-146">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68f7f-147">Quando um aplicativo cria o host usando o [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), as variáveis de ambiente que configuram o host usam o prefixo `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="68f7f-147">When an app builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="68f7f-148">Para saber mais, confira <xref:fundamentals/host/web-host> e o [Provedor de configuração de variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="68f7f-148">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="68f7f-149">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="68f7f-149">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="68f7f-150">O [Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), que configura o Middleware de cabeçalhos encaminhados ao hospedar [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model), e o módulo do ASP.NET Core são configurados para encaminhar o esquema (HTTP/HTTPS) e o endereço IP remoto de onde a solicitação foi originada.</span><span class="sxs-lookup"><span data-stu-id="68f7f-150">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="68f7f-151">Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy adicionais e balanceadores de carga.</span><span class="sxs-lookup"><span data-stu-id="68f7f-151">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="68f7f-152">Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="68f7f-152">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="68f7f-153">Monitoramento e registro em log</span><span class="sxs-lookup"><span data-stu-id="68f7f-153">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68f7f-154">Os aplicativos ASP.NET Core implantados no Serviço de Aplicativo recebem automaticamente uma extensão do Serviço de Aplicativo, **Integração de Log do ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-154">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="68f7f-155">A extensão habilita a integração de log para aplicativos ASP.NET Core no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="68f7f-155">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68f7f-156">Os aplicativos ASP.NET Core implantados no Serviço de Aplicativo recebem automaticamente uma extensão do Serviço de Aplicativo, **Extensões de Log do ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-156">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="68f7f-157">A extensão habilita a integração de log para aplicativos ASP.NET Core no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="68f7f-157">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="68f7f-158">Para monitoramento, registro em log e informações de solução de problemas, veja os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="68f7f-158">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="68f7f-159">Monitore aplicativos no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="68f7f-159">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="68f7f-160">Saiba como examinar as cotas e métricas para aplicativos e planos do Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68f7f-160">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="68f7f-161">Habilite log de diagnósticos para aplicativos no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="68f7f-161">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="68f7f-162">Descubra como habilitar e acessar o log de diagnósticos para os códigos de status HTTP, solicitações com falha e atividade do servidor Web.</span><span class="sxs-lookup"><span data-stu-id="68f7f-162">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="68f7f-163">Entenda as abordagens comuns para o tratamento de erros em aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68f7f-163">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="68f7f-164">Saiba como diagnosticar problemas com implantações do Serviço de Aplicativo do Azure com aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68f7f-164">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="68f7f-165">Consulte os erros comuns de configuração de implantação para aplicativos hospedados pelo Serviço de Aplicativo do Azure/IIS com orientação para solução de problemas.</span><span class="sxs-lookup"><span data-stu-id="68f7f-165">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="68f7f-166">Anel de chave de proteção de dados e slots de implantação</span><span class="sxs-lookup"><span data-stu-id="68f7f-166">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="68f7f-167">[Chaves de proteção de dados](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) são mantidas na pasta *%HOME%\ASP.NET\DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="68f7f-167">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="68f7f-168">Essa pasta é apoiada pelo repositório de rede e é sincronizada em todos os computadores que hospedam o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68f7f-168">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="68f7f-169">As chaves não são protegidas em repouso.</span><span class="sxs-lookup"><span data-stu-id="68f7f-169">Keys aren't protected at rest.</span></span> <span data-ttu-id="68f7f-170">Essa pasta fornece o anel de chave para todas as instâncias de um aplicativo em um único slot de implantação.</span><span class="sxs-lookup"><span data-stu-id="68f7f-170">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="68f7f-171">Slots de implantação separados, como de preparo e produção, não compartilham um anel de chave.</span><span class="sxs-lookup"><span data-stu-id="68f7f-171">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="68f7f-172">Quando ocorre a troca entre os slots de implantação, nenhum sistema que usa a proteção de dados consegue descriptografar dados armazenados usando o anel de chave dentro do slot anterior.</span><span class="sxs-lookup"><span data-stu-id="68f7f-172">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="68f7f-173">O middleware de cookie do ASP.NET usa a proteção de dados para proteger seus cookies.</span><span class="sxs-lookup"><span data-stu-id="68f7f-173">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="68f7f-174">Com isso, os usuários são desconectados de um aplicativo que usa o Middleware de Cookie padrão do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="68f7f-174">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="68f7f-175">Para uma solução de anel de chave independente de slot, use um provedor de anel de chave externo, como:</span><span class="sxs-lookup"><span data-stu-id="68f7f-175">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="68f7f-176">Armazenamento do Blobs do Azure</span><span class="sxs-lookup"><span data-stu-id="68f7f-176">Azure Blob Storage</span></span>
* <span data-ttu-id="68f7f-177">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="68f7f-177">Azure Key Vault</span></span>
* <span data-ttu-id="68f7f-178">Repositório SQL</span><span class="sxs-lookup"><span data-stu-id="68f7f-178">SQL store</span></span>
* <span data-ttu-id="68f7f-179">Cache redis</span><span class="sxs-lookup"><span data-stu-id="68f7f-179">Redis cache</span></span>

<span data-ttu-id="68f7f-180">Para obter mais informações, consulte <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="68f7f-180">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-aspnet-core-30-to-azure-app-service"></a><span data-ttu-id="68f7f-181">Implantar o ASP.NET Core 3,0 no serviço Azure App</span><span class="sxs-lookup"><span data-stu-id="68f7f-181">Deploy ASP.NET Core 3.0 to Azure App Service</span></span>

<span data-ttu-id="68f7f-182">ASP.NET Core 3,0 tem suporte no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="68f7f-182">ASP.NET Core 3.0 is supported on Azure App Service.</span></span> <span data-ttu-id="68f7f-183">Para implantar uma versão de visualização de versões do .NET Core posteriores ao .NET Core 3,0, use uma das técnicas a seguir.</span><span class="sxs-lookup"><span data-stu-id="68f7f-183">To deploy a preview release of a .NET Core version later than .NET Core 3.0, use one of the following techniques.</span></span> <span data-ttu-id="68f7f-184">Essas abordagens também são usadas quando o tempo de execução está disponível, mas o SDK não foi instalado no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="68f7f-184">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="68f7f-185">Especifique a versão de SDK do .NET Core usando Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="68f7f-185">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* <span data-ttu-id="68f7f-186">[Implantar um aplicativo autossuficiente em versão prévia](#deploy-a-self-contained-preview-app).</span><span class="sxs-lookup"><span data-stu-id="68f7f-186">[Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).</span></span>
* <span data-ttu-id="68f7f-187">[Usar o Docker com aplicativos Web para contêineres](#use-docker-with-web-apps-for-containers).</span><span class="sxs-lookup"><span data-stu-id="68f7f-187">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>
* <span data-ttu-id="68f7f-188">[Instalar a extensão de site da versão prévia](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="68f7f-188">[Install the preview site extension](#install-the-preview-site-extension).</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="68f7f-189">Especifique a versão de SDK do .NET Core usando Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="68f7f-189">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="68f7f-190">Use [Azure app cenários de CI/CD de serviço](/azure/app-service/deploy-continuous-deployment) para configurar uma compilação de integração contínua com o Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="68f7f-190">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="68f7f-191">Depois que a compilação DevOps do Azure for criada, configure opcionalmente a compilação para usar uma versão específica do SDK.</span><span class="sxs-lookup"><span data-stu-id="68f7f-191">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="68f7f-192">Especificar a versão de SDK do .NET Core</span><span class="sxs-lookup"><span data-stu-id="68f7f-192">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="68f7f-193">Ao usar o centro de implantação do serviço de aplicativo para criar uma compilação DevOps do Azure, o pipeline de compilação padrão inclui etapas para `Restore`, `Build`, `Test`e `Publish`.</span><span class="sxs-lookup"><span data-stu-id="68f7f-193">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="68f7f-194">Para especificar a versão do SDK, selecione o botão **Adicionar (+)** na lista de trabalhos do agente para adicionar uma nova etapa.</span><span class="sxs-lookup"><span data-stu-id="68f7f-194">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="68f7f-195">Pesquise **SDK do .NET Core** na barra de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="68f7f-195">Search for **.NET Core SDK** in the search bar.</span></span> 

![Adicionar a etapa de SDK do .NET Core](index/add-sdk-step.png)

<span data-ttu-id="68f7f-197">Mova a etapa para a primeira posição na compilação para que as etapas a seguir usem a versão especificada do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="68f7f-197">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="68f7f-198">Especifique a versão do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="68f7f-198">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="68f7f-199">Neste exemplo, o SDK é definido como `3.0.100`.</span><span class="sxs-lookup"><span data-stu-id="68f7f-199">In this example, the SDK is set to `3.0.100`.</span></span>

![Etapa do SDK concluída](index/sdk-step-first-place.png)

<span data-ttu-id="68f7f-201">Para publicar uma [SCD (implantação autônoma)](/dotnet/core/deploying/#self-contained-deployments-scd), configure o SCD na etapa `Publish` e forneça o [RID (identificador de tempo de execução)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="68f7f-201">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Publicação independente](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="68f7f-203">Implantar um aplicativo autossuficiente em versão prévia</span><span class="sxs-lookup"><span data-stu-id="68f7f-203">Deploy a self-contained preview app</span></span>

<span data-ttu-id="68f7f-204">Uma [SCD (implantação autocontida)](/dotnet/core/deploying/#self-contained-deployments-scd) voltada para um tempo de execução de versão prévia transporta o tempo de execução da versão prévia na implantação.</span><span class="sxs-lookup"><span data-stu-id="68f7f-204">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="68f7f-205">Ao implantar um aplicativo autocontido:</span><span class="sxs-lookup"><span data-stu-id="68f7f-205">When deploying a self-contained app:</span></span>

* <span data-ttu-id="68f7f-206">O site no Serviço de Aplicativo do Azure não exige a [extensão de site da versão prévia](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="68f7f-206">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="68f7f-207">O aplicativo precisa ser publicado após uma abordagem diferente da publicação em uma [FDD (implantação dependente de estrutura)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="68f7f-207">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="68f7f-208">Siga as orientações fornecidas na seção [Implantar o aplicativo autossuficiente](#deploy-the-app-self-contained).</span><span class="sxs-lookup"><span data-stu-id="68f7f-208">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="68f7f-209">Usar o Docker com aplicativos Web para contêineres</span><span class="sxs-lookup"><span data-stu-id="68f7f-209">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="68f7f-210">O [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contém as imagens de versão prévia do Docker mais recentes.</span><span class="sxs-lookup"><span data-stu-id="68f7f-210">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="68f7f-211">As imagens podem ser usadas como uma imagem de base.</span><span class="sxs-lookup"><span data-stu-id="68f7f-211">The images can be used as a base image.</span></span> <span data-ttu-id="68f7f-212">Use a imagem e implante aplicativos Web para contêineres normalmente.</span><span class="sxs-lookup"><span data-stu-id="68f7f-212">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="68f7f-213">Instalar a extensão de site de visualização</span><span class="sxs-lookup"><span data-stu-id="68f7f-213">Install the preview site extension</span></span>

<span data-ttu-id="68f7f-214">Se houver problemas ao usar a extensão de site de visualização, abra um [aspnet/AspNetCore](https://github.com/aspnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="68f7f-214">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="68f7f-215">No portal do Azure, navegue até o Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68f7f-215">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="68f7f-216">Selecione o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="68f7f-216">Select the web app.</span></span>
1. <span data-ttu-id="68f7f-217">Insira "ex" na caixa de pesquisa para filtrar por "Extensões" ou role para baixo na lista de ferramentas de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="68f7f-217">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="68f7f-218">Selecione **Extensões**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-218">Select **Extensions**.</span></span>
1. <span data-ttu-id="68f7f-219">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-219">Select **Add**.</span></span>
1. <span data-ttu-id="68f7f-220">Selecione a extensão **Tempo de execução do ASP.NET Core {X.Y} ({x64|x86})** na lista, em que `{X.Y}` é a versão prévia do ASP.NET Core e `{x64|x86}` especifica a plataforma.</span><span class="sxs-lookup"><span data-stu-id="68f7f-220">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="68f7f-221">Selecione **OK** para aceitar os termos legais.</span><span class="sxs-lookup"><span data-stu-id="68f7f-221">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="68f7f-222">Selecione **OK** para instalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="68f7f-222">Select **OK** to install the extension.</span></span>

<span data-ttu-id="68f7f-223">Quando a operação for concluída, a versão prévia mais recente do .NET Core será instalada.</span><span class="sxs-lookup"><span data-stu-id="68f7f-223">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="68f7f-224">Verifique a instalação:</span><span class="sxs-lookup"><span data-stu-id="68f7f-224">Verify the installation:</span></span>

1. <span data-ttu-id="68f7f-225">Selecione **Ferramentas Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-225">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="68f7f-226">Selecione **Acessar** em **Ferramentas Avançadas**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-226">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="68f7f-227">Selecione o item de menu **Console de depuração** > **PowerShell**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-227">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="68f7f-228">No prompt do PowerShell, execute o seguinte comando.</span><span class="sxs-lookup"><span data-stu-id="68f7f-228">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="68f7f-229">Substitua a versão do tempo de execução do ASP.NET Core por `{X.Y}` e a plataforma por `{PLATFORM}` no comando:</span><span class="sxs-lookup"><span data-stu-id="68f7f-229">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="68f7f-230">O comando retornará `True` quando o tempo de execução da versão prévia x64 estiver instalado.</span><span class="sxs-lookup"><span data-stu-id="68f7f-230">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="68f7f-231">A arquitetura da plataforma (x86/x64) de um aplicativo dos Serviços de Aplicativos é definida nas configurações do aplicativo no portal do Azure para aplicativos hospedados em um nível de hospedagem de computação da série A ou melhor.</span><span class="sxs-lookup"><span data-stu-id="68f7f-231">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute or better hosting tier.</span></span> <span data-ttu-id="68f7f-232">Se o aplicativo for executado no modo em processo e a arquitetura da plataforma estiver configurada para 64 bits (x64), o Módulo do ASP.NET Core usará o runtime da versão prévia de 64 bits, se estiver presente.</span><span class="sxs-lookup"><span data-stu-id="68f7f-232">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="68f7f-233">Instale a extensão **Tempo de execução do ASP.NET Core {X.Y} (x64)** .</span><span class="sxs-lookup"><span data-stu-id="68f7f-233">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension.</span></span>
>
> <span data-ttu-id="68f7f-234">Depois de instalar o runtime da versão prévia x64, execute o seguinte comando na janela de comando do Kudu PowerShell para verificar a instalação.</span><span class="sxs-lookup"><span data-stu-id="68f7f-234">After installing the x64 preview runtime, run the following command in the Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="68f7f-235">Substitua a versão de tempo de execução do ASP.NET Core por `{X.Y}` no comando:</span><span class="sxs-lookup"><span data-stu-id="68f7f-235">Substitute the ASP.NET Core runtime version for `{X.Y}` in the command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="68f7f-236">O comando retornará `True` quando o tempo de execução da versão prévia x64 estiver instalado.</span><span class="sxs-lookup"><span data-stu-id="68f7f-236">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="68f7f-237">As **Extensões do ASP.NET Core** habilitam uma funcionalidade adicional para o ASP.NET Core nos Serviços de Aplicativo do Azure, como a habilitação do registro em log do Azure.</span><span class="sxs-lookup"><span data-stu-id="68f7f-237">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="68f7f-238">A extensão é instalada automaticamente durante a implantação do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68f7f-238">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="68f7f-239">Se a extensão não estiver instalada, instale-a para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68f7f-239">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="68f7f-240">**Usar a extensão de site de visualização com um modelo do ARM**</span><span class="sxs-lookup"><span data-stu-id="68f7f-240">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="68f7f-241">Se um modelo do ARM for usado para criar e implantar aplicativos, o tipo de recurso `siteextensions` poderá ser usado para adicionar a extensão de site a um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="68f7f-241">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="68f7f-242">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="68f7f-242">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="68f7f-243">Publicar e implantar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="68f7f-243">Publish and deploy the app</span></span>

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="68f7f-244">Implantar o aplicativo dependente de estrutura de aplicativos</span><span class="sxs-lookup"><span data-stu-id="68f7f-244">Deploy the app framework-dependent</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="68f7f-245">Para uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd) de 64 bits:</span><span class="sxs-lookup"><span data-stu-id="68f7f-245">For a 64-bit [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

* <span data-ttu-id="68f7f-246">Use um SDK do .NET Core de 64 bits para compilar um aplicativo de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="68f7f-246">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="68f7f-247">Configure a **Plataforma** como **64 bits** na seção **Configuração** > **Configurações gerais** do Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68f7f-247">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="68f7f-248">O aplicativo deve usar um plano de serviço Básico ou superior para possibilitar a escolha do número de bits da plataforma.</span><span class="sxs-lookup"><span data-stu-id="68f7f-248">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68f7f-249">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68f7f-249">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="68f7f-250">Selecione **Compilar** > **Publicar {Application Name}** na barra de ferramentas do Visual Studio ou clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-250">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="68f7f-251">Na caixa de diálogo **Escolher um destino de publicação**, confirme se o **Serviço de Aplicativo** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="68f7f-251">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="68f7f-252">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-252">Select **Advanced**.</span></span> <span data-ttu-id="68f7f-253">A caixa de diálogo **Publicar** será aberta.</span><span class="sxs-lookup"><span data-stu-id="68f7f-253">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="68f7f-254">Na caixa de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="68f7f-254">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="68f7f-255">Confirme se a configuração **Versão** está selecionada.</span><span class="sxs-lookup"><span data-stu-id="68f7f-255">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="68f7f-256">Abra a lista suspensa **Modo de Implantação** e selecione **Dependente de Estrutura**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-256">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="68f7f-257">Selecione **Portátil** como o **Tempo de Execução de Destino**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-257">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="68f7f-258">Se você precisar remover arquivos adicionais após a implantação, abra as **Opções de Publicação do Arquivo** e marque a caixa de seleção para remover arquivos adicionais no destino.</span><span class="sxs-lookup"><span data-stu-id="68f7f-258">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="68f7f-259">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-259">Select **Save**.</span></span>
1. <span data-ttu-id="68f7f-260">Crie um novo site ou atualize um site existente seguindo as solicitações restantes do assistente de publicação.</span><span class="sxs-lookup"><span data-stu-id="68f7f-260">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="68f7f-261">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="68f7f-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="68f7f-262">No arquivo de projeto, não especifique um [RID (Identificador de Tempo de Execução)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="68f7f-262">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="68f7f-263">Em um shell de comando, publique o aplicativo na Configuração de versão usando o comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="68f7f-263">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="68f7f-264">No exemplo a seguir, o aplicativo é publicado como dependente de estrutura:</span><span class="sxs-lookup"><span data-stu-id="68f7f-264">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="68f7f-265">Mova o conteúdo do diretório *bin/Release/{TARGET FRAMEWORK}/publish* para o site no Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68f7f-265">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="68f7f-266">Se for arrastar o conteúdo da pasta *publish* de seu disco rígido local ou do compartilhamento de rede diretamente para o Serviço de Aplicativo no console do [Kudu](https://github.com/projectkudu/kudu/wiki), arraste os arquivos para a pasta `D:\home\site\wwwroot` no console do Kudu.</span><span class="sxs-lookup"><span data-stu-id="68f7f-266">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="68f7f-267">Implantar o aplicativo autocontido</span><span class="sxs-lookup"><span data-stu-id="68f7f-267">Deploy the app self-contained</span></span>

<span data-ttu-id="68f7f-268">Use o Visual Studio ou as ferramentas da CLI (interface de linha de comando) para uma [SCD (implantação autossuficiente)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="68f7f-268">Use Visual Studio or the command-line interface (CLI) tools for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="68f7f-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68f7f-269">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="68f7f-270">Selecione **Compilar** > **Publicar {Application Name}** na barra de ferramentas do Visual Studio ou clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-270">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="68f7f-271">Na caixa de diálogo **Escolher um destino de publicação**, confirme se o **Serviço de Aplicativo** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="68f7f-271">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="68f7f-272">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-272">Select **Advanced**.</span></span> <span data-ttu-id="68f7f-273">A caixa de diálogo **Publicar** será aberta.</span><span class="sxs-lookup"><span data-stu-id="68f7f-273">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="68f7f-274">Na caixa de diálogo **Publicar**:</span><span class="sxs-lookup"><span data-stu-id="68f7f-274">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="68f7f-275">Confirme se a configuração **Versão** está selecionada.</span><span class="sxs-lookup"><span data-stu-id="68f7f-275">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="68f7f-276">Abra a lista suspensa **Modo de Implantação** e selecione **Autocontido**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-276">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="68f7f-277">Selecione o tempo de execução de destino na lista suspensa **Tempo de Execução de Destino**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-277">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="68f7f-278">O padrão é `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="68f7f-278">The default is `win-x86`.</span></span>
   * <span data-ttu-id="68f7f-279">Se você precisar remover arquivos adicionais após a implantação, abra as **Opções de Publicação do Arquivo** e marque a caixa de seleção para remover arquivos adicionais no destino.</span><span class="sxs-lookup"><span data-stu-id="68f7f-279">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="68f7f-280">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="68f7f-280">Select **Save**.</span></span>
1. <span data-ttu-id="68f7f-281">Crie um novo site ou atualize um site existente seguindo as solicitações restantes do assistente de publicação.</span><span class="sxs-lookup"><span data-stu-id="68f7f-281">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="68f7f-282">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="68f7f-282">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="68f7f-283">No arquivo de projeto, especifique um ou mais [RIDs (identificadores de tempo de execução)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="68f7f-283">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="68f7f-284">Use `<RuntimeIdentifier>` (singular) para um único RID ou use `<RuntimeIdentifiers>` (plural) para fornecer uma lista de RIDs delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="68f7f-284">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="68f7f-285">No exemplo a seguir, o RID `win-x86` é especificado:</span><span class="sxs-lookup"><span data-stu-id="68f7f-285">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="68f7f-286">Em um shell de comando, publique o aplicativo na configuração de Versão do tempo de execução do host com o comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="68f7f-286">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="68f7f-287">No exemplo a seguir, o aplicativo é publicado para o RID `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="68f7f-287">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="68f7f-288">O RID fornecido para a opção `--runtime` precisa ser fornecida na propriedade `<RuntimeIdentifier>` (ou `<RuntimeIdentifiers>`) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="68f7f-288">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="68f7f-289">Mova o conteúdo do diretório *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* para o site no Serviço de Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="68f7f-289">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="68f7f-290">Se for arrastar o conteúdo da pasta *publish* de seu disco rígido local ou do compartilhamento de rede diretamente para o Serviço de Aplicativo no console do Kudu, arraste os arquivos para a pasta `D:\home\site\wwwroot` no console do Kudu.</span><span class="sxs-lookup"><span data-stu-id="68f7f-290">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="68f7f-291">Configurações de protocolo (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="68f7f-291">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="68f7f-292">As associações de protocolo de segurança permitem que você especifique um certificado a ser usado ao responder a solicitações em HTTPS.</span><span class="sxs-lookup"><span data-stu-id="68f7f-292">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="68f7f-293">A associação requer um certificado privado válido ( *.pfx*) emitido para o nome do host específico.</span><span class="sxs-lookup"><span data-stu-id="68f7f-293">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="68f7f-294">Para obter mais informações, consulte [tutorial: associar um certificado SSL personalizado existente ao serviço de Azure app](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="68f7f-294">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="68f7f-295">Transformação do Web.config</span><span class="sxs-lookup"><span data-stu-id="68f7f-295">Transform web.config</span></span>

<span data-ttu-id="68f7f-296">Se você precisar transformar o *Web.config* em publicação (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), consulte <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="68f7f-296">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68f7f-297">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="68f7f-297">Additional resources</span></span>

* [<span data-ttu-id="68f7f-298">Visão geral do Serviço de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="68f7f-298">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="68f7f-299">Serviço de Aplicativo do Azure: o melhor lugar para hospedar seus aplicativos .NET (vídeo de visão geral com 55 minutos)</span><span class="sxs-lookup"><span data-stu-id="68f7f-299">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="68f7f-300">Azure Friday: experiência de diagnóstico e solução de problemas do Serviço de Aplicativo do Azure (vídeo com 12 minutos)</span><span class="sxs-lookup"><span data-stu-id="68f7f-300">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="68f7f-301">Visão geral de diagnóstico do Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="68f7f-301">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="68f7f-302">O Serviço de Aplicativo do Azure no Windows Server usa o [IIS (Serviços de Informações da Internet)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="68f7f-302">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="68f7f-303">Os tópicos a seguir estão relacionados com a tecnologia subjacente do IIS:</span><span class="sxs-lookup"><span data-stu-id="68f7f-303">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="68f7f-304">Windows Server – conteúdo do administrador de TI para versões atuais e anteriores</span><span class="sxs-lookup"><span data-stu-id="68f7f-304">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
