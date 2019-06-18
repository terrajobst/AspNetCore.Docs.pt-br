---
title: Suporte a regulamentação de proteção de dados gerais (GDPR) no ASP.NET Core
author: rick-anderson
description: Saiba como acessar os pontos de extensão do GDPR em um aplicativo web ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/05/2019
uid: security/gdpr
ms.openlocfilehash: 1580187afef56e8e2f5be7a4bae32912e6305c5a
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152858"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="66387-103">Suporte da UE Data Protection GDPR (regulamento geral) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66387-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="66387-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="66387-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="66387-105">O ASP.NET Core fornece modelos e APIs para ajudar a atender a alguns dos [regulamentação de proteção de dados geral (GDPR) da UE](https://www.eugdpr.org/) requisitos:</span><span class="sxs-lookup"><span data-stu-id="66387-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements:</span></span>

* <span data-ttu-id="66387-106">Os modelos de projeto incluem pontos de extensão e a marcação de stub que você pode substituir por sua privacidade e a política de uso de cookies.</span><span class="sxs-lookup"><span data-stu-id="66387-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="66387-107">Um recurso de consentimento do cookie permite que você pedir consentimento (e acompanhar uma) de seus usuários para armazenar informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="66387-107">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="66387-108">Se um usuário não tiver consentido para coleta de dados e o aplicativo tem [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) definido como `true`, não-essenciais cookies não são enviados para o navegador.</span><span class="sxs-lookup"><span data-stu-id="66387-108">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="66387-109">Os cookies podem ser marcados como essenciais.</span><span class="sxs-lookup"><span data-stu-id="66387-109">Cookies can be marked as essential.</span></span> <span data-ttu-id="66387-110">Cookies essenciais são enviados ao navegador, mesmo quando o usuário não tiver consentido e acompanhamento está desabilitado.</span><span class="sxs-lookup"><span data-stu-id="66387-110">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="66387-111">[Cookies de sessão e TempData](#tempdata) não são funcionais quando o rastreamento está desabilitado.</span><span class="sxs-lookup"><span data-stu-id="66387-111">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="66387-112">O [gerenciar identidades](#pd) página fornece um link para baixar e excluir dados de usuário.</span><span class="sxs-lookup"><span data-stu-id="66387-112">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="66387-113">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) permite que você teste a maioria dos pontos de extensão de GDPR e APIs adicionadas para os modelos do ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="66387-113">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="66387-114">Consulte a [Leiame](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) arquivo para obter instruções de teste.</span><span class="sxs-lookup"><span data-stu-id="66387-114">See the [ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="66387-115">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="66387-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="66387-116">Suporte de GDPR do ASP.NET Core no código gerado pelo modelo</span><span class="sxs-lookup"><span data-stu-id="66387-116">ASP.NET Core GDPR support in template-generated code</span></span>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="66387-117">Páginas do Razor e MVC projetos criados com os modelos de projeto não têm suporte para consentimento GDPR ou cookie.</span><span class="sxs-lookup"><span data-stu-id="66387-117">Razor Pages and MVC projects created with the project templates have no support for GDPR or cookie consent.</span></span> <span data-ttu-id="66387-118">Para adicionar o GDPR, copie o código gerado nos modelos do ASP.NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="66387-118">To add GDPR, copy the code generated in the ASP.NET Core 2.2 templates.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="66387-119">Páginas do Razor e MVC projetos criados com os modelos de projeto incluem o seguinte suporte GDPR:</span><span class="sxs-lookup"><span data-stu-id="66387-119">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

::: moniker-end

