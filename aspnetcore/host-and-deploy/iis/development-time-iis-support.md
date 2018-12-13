---
title: Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core
author: shirhatti
description: Descubra o suporte para depuração de aplicativos do ASP.NET Core quando executado por trás do IIS no Windows Server.
ms.author: riande
ms.custom: mvc
ms.date: 11/30/2018
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 51375e6a6bb25a469d467ca97a151abd305c1ece
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862376"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="05e4b-103">Suporte ao IIS no tempo de desenvolvimento no Visual Studio para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05e4b-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="05e4b-104">Por [Sourabh Shirhatti](https://twitter.com/sshirhatti) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="05e4b-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="05e4b-105">Este artigo descreve o suporte do [Visual Studio](https://www.visualstudio.com/vs/) a depuração de aplicativos do ASP.NET Core em execução por trás do IIS no Windows Server.</span><span class="sxs-lookup"><span data-stu-id="05e4b-105">This article describes [Visual Studio](https://www.visualstudio.com/vs/) support for debugging ASP.NET Core apps running behind IIS on Windows Server.</span></span> <span data-ttu-id="05e4b-106">Este tópico orienta você sobre como habilitar esse recurso e configurar um projeto.</span><span class="sxs-lookup"><span data-stu-id="05e4b-106">This topic walks through enabling this feature and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="05e4b-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="05e4b-107">Prerequisites</span></span>

* [<span data-ttu-id="05e4b-108">Visual Studio para Windows</span><span class="sxs-lookup"><span data-stu-id="05e4b-108">Visual Studio for Windows</span></span>](https://www.microsoft.com/net/download/windows)
* <span data-ttu-id="05e4b-109">Carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="05e4b-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="05e4b-110">Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**</span><span class="sxs-lookup"><span data-stu-id="05e4b-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="05e4b-111">Certificado de segurança X.509</span><span class="sxs-lookup"><span data-stu-id="05e4b-111">X.509 security certificate</span></span>

## <a name="enable-iis"></a><span data-ttu-id="05e4b-112">Habilitar o IIS</span><span class="sxs-lookup"><span data-stu-id="05e4b-112">Enable IIS</span></span>

1. <span data-ttu-id="05e4b-113">Navegue para **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="05e4b-113">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="05e4b-114">Selecione a caixa de seleção **Serviços de Informações da Internet**.</span><span class="sxs-lookup"><span data-stu-id="05e4b-114">Select the **Internet Information Services** check box.</span></span>

![Recursos do Windows mostrando a caixa de seleção Serviços de Informações da Internet marcada como um quadrado preto (não uma marca de seleção), indicando que alguns dos recursos do IIS estão habilitados](development-time-iis-support/_static/enable_iis.png)

<span data-ttu-id="05e4b-116">A instalação do IIS pode exigir uma reinicialização do sistema.</span><span class="sxs-lookup"><span data-stu-id="05e4b-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="05e4b-117">Configurar o IIS</span><span class="sxs-lookup"><span data-stu-id="05e4b-117">Configure IIS</span></span>

<span data-ttu-id="05e4b-118">O IIS deve ter um site configurado com o seguinte:</span><span class="sxs-lookup"><span data-stu-id="05e4b-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="05e4b-119">Um nome do host que corresponda ao nome do host da URL do perfil de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="05e4b-119">A host name that matches the app's launch profile URL host name.</span></span>
* <span data-ttu-id="05e4b-120">Associação para a porta 443, com um certificado atribuído.</span><span class="sxs-lookup"><span data-stu-id="05e4b-120">Binding for port 443 with an assigned certificate.</span></span>

<span data-ttu-id="05e4b-121">Por exemplo, o **Nome do host** para um site adicionado é definido como "localhost" (o perfil de inicialização também usará "localhost" posteriormente neste tópico).</span><span class="sxs-lookup"><span data-stu-id="05e4b-121">For example, the **Host name** for an added website is set to "localhost" (the launch profile will also use "localhost" later in this topic).</span></span> <span data-ttu-id="05e4b-122">A porta é definida para "443" (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="05e4b-122">The port is set to "443" (HTTPS).</span></span> <span data-ttu-id="05e4b-123">O **Certificado de Desenvolvimento do IIS Express** é atribuído ao site, mas nenhum certificado válido funciona:</span><span class="sxs-lookup"><span data-stu-id="05e4b-123">The **IIS Express Development Certificate** is assigned to the website, but any valid certificate works:</span></span>

![Adicione a janela Site no IIS, mostrando o conjunto de associação para o localhost na porta 443 com um certificado atribuído.](development-time-iis-support/_static/add-website-window.png)

<span data-ttu-id="05e4b-125">Se a instalação do IIS já tiver um **site da Web padrão** com um nome do host que corresponde ao nome do host da URL do perfil de inicialização do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="05e4b-125">If the IIS installation already has a **Default Web Site** with a host name that matches the app's launch profile URL host name:</span></span>

* <span data-ttu-id="05e4b-126">Adicione uma associação de porta para a porta 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="05e4b-126">Add a port binding for port 443 (HTTPS).</span></span>
* <span data-ttu-id="05e4b-127">Atribua um certificado válido para o site.</span><span class="sxs-lookup"><span data-stu-id="05e4b-127">Assign a valid certificate to the website.</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="05e4b-128">Habilitar o suporte ao IIS no tempo de desenvolvimento no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05e4b-128">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="05e4b-129">Inicie o Instalador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="05e4b-129">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="05e4b-130">Selecione o componente **Suporte ao IIS no tempo de desenvolvimento**.</span><span class="sxs-lookup"><span data-stu-id="05e4b-130">Select the **Development time IIS support** component.</span></span> <span data-ttu-id="05e4b-131">O componente está listado como opcional no painel **Resumo** para a carga de trabalho **Desenvolvimento Web e ASP.NET**.</span><span class="sxs-lookup"><span data-stu-id="05e4b-131">The component is listed as optional in the **Summary** panel for the **ASP.NET and web development** workload.</span></span> <span data-ttu-id="05e4b-132">O componente instala o [Módulo do ASP.NET Core](xref:fundamentals/servers/aspnet-core-module), que é um módulo nativo do IIS necessário para executar aplicativos ASP.NET Core com o IIS.</span><span class="sxs-lookup"><span data-stu-id="05e4b-132">The component installs the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

![Modificando os recursos do Visual Studio: a guia Cargas de Trabalho é selecionada.](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a><span data-ttu-id="05e4b-136">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="05e4b-136">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="05e4b-137">Redirecionamento para HTTPS</span><span class="sxs-lookup"><span data-stu-id="05e4b-137">HTTPS redirection</span></span>

<span data-ttu-id="05e4b-138">Para um novo projeto, selecione a caixa de seleção para **Configurar para HTTPS** na janela **Novo Aplicativo Web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="05e4b-138">For a new project, select the check box to **Configure for HTTPS** in the **New ASP.NET Core Web Application** window:</span></span>

![Janela Novo Aplicativo Web ASP.NET Core com a caixa de seleção Configurar para HTTPS selecionada.](development-time-iis-support/_static/new-app.png)

<span data-ttu-id="05e4b-140">Em um projeto existente, use o middleware de redirecionamento para HTTPS no `Startup.Configure` chamando o método de extensão [UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection):</span><span class="sxs-lookup"><span data-stu-id="05e4b-140">In an existing project, use HTTPS Redirection Middleware in `Startup.Configure` by calling the [UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection) extension method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();

    app.UseMvc();
}
```

### <a name="iis-launch-profile"></a><span data-ttu-id="05e4b-141">Perfil de inicialização do IIS</span><span class="sxs-lookup"><span data-stu-id="05e4b-141">IIS launch profile</span></span>

<span data-ttu-id="05e4b-142">Crie um novo perfil de inicialização para adicionar suporte ao IIS no tempo de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="05e4b-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="05e4b-143">Para **Perfil**, selecione o botão **Novo**.</span><span class="sxs-lookup"><span data-stu-id="05e4b-143">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="05e4b-144">Nomeie o perfil "IIS" na janela pop-up.</span><span class="sxs-lookup"><span data-stu-id="05e4b-144">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="05e4b-145">Selecione **OK** para criar o perfil.</span><span class="sxs-lookup"><span data-stu-id="05e4b-145">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="05e4b-146">Para a configuração **Iniciar**, selecione **IIS** da lista.</span><span class="sxs-lookup"><span data-stu-id="05e4b-146">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="05e4b-147">Selecione a caixa de seleção **Iniciar navegador** e forneça a URL de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="05e4b-147">Select the check box for **Launch browser** and provide the endpoint URL.</span></span> <span data-ttu-id="05e4b-148">Use o protocolo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="05e4b-148">Use the HTTPS protocol.</span></span> <span data-ttu-id="05e4b-149">Este exemplo usa `https://localhost/WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="05e4b-149">This example uses `https://localhost/WebApplication1`.</span></span>
1. <span data-ttu-id="05e4b-150">Na seção **Variáveis de ambiente**, selecione o botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="05e4b-150">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="05e4b-151">Fornecer uma variável de ambiente com uma chave `ASPNETCORE_ENVIRONMENT` e um valor `Development`.</span><span class="sxs-lookup"><span data-stu-id="05e4b-151">Provide an environment variable with a key of `ASPNETCORE_ENVIRONMENT` and a value of `Development`.</span></span>
1. <span data-ttu-id="05e4b-152">Na área **Configurações do Servidor Web**, defina a **URL do Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="05e4b-152">In the **Web Server Settings** area, set the **App URL**.</span></span> <span data-ttu-id="05e4b-153">Este exemplo usa `https://localhost/WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="05e4b-153">This example uses `https://localhost/WebApplication1`.</span></span>
1. <span data-ttu-id="05e4b-154">Salve o perfil.</span><span class="sxs-lookup"><span data-stu-id="05e4b-154">Save the profile.</span></span>

![Janela de propriedades de projeto com a guia Depurar selecionada.](development-time-iis-support/_static/project_properties.png)

<span data-ttu-id="05e4b-159">Como alternativa, adicione manualmente um perfil de inicialização para o arquivo [launchSettings.json](http://json.schemastore.org/launchsettings) no aplicativo:</span><span class="sxs-lookup"><span data-stu-id="05e4b-159">Alternatively, manually add a launch profile to the [launchSettings.json](http://json.schemastore.org/launchsettings) file in the app:</span></span>

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

## <a name="run-the-project"></a><span data-ttu-id="05e4b-160">Executar o projeto</span><span class="sxs-lookup"><span data-stu-id="05e4b-160">Run the project</span></span>

<span data-ttu-id="05e4b-161">No Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="05e4b-161">In Visual Studio:</span></span>

* <span data-ttu-id="05e4b-162">Confirme se a lista de lista suspensa de configuração de compilação está definida para **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="05e4b-162">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="05e4b-163">Defina o botão Executar para o perfil do **IIS** e selecione o botão para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="05e4b-163">Set the Run button to the **IIS** profile and select the button to start the app.</span></span>

![O botão Executar, na barra de ferramentas do VS, é definido para o perfil do IIS com a lista suspensa de configuração de compilação definida para Versão.](development-time-iis-support/_static/toolbar.png)

<span data-ttu-id="05e4b-165">O Visual Studio poderá solicitar uma reinicialização se não estiver executando como administrador.</span><span class="sxs-lookup"><span data-stu-id="05e4b-165">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="05e4b-166">Se solicitado, reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="05e4b-166">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="05e4b-167">Se for usado um certificado de desenvolvimento não confiável, o navegador poderá exigir a criação de uma exceção para o certificado não confiável.</span><span class="sxs-lookup"><span data-stu-id="05e4b-167">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="05e4b-168">A depuração de uma configuração de Compilação de versão com [Apenas Meu Código](/visualstudio/debugger/just-my-code) e otimizações de compilador resulta em uma experiência inadequada.</span><span class="sxs-lookup"><span data-stu-id="05e4b-168">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="05e4b-169">Por exemplo, os pontos de interrupção não são atingidos.</span><span class="sxs-lookup"><span data-stu-id="05e4b-169">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="05e4b-170">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="05e4b-170">Additional resources</span></span>

* [<span data-ttu-id="05e4b-171">Hospedar o ASP.NET Core no Windows com o IIS</span><span class="sxs-lookup"><span data-stu-id="05e4b-171">Host ASP.NET Core on Windows with IIS</span></span>](xref:host-and-deploy/iis/index)
* [<span data-ttu-id="05e4b-172">Introdução ao Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05e4b-172">Introduction to ASP.NET Core Module</span></span>](xref:fundamentals/servers/aspnet-core-module)
* [<span data-ttu-id="05e4b-173">Referência de configuração do Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05e4b-173">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="05e4b-174">Impor o HTTPS</span><span class="sxs-lookup"><span data-stu-id="05e4b-174">Enforce HTTPS</span></span>](xref:security/enforcing-ssl)
