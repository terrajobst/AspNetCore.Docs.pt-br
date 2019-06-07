---
title: Configurar a autenticação do Windows no ASP.NET Core
author: scottaddie
description: Saiba como configurar a autenticação do Windows no ASP.NET Core para IIS e o HTTP. sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/05/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 900bbf5f14b1876ad537b2b77e4ba07d7aa168f2
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750158"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="515d6-103">Configurar a autenticação do Windows no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="515d6-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="515d6-104">Por [Scott Addie](https://twitter.com/Scott_Addie) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="515d6-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="515d6-105">[Autenticação do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) podem ser configuradas para aplicativos ASP.NET Core hospedados com [IIS](xref:host-and-deploy/iis/index) ou [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="515d6-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="515d6-106">Autenticação do Windows se baseia no sistema operacional para autenticar usuários de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="515d6-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="515d6-107">Você pode usar a autenticação do Windows quando o servidor é executado em uma rede corporativa usando identidades de domínio do Active Directory ou contas do Windows para identificar os usuários.</span><span class="sxs-lookup"><span data-stu-id="515d6-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="515d6-108">Autenticação do Windows é mais adequada para ambientes de intranet em que os usuários, aplicativos cliente e servidores web pertencem ao mesmo domínio do Windows.</span><span class="sxs-lookup"><span data-stu-id="515d6-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="515d6-109">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="515d6-109">IIS/IIS Express</span></span>

<span data-ttu-id="515d6-110">Adicionar serviços de autenticação, invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="515d6-110">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="515d6-111">Configurações (depurador) de inicialização</span><span class="sxs-lookup"><span data-stu-id="515d6-111">Launch settings (debugger)</span></span>

<span data-ttu-id="515d6-112">Configuração para configurações de inicialização afeta apenas o *Properties/launchSettings.json* de arquivo para o IIS Express e não configura o IIS para a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="515d6-112">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="515d6-113">Configuração do servidor é explicada com o [IIS](#iis) seção.</span><span class="sxs-lookup"><span data-stu-id="515d6-113">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="515d6-114">O **aplicativo Web** modelo disponível por meio do Visual Studio ou a CLI do .NET Core pode ser configurado para dar suporte à autenticação do Windows, que atualiza o *Properties/launchSettings.json* arquivo automaticamente.</span><span class="sxs-lookup"><span data-stu-id="515d6-114">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="515d6-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="515d6-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="515d6-116">**novo projeto**</span><span class="sxs-lookup"><span data-stu-id="515d6-116">**New project**</span></span>

1. <span data-ttu-id="515d6-117">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="515d6-117">Create a new project.</span></span>
1. <span data-ttu-id="515d6-118">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="515d6-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="515d6-119">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="515d6-119">Select **Next**.</span></span>
1. <span data-ttu-id="515d6-120">Forneça um nome na **nome do projeto** campo.</span><span class="sxs-lookup"><span data-stu-id="515d6-120">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="515d6-121">Confirme se o **local** entrada está correta ou fornecer um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="515d6-121">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="515d6-122">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="515d6-122">Select **Create**.</span></span>
1. <span data-ttu-id="515d6-123">Selecione **alteração** sob **autenticação**.</span><span class="sxs-lookup"><span data-stu-id="515d6-123">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="515d6-124">No **alterar autenticação** janela, selecione **autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="515d6-124">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="515d6-125">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="515d6-125">Select **OK**.</span></span>
1. <span data-ttu-id="515d6-126">Selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="515d6-126">Select **Web Application**.</span></span>
1. <span data-ttu-id="515d6-127">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="515d6-127">Select **Create**.</span></span>

<span data-ttu-id="515d6-128">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="515d6-128">Run the app.</span></span> <span data-ttu-id="515d6-129">O nome de usuário é exibido na interface do usuário do aplicativo renderizado.</span><span class="sxs-lookup"><span data-stu-id="515d6-129">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="515d6-130">**Projeto existente**</span><span class="sxs-lookup"><span data-stu-id="515d6-130">**Existing project**</span></span>

<span data-ttu-id="515d6-131">Propriedades do projeto, habilitar a autenticação do Windows e desabilite a autenticação anônima:</span><span class="sxs-lookup"><span data-stu-id="515d6-131">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="515d6-132">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="515d6-132">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="515d6-133">Selecione a guia **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="515d6-133">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="515d6-134">Desmarque a caixa de seleção **habilitar a autenticação anônima**.</span><span class="sxs-lookup"><span data-stu-id="515d6-134">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="515d6-135">Marque a caixa de seleção **habilitar a autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="515d6-135">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="515d6-136">Salve e feche a página de propriedades.</span><span class="sxs-lookup"><span data-stu-id="515d6-136">Save and close the property page.</span></span>

<span data-ttu-id="515d6-137">Como alternativa, as propriedades podem ser configuradas na `iisSettings` nó do *launchsettings. JSON* arquivo:</span><span class="sxs-lookup"><span data-stu-id="515d6-137">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="515d6-138">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="515d6-138">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="515d6-139">**novo projeto**</span><span class="sxs-lookup"><span data-stu-id="515d6-139">**New project**</span></span>

<span data-ttu-id="515d6-140">Execute o [dotnet new](/dotnet/core/tools/dotnet-new) com o `webapp` argumento (aplicativo Web do ASP.NET Core) e `--auth Windows` mudar:</span><span class="sxs-lookup"><span data-stu-id="515d6-140">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="515d6-141">**Projeto existente**</span><span class="sxs-lookup"><span data-stu-id="515d6-141">**Existing project**</span></span>

<span data-ttu-id="515d6-142">Atualizar o `iisSettings` nó do *launchsettings. JSON* arquivo:</span><span class="sxs-lookup"><span data-stu-id="515d6-142">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="515d6-143">Ao modificar um projeto existente, confirme se o arquivo de projeto inclui uma referência de pacote para o [metapacote do Microsoft](xref:fundamentals/metapackage-app) **ou** o [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="515d6-143">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="515d6-144">IIS</span><span class="sxs-lookup"><span data-stu-id="515d6-144">IIS</span></span>

<span data-ttu-id="515d6-145">O IIS usa a [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para hospedar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="515d6-145">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="515d6-146">Autenticação do Windows está configurada para o IIS por meio de *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="515d6-146">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="515d6-147">As seções a seguir mostram como:</span><span class="sxs-lookup"><span data-stu-id="515d6-147">The following sections show how to:</span></span>

* <span data-ttu-id="515d6-148">Forneça um local *Web. config* arquivo que ativa a autenticação do Windows no servidor quando o aplicativo é implantado.</span><span class="sxs-lookup"><span data-stu-id="515d6-148">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="515d6-149">Use o Gerenciador do IIS para configurar o *Web. config* arquivo de um aplicativo ASP.NET Core que já tenha sido implantado no servidor.</span><span class="sxs-lookup"><span data-stu-id="515d6-149">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="515d6-150">Se você ainda não fez isso, habilite o IIS para hospedar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="515d6-150">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="515d6-151">Para obter mais informações, consulte <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="515d6-151">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="515d6-152">Habilite o serviço de função do IIS para autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="515d6-152">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="515d6-153">Para obter mais informações, consulte [habilitar autenticação do Windows nos serviços de função do IIS (consulte a etapa 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="515d6-153">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="515d6-154">[Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) está configurado para autenticar solicitações de automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="515d6-154">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="515d6-155">Para obter mais informações, consulte [Host ASP.NET Core no Windows com o IIS: Opções do IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="515d6-155">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="515d6-156">O módulo do ASP.NET está configurado para encaminhar o token de autenticação do Windows para o aplicativo por padrão.</span><span class="sxs-lookup"><span data-stu-id="515d6-156">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="515d6-157">Para obter mais informações, consulte [referência de configuração do módulo do ASP.NET Core: Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="515d6-157">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="515d6-158">Use **qualquer** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="515d6-158">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="515d6-159">**Antes de publicar e implantar o projeto** adicione o seguinte *Web. config* arquivo para a raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="515d6-159">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="515d6-160">Quando o projeto é publicado pelo SDK do .NET Core (sem o `<IsTransformWebConfigDisabled>` propriedade definida como `true` no arquivo de projeto), publicado *Web. config* arquivo inclui o `<location><system.webServer><security><authentication>` seção.</span><span class="sxs-lookup"><span data-stu-id="515d6-160">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="515d6-161">Para obter mais informações sobre o `<IsTransformWebConfigDisabled>` propriedade, consulte <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="515d6-161">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="515d6-162">**Após a publicação e implantação de projeto,** executar a configuração do lado do servidor com o Gerenciador do IIS:</span><span class="sxs-lookup"><span data-stu-id="515d6-162">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="515d6-163">No Gerenciador do IIS, selecione o site do IIS sob o **Sites** nó do **conexões** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="515d6-163">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="515d6-164">Clique duas vezes em **autenticação** na **IIS** área.</span><span class="sxs-lookup"><span data-stu-id="515d6-164">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="515d6-165">Selecione **autenticação anônima**.</span><span class="sxs-lookup"><span data-stu-id="515d6-165">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="515d6-166">Selecione **desabilite** na **ações** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="515d6-166">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="515d6-167">Selecione **autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="515d6-167">Select **Windows Authentication**.</span></span> <span data-ttu-id="515d6-168">Selecione **habilitar** na **ações** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="515d6-168">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="515d6-169">Quando essas ações são executadas, o Gerenciador do IIS modifica o aplicativo *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="515d6-169">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="515d6-170">Um `<system.webServer><security><authentication>` nó for adicionado com configurações atualizadas para `anonymousAuthentication` e `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="515d6-170">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="515d6-171">O `<system.webServer>` seção adicionada para o *Web. config* arquivo pelo Gerenciador do IIS está fora do aplicativo `<location>` seção adicionada pelo SDK do .NET Core quando o aplicativo for publicado.</span><span class="sxs-lookup"><span data-stu-id="515d6-171">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="515d6-172">Porque a seção é adicionada fora das `<location>` nó, as configurações são herdadas por qualquer [subaplicativos](xref:host-and-deploy/iis/index#sub-applications) ao aplicativo atual.</span><span class="sxs-lookup"><span data-stu-id="515d6-172">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="515d6-173">Para impedir a herança, mova a adicionada `<security>` seção dentro do `<location><system.webServer>` seção SDK do .NET Core é fornecido.</span><span class="sxs-lookup"><span data-stu-id="515d6-173">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="515d6-174">Quando o Gerenciador do IIS é usado para adicionar a configuração do IIS, ele afeta somente o aplicativo *Web. config* arquivo no servidor.</span><span class="sxs-lookup"><span data-stu-id="515d6-174">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="515d6-175">Uma implantação subsequente do aplicativo pode substituir as configurações no servidor se a cópia do servidor do *Web. config* é substituído do projeto *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="515d6-175">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="515d6-176">Use **qualquer** das abordagens a seguir para gerenciar as configurações:</span><span class="sxs-lookup"><span data-stu-id="515d6-176">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="515d6-177">Use o Gerenciador do IIS para redefinir as configurações na *Web. config* depois que o arquivo será substituído na implantação de arquivos.</span><span class="sxs-lookup"><span data-stu-id="515d6-177">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="515d6-178">Adicionar um *arquivo Web. config* para o aplicativo localmente com as configurações.</span><span class="sxs-lookup"><span data-stu-id="515d6-178">Add a *web.config file* to the app locally with the settings.</span></span>

## <a name="httpsys"></a><span data-ttu-id="515d6-179">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="515d6-179">HTTP.sys</span></span>

<span data-ttu-id="515d6-180">Em cenários de hospedagem interna [Kestrel](xref:fundamentals/servers/kestrel) não suporte à autenticação do Windows, mas você pode usar [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="515d6-180">In self-hosted scenarios, [Kestrel](xref:fundamentals/servers/kestrel) doesn't support Windows Authentication, but you can use [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="515d6-181">Adicionar serviços de autenticação, invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="515d6-181">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="515d6-182">Configurar o host de web do aplicativo para usar o HTTP. sys com a autenticação do Windows (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="515d6-182">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="515d6-183"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> está no <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span><span class="sxs-lookup"><span data-stu-id="515d6-183"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="515d6-184">O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos.</span><span class="sxs-lookup"><span data-stu-id="515d6-184">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="515d6-185">Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="515d6-185">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="515d6-186">A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="515d6-186">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="515d6-187">Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="515d6-187">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="515d6-188">Não há suporte para http. sys no Nano Server versão 1709 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="515d6-188">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="515d6-189">Para usar a autenticação do Windows e o HTTP. sys com o Nano Server, use uma [contêiner de Server Core (microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="515d6-189">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="515d6-190">Para obter mais informações sobre o Server Core, consulte [qual é a opção de instalação Server Core no Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="515d6-190">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="515d6-191">Autorizar usuários</span><span class="sxs-lookup"><span data-stu-id="515d6-191">Authorize users</span></span>

<span data-ttu-id="515d6-192">Estado da configuração do acesso anônimo determina o modo no qual o `[Authorize]` e `[AllowAnonymous]` atributos são usados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="515d6-192">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="515d6-193">As seções a seguir explicam como lidar com os estados de configuração não permitidos e têm permissão de acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="515d6-193">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="515d6-194">Não permitir acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="515d6-194">Disallow anonymous access</span></span>

<span data-ttu-id="515d6-195">Quando a autenticação do Windows está habilitada e o acesso anônimo é desabilitado, o `[Authorize]` e `[AllowAnonymous]` atributos não têm nenhum efeito.</span><span class="sxs-lookup"><span data-stu-id="515d6-195">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="515d6-196">Se um site do IIS é configurado para não permitir acesso anônimo, a solicitação nunca alcance o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="515d6-196">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="515d6-197">Por esse motivo, o `[AllowAnonymous]` atributo não é aplicável.</span><span class="sxs-lookup"><span data-stu-id="515d6-197">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="515d6-198">Permitir o acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="515d6-198">Allow anonymous access</span></span>

<span data-ttu-id="515d6-199">Quando a autenticação do Windows e o acesso anônimo estão habilitados, use o `[Authorize]` e `[AllowAnonymous]` atributos.</span><span class="sxs-lookup"><span data-stu-id="515d6-199">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="515d6-200">O `[Authorize]` atributo permite proteger pontos de extremidade do aplicativo que exige autenticação.</span><span class="sxs-lookup"><span data-stu-id="515d6-200">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="515d6-201">O `[AllowAnonymous]` substituições de atributo a `[Authorize]` atributo em aplicativos que permitem acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="515d6-201">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="515d6-202">Para obter detalhes de uso do atributo, consulte <xref:security/authorization/simple>.</span><span class="sxs-lookup"><span data-stu-id="515d6-202">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="515d6-203">Por padrão, os usuários que não têm autorização para acessar uma página são apresentados com uma resposta HTTP 403 vazia.</span><span class="sxs-lookup"><span data-stu-id="515d6-203">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="515d6-204">O [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) pode ser configurado para fornecer aos usuários uma melhor experiência de "Acesso negado".</span><span class="sxs-lookup"><span data-stu-id="515d6-204">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="515d6-205">Representação</span><span class="sxs-lookup"><span data-stu-id="515d6-205">Impersonation</span></span>

<span data-ttu-id="515d6-206">ASP.NET Core não implementar a representação.</span><span class="sxs-lookup"><span data-stu-id="515d6-206">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="515d6-207">Aplicativos executados com a identidade do aplicativo para todas as solicitações, usando a identidade de pool ou processo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="515d6-207">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="515d6-208">Se o aplicativo deve executar uma ação em nome do usuário, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) em um [middleware embutido terminal](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="515d6-208">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="515d6-209">Executar uma única ação nesse contexto e, em seguida, feche o contexto.</span><span class="sxs-lookup"><span data-stu-id="515d6-209">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="515d6-210">`RunImpersonated` não oferece suporte a operações assíncronas e não deve ser usado para cenários complexos.</span><span class="sxs-lookup"><span data-stu-id="515d6-210">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="515d6-211">Por exemplo, solicitações de todas ou cadeias de middleware de encapsulamento não é tem suporte ou recomendado.</span><span class="sxs-lookup"><span data-stu-id="515d6-211">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

## <a name="claims-transformations"></a><span data-ttu-id="515d6-212">Transformações de declarações</span><span class="sxs-lookup"><span data-stu-id="515d6-212">Claims transformations</span></span>

<span data-ttu-id="515d6-213">Ao hospedar com o modo de em processo do IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="515d6-213">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="515d6-214">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="515d6-214">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="515d6-215">Para obter mais informações e um exemplo de código que ativa as transformações de declarações quando no processo de hospedagem, consulte <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="515d6-215">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="515d6-216">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="515d6-216">Additional resources</span></span>

* [<span data-ttu-id="515d6-217">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="515d6-217">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
