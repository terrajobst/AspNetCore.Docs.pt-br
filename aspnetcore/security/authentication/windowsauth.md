---
title: Configurar a autenticação do Windows no ASP.NET Core
author: scottaddie
description: Saiba como configurar a autenticação do Windows no ASP.NET Core, usando o IIS Express, o IIS, o HTTP. sys e o WebListener.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/01/2018
uid: security/authentication/windowsauth
ms.openlocfilehash: 15e388433cc9b01e9db3e2fb56aca1ebb5ba5ba4
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284403"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="3ecbe-103">Configurar a autenticação do Windows no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ecbe-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="3ecbe-104">Por [Steve Smith](https://ardalis.com) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3ecbe-104">By [Steve Smith](https://ardalis.com) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="3ecbe-105">Autenticação do Windows podem ser configurada para aplicativos ASP.NET Core hospedados com o IIS [HTTP. sys](xref:fundamentals/servers/httpsys), ou [WebListener](xref:fundamentals/servers/weblistener).</span><span class="sxs-lookup"><span data-stu-id="3ecbe-105">Windows Authentication can be configured for ASP.NET Core apps hosted with IIS, [HTTP.sys](xref:fundamentals/servers/httpsys), or [WebListener](xref:fundamentals/servers/weblistener).</span></span>

## <a name="windows-authentication"></a><span data-ttu-id="3ecbe-106">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="3ecbe-106">Windows Authentication</span></span>

<span data-ttu-id="3ecbe-107">Autenticação do Windows se baseia no sistema operacional para autenticar usuários de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="3ecbe-108">Você pode usar a autenticação do Windows quando o servidor é executado em uma rede corporativa usando identidades de domínio do Active Directory ou outras contas do Windows para identificar os usuários.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or other Windows accounts to identify users.</span></span> <span data-ttu-id="3ecbe-109">Autenticação do Windows é mais adequada para ambientes de intranet em que os usuários, aplicativos cliente e servidores web pertencem ao mesmo domínio do Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-109">Windows Authentication is best suited to intranet environments in which users, client applications, and web servers belong to the same Windows domain.</span></span>

<span data-ttu-id="3ecbe-110">[Saiba mais sobre a autenticação do Windows e instalá-lo para o IIS](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="3ecbe-110">[Learn more about Windows Authentication and installing it for IIS](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="3ecbe-111">Habilitar a autenticação do Windows em um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ecbe-111">Enable Windows Authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="3ecbe-112">O modelo de aplicativo Web Visual Studio pode ser configurado para dar suporte à autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-112">The Visual Studio Web Application template can be configured to support Windows Authentication.</span></span>

### <a name="use-the-windows-authentication-app-template"></a><span data-ttu-id="3ecbe-113">Use o modelo de aplicativo de autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="3ecbe-113">Use the Windows Authentication app template</span></span>

<span data-ttu-id="3ecbe-114">No Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3ecbe-114">In Visual Studio:</span></span>

1. <span data-ttu-id="3ecbe-115">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-115">Create a new ASP.NET Core Web Application.</span></span>
1. <span data-ttu-id="3ecbe-116">Selecione o aplicativo Web na lista de modelos.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-116">Select Web Application from the list of templates.</span></span>
1. <span data-ttu-id="3ecbe-117">Selecione o **alterar autenticação** botão e selecione **autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-117">Select the **Change Authentication** button and select **Windows Authentication**.</span></span>

<span data-ttu-id="3ecbe-118">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-118">Run the app.</span></span> <span data-ttu-id="3ecbe-119">O nome de usuário aparece na parte superior direita do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-119">The username appears in the top right of the app.</span></span>

![Captura de tela de navegador de autenticação Windows](windowsauth/_static/browser-screenshot.png)

<span data-ttu-id="3ecbe-121">Para trabalhos de desenvolvimento usando o IIS Express, o modelo fornece toda a configuração necessária para usar a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-121">For development work using IIS Express, the template provides all the configuration necessary to use Windows Authentication.</span></span> <span data-ttu-id="3ecbe-122">A seção a seguir mostra como configurar manualmente um aplicativo ASP.NET Core para autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-122">The following section shows how to manually configure an ASP.NET Core app for Windows Authentication.</span></span>

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a><span data-ttu-id="3ecbe-123">Configurações do Visual Studio para Windows e autenticação anônima</span><span class="sxs-lookup"><span data-stu-id="3ecbe-123">Visual Studio settings for Windows and anonymous authentication</span></span>

<span data-ttu-id="3ecbe-124">O projeto do Visual Studio **propriedades** da página **depurar** guia fornece caixas de seleção para a autenticação do Windows e autenticação anônima.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-124">The Visual Studio project **Properties** page's **Debug** tab provides check boxes for Windows Authentication and anonymous authentication.</span></span>

![Captura de tela do navegador de autenticação de Windows com as opções de autenticação realçadas](windowsauth/_static/vs-auth-property-menu.png)

<span data-ttu-id="3ecbe-126">Como alternativa, essas duas propriedades podem ser configuradas na *launchsettings. JSON* arquivo:</span><span class="sxs-lookup"><span data-stu-id="3ecbe-126">Alternatively, these two properties can be configured in the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample/launchSettings.json?highlight=3-4)]

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="3ecbe-127">Habilitar a autenticação do Windows com o IIS</span><span class="sxs-lookup"><span data-stu-id="3ecbe-127">Enable Windows Authentication with IIS</span></span>

<span data-ttu-id="3ecbe-128">O IIS usa a [módulo do ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) para hospedar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-128">IIS uses the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="3ecbe-129">Autenticação do Windows está configurada no IIS, não o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-129">Windows Authentication is configured in IIS, not the app.</span></span> <span data-ttu-id="3ecbe-130">As seções a seguir mostram como usar o Gerenciador do IIS para configurar um aplicativo ASP.NET Core para usar a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-130">The following sections show how to use IIS Manager to configure an ASP.NET Core app to use Windows Authentication.</span></span>

### <a name="iis-configuration"></a><span data-ttu-id="3ecbe-131">Configuração do IIS</span><span class="sxs-lookup"><span data-stu-id="3ecbe-131">IIS configuration</span></span>

<span data-ttu-id="3ecbe-132">Habilite o serviço de função do IIS para autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-132">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="3ecbe-133">Para obter mais informações, consulte [habilitar autenticação do Windows nos serviços de função do IIS (consulte a etapa 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="3ecbe-133">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="3ecbe-134">Middleware de integração do IIS está configurado para autenticar solicitações de automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-134">IIS Integration Middleware is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="3ecbe-135">Para obter mais informações, consulte [Host ASP.NET Core no Windows com o IIS: Opções do IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="3ecbe-135">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="3ecbe-136">O módulo do ASP.NET está configurado para encaminhar o token de autenticação do Windows para o aplicativo por padrão.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-136">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="3ecbe-137">Para obter mais informações, consulte [referência de configuração do módulo do ASP.NET Core: Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="3ecbe-137">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="3ecbe-138">Criar um novo site do IIS</span><span class="sxs-lookup"><span data-stu-id="3ecbe-138">Create a new IIS site</span></span>

<span data-ttu-id="3ecbe-139">Especifique um nome e uma pasta e permitir que ele crie um novo pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-139">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="customize-authentication"></a><span data-ttu-id="3ecbe-140">Personalizar autenticação</span><span class="sxs-lookup"><span data-stu-id="3ecbe-140">Customize authentication</span></span>

<span data-ttu-id="3ecbe-141">Abra os recursos de autenticação para o site.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-141">Open the Authentication features for the site.</span></span>

![Menu de autenticação do IIS](windowsauth/_static/iis-authentication-menu.png)

<span data-ttu-id="3ecbe-143">Desabilitar a autenticação anônima e habilitar a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-143">Disable Anonymous Authentication and enable Windows Authentication.</span></span>

![Configurações de autenticação do IIS](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a><span data-ttu-id="3ecbe-145">Publicar seu projeto para a pasta de site do IIS</span><span class="sxs-lookup"><span data-stu-id="3ecbe-145">Publish your project to the IIS site folder</span></span>

<span data-ttu-id="3ecbe-146">Usando o Visual Studio ou a CLI do .NET Core, publica o aplicativo para a pasta de destino.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-146">Using Visual Studio or the .NET Core CLI, publish the app to the destination folder.</span></span>

![Caixa de diálogo de publicação do Visual Studio](windowsauth/_static/vs-publish-app.png)

<span data-ttu-id="3ecbe-148">Saiba mais sobre [publicar no IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="3ecbe-148">Learn more about [publishing to IIS](xref:host-and-deploy/iis/index).</span></span>

<span data-ttu-id="3ecbe-149">Inicie o aplicativo para verificar se a autenticação do Windows está funcionando.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-149">Launch the app to verify Windows Authentication is working.</span></span>

::: moniker range=">= aspnetcore-2.0"

## <a name="enable-windows-authentication-with-httpsys"></a><span data-ttu-id="3ecbe-150">Habilitar a autenticação do Windows com o HTTP. sys</span><span class="sxs-lookup"><span data-stu-id="3ecbe-150">Enable Windows Authentication with HTTP.sys</span></span>

<span data-ttu-id="3ecbe-151">Embora o Kestrel não dá suporte a autenticação do Windows, você pode usar [HTTP. sys](xref:fundamentals/servers/httpsys) para dar suporte a cenários são hospedados no Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-151">Although Kestrel doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="3ecbe-152">O exemplo a seguir configura o host de web do aplicativo para usar o HTTP. sys com a autenticação do Windows:</span><span class="sxs-lookup"><span data-stu-id="3ecbe-152">The following example configures the app's web host to use HTTP.sys with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample/Program2x.cs?highlight=9-14)]

> [!NOTE]
> <span data-ttu-id="3ecbe-153">O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-153">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="3ecbe-154">Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-154">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="3ecbe-155">A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-155">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="3ecbe-156">Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-156">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3ecbe-157">Não há suporte para http. sys no Nano Server versão 1709 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-157">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="3ecbe-158">Para usar a autenticação do Windows e o HTTP. sys com o Nano Server, use uma [contêiner de Server Core (microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="3ecbe-158">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="3ecbe-159">Para obter mais informações sobre o Server Core, consulte [qual é a opção de instalação Server Core no Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="3ecbe-159">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

## <a name="enable-windows-authentication-with-weblistener"></a><span data-ttu-id="3ecbe-160">Habilitar a autenticação do Windows com o WebListener</span><span class="sxs-lookup"><span data-stu-id="3ecbe-160">Enable Windows Authentication with WebListener</span></span>

<span data-ttu-id="3ecbe-161">Embora o Kestrel não dá suporte a autenticação do Windows, você pode usar [WebListener](xref:fundamentals/servers/weblistener) para dar suporte a cenários são hospedados no Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-161">Although Kestrel doesn't support Windows Authentication, you can use [WebListener](xref:fundamentals/servers/weblistener) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="3ecbe-162">O exemplo a seguir configura o host de web do aplicativo para usar WebListener com a autenticação do Windows:</span><span class="sxs-lookup"><span data-stu-id="3ecbe-162">The following example configures the app's web host to use WebListener with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample/Program1x.cs?highlight=6-11)]

> [!NOTE]
> <span data-ttu-id="3ecbe-163">O WebListener delega à autenticação de modo kernel com o protocolo de autenticação Kerberos.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-163">WebListener delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="3ecbe-164">Não há suporte para autenticação de modo de usuário com o Kerberos e o WebListener.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-164">User mode authentication isn't supported with Kerberos and WebListener.</span></span> <span data-ttu-id="3ecbe-165">A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-165">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="3ecbe-166">Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-166">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

::: moniker-end

## <a name="work-with-windows-authentication"></a><span data-ttu-id="3ecbe-167">Trabalhar com a autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="3ecbe-167">Work with Windows Authentication</span></span>

<span data-ttu-id="3ecbe-168">Estado da configuração do acesso anônimo determina o modo no qual o `[Authorize]` e `[AllowAnonymous]` atributos são usados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-168">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="3ecbe-169">As seções a seguir explicam como lidar com os estados de configuração não permitidos e têm permissão de acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-169">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="3ecbe-170">Não permitir acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="3ecbe-170">Disallow anonymous access</span></span>

<span data-ttu-id="3ecbe-171">Quando a autenticação do Windows está habilitada e o acesso anônimo é desabilitado, o `[Authorize]` e `[AllowAnonymous]` atributos não têm nenhum efeito.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-171">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="3ecbe-172">Se o site do IIS (ou servidor HTTP. sys ou WebListener) estiver configurado para não permitir acesso anônimo, a solicitação nunca atinge seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-172">If the IIS site (or HTTP.sys or WebListener server) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="3ecbe-173">Por esse motivo, o `[AllowAnonymous]` atributo não é aplicável.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-173">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="3ecbe-174">Permitir o acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="3ecbe-174">Allow anonymous access</span></span>

<span data-ttu-id="3ecbe-175">Quando a autenticação do Windows e o acesso anônimo estão habilitados, use o `[Authorize]` e `[AllowAnonymous]` atributos.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-175">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="3ecbe-176">O `[Authorize]` atributo permite que você possa proteger partes do aplicativo que realmente exigem a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-176">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows Authentication.</span></span> <span data-ttu-id="3ecbe-177">O `[AllowAnonymous]` substituições de atributo `[Authorize]` atributo uso dentro de aplicativos que permitem o acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-177">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="3ecbe-178">Ver [autorização simples](xref:security/authorization/simple) para obter detalhes de uso do atributo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-178">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="3ecbe-179">No ASP.NET Core 2.x, o `[Authorize]` atributo requer configuração adicional no *Startup.cs* desafiar solicitações anônimas para a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-179">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows Authentication.</span></span> <span data-ttu-id="3ecbe-180">A configuração recomendada varia um pouco com base no servidor web que está sendo usado.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-180">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="3ecbe-181">Por padrão, os usuários que não têm autorização para acessar uma página são apresentados com uma resposta HTTP 403 vazia.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-181">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="3ecbe-182">O [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) pode ser configurado para fornecer aos usuários uma melhor experiência de "Acesso negado".</span><span class="sxs-lookup"><span data-stu-id="3ecbe-182">The [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="3ecbe-183">IIS</span><span class="sxs-lookup"><span data-stu-id="3ecbe-183">IIS</span></span>

<span data-ttu-id="3ecbe-184">Se usar o IIS, adicione o seguinte para o `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="3ecbe-184">If using IIS, add the following to the `ConfigureServices` method:</span></span>

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="3ecbe-185">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="3ecbe-185">HTTP.sys</span></span>

<span data-ttu-id="3ecbe-186">Se usando HTTP. sys, adicione o seguinte para o `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="3ecbe-186">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="3ecbe-187">Representação</span><span class="sxs-lookup"><span data-stu-id="3ecbe-187">Impersonation</span></span>

<span data-ttu-id="3ecbe-188">ASP.NET Core não implementar a representação.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-188">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="3ecbe-189">Aplicativos executados com a identidade do aplicativo para todas as solicitações, usando a identidade de pool ou processo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-189">Apps run with the application identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="3ecbe-190">Se você precisar executar explicitamente uma ação em nome do usuário, use `WindowsIdentity.RunImpersonated`.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-190">If you need to explicitly perform an action on behalf of a user, use `WindowsIdentity.RunImpersonated`.</span></span> <span data-ttu-id="3ecbe-191">Executar uma única ação nesse contexto e, em seguida, feche o contexto.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-191">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample/Startup.cs?name=snippet_Impersonate&highlight=10-18)]

<span data-ttu-id="3ecbe-192">Observe que `RunImpersonated` não oferece suporte a operações assíncronas e não deve ser usado para cenários complexos.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-192">Note that `RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="3ecbe-193">Por exemplo, solicitações de todas ou cadeias de middleware de encapsulamento não é tem suporte ou recomendado.</span><span class="sxs-lookup"><span data-stu-id="3ecbe-193">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>
