---
title: Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core
author: guardrex
description: Descubra o suporte para depuração de aplicativos do ASP.NET Core durante a execução com IIS no Windows Server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: d2b2456c7ab6b72f2270b6edc17000695061cc2b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887751"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="331d0-103">Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="331d0-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="331d0-104">Por [Sourabh Shirhatti](https://twitter.com/sshirhatti) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="331d0-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="331d0-105">Este artigo descreve o suporte do [Visual Studio](https://visualstudio.microsoft.com) para a depuração de aplicativos do ASP.NET Core em execução com o IIS no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="331d0-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="331d0-106">Este tópico orienta como habilitar esse cenário e configurar um projeto.</span><span class="sxs-lookup"><span data-stu-id="331d0-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="331d0-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="331d0-107">Prerequisites</span></span>

* [<span data-ttu-id="331d0-108">Visual Studio para Windows</span><span class="sxs-lookup"><span data-stu-id="331d0-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="331d0-109">Carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="331d0-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="331d0-110">Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**</span><span class="sxs-lookup"><span data-stu-id="331d0-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="331d0-111">Certificado de segurança X.509 (para o suporte a HTTPS)</span><span class="sxs-lookup"><span data-stu-id="331d0-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="331d0-112">Habilitar o IIS</span><span class="sxs-lookup"><span data-stu-id="331d0-112">Enable IIS</span></span>

1. <span data-ttu-id="331d0-113">No Windows, navegue até **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="331d0-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="331d0-114">Selecione a caixa de seleção **Serviços de Informações da Internet**.</span><span class="sxs-lookup"><span data-stu-id="331d0-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="331d0-115">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="331d0-115">Select **OK**.</span></span>

<span data-ttu-id="331d0-116">A instalação do IIS pode exigir uma reinicialização do sistema.</span><span class="sxs-lookup"><span data-stu-id="331d0-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="331d0-117">Configurar o IIS</span><span class="sxs-lookup"><span data-stu-id="331d0-117">Configure IIS</span></span>

<span data-ttu-id="331d0-118">O IIS deve ter um site configurado com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="331d0-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="331d0-119">**Nome do host** &ndash; Normalmente, o **Site padrão** é usado com um **nome do Host** de `localhost`.</span><span class="sxs-lookup"><span data-stu-id="331d0-119">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="331d0-120">No entanto, qualquer site válido do IIS com um nome de host exclusivo funciona.</span><span class="sxs-lookup"><span data-stu-id="331d0-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="331d0-121">**Associação do site**</span><span class="sxs-lookup"><span data-stu-id="331d0-121">**Site Binding**</span></span>
  * <span data-ttu-id="331d0-122">Para aplicativos que exijam HTTPS, crie uma associação à porta 443 com um certificado.</span><span class="sxs-lookup"><span data-stu-id="331d0-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="331d0-123">Tipicamente, o **Certificado de Desenvolvimento do IIS Express** é usado, mas qualquer certificado válido funciona.</span><span class="sxs-lookup"><span data-stu-id="331d0-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="331d0-124">Para aplicativos que usam HTTP, confirme a existência de uma associação à porta 80 ou crie uma para um novo site.</span><span class="sxs-lookup"><span data-stu-id="331d0-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="331d0-125">Uso de uma associação simples para HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="331d0-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="331d0-126">**Não há suporte para a associação simultânea às portas HTTP e HTTPS.**</span><span class="sxs-lookup"><span data-stu-id="331d0-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="331d0-127">Habilitar o suporte ao IIS no tempo de desenvolvimento no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="331d0-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="331d0-128">Inicie o Instalador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="331d0-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="331d0-129">Selecione **Modificar** para a instalação do Visual Studio que você planeja usar para o suporte de tempo de desenvolvimento do IIS.</span><span class="sxs-lookup"><span data-stu-id="331d0-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="331d0-130">Para a carga de trabalho de **desenvolvimento da web e ASP.NET**, localize e instale o componente de**suporte IIS ao tempo de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="331d0-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="331d0-131">O componente está listado na seção **Opcional**, em **Suporte IIS ao tempo de desenvolvimento** no painel **Detalhes da instalação** à direita das cargas de trabalho.</span><span class="sxs-lookup"><span data-stu-id="331d0-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="331d0-132">O componente instala o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), que é um módulo nativo do IIS necessário para executar aplicativos ASP.NET Core com o IIS.</span><span class="sxs-lookup"><span data-stu-id="331d0-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="331d0-133">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="331d0-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="331d0-134">Redirecionamento para HTTPS</span><span class="sxs-lookup"><span data-stu-id="331d0-134">HTTPS redirection</span></span>

<span data-ttu-id="331d0-135">Para um novo projeto que exija HTTPS, selecione a caixa de seleção para **Configurar para HTTPS** na janela **Criar um novo Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="331d0-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="331d0-136">A marcação da caixa de seleção adiciona ao aplicativo um [Redirecionamento de HTTPS e Middleware HSTS](xref:security/enforcing-ssl) quando ele é criado.</span><span class="sxs-lookup"><span data-stu-id="331d0-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="331d0-137">Para um projeto existente que exija HTTPS, use o Redirecionamento de HTTPS e Middleware HSTS em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="331d0-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="331d0-138">Para obter mais informações, consulte <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="331d0-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="331d0-139">Para um projeto que usa HTTP, o [Redirecionamento de HTTPS e o Middleware HSTS](xref:security/enforcing-ssl) não são adicionados ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="331d0-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="331d0-140">Nenhuma configuração de aplicativo é necessária.</span><span class="sxs-lookup"><span data-stu-id="331d0-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="331d0-141">Perfil de inicialização do IIS</span><span class="sxs-lookup"><span data-stu-id="331d0-141">IIS launch profile</span></span>

<span data-ttu-id="331d0-142">Crie um novo perfil de inicialização para adicionar suporte ao IIS no tempo de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="331d0-142">Create a new launch profile to add development-time IIS support:</span></span>

::: moniker range=">= aspnetcore-3.0"

1. <span data-ttu-id="331d0-143">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções**.</span><span class="sxs-lookup"><span data-stu-id="331d0-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="331d0-144">Selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="331d0-144">Select **Properties**.</span></span> <span data-ttu-id="331d0-145">Abra a guia **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="331d0-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="331d0-146">Para **Perfil**, selecione o botão **Novo**.</span><span class="sxs-lookup"><span data-stu-id="331d0-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="331d0-147">Nomeie o perfil "IIS" na janela pop-up.</span><span class="sxs-lookup"><span data-stu-id="331d0-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="331d0-148">Selecione **OK** para criar o perfil.</span><span class="sxs-lookup"><span data-stu-id="331d0-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="331d0-149">Para a configuração **Iniciar**, selecione **IIS** da lista.</span><span class="sxs-lookup"><span data-stu-id="331d0-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="331d0-150">Selecione a caixa de seleção **Iniciar navegador** e forneça a URL de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="331d0-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="331d0-151">Quando o aplicativo exigir HTTPS, use um ponto de extremidade HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="331d0-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="331d0-152">Para HTTP, use um ponto de extremidade HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="331d0-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="331d0-153">Forneça o mesmo nome de host e porta usados pela [configuração do IIS especificada anteriormente](#configure-iis), normalmente `localhost`.</span><span class="sxs-lookup"><span data-stu-id="331d0-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="331d0-154">Forneça o nome do aplicativo no final da URL.</span><span class="sxs-lookup"><span data-stu-id="331d0-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="331d0-155">Por exemplo, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) são URLs de ponto de extremidade válidas.</span><span class="sxs-lookup"><span data-stu-id="331d0-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="331d0-156">Na seção **Variáveis de ambiente**, selecione o botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="331d0-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="331d0-157">Forneça uma variável de ambiente com um **Nome** de `ASPNETCORE_ENVIRONMENT` e um **Valor** de `Development`.</span><span class="sxs-lookup"><span data-stu-id="331d0-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="331d0-158">Na área de **Configurações do servidor Web**, defina a **URL do aplicativo** com o mesmo valor usado para a URL de ponto de extremidade **Iniciar navegador**.</span><span class="sxs-lookup"><span data-stu-id="331d0-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="331d0-159">Para a configuração de **Modelo de hospedagem** no Visual Studio 2019 ou posterior, selecione **Padrão** para usar o modelo de hospedagem usado pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="331d0-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="331d0-160">Se o projeto definir a propriedade `<AspNetCoreHostingModel>` no arquivo de projeto, o valor da propriedade (`InProcess` ou `OutOfProcess`) será usado.</span><span class="sxs-lookup"><span data-stu-id="331d0-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="331d0-161">Se a propriedade não estiver presente, o modelo do aplicativo de hospedagem padrão será usado, que está em processo.</span><span class="sxs-lookup"><span data-stu-id="331d0-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="331d0-162">Se o aplicativo exigir uma configuração explícita de modelo de hospedagem diferente da do modelo de hospedagem normal do aplicativo, defina o **Modelo de hospedagem** para `In Process` ou `Out Of Process`, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="331d0-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="331d0-163">Salve o perfil.</span><span class="sxs-lookup"><span data-stu-id="331d0-163">Save the profile.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

1. <span data-ttu-id="331d0-164">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções**.</span><span class="sxs-lookup"><span data-stu-id="331d0-164">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="331d0-165">Selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="331d0-165">Select **Properties**.</span></span> <span data-ttu-id="331d0-166">Abra a guia **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="331d0-166">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="331d0-167">Para **Perfil**, selecione o botão **Novo**.</span><span class="sxs-lookup"><span data-stu-id="331d0-167">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="331d0-168">Nomeie o perfil "IIS" na janela pop-up.</span><span class="sxs-lookup"><span data-stu-id="331d0-168">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="331d0-169">Selecione **OK** para criar o perfil.</span><span class="sxs-lookup"><span data-stu-id="331d0-169">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="331d0-170">Para a configuração **Iniciar**, selecione **IIS** da lista.</span><span class="sxs-lookup"><span data-stu-id="331d0-170">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="331d0-171">Selecione a caixa de seleção **Iniciar navegador** e forneça a URL de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="331d0-171">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="331d0-172">Quando o aplicativo exigir HTTPS, use um ponto de extremidade HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="331d0-172">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="331d0-173">Para HTTP, use um ponto de extremidade HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="331d0-173">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="331d0-174">Forneça o mesmo nome de host e porta usados pela [configuração do IIS especificada anteriormente](#configure-iis), normalmente `localhost`.</span><span class="sxs-lookup"><span data-stu-id="331d0-174">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="331d0-175">Forneça o nome do aplicativo no final da URL.</span><span class="sxs-lookup"><span data-stu-id="331d0-175">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="331d0-176">Por exemplo, `https://localhost/WebApplication1` (HTTPS) ou `http://localhost/WebApplication1` (HTTP) são URLs de ponto de extremidade válidas.</span><span class="sxs-lookup"><span data-stu-id="331d0-176">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="331d0-177">Na seção **Variáveis de ambiente**, selecione o botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="331d0-177">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="331d0-178">Forneça uma variável de ambiente com um **Nome** de `ASPNETCORE_ENVIRONMENT` e um **Valor** de `Development`.</span><span class="sxs-lookup"><span data-stu-id="331d0-178">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="331d0-179">Na área de **Configurações do servidor Web**, defina a **URL do aplicativo** com o mesmo valor usado para a URL de ponto de extremidade **Iniciar navegador**.</span><span class="sxs-lookup"><span data-stu-id="331d0-179">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="331d0-180">Para a configuração de **Modelo de hospedagem** no Visual Studio 2019 ou posterior, selecione **Padrão** para usar o modelo de hospedagem usado pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="331d0-180">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="331d0-181">Se o projeto definir a propriedade `<AspNetCoreHostingModel>` no arquivo de projeto, o valor da propriedade (`InProcess` ou `OutOfProcess`) será usado.</span><span class="sxs-lookup"><span data-stu-id="331d0-181">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="331d0-182">Se a propriedade não estiver presente, o modelo do aplicativo de hospedagem padrão será usado, que está fora de processo.</span><span class="sxs-lookup"><span data-stu-id="331d0-182">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="331d0-183">Se o aplicativo exigir uma configuração explícita de modelo de hospedagem diferente da do modelo de hospedagem normal do aplicativo, defina o **Modelo de hospedagem** para `In Process` ou `Out Of Process`, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="331d0-183">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="331d0-184">Salve o perfil.</span><span class="sxs-lookup"><span data-stu-id="331d0-184">Save the profile.</span></span>

::: moniker-end

<span data-ttu-id="331d0-185">Quando não estiver usando o Visual Studio, adicione manualmente um perfil de inicialização ao arquivo [launchSettings.json](http://json.schemastore.org/launchsettings) na pasta *Propriedades*.</span><span class="sxs-lookup"><span data-stu-id="331d0-185">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](http://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="331d0-186">O exemplo a seguir configura o perfil para usar o protocolo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="331d0-186">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="331d0-187">Confirme se os pontos de extremidade `applicationUrl` e `launchUrl` correspondem e usam o mesmo protocolo que a configuração de associação do IIS, seja HTTP ou HTTPS.</span><span class="sxs-lookup"><span data-stu-id="331d0-187">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="331d0-188">Executar o projeto</span><span class="sxs-lookup"><span data-stu-id="331d0-188">Run the project</span></span>

<span data-ttu-id="331d0-189">Execute o Visual Studio como um administrador:</span><span class="sxs-lookup"><span data-stu-id="331d0-189">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="331d0-190">Confirme se a lista de lista suspensa de configuração de compilação está definida para **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="331d0-190">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="331d0-191">Defina o botão Executar para o perfil do **IIS** e selecione o botão para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="331d0-191">Set the Run button to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="331d0-192">O Visual Studio poderá solicitar uma reinicialização se não estiver executando como administrador.</span><span class="sxs-lookup"><span data-stu-id="331d0-192">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="331d0-193">Se solicitado, reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="331d0-193">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="331d0-194">Se for usado um certificado de desenvolvimento não confiável, o navegador poderá exigir a criação de uma exceção para o certificado não confiável.</span><span class="sxs-lookup"><span data-stu-id="331d0-194">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="331d0-195">A depuração de uma configuração de Compilação de versão com [Apenas Meu Código](/visualstudio/debugger/just-my-code) e otimizações de compilador resulta em uma experiência inadequada.</span><span class="sxs-lookup"><span data-stu-id="331d0-195">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="331d0-196">Por exemplo, os pontos de interrupção não são atingidos.</span><span class="sxs-lookup"><span data-stu-id="331d0-196">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="331d0-197">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="331d0-197">Additional resources</span></span>

* [<span data-ttu-id="331d0-198">Introdução ao Gerenciador do IIS no IIS</span><span class="sxs-lookup"><span data-stu-id="331d0-198">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="331d0-199">Hospedar o ASP.NET Core no Windows com o IIS</span><span class="sxs-lookup"><span data-stu-id="331d0-199">Host ASP.NET Core on Windows with IIS</span></span>](xref:host-and-deploy/iis/index)
* [<span data-ttu-id="331d0-200">Introdução ao Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="331d0-200">Introduction to ASP.NET Core Module</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="331d0-201">Referência de configuração do Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="331d0-201">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="331d0-202">Impor o HTTPS</span><span class="sxs-lookup"><span data-stu-id="331d0-202">Enforce HTTPS</span></span>](xref:security/enforcing-ssl)