* <span data-ttu-id="66387-120">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) e [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) são definidos `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="66387-120">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="66387-121">O  *\_CookieConsentPartial.cshtml* [exibição parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="66387-121">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="66387-122">Uma **Accept** botão está incluído nesse arquivo.</span><span class="sxs-lookup"><span data-stu-id="66387-122">An **Accept** button is included in this file.</span></span> <span data-ttu-id="66387-123">Quando o usuário clica o **Accept** botão, para armazenar cookies de consentimento é fornecido.</span><span class="sxs-lookup"><span data-stu-id="66387-123">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="66387-124">O *Pages/Privacy.cshtml* página ou *Views/Home/Privacy.cshtml* exibição fornece uma página para a política de privacidade do seu site de detalhe.</span><span class="sxs-lookup"><span data-stu-id="66387-124">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="66387-125">O  *\_CookieConsentPartial.cshtml* arquivo gera um link para a página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="66387-125">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="66387-126">Para os aplicativos criados com as contas de usuário individual, a página Gerenciar fornece links para baixar e excluir [dados pessoais do usuário](#pd).</span><span class="sxs-lookup"><span data-stu-id="66387-126">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="66387-127">CookiePolicyOptions e UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="66387-127">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="66387-128">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) são inicializadas em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="66387-128">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="66387-129">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) é chamado no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="66387-129">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="66387-130">\_Modo de exibição parcial CookieConsentPartial.cshtml</span><span class="sxs-lookup"><span data-stu-id="66387-130">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="66387-131">O  *\_CookieConsentPartial.cshtml* exibição parcial:</span><span class="sxs-lookup"><span data-stu-id="66387-131">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="66387-132">Essa parcial:</span><span class="sxs-lookup"><span data-stu-id="66387-132">This partial:</span></span>

* <span data-ttu-id="66387-133">Obtém o estado do controle para o usuário.</span><span class="sxs-lookup"><span data-stu-id="66387-133">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="66387-134">Se o aplicativo estiver configurado para exigir o consentimento, o usuário deve consentir antes de cookies podem ser controlados.</span><span class="sxs-lookup"><span data-stu-id="66387-134">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="66387-135">Se for necessário o consentimento, o painel de consentimento do cookie é fixa na parte superior da barra de navegação criada pelo  *\_layout. cshtml* arquivo.</span><span class="sxs-lookup"><span data-stu-id="66387-135">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="66387-136">Fornece um HTML `<p>` elemento para resumir a sua privacidade e cookie usar a política.</span><span class="sxs-lookup"><span data-stu-id="66387-136">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="66387-137">Fornece um link para a página de privacidade ou exibição onde você pode detalhar a política de privacidade do seu site.</span><span class="sxs-lookup"><span data-stu-id="66387-137">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="66387-138">Cookies essenciais</span><span class="sxs-lookup"><span data-stu-id="66387-138">Essential cookies</span></span>

<span data-ttu-id="66387-139">Se dar consentimento armazenar cookies ainda não foi fornecido, somente os cookies marcados essenciais são enviados para o navegador.</span><span class="sxs-lookup"><span data-stu-id="66387-139">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="66387-140">O código a seguir faz com que um cookie essenciais:</span><span class="sxs-lookup"><span data-stu-id="66387-140">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a><span data-ttu-id="66387-141">Cookies de estado de sessão e o provedor de TempData não são essenciais</span><span class="sxs-lookup"><span data-stu-id="66387-141">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="66387-142">O [provedor de TempData](xref:fundamentals/app-state#tempdata) cookie não essencial.</span><span class="sxs-lookup"><span data-stu-id="66387-142">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="66387-143">Se o controle estiver desabilitado, o provedor de TempData não é funcional.</span><span class="sxs-lookup"><span data-stu-id="66387-143">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="66387-144">Para habilitar o provedor de TempData quando o rastreamento está desabilitado, marcar o cookie de TempData como essencial para `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="66387-144">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Startup.cs?name=snippet1)]

<span data-ttu-id="66387-145">[Estado de sessão](xref:fundamentals/app-state) cookies não são essenciais.</span><span class="sxs-lookup"><span data-stu-id="66387-145">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="66387-146">Estado de sessão não está funcionando quando o rastreamento está desabilitado.</span><span class="sxs-lookup"><span data-stu-id="66387-146">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="66387-147">O código a seguir faz com que os cookies de sessão essenciais:</span><span class="sxs-lookup"><span data-stu-id="66387-147">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="66387-148">Dados pessoais</span><span class="sxs-lookup"><span data-stu-id="66387-148">Personal data</span></span>

