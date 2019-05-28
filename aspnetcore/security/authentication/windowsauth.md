---
title: Configurar a autenticação do Windows no ASP.NET Core
author: scottaddie
description: Saiba como configurar a autenticação do Windows no ASP.NET Core, usando o IIS Express, o IIS e o HTTP. sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 04/03/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 46c9e054b6e9228a709f93c6b73772708f6c6eb0
ms.sourcegitcommit: b8ed594ab9f47fa32510574f3e1b210cff000967
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66251222"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="075d7-103">Configurar a autenticação do Windows no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="075d7-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="075d7-104">Por [Scott Addie](https://twitter.com/Scott_Addie) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="075d7-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="075d7-105">[Autenticação do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) podem ser configuradas para aplicativos ASP.NET Core hospedados com [IIS](xref:host-and-deploy/iis/index) ou [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="075d7-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="075d7-106">Autenticação do Windows se baseia no sistema operacional para autenticar usuários de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="075d7-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="075d7-107">Você pode usar a autenticação do Windows quando o servidor é executado em uma rede corporativa usando identidades de domínio do Active Directory ou contas do Windows para identificar os usuários.</span><span class="sxs-lookup"><span data-stu-id="075d7-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="075d7-108">Autenticação do Windows é mais adequada para ambientes de intranet em que os usuários, aplicativos cliente e servidores web pertencem ao mesmo domínio do Windows.</span><span class="sxs-lookup"><span data-stu-id="075d7-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="075d7-109">Habilitar a autenticação do Windows em um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="075d7-109">Enable Windows Authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="075d7-110">O **aplicativo Web** modelo disponível por meio do Visual Studio ou a CLI do .NET Core pode ser configurado para dar suporte à autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="075d7-110">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="075d7-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="075d7-111">Visual Studio</span></span>](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a><span data-ttu-id="075d7-112">Usar o modelo de aplicativo de autenticação do Windows para um novo projeto</span><span class="sxs-lookup"><span data-stu-id="075d7-112">Use the Windows Authentication app template for a new project</span></span>

<span data-ttu-id="075d7-113">No Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="075d7-113">In Visual Studio:</span></span>

1. <span data-ttu-id="075d7-114">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="075d7-114">Create a new project.</span></span>
1. <span data-ttu-id="075d7-115">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="075d7-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="075d7-116">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="075d7-116">Select **Next**.</span></span>
1. <span data-ttu-id="075d7-117">Forneça um nome na **nome do projeto** campo.</span><span class="sxs-lookup"><span data-stu-id="075d7-117">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="075d7-118">Confirme se o **local** entrada está correta ou fornecer um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="075d7-118">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="075d7-119">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="075d7-119">Select **Create**.</span></span>
1. <span data-ttu-id="075d7-120">Selecione **alteração** sob **autenticação**.</span><span class="sxs-lookup"><span data-stu-id="075d7-120">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="075d7-121">No **alterar autenticação** janela, selecione **autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="075d7-121">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="075d7-122">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="075d7-122">Select **OK**.</span></span>
1. <span data-ttu-id="075d7-123">Selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="075d7-123">Select **Web Application**.</span></span>
1. <span data-ttu-id="075d7-124">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="075d7-124">Select **Create**.</span></span>

<span data-ttu-id="075d7-125">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="075d7-125">Run the app.</span></span> <span data-ttu-id="075d7-126">O nome de usuário é exibido na interface do usuário do aplicativo renderizado.</span><span class="sxs-lookup"><span data-stu-id="075d7-126">The username appears in the rendered app's user interface.</span></span>

### <a name="manual-configuration-for-an-existing-project"></a><span data-ttu-id="075d7-127">Configuração manual para um projeto existente</span><span class="sxs-lookup"><span data-stu-id="075d7-127">Manual configuration for an existing project</span></span>

<span data-ttu-id="075d7-128">As propriedades do projeto permitem que você habilitar a autenticação do Windows e desabilite a autenticação anônima:</span><span class="sxs-lookup"><span data-stu-id="075d7-128">The project's properties allow you to enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="075d7-129">Clique com botão direito no projeto no Visual Studio **Gerenciador de soluções** e selecione **propriedades**.</span><span class="sxs-lookup"><span data-stu-id="075d7-129">Right-click the project in Visual Studio's **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="075d7-130">Selecione a guia **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="075d7-130">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="075d7-131">Desmarque a caixa de seleção **habilitar a autenticação anônima**.</span><span class="sxs-lookup"><span data-stu-id="075d7-131">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="075d7-132">Marque a caixa de seleção **habilitar a autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="075d7-132">Select the check box for **Enable Windows Authentication**.</span></span>

<span data-ttu-id="075d7-133">Como alternativa, as propriedades podem ser configuradas na `iisSettings` nó do *launchsettings. JSON* arquivo:</span><span class="sxs-lookup"><span data-stu-id="075d7-133">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="075d7-134">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="075d7-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="075d7-135">Use o **autenticação do Windows** modelo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="075d7-135">Use the **Windows Authentication** app template.</span></span>

<span data-ttu-id="075d7-136">Execute o [dotnet new](/dotnet/core/tools/dotnet-new) com o `webapp` argumento (aplicativo Web do ASP.NET Core) e `--auth Windows` mudar:</span><span class="sxs-lookup"><span data-stu-id="075d7-136">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

---

<span data-ttu-id="075d7-137">Ao modificar um projeto existente, confirme se o arquivo de projeto inclui uma referência de pacote para o [metapacote do Microsoft](xref:fundamentals/metapackage-app) **ou** o [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="075d7-137">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="075d7-138">Habilitar a autenticação do Windows com o IIS</span><span class="sxs-lookup"><span data-stu-id="075d7-138">Enable Windows Authentication with IIS</span></span>

<span data-ttu-id="075d7-139">O IIS usa a [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para hospedar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="075d7-139">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="075d7-140">Autenticação do Windows está configurada para o IIS por meio de *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="075d7-140">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="075d7-141">As seções a seguir mostram como:</span><span class="sxs-lookup"><span data-stu-id="075d7-141">The following sections show how to:</span></span>

* <span data-ttu-id="075d7-142">Forneça um local *Web. config* arquivo que ativa a autenticação do Windows no servidor quando o aplicativo é implantado.</span><span class="sxs-lookup"><span data-stu-id="075d7-142">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="075d7-143">Use o Gerenciador do IIS para configurar o *Web. config* arquivo de um aplicativo ASP.NET Core que já tenha sido implantado no servidor.</span><span class="sxs-lookup"><span data-stu-id="075d7-143">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

### <a name="iis-configuration"></a><span data-ttu-id="075d7-144">Configuração do IIS</span><span class="sxs-lookup"><span data-stu-id="075d7-144">IIS configuration</span></span>

<span data-ttu-id="075d7-145">Se você ainda não fez isso, habilite o IIS para hospedar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="075d7-145">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="075d7-146">Para obter mais informações, consulte <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="075d7-146">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="075d7-147">Habilite o serviço de função do IIS para autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="075d7-147">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="075d7-148">Para obter mais informações, consulte [habilitar autenticação do Windows nos serviços de função do IIS (consulte a etapa 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="075d7-148">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="075d7-149">[Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) está configurado para autenticar solicitações de automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="075d7-149">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="075d7-150">Para obter mais informações, consulte [Host ASP.NET Core no Windows com o IIS: Opções do IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="075d7-150">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="075d7-151">O módulo do ASP.NET está configurado para encaminhar o token de autenticação do Windows para o aplicativo por padrão.</span><span class="sxs-lookup"><span data-stu-id="075d7-151">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="075d7-152">Para obter mais informações, consulte [referência de configuração do módulo do ASP.NET Core: Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="075d7-152">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="075d7-153">Criar um novo site do IIS</span><span class="sxs-lookup"><span data-stu-id="075d7-153">Create a new IIS site</span></span>

<span data-ttu-id="075d7-154">Especifique um nome e uma pasta e permitir que ele crie um novo pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="075d7-154">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="enable-windows-authentication-for-the-app-in-iis"></a><span data-ttu-id="075d7-155">Habilitar a autenticação do Windows para o aplicativo no IIS</span><span class="sxs-lookup"><span data-stu-id="075d7-155">Enable Windows Authentication for the app in IIS</span></span>

<span data-ttu-id="075d7-156">Use **qualquer** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="075d7-156">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="075d7-157">[Configuração do lado do desenvolvimento antes de publicar o aplicativo](#development-side-configuration-with-a-local-webconfig-file) (*recomendado*)</span><span class="sxs-lookup"><span data-stu-id="075d7-157">[Development-side configuration before publishing the app](#development-side-configuration-with-a-local-webconfig-file) (*Recommended*)</span></span>
* [<span data-ttu-id="075d7-158">Configuração do lado do servidor depois de publicar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="075d7-158">Server-side configuration after publishing the app</span></span>](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a><span data-ttu-id="075d7-159">Configuração do lado do desenvolvimento com um arquivo Web. config local</span><span class="sxs-lookup"><span data-stu-id="075d7-159">Development-side configuration with a local web.config file</span></span>

<span data-ttu-id="075d7-160">Execute as seguintes etapas **antes de** você [publicar e implantar seu projeto](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="075d7-160">Perform the following steps **before** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

<span data-ttu-id="075d7-161">Adicione o seguinte *Web. config* arquivo para a raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="075d7-161">Add the following *web.config* file to the project root:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

<span data-ttu-id="075d7-162">Quando o projeto é publicado pelo SDK (sem o `<IsTransformWebConfigDisabled>` propriedade definida como `true` no arquivo de projeto), publicado *Web. config* arquivo inclui o `<location><system.webServer><security><authentication>` seção.</span><span class="sxs-lookup"><span data-stu-id="075d7-162">When the project is published by the SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="075d7-163">Para obter mais informações sobre o `<IsTransformWebConfigDisabled>` propriedade, consulte <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="075d7-163">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

#### <a name="server-side-configuration-with-the-iis-manager"></a><span data-ttu-id="075d7-164">Configuração do lado do servidor com o Gerenciador do IIS</span><span class="sxs-lookup"><span data-stu-id="075d7-164">Server-side configuration with the IIS Manager</span></span>

<span data-ttu-id="075d7-165">Execute as seguintes etapas **após** você [publicar e implantar seu projeto](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="075d7-165">Perform the following steps **after** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

1. <span data-ttu-id="075d7-166">No Gerenciador do IIS, selecione o site do IIS sob o **Sites** nó do **conexões** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="075d7-166">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
1. <span data-ttu-id="075d7-167">Clique duas vezes em **autenticação** na **IIS** área.</span><span class="sxs-lookup"><span data-stu-id="075d7-167">Double-click **Authentication** in the **IIS** area.</span></span>
1. <span data-ttu-id="075d7-168">Selecione **autenticação anônima**.</span><span class="sxs-lookup"><span data-stu-id="075d7-168">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="075d7-169">Selecione **desabilite** na **ações** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="075d7-169">Select **Disable** in the **Actions** sidebar.</span></span>
1. <span data-ttu-id="075d7-170">Selecione **autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="075d7-170">Select **Windows Authentication**.</span></span> <span data-ttu-id="075d7-171">Selecione **habilitar** na **ações** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="075d7-171">Select **Enable** in the **Actions** sidebar.</span></span>

<span data-ttu-id="075d7-172">Quando essas ações são executadas, o Gerenciador do IIS modifica o aplicativo *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="075d7-172">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="075d7-173">Um `<system.webServer><security><authentication>` nó for adicionado com configurações atualizadas para `anonymousAuthentication` e `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="075d7-173">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

<span data-ttu-id="075d7-174">O `<system.webServer>` seção adicionada para o *Web. config* arquivo pelo Gerenciador do IIS está fora do aplicativo `<location>` seção adicionada pelo SDK do .NET Core quando o aplicativo for publicado.</span><span class="sxs-lookup"><span data-stu-id="075d7-174">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="075d7-175">Porque a seção é adicionada fora das `<location>` nó, as configurações são herdadas por qualquer [subaplicativos](xref:host-and-deploy/iis/index#sub-applications) ao aplicativo atual.</span><span class="sxs-lookup"><span data-stu-id="075d7-175">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="075d7-176">Para impedir a herança, mova a adicionada `<security>` seção dentro do `<location><system.webServer>` seção que o SDK fornecido.</span><span class="sxs-lookup"><span data-stu-id="075d7-176">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the SDK provided.</span></span>

<span data-ttu-id="075d7-177">Quando o Gerenciador do IIS é usado para adicionar a configuração do IIS, ele afeta somente o aplicativo *Web. config* arquivo no servidor.</span><span class="sxs-lookup"><span data-stu-id="075d7-177">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="075d7-178">Uma implantação subsequente do aplicativo pode substituir as configurações no servidor se a cópia do servidor do *Web. config* é substituído do projeto *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="075d7-178">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="075d7-179">Use **qualquer** das abordagens a seguir para gerenciar as configurações:</span><span class="sxs-lookup"><span data-stu-id="075d7-179">Use **either** of the following approaches to manage the settings:</span></span>

* <span data-ttu-id="075d7-180">Use o Gerenciador do IIS para redefinir as configurações na *Web. config* depois que o arquivo será substituído na implantação de arquivos.</span><span class="sxs-lookup"><span data-stu-id="075d7-180">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
* <span data-ttu-id="075d7-181">Adicionar um *arquivo Web. config* para o aplicativo localmente com as configurações.</span><span class="sxs-lookup"><span data-stu-id="075d7-181">Add a *web.config file* to the app locally with the settings.</span></span> <span data-ttu-id="075d7-182">Para obter mais informações, consulte o [configuração do lado do desenvolvimento](#development-side-configuration-with-a-local-webconfig-file) seção.</span><span class="sxs-lookup"><span data-stu-id="075d7-182">For more information, see the [Development-side configuration](#development-side-configuration-with-a-local-webconfig-file) section.</span></span>

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a><span data-ttu-id="075d7-183">Publicar e implantar seu projeto para a pasta de site do IIS</span><span class="sxs-lookup"><span data-stu-id="075d7-183">Publish and deploy your project to the IIS site folder</span></span>

<span data-ttu-id="075d7-184">Usando o Visual Studio ou a CLI do .NET Core, publicar e implantar o aplicativo para a pasta de destino.</span><span class="sxs-lookup"><span data-stu-id="075d7-184">Using Visual Studio or the .NET Core CLI, publish and deploy the app to the destination folder.</span></span>

<span data-ttu-id="075d7-185">Para obter mais informações sobre hospedagem com o IIS, publicação e implantação, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="075d7-185">For more information on hosting with IIS, publishing, and deployment, see the following topics:</span></span>

* [<span data-ttu-id="075d7-186">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="075d7-186">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

<span data-ttu-id="075d7-187">Inicie o aplicativo para verificar se a autenticação do Windows está funcionando.</span><span class="sxs-lookup"><span data-stu-id="075d7-187">Launch the app to verify Windows Authentication is working.</span></span>

## <a name="enable-windows-authentication-with-httpsys"></a><span data-ttu-id="075d7-188">Habilitar a autenticação do Windows com o HTTP. sys</span><span class="sxs-lookup"><span data-stu-id="075d7-188">Enable Windows Authentication with HTTP.sys</span></span>

<span data-ttu-id="075d7-189">Embora o Kestrel não dá suporte a autenticação do Windows, você pode usar [HTTP. sys](xref:fundamentals/servers/httpsys) para dar suporte a cenários são hospedados no Windows.</span><span class="sxs-lookup"><span data-stu-id="075d7-189">Although Kestrel doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="075d7-190">O exemplo a seguir configura o host de web do aplicativo para usar o HTTP. sys com a autenticação do Windows:</span><span class="sxs-lookup"><span data-stu-id="075d7-190">The following example configures the app's web host to use HTTP.sys with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> <span data-ttu-id="075d7-191">O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos.</span><span class="sxs-lookup"><span data-stu-id="075d7-191">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="075d7-192">Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="075d7-192">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="075d7-193">A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="075d7-193">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="075d7-194">Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="075d7-194">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="075d7-195">Não há suporte para http. sys no Nano Server versão 1709 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="075d7-195">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="075d7-196">Para usar a autenticação do Windows e o HTTP. sys com o Nano Server, use uma [contêiner de Server Core (microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="075d7-196">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="075d7-197">Para obter mais informações sobre o Server Core, consulte [qual é a opção de instalação Server Core no Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="075d7-197">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="work-with-windows-authentication"></a><span data-ttu-id="075d7-198">Trabalhar com a autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="075d7-198">Work with Windows Authentication</span></span>

<span data-ttu-id="075d7-199">Estado da configuração do acesso anônimo determina o modo no qual o `[Authorize]` e `[AllowAnonymous]` atributos são usados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="075d7-199">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="075d7-200">As seções a seguir explicam como lidar com os estados de configuração não permitidos e têm permissão de acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="075d7-200">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="075d7-201">Não permitir acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="075d7-201">Disallow anonymous access</span></span>

<span data-ttu-id="075d7-202">Quando a autenticação do Windows está habilitada e o acesso anônimo é desabilitado, o `[Authorize]` e `[AllowAnonymous]` atributos não têm nenhum efeito.</span><span class="sxs-lookup"><span data-stu-id="075d7-202">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="075d7-203">Se o site do IIS (ou HTTP. sys) estiver configurado para não permitir acesso anônimo, a solicitação nunca atinge seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="075d7-203">If the IIS site (or HTTP.sys) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="075d7-204">Por esse motivo, o `[AllowAnonymous]` atributo não é aplicável.</span><span class="sxs-lookup"><span data-stu-id="075d7-204">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="075d7-205">Permitir o acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="075d7-205">Allow anonymous access</span></span>

<span data-ttu-id="075d7-206">Quando a autenticação do Windows e o acesso anônimo estão habilitados, use o `[Authorize]` e `[AllowAnonymous]` atributos.</span><span class="sxs-lookup"><span data-stu-id="075d7-206">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="075d7-207">O `[Authorize]` atributo permite que você possa proteger partes do aplicativo que realmente exigem a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="075d7-207">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows Authentication.</span></span> <span data-ttu-id="075d7-208">O `[AllowAnonymous]` substituições de atributo `[Authorize]` atributo uso dentro de aplicativos que permitem o acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="075d7-208">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="075d7-209">Ver [autorização simples](xref:security/authorization/simple) para obter detalhes de uso do atributo.</span><span class="sxs-lookup"><span data-stu-id="075d7-209">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="075d7-210">No ASP.NET Core 2.x, o `[Authorize]` atributo requer configuração adicional no *Startup.cs* desafiar solicitações anônimas para a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="075d7-210">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows Authentication.</span></span> <span data-ttu-id="075d7-211">A configuração recomendada varia um pouco com base no servidor web que está sendo usado.</span><span class="sxs-lookup"><span data-stu-id="075d7-211">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="075d7-212">Por padrão, os usuários que não têm autorização para acessar uma página são apresentados com uma resposta HTTP 403 vazia.</span><span class="sxs-lookup"><span data-stu-id="075d7-212">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="075d7-213">O [StatusCodePages middleware](xref:fundamentals/error-handling#usestatuscodepages) pode ser configurado para fornecer aos usuários uma melhor experiência de "Acesso negado".</span><span class="sxs-lookup"><span data-stu-id="075d7-213">The [StatusCodePages middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="075d7-214">IIS</span><span class="sxs-lookup"><span data-stu-id="075d7-214">IIS</span></span>

<span data-ttu-id="075d7-215">Se usar o IIS, adicione o seguinte para o `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="075d7-215">If using IIS, add the following to the `ConfigureServices` method:</span></span>

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="075d7-216">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="075d7-216">HTTP.sys</span></span>

<span data-ttu-id="075d7-217">Se usando HTTP. sys, adicione o seguinte para o `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="075d7-217">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="075d7-218">Representação</span><span class="sxs-lookup"><span data-stu-id="075d7-218">Impersonation</span></span>

<span data-ttu-id="075d7-219">ASP.NET Core não implementar a representação.</span><span class="sxs-lookup"><span data-stu-id="075d7-219">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="075d7-220">Aplicativos executados com a identidade do aplicativo para todas as solicitações, usando a identidade de pool ou processo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="075d7-220">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="075d7-221">Se você precisar executar explicitamente uma ação em nome do usuário, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) em um [middleware embutido terminal](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="075d7-221">If you need to explicitly perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="075d7-222">Executar uma única ação nesse contexto e, em seguida, feche o contexto.</span><span class="sxs-lookup"><span data-stu-id="075d7-222">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="075d7-223">`RunImpersonated` não oferece suporte a operações assíncronas e não deve ser usado para cenários complexos.</span><span class="sxs-lookup"><span data-stu-id="075d7-223">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="075d7-224">Por exemplo, solicitações de todas ou cadeias de middleware de encapsulamento não é tem suporte ou recomendado.</span><span class="sxs-lookup"><span data-stu-id="075d7-224">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

### <a name="claims-transformations"></a><span data-ttu-id="075d7-225">Transformações de declarações</span><span class="sxs-lookup"><span data-stu-id="075d7-225">Claims transformations</span></span>

<span data-ttu-id="075d7-226">Ao hospedar com o modo de em processo do IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="075d7-226">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="075d7-227">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="075d7-227">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="075d7-228">Para obter mais informações e um exemplo de código que ativa as transformações de declarações quando no processo de hospedagem, consulte <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="075d7-228">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>
