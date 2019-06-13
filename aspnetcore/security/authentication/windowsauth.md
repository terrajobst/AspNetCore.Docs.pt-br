---
title: Configurar a autenticação do Windows no ASP.NET Core
author: scottaddie
description: Saiba como configurar a autenticação do Windows no ASP.NET Core para IIS e o HTTP. sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/12/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 93f833adff95f25d570947cd1a9035d652f522c2
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034958"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="1fcf2-103">Configurar a autenticação do Windows no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1fcf2-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="1fcf2-104">Por [Scott Addie](https://twitter.com/Scott_Addie) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1fcf2-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1fcf2-105">Autenticação do Windows (também conhecida como autenticação Negotiate, Kerberos ou NTLM) podem ser configurada para aplicativos ASP.NET Core hospedados com [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), ou [HTTP. sys](xref:fundamentals/servers/httpsys) .</span><span class="sxs-lookup"><span data-stu-id="1fcf2-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1fcf2-106">Autenticação do Windows (também conhecida como autenticação Negotiate, Kerberos ou NTLM) podem ser configurada para aplicativos ASP.NET Core hospedados com [IIS](xref:host-and-deploy/iis/index) ou [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="1fcf2-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="1fcf2-107">Autenticação do Windows se baseia no sistema operacional para autenticar usuários de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="1fcf2-108">Você pode usar a autenticação do Windows quando o servidor é executado em uma rede corporativa usando identidades de domínio do Active Directory ou contas do Windows para identificar os usuários.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="1fcf2-109">Autenticação do Windows é mais adequada para ambientes de intranet em que os usuários, aplicativos cliente e servidores web pertencem ao mesmo domínio do Windows.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="1fcf2-110">Não há suporte para autenticação do Windows com o HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="1fcf2-111">Desafios de autenticação podem ser enviados em respostas HTTP/2, mas o cliente deve fazer o downgrade para HTTP/1.1 antes da autenticação.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="1fcf2-112">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="1fcf2-112">IIS/IIS Express</span></span>

<span data-ttu-id="1fcf2-113">Adicionar serviços de autenticação, invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-113">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="1fcf2-114">Configurações (depurador) de inicialização</span><span class="sxs-lookup"><span data-stu-id="1fcf2-114">Launch settings (debugger)</span></span>

<span data-ttu-id="1fcf2-115">Configuração para configurações de inicialização afeta apenas o *Properties/launchSettings.json* de arquivo para o IIS Express e não configura o IIS para a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-115">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="1fcf2-116">Configuração do servidor é explicada com o [IIS](#iis) seção.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-116">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="1fcf2-117">O **aplicativo Web** modelo disponível por meio do Visual Studio ou a CLI do .NET Core pode ser configurado para dar suporte à autenticação do Windows, que atualiza o *Properties/launchSettings.json* arquivo automaticamente.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-117">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1fcf2-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1fcf2-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1fcf2-119">**novo projeto**</span><span class="sxs-lookup"><span data-stu-id="1fcf2-119">**New project**</span></span>

1. <span data-ttu-id="1fcf2-120">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-120">Create a new project.</span></span>
1. <span data-ttu-id="1fcf2-121">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-121">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="1fcf2-122">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-122">Select **Next**.</span></span>
1. <span data-ttu-id="1fcf2-123">Forneça um nome na **nome do projeto** campo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-123">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="1fcf2-124">Confirme se o **local** entrada está correta ou fornecer um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="1fcf2-125">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-125">Select **Create**.</span></span>
1. <span data-ttu-id="1fcf2-126">Selecione **alteração** sob **autenticação**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-126">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="1fcf2-127">No **alterar autenticação** janela, selecione **autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-127">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="1fcf2-128">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-128">Select **OK**.</span></span>
1. <span data-ttu-id="1fcf2-129">Selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-129">Select **Web Application**.</span></span>
1. <span data-ttu-id="1fcf2-130">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-130">Select **Create**.</span></span>

<span data-ttu-id="1fcf2-131">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-131">Run the app.</span></span> <span data-ttu-id="1fcf2-132">O nome de usuário é exibido na interface do usuário do aplicativo renderizado.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-132">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="1fcf2-133">**Projeto existente**</span><span class="sxs-lookup"><span data-stu-id="1fcf2-133">**Existing project**</span></span>

<span data-ttu-id="1fcf2-134">Propriedades do projeto, habilitar a autenticação do Windows e desabilite a autenticação anônima:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-134">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="1fcf2-135">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-135">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="1fcf2-136">Selecione a guia **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-136">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="1fcf2-137">Desmarque a caixa de seleção **habilitar a autenticação anônima**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-137">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="1fcf2-138">Marque a caixa de seleção **habilitar a autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-138">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="1fcf2-139">Salve e feche a página de propriedades.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-139">Save and close the property page.</span></span>

<span data-ttu-id="1fcf2-140">Como alternativa, as propriedades podem ser configuradas na `iisSettings` nó do *launchsettings. JSON* arquivo:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-140">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="1fcf2-141">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="1fcf2-141">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="1fcf2-142">**novo projeto**</span><span class="sxs-lookup"><span data-stu-id="1fcf2-142">**New project**</span></span>

<span data-ttu-id="1fcf2-143">Execute o [dotnet new](/dotnet/core/tools/dotnet-new) com o `webapp` argumento (aplicativo Web do ASP.NET Core) e `--auth Windows` mudar:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-143">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="1fcf2-144">**Projeto existente**</span><span class="sxs-lookup"><span data-stu-id="1fcf2-144">**Existing project**</span></span>

<span data-ttu-id="1fcf2-145">Atualizar o `iisSettings` nó do *launchsettings. JSON* arquivo:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-145">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="1fcf2-146">Ao modificar um projeto existente, confirme se o arquivo de projeto inclui uma referência de pacote para o [metapacote do Microsoft](xref:fundamentals/metapackage-app) **ou** o [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-146">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="1fcf2-147">IIS</span><span class="sxs-lookup"><span data-stu-id="1fcf2-147">IIS</span></span>

<span data-ttu-id="1fcf2-148">O IIS usa a [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para hospedar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-148">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="1fcf2-149">Autenticação do Windows está configurada para o IIS por meio de *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-149">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="1fcf2-150">As seções a seguir mostram como:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-150">The following sections show how to:</span></span>

* <span data-ttu-id="1fcf2-151">Forneça um local *Web. config* arquivo que ativa a autenticação do Windows no servidor quando o aplicativo é implantado.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-151">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="1fcf2-152">Use o Gerenciador do IIS para configurar o *Web. config* arquivo de um aplicativo ASP.NET Core que já tenha sido implantado no servidor.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-152">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="1fcf2-153">Se você ainda não fez isso, habilite o IIS para hospedar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-153">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="1fcf2-154">Para obter mais informações, consulte <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-154">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="1fcf2-155">Habilite o serviço de função do IIS para autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-155">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="1fcf2-156">Para obter mais informações, consulte [habilitar autenticação do Windows nos serviços de função do IIS (consulte a etapa 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="1fcf2-156">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="1fcf2-157">[Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) está configurado para autenticar solicitações de automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-157">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="1fcf2-158">Para obter mais informações, consulte [Host ASP.NET Core no Windows com o IIS: Opções do IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="1fcf2-158">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="1fcf2-159">O módulo do ASP.NET está configurado para encaminhar o token de autenticação do Windows para o aplicativo por padrão.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-159">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="1fcf2-160">Para obter mais informações, consulte [referência de configuração do módulo do ASP.NET Core: Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="1fcf2-160">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="1fcf2-161">Use **qualquer** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-161">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="1fcf2-162">**Antes de publicar e implantar o projeto** adicione o seguinte *Web. config* arquivo para a raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-162">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="1fcf2-163">Quando o projeto é publicado pelo SDK do .NET Core (sem o `<IsTransformWebConfigDisabled>` propriedade definida como `true` no arquivo de projeto), publicado *Web. config* arquivo inclui o `<location><system.webServer><security><authentication>` seção.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-163">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="1fcf2-164">Para obter mais informações sobre o `<IsTransformWebConfigDisabled>` propriedade, consulte <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-164">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="1fcf2-165">**Após a publicação e implantação de projeto,** executar a configuração do lado do servidor com o Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-165">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="1fcf2-166">No Gerenciador do IIS, selecione o site do IIS sob o **Sites** nó do **conexões** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-166">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="1fcf2-167">Clique duas vezes em **autenticação** na **IIS** área.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-167">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="1fcf2-168">Selecione **autenticação anônima**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-168">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="1fcf2-169">Selecione **desabilite** na **ações** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-169">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="1fcf2-170">Selecione **autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-170">Select **Windows Authentication**.</span></span> <span data-ttu-id="1fcf2-171">Selecione **habilitar** na **ações** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-171">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="1fcf2-172">Quando essas ações são executadas, o Gerenciador do IIS modifica o aplicativo *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-172">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="1fcf2-173">Um `<system.webServer><security><authentication>` nó for adicionado com configurações atualizadas para `anonymousAuthentication` e `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-173">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="1fcf2-174">O `<system.webServer>` seção adicionada para o *Web. config* arquivo pelo Gerenciador do IIS está fora do aplicativo `<location>` seção adicionada pelo SDK do .NET Core quando o aplicativo for publicado.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-174">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="1fcf2-175">Porque a seção é adicionada fora das `<location>` nó, as configurações são herdadas por qualquer [subaplicativos](xref:host-and-deploy/iis/index#sub-applications) ao aplicativo atual.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-175">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="1fcf2-176">Para impedir a herança, mova a adicionada `<security>` seção dentro do `<location><system.webServer>` seção SDK do .NET Core é fornecido.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-176">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="1fcf2-177">Quando o Gerenciador do IIS é usado para adicionar a configuração do IIS, ele afeta somente o aplicativo *Web. config* arquivo no servidor.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-177">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="1fcf2-178">Uma implantação subsequente do aplicativo pode substituir as configurações no servidor se a cópia do servidor do *Web. config* é substituído do projeto *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-178">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="1fcf2-179">Use **qualquer** das abordagens a seguir para gerenciar as configurações:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-179">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="1fcf2-180">Use o Gerenciador do IIS para redefinir as configurações na *Web. config* depois que o arquivo será substituído na implantação de arquivos.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-180">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="1fcf2-181">Adicionar um *arquivo Web. config* para o aplicativo localmente com as configurações.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-181">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="1fcf2-182">Kestrel</span><span class="sxs-lookup"><span data-stu-id="1fcf2-182">Kestrel</span></span>

 <span data-ttu-id="1fcf2-183">O [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) pacote do NuGet pode ser usado com [Kestrel](xref:fundamentals/servers/kestrel) para dar suporte à autenticação do Windows usando Negotiate, Kerberos e NTLM no Windows, Linux e macOS.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-183">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate, Kerberos, and NTLM on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="1fcf2-184">As credenciais podem ser persistidas entre as solicitações em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-184">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="1fcf2-185">*Negociar a autenticação não deve ser usada com proxies, a menos que o proxy mantém uma afinidade de conexão de 1:1 (uma conexão persistente) com o Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="1fcf2-185">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span> <span data-ttu-id="1fcf2-186">Isso significa que a autenticação de negociação não deve ser usada com o Kestrel por trás do IIS [fora do processo do ASP.NET Core ANCM (módulo)](xref:host-and-deploy/iis/index#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="1fcf2-186">This means that Negotiate authentication must not be used with Kestrel behind the IIS [ASP.NET Core Module (ANCM) out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model).</span></span>

 <span data-ttu-id="1fcf2-187">Adicionar serviços de autenticação, invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) e `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-187">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) and `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) in `Startup.ConfigureServices`:</span></span>

 ```csharp
services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="1fcf2-188">Adicione o Middleware de autenticação chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-188">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();

app.UseMvc();
```

<span data-ttu-id="1fcf2-189">Para obter mais informações sobre o middleware, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-189">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="1fcf2-190">As solicitações anônimas são permitidas.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-190">Anonymous requests are allowed.</span></span> <span data-ttu-id="1fcf2-191">Use [autorização do ASP.NET Core](xref:security/authorization/introduction) para solicitações anônimas para a autenticação de desafio.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-191">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="1fcf2-192">Configuração de ambiente do Windows</span><span class="sxs-lookup"><span data-stu-id="1fcf2-192">Windows environment configuration</span></span>

<span data-ttu-id="1fcf2-193">O [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) componente executa a autenticação de modo de usuário.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-193">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="1fcf2-194">Nomes de entidade de serviço (SPNs) deve ser adicionados à conta de usuário que executa o serviço, não a conta do computador.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-194">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="1fcf2-195">Executar `setspn -S HTTP/mysrevername.mydomain.com myuser` em um shell de comando administrativo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-195">Execute `setspn -S HTTP/mysrevername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="1fcf2-196">Configuração de ambiente do Linux e macOS</span><span class="sxs-lookup"><span data-stu-id="1fcf2-196">Linux and macOS environment configuration</span></span>

<span data-ttu-id="1fcf2-197">As instruções para ingressar em um computador Linux ou macOS em um domínio do Windows estão disponíveis na [Studio conectar-se do Azure Data ao SQL Server usando a autenticação do Windows - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) artigo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-197">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="1fcf2-198">As instruções de criem uma conta de computador para a máquina Linux no domínio.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-198">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="1fcf2-199">Os SPNs devem ser adicionados a essa conta de computador.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-199">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="1fcf2-200">Ao seguir as diretrizes a [Studio conectar-se do Azure Data ao SQL Server usando a autenticação do Windows - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) do artigo, substitua `python-software-properties` com `python3-software-properties` se necessário.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-200">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="1fcf2-201">Depois que o computador Linux ou macOS é ingressado no domínio, são necessárias etapas adicionais para fornecer um [arquivo keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) com os SPNs:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-201">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="1fcf2-202">No controlador de domínio, adicione o novo serviço web SPNs à conta da máquina:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-202">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="1fcf2-203">Use [ktpass](/windows-server/administration/windows-commands/ktpass) para gerar um arquivo keytab:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-203">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="1fcf2-204">Alguns campos devem ser especificados em letras maiusculas conforme indicado.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-204">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="1fcf2-205">Copie o arquivo keytab para a máquina Linux ou macOS.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-205">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="1fcf2-206">Selecione o arquivo keytab por meio de uma variável de ambiente: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="1fcf2-206">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="1fcf2-207">Invocar `klist` para mostrar os SPNs atualmente disponíveis para uso.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-207">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="1fcf2-208">Um arquivo keytab contém as credenciais de acesso de domínio e deve ser protegido adequadamente.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-208">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="1fcf2-209">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1fcf2-209">HTTP.sys</span></span>

<span data-ttu-id="1fcf2-210">[O HTTP. sys](xref:fundamentals/servers/httpsys) dá suporte à autenticação do Windows de modo de Kernel usando Negotiate, NTLM ou autenticação básica.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-210">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="1fcf2-211">Adicionar serviços de autenticação, invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1fcf2-211">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="1fcf2-212">Configurar o host de web do aplicativo para usar o HTTP. sys com a autenticação do Windows (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="1fcf2-212">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="1fcf2-213"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> está no <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-213"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1fcf2-214">O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-214">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="1fcf2-215">Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-215">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="1fcf2-216">A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-216">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="1fcf2-217">Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-217">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="1fcf2-218">Não há suporte para http. sys no Nano Server versão 1709 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-218">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="1fcf2-219">Para usar a autenticação do Windows e o HTTP. sys com o Nano Server, use uma [contêiner de Server Core (microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="1fcf2-219">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="1fcf2-220">Para obter mais informações sobre o Server Core, consulte [qual é a opção de instalação Server Core no Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="1fcf2-220">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="1fcf2-221">Autorizar usuários</span><span class="sxs-lookup"><span data-stu-id="1fcf2-221">Authorize users</span></span>

<span data-ttu-id="1fcf2-222">Estado da configuração do acesso anônimo determina o modo no qual o `[Authorize]` e `[AllowAnonymous]` atributos são usados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-222">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="1fcf2-223">As seções a seguir explicam como lidar com os estados de configuração não permitidos e têm permissão de acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-223">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="1fcf2-224">Não permitir acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="1fcf2-224">Disallow anonymous access</span></span>

<span data-ttu-id="1fcf2-225">Quando a autenticação do Windows está habilitada e o acesso anônimo é desabilitado, o `[Authorize]` e `[AllowAnonymous]` atributos não têm nenhum efeito.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-225">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="1fcf2-226">Se um site do IIS é configurado para não permitir acesso anônimo, a solicitação nunca alcance o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-226">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="1fcf2-227">Por esse motivo, o `[AllowAnonymous]` atributo não é aplicável.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-227">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="1fcf2-228">Permitir o acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="1fcf2-228">Allow anonymous access</span></span>

<span data-ttu-id="1fcf2-229">Quando a autenticação do Windows e o acesso anônimo estão habilitados, use o `[Authorize]` e `[AllowAnonymous]` atributos.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-229">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="1fcf2-230">O `[Authorize]` atributo permite proteger pontos de extremidade do aplicativo que exige autenticação.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-230">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="1fcf2-231">O `[AllowAnonymous]` substituições de atributo a `[Authorize]` atributo em aplicativos que permitem acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-231">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="1fcf2-232">Para obter detalhes de uso do atributo, consulte <xref:security/authorization/simple>.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-232">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="1fcf2-233">Por padrão, os usuários que não têm autorização para acessar uma página são apresentados com uma resposta HTTP 403 vazia.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-233">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="1fcf2-234">O [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) pode ser configurado para fornecer aos usuários uma melhor experiência de "Acesso negado".</span><span class="sxs-lookup"><span data-stu-id="1fcf2-234">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="1fcf2-235">Representação</span><span class="sxs-lookup"><span data-stu-id="1fcf2-235">Impersonation</span></span>

<span data-ttu-id="1fcf2-236">ASP.NET Core não implementar a representação.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-236">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="1fcf2-237">Aplicativos executados com a identidade do aplicativo para todas as solicitações, usando a identidade de pool ou processo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-237">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="1fcf2-238">Se o aplicativo deve executar uma ação em nome do usuário, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) em um [middleware embutido terminal](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-238">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="1fcf2-239">Executar uma única ação nesse contexto e, em seguida, feche o contexto.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-239">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="1fcf2-240">`RunImpersonated` não oferece suporte a operações assíncronas e não deve ser usado para cenários complexos.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-240">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="1fcf2-241">Por exemplo, solicitações de todas ou cadeias de middleware de encapsulamento não é tem suporte ou recomendado.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-241">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1fcf2-242">Enquanto o [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) pacote permite que a autenticação no Windows, Linux e macOS, representação só tem suporte no Windows.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-242">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="1fcf2-243">Transformações de declarações</span><span class="sxs-lookup"><span data-stu-id="1fcf2-243">Claims transformations</span></span>

<span data-ttu-id="1fcf2-244">Ao hospedar com o modo de em processo do IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-244">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="1fcf2-245">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-245">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="1fcf2-246">Para obter mais informações e um exemplo de código que ativa as transformações de declarações quando no processo de hospedagem, consulte <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="1fcf2-246">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1fcf2-247">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="1fcf2-247">Additional resources</span></span>

* [<span data-ttu-id="1fcf2-248">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="1fcf2-248">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