<span data-ttu-id="66387-149">Aplicativos ASP.NET Core criados com contas de usuário individuais incluem código para baixar e excluir dados pessoais.</span><span class="sxs-lookup"><span data-stu-id="66387-149">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="66387-150">Selecione o nome de usuário e, em seguida, selecione **dados pessoais**:</span><span class="sxs-lookup"><span data-stu-id="66387-150">Select the user name and then select **Personal data**:</span></span>

![Gerenciar dados pessoais de página](gdpr/_static/pd.png)

<span data-ttu-id="66387-152">Notas:</span><span class="sxs-lookup"><span data-stu-id="66387-152">Notes:</span></span>

* <span data-ttu-id="66387-153">Para gerar a `Account/Manage` de código, consulte [Scaffold identidade](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="66387-153">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="66387-154">O **exclua** e **baixar** links atuam somente em dados de identidade padrão.</span><span class="sxs-lookup"><span data-stu-id="66387-154">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="66387-155">Aplicativos que criam os dados de usuário personalizada devem ser estendidos para exclusão/baixar os dados de usuário personalizada.</span><span class="sxs-lookup"><span data-stu-id="66387-155">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="66387-156">Para obter mais informações, consulte [adicionar, baixar e excluir dados de usuário personalizada para identidade](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="66387-156">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="66387-157">Salva os tokens para o usuário que são armazenados na tabela de banco de dados de identidade `AspNetUserTokens` são excluídos quando o usuário é excluído por meio do comportamento de exclusão em cascata devido ao [chave estrangeira](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="66387-157">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="66387-158">[Autenticação de provedor externo](xref:security/authentication/social/index), como Facebook e Google, não está disponível antes da política de cookie é aceito.</span><span class="sxs-lookup"><span data-stu-id="66387-158">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

## <a name="encryption-at-rest"></a><span data-ttu-id="66387-159">Criptografia em repouso</span><span class="sxs-lookup"><span data-stu-id="66387-159">Encryption at rest</span></span>

<span data-ttu-id="66387-160">Alguns bancos de dados e mecanismos de armazenamento permitem a criptografia em repouso.</span><span class="sxs-lookup"><span data-stu-id="66387-160">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="66387-161">Criptografia em repouso:</span><span class="sxs-lookup"><span data-stu-id="66387-161">Encryption at rest:</span></span>

* <span data-ttu-id="66387-162">Criptografa dados armazenados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="66387-162">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="66387-163">Criptografa sem configuração, programação ou outro trabalho para o software que acessa os dados.</span><span class="sxs-lookup"><span data-stu-id="66387-163">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="66387-164">É a opção mais segura e fácil.</span><span class="sxs-lookup"><span data-stu-id="66387-164">Is the easiest and safest option.</span></span>
* <span data-ttu-id="66387-165">Permite que o banco de dados gerenciar chaves e criptografia.</span><span class="sxs-lookup"><span data-stu-id="66387-165">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="66387-166">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="66387-166">For example:</span></span>

* <span data-ttu-id="66387-167">Microsoft SQL e SQL Azure fornecem [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="66387-167">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="66387-168">SQL Azure criptografa o banco de dados por padrão</span><span class="sxs-lookup"><span data-stu-id="66387-168">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="66387-169">[Blobs, arquivos, tabela e o armazenamento de filas do Azure são criptografados por padrão](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="66387-169">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="66387-170">Para bancos de dados que não fornecem internos de criptografia em repouso, você poderá usar a criptografia de disco para fornecer a mesma proteção.</span><span class="sxs-lookup"><span data-stu-id="66387-170">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="66387-171">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="66387-171">For example:</span></span>

* [<span data-ttu-id="66387-172">BitLocker para Windows Server</span><span class="sxs-lookup"><span data-stu-id="66387-172">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="66387-173">Linux:</span><span class="sxs-lookup"><span data-stu-id="66387-173">Linux:</span></span>
  * [<span data-ttu-id="66387-174">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="66387-174">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="66387-175">[EncFS](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="66387-175">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66387-176">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="66387-176">Additional resources</span></span>

* [<span data-ttu-id="66387-177">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="66387-177">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
