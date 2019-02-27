---
title: Configurar a autenticação do Windows no ASP.NET Core
author: scottaddie
description: Saiba como configurar a autenticação do Windows no ASP.NET Core, usando o IIS Express, o IIS e o HTTP. sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/25/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 15fc41efba77f88fc8129f875b85836ac1b5f886
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833690"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="e4500-103">Configurar a autenticação do Windows no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4500-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="e4500-104">Por [Scott Addie](https://twitter.com/Scott_Addie) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e4500-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e4500-105">[Autenticação do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) podem ser configuradas para aplicativos ASP.NET Core hospedados com [IIS](xref:host-and-deploy/iis/index) ou [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="e4500-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="e4500-106">Autenticação do Windows se baseia no sistema operacional para autenticar usuários de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4500-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="e4500-107">Você pode usar a autenticação do Windows quando o servidor é executado em uma rede corporativa usando identidades de domínio do Active Directory ou contas do Windows para identificar os usuários.</span><span class="sxs-lookup"><span data-stu-id="e4500-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="e4500-108">Autenticação do Windows é mais adequada para ambientes de intranet em que os usuários, aplicativos cliente e servidores web pertencem ao mesmo domínio do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4500-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="e4500-109">Habilitar a autenticação do Windows em um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4500-109">Enable Windows Authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="e4500-110">O **aplicativo Web** modelo disponível por meio do Visual Studio ou a CLI do .NET Core pode ser configurado para dar suporte à autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4500-110">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e4500-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e4500-111">Visual Studio</span></span>](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a><span data-ttu-id="e4500-112">Usar o modelo de aplicativo de autenticação do Windows para um novo projeto</span><span class="sxs-lookup"><span data-stu-id="e4500-112">Use the Windows Authentication app template for a new project</span></span>

<span data-ttu-id="e4500-113">No Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e4500-113">In Visual Studio:</span></span>

1. <span data-ttu-id="e4500-114">Criar um novo **aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="e4500-114">Create a new **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="e4500-115">Selecione **aplicativo Web** da lista de modelos.</span><span class="sxs-lookup"><span data-stu-id="e4500-115">Select **Web Application** from the list of templates.</span></span>
1. <span data-ttu-id="e4500-116">Selecione o **alterar autenticação** botão e selecione **autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="e4500-116">Select the **Change Authentication** button and select **Windows Authentication**.</span></span>

<span data-ttu-id="e4500-117">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4500-117">Run the app.</span></span> <span data-ttu-id="e4500-118">O nome de usuário é exibido na interface do usuário do aplicativo renderizado.</span><span class="sxs-lookup"><span data-stu-id="e4500-118">The username appears in the rendered app's user interface.</span></span>

### <a name="manual-configuration-for-an-existing-project"></a><span data-ttu-id="e4500-119">Configuração manual para um projeto existente</span><span class="sxs-lookup"><span data-stu-id="e4500-119">Manual configuration for an existing project</span></span>

<span data-ttu-id="e4500-120">As propriedades do projeto permitem que você habilitar a autenticação do Windows e desabilite a autenticação anônima:</span><span class="sxs-lookup"><span data-stu-id="e4500-120">The project's properties allow you to enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="e4500-121">Clique com botão direito no projeto no Visual Studio **Gerenciador de soluções** e selecione **propriedades**.</span><span class="sxs-lookup"><span data-stu-id="e4500-121">Right-click the project in Visual Studio's **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="e4500-122">Selecione a guia **Depurar**.</span><span class="sxs-lookup"><span data-stu-id="e4500-122">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="e4500-123">Desmarque a caixa de seleção **habilitar a autenticação anônima**.</span><span class="sxs-lookup"><span data-stu-id="e4500-123">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="e4500-124">Marque a caixa de seleção **habilitar a autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="e4500-124">Select the check box for **Enable Windows Authentication**.</span></span>

<span data-ttu-id="e4500-125">Como alternativa, as propriedades podem ser configuradas na `iisSettings` nó do *launchsettings. JSON* arquivo:</span><span class="sxs-lookup"><span data-stu-id="e4500-125">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e4500-126">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e4500-126">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e4500-127">Use o **autenticação do Windows** modelo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4500-127">Use the **Windows Authentication** app template.</span></span>

<span data-ttu-id="e4500-128">Execute o [dotnet new](/dotnet/core/tools/dotnet-new) com o `webapp` argumento (aplicativo Web do ASP.NET Core) e `--auth Windows` mudar:</span><span class="sxs-lookup"><span data-stu-id="e4500-128">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

---

<span data-ttu-id="e4500-129">Ao modificar um projeto existente, confirme se o arquivo de projeto inclui uma referência de pacote para o [metapacote do Microsoft](xref:fundamentals/metapackage-app) **ou** o [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="e4500-129">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="e4500-130">Habilitar a autenticação do Windows com o IIS</span><span class="sxs-lookup"><span data-stu-id="e4500-130">Enable Windows Authentication with IIS</span></span>

<span data-ttu-id="e4500-131">O IIS usa a [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para hospedar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4500-131">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="e4500-132">Autenticação do Windows está configurada para o IIS por meio de *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e4500-132">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="e4500-133">As seções a seguir mostram como:</span><span class="sxs-lookup"><span data-stu-id="e4500-133">The following sections show how to:</span></span>

* <span data-ttu-id="e4500-134">Forneça um local *Web. config* arquivo que ativa a autenticação do Windows no servidor quando o aplicativo é implantado.</span><span class="sxs-lookup"><span data-stu-id="e4500-134">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="e4500-135">Use o Gerenciador do IIS para configurar o *Web. config* arquivo de um aplicativo ASP.NET Core que já tenha sido implantado no servidor.</span><span class="sxs-lookup"><span data-stu-id="e4500-135">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

### <a name="iis-configuration"></a><span data-ttu-id="e4500-136">Configuração do IIS</span><span class="sxs-lookup"><span data-stu-id="e4500-136">IIS configuration</span></span>

<span data-ttu-id="e4500-137">Se você ainda não fez isso, habilite o IIS para hospedar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4500-137">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="e4500-138">Para obter mais informações, consulte <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="e4500-138">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="e4500-139">Habilite o serviço de função do IIS para autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4500-139">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="e4500-140">Para obter mais informações, consulte [habilitar autenticação do Windows nos serviços de função do IIS (consulte a etapa 2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="e4500-140">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="e4500-141">Middleware de integração do IIS está configurado para autenticar solicitações de automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="e4500-141">IIS Integration Middleware is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="e4500-142">Para obter mais informações, consulte [Host ASP.NET Core no Windows com o IIS: Opções do IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="e4500-142">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="e4500-143">O módulo do ASP.NET está configurado para encaminhar o token de autenticação do Windows para o aplicativo por padrão.</span><span class="sxs-lookup"><span data-stu-id="e4500-143">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="e4500-144">Para obter mais informações, consulte [referência de configuração do módulo do ASP.NET Core: Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="e4500-144">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="e4500-145">Criar um novo site do IIS</span><span class="sxs-lookup"><span data-stu-id="e4500-145">Create a new IIS site</span></span>

<span data-ttu-id="e4500-146">Especifique um nome e uma pasta e permitir que ele crie um novo pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="e4500-146">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="enable-windows-authentication-for-the-app-in-iis"></a><span data-ttu-id="e4500-147">Habilitar a autenticação do Windows para o aplicativo no IIS</span><span class="sxs-lookup"><span data-stu-id="e4500-147">Enable Windows Authentication for the app in IIS</span></span>

<span data-ttu-id="e4500-148">Use **qualquer** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e4500-148">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="e4500-149">[Configuração do lado do desenvolvimento antes de publicar o aplicativo](#development-side-configuration-with-a-local-webconfig-file) (*recomendado*)</span><span class="sxs-lookup"><span data-stu-id="e4500-149">[Development-side configuration before publishing the app](#development-side-configuration-with-a-local-webconfig-file) (*Recommended*)</span></span>
* [<span data-ttu-id="e4500-150">Configuração do lado do servidor depois de publicar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e4500-150">Server-side configuration after publishing the app</span></span>](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a><span data-ttu-id="e4500-151">Configuração do lado do desenvolvimento com um arquivo Web. config local</span><span class="sxs-lookup"><span data-stu-id="e4500-151">Development-side configuration with a local web.config file</span></span>

<span data-ttu-id="e4500-152">Execute as seguintes etapas **antes de** você [publicar e implantar seu projeto](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="e4500-152">Perform the following steps **before** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

<span data-ttu-id="e4500-153">Adicione o seguinte *Web. config* arquivo para a raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="e4500-153">Add the following *web.config* file to the project root:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

<span data-ttu-id="e4500-154">Quando o projeto é publicado pelo SDK (sem o `<IsTransformWebConfigDisabled>` propriedade definida como `true` no arquivo de projeto), publicado *Web. config* arquivo inclui o `<location><system.webServer><security><authentication>` seção.</span><span class="sxs-lookup"><span data-stu-id="e4500-154">When the project is published by the SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="e4500-155">Para obter mais informações sobre o `<IsTransformWebConfigDisabled>` propriedade, consulte <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="e4500-155">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

#### <a name="server-side-configuration-with-the-iis-manager"></a><span data-ttu-id="e4500-156">Configuração do lado do servidor com o Gerenciador do IIS</span><span class="sxs-lookup"><span data-stu-id="e4500-156">Server-side configuration with the IIS Manager</span></span>

<span data-ttu-id="e4500-157">Execute as seguintes etapas **após** você [publicar e implantar seu projeto](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="e4500-157">Perform the following steps **after** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

1. <span data-ttu-id="e4500-158">No Gerenciador do IIS, selecione o site do IIS sob o **Sites** nó do **conexões** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="e4500-158">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
1. <span data-ttu-id="e4500-159">Clique duas vezes em **autenticação** na **IIS** área.</span><span class="sxs-lookup"><span data-stu-id="e4500-159">Double-click **Authentication** in the **IIS** area.</span></span>
1. <span data-ttu-id="e4500-160">Selecione **autenticação anônima**.</span><span class="sxs-lookup"><span data-stu-id="e4500-160">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="e4500-161">Selecione **desabilite** na **ações** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="e4500-161">Select **Disable** in the **Actions** sidebar.</span></span>
1. <span data-ttu-id="e4500-162">Selecione **autenticação do Windows**.</span><span class="sxs-lookup"><span data-stu-id="e4500-162">Select **Windows Authentication**.</span></span> <span data-ttu-id="e4500-163">Selecione **habilitar** na **ações** barra lateral.</span><span class="sxs-lookup"><span data-stu-id="e4500-163">Select **Enable** in the **Actions** sidebar.</span></span>

<span data-ttu-id="e4500-164">Quando essas ações são executadas, o Gerenciador do IIS modifica o aplicativo *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e4500-164">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="e4500-165">Um `<system.webServer><security><authentication>` nó for adicionado com configurações atualizadas para `anonymousAuthentication` e `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="e4500-165">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

<span data-ttu-id="e4500-166">O `<system.webServer>` seção adicionada para o *Web. config* arquivo pelo Gerenciador do IIS está fora do aplicativo `<location>` seção adicionada pelo SDK do .NET Core quando o aplicativo for publicado.</span><span class="sxs-lookup"><span data-stu-id="e4500-166">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="e4500-167">Porque a seção é adicionada fora das `<location>` nó, as configurações são herdadas por qualquer [subaplicativos](xref:host-and-deploy/iis/index#sub-applications) ao aplicativo atual.</span><span class="sxs-lookup"><span data-stu-id="e4500-167">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="e4500-168">Para impedir a herança, mova a adicionada `<security>` seção dentro do `<location><system.webServer>` seção que o SDK fornecido.</span><span class="sxs-lookup"><span data-stu-id="e4500-168">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the SDK provided.</span></span>

<span data-ttu-id="e4500-169">Quando o Gerenciador do IIS é usado para adicionar a configuração do IIS, ele afeta somente o aplicativo *Web. config* arquivo no servidor.</span><span class="sxs-lookup"><span data-stu-id="e4500-169">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="e4500-170">Uma implantação subsequente do aplicativo pode substituir as configurações no servidor se a cópia do servidor do *Web. config* é substituído do projeto *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e4500-170">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="e4500-171">Use **qualquer** das abordagens a seguir para gerenciar as configurações:</span><span class="sxs-lookup"><span data-stu-id="e4500-171">Use **either** of the following approaches to manage the settings:</span></span>

* <span data-ttu-id="e4500-172">Use o Gerenciador do IIS para redefinir as configurações na *Web. config* depois que o arquivo será substituído na implantação de arquivos.</span><span class="sxs-lookup"><span data-stu-id="e4500-172">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
* <span data-ttu-id="e4500-173">Adicionar um *arquivo Web. config* para o aplicativo localmente com as configurações.</span><span class="sxs-lookup"><span data-stu-id="e4500-173">Add a *web.config file* to the app locally with the settings.</span></span> <span data-ttu-id="e4500-174">Para obter mais informações, consulte o [configuração do lado do desenvolvimento](#development-side-configuration-with-a-local-webconfig-file) seção.</span><span class="sxs-lookup"><span data-stu-id="e4500-174">For more information, see the [Development-side configuration](#development-side-configuration-with-a-local-webconfig-file) section.</span></span>

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a><span data-ttu-id="e4500-175">Publicar e implantar seu projeto para a pasta de site do IIS</span><span class="sxs-lookup"><span data-stu-id="e4500-175">Publish and deploy your project to the IIS site folder</span></span>

<span data-ttu-id="e4500-176">Usando o Visual Studio ou a CLI do .NET Core, publicar e implantar o aplicativo para a pasta de destino.</span><span class="sxs-lookup"><span data-stu-id="e4500-176">Using Visual Studio or the .NET Core CLI, publish and deploy the app to the destination folder.</span></span>

<span data-ttu-id="e4500-177">Para obter mais informações sobre hospedagem com o IIS, publicação e implantação, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="e4500-177">For more information on hosting with IIS, publishing, and deployment, see the following topics:</span></span>

* [<span data-ttu-id="e4500-178">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="e4500-178">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

<span data-ttu-id="e4500-179">Inicie o aplicativo para verificar se a autenticação do Windows está funcionando.</span><span class="sxs-lookup"><span data-stu-id="e4500-179">Launch the app to verify Windows Authentication is working.</span></span>

## <a name="enable-windows-authentication-with-httpsys"></a><span data-ttu-id="e4500-180">Habilitar a autenticação do Windows com o HTTP. sys</span><span class="sxs-lookup"><span data-stu-id="e4500-180">Enable Windows Authentication with HTTP.sys</span></span>

<span data-ttu-id="e4500-181">Embora o Kestrel não dá suporte a autenticação do Windows, você pode usar [HTTP. sys](xref:fundamentals/servers/httpsys) para dar suporte a cenários são hospedados no Windows.</span><span class="sxs-lookup"><span data-stu-id="e4500-181">Although Kestrel doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="e4500-182">O exemplo a seguir configura o host de web do aplicativo para usar o HTTP. sys com a autenticação do Windows:</span><span class="sxs-lookup"><span data-stu-id="e4500-182">The following example configures the app's web host to use HTTP.sys with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> <span data-ttu-id="e4500-183">O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos.</span><span class="sxs-lookup"><span data-stu-id="e4500-183">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="e4500-184">Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e4500-184">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="e4500-185">A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="e4500-185">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="e4500-186">Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4500-186">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="e4500-187">Não há suporte para http. sys no Nano Server versão 1709 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="e4500-187">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="e4500-188">Para usar a autenticação do Windows e o HTTP. sys com o Nano Server, use uma [contêiner de Server Core (microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="e4500-188">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="e4500-189">Para obter mais informações sobre o Server Core, consulte [qual é a opção de instalação Server Core no Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="e4500-189">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="work-with-windows-authentication"></a><span data-ttu-id="e4500-190">Trabalhar com a autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="e4500-190">Work with Windows Authentication</span></span>

<span data-ttu-id="e4500-191">Estado da configuração do acesso anônimo determina o modo no qual o `[Authorize]` e `[AllowAnonymous]` atributos são usados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4500-191">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="e4500-192">As seções a seguir explicam como lidar com os estados de configuração não permitidos e têm permissão de acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="e4500-192">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="e4500-193">Não permitir acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="e4500-193">Disallow anonymous access</span></span>

<span data-ttu-id="e4500-194">Quando a autenticação do Windows está habilitada e o acesso anônimo é desabilitado, o `[Authorize]` e `[AllowAnonymous]` atributos não têm nenhum efeito.</span><span class="sxs-lookup"><span data-stu-id="e4500-194">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="e4500-195">Se o site do IIS (ou HTTP. sys) estiver configurado para não permitir acesso anônimo, a solicitação nunca atinge seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4500-195">If the IIS site (or HTTP.sys) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="e4500-196">Por esse motivo, o `[AllowAnonymous]` atributo não é aplicável.</span><span class="sxs-lookup"><span data-stu-id="e4500-196">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="e4500-197">Permitir o acesso anônimo</span><span class="sxs-lookup"><span data-stu-id="e4500-197">Allow anonymous access</span></span>

<span data-ttu-id="e4500-198">Quando a autenticação do Windows e o acesso anônimo estão habilitados, use o `[Authorize]` e `[AllowAnonymous]` atributos.</span><span class="sxs-lookup"><span data-stu-id="e4500-198">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="e4500-199">O `[Authorize]` atributo permite que você possa proteger partes do aplicativo que realmente exigem a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4500-199">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows Authentication.</span></span> <span data-ttu-id="e4500-200">O `[AllowAnonymous]` substituições de atributo `[Authorize]` atributo uso dentro de aplicativos que permitem o acesso anônimo.</span><span class="sxs-lookup"><span data-stu-id="e4500-200">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="e4500-201">Ver [autorização simples](xref:security/authorization/simple) para obter detalhes de uso do atributo.</span><span class="sxs-lookup"><span data-stu-id="e4500-201">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="e4500-202">No ASP.NET Core 2.x, o `[Authorize]` atributo requer configuração adicional no *Startup.cs* desafiar solicitações anônimas para a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="e4500-202">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows Authentication.</span></span> <span data-ttu-id="e4500-203">A configuração recomendada varia um pouco com base no servidor web que está sendo usado.</span><span class="sxs-lookup"><span data-stu-id="e4500-203">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="e4500-204">Por padrão, os usuários que não têm autorização para acessar uma página são apresentados com uma resposta HTTP 403 vazia.</span><span class="sxs-lookup"><span data-stu-id="e4500-204">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="e4500-205">O [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) pode ser configurado para fornecer aos usuários uma melhor experiência de "Acesso negado".</span><span class="sxs-lookup"><span data-stu-id="e4500-205">The [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="e4500-206">IIS</span><span class="sxs-lookup"><span data-stu-id="e4500-206">IIS</span></span>

<span data-ttu-id="e4500-207">Se usar o IIS, adicione o seguinte para o `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="e4500-207">If using IIS, add the following to the `ConfigureServices` method:</span></span>

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="e4500-208">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e4500-208">HTTP.sys</span></span>

<span data-ttu-id="e4500-209">Se usando HTTP. sys, adicione o seguinte para o `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="e4500-209">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="e4500-210">Representação</span><span class="sxs-lookup"><span data-stu-id="e4500-210">Impersonation</span></span>

<span data-ttu-id="e4500-211">ASP.NET Core não implementar a representação.</span><span class="sxs-lookup"><span data-stu-id="e4500-211">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="e4500-212">Aplicativos executados com a identidade do aplicativo para todas as solicitações, usando a identidade de pool ou processo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4500-212">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="e4500-213">Se você precisar executar explicitamente uma ação em nome do usuário, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) em um [middleware embutido terminal](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e4500-213">If you need to explicitly perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="e4500-214">Executar uma única ação nesse contexto e, em seguida, feche o contexto.</span><span class="sxs-lookup"><span data-stu-id="e4500-214">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="e4500-215">`RunImpersonated` não oferece suporte a operações assíncronas e não deve ser usado para cenários complexos.</span><span class="sxs-lookup"><span data-stu-id="e4500-215">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="e4500-216">Por exemplo, solicitações de todas ou cadeias de middleware de encapsulamento não é tem suporte ou recomendado.</span><span class="sxs-lookup"><span data-stu-id="e4500-216">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

### <a name="claims-transformations"></a><span data-ttu-id="e4500-217">Transformações de declarações</span><span class="sxs-lookup"><span data-stu-id="e4500-217">Claims transformations</span></span>

<span data-ttu-id="e4500-218">Ao hospedar com o modo de em processo do IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="e4500-218">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="e4500-219">Portanto, um <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementação usada para transformar as declarações após cada autenticação não está ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="e4500-219">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="e4500-220">Para obter mais informações e um exemplo de código que ativa as transformações de declarações quando no processo de hospedagem, consulte <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="e4500-220">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>
