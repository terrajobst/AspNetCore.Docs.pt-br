---
title: Suporte a Regulamento Geral sobre a Proteção de Dados (GDPR) no ASP.NET Core
author: rick-anderson
description: Saiba como acessar os pontos de extensão do GDPR em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 2ccba780ba81bd805d08c9b898617387a879bed3
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660541"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="af334-103">Suporte a Regulamento Geral sobre a Proteção de Dados da UE (GDPR) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af334-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="af334-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="af334-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="af334-105">ASP.NET Core fornece APIs e modelos para ajudar a atender a alguns dos requisitos de [regulamento geral sobre a proteção de dados da UE (GDPR)](https://www.eugdpr.org/) :</span><span class="sxs-lookup"><span data-stu-id="af334-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="af334-106">Os modelos de projeto incluem pontos de extensão e marcação fragmentado que você pode substituir por sua política de uso de privacidade e cookies.</span><span class="sxs-lookup"><span data-stu-id="af334-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="af334-107">A página *Pages/privacy. cshtml* ou *views/Home/privacy. cshtml* fornece uma página para detalhar a política de privacidade do seu site.</span><span class="sxs-lookup"><span data-stu-id="af334-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="af334-108">Para habilitar o recurso de consentimento de cookie padrão como o encontrado nos modelos ASP.NET Core 2,2 em um aplicativo gerado pelo modelo ASP.NET Core 3,0:</span><span class="sxs-lookup"><span data-stu-id="af334-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="af334-109">Adicione `using Microsoft.AspNetCore.Http` à lista de diretivas de uso.</span><span class="sxs-lookup"><span data-stu-id="af334-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="af334-110">Adicionar [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a `Startup.ConfigureServices` e [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) ao `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="af334-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="af334-111">Adicione o consentimento do cookie parcial ao arquivo *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="af334-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="af334-112">Adicione o *\_arquivo CookieConsentPartial. cshtml* ao projeto:</span><span class="sxs-lookup"><span data-stu-id="af334-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="af334-113">Selecione a versão ASP.NET Core 2,2 deste artigo para ler sobre o recurso de consentimento de cookie.</span><span class="sxs-lookup"><span data-stu-id="af334-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="af334-114">Os modelos de projeto incluem pontos de extensão e marcação fragmentado que você pode substituir por sua política de uso de privacidade e cookies.</span><span class="sxs-lookup"><span data-stu-id="af334-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="af334-115">Um recurso de consentimento de cookie permite que você solicite (e rastreie) o consentimento de seus usuários para armazenar informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="af334-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="af334-116">Se um usuário não tiver consentido com a coleta de dados e o aplicativo tiver [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) definido como `true`, os cookies não essenciais não serão enviados para o navegador.</span><span class="sxs-lookup"><span data-stu-id="af334-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="af334-117">Os cookies podem ser marcados como essenciais.</span><span class="sxs-lookup"><span data-stu-id="af334-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="af334-118">Os cookies essenciais são enviados ao navegador mesmo quando o usuário não consentiu e o rastreamento está desabilitado.</span><span class="sxs-lookup"><span data-stu-id="af334-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="af334-119">[Os cookies TempData e Session](#tempdata) não são funcionais quando o rastreamento está desabilitado.</span><span class="sxs-lookup"><span data-stu-id="af334-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="af334-120">A página [gerenciar identidade](#pd) fornece um link para baixar e excluir dados do usuário.</span><span class="sxs-lookup"><span data-stu-id="af334-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="af334-121">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) permite que você teste a maioria dos pontos de extensão GDPR e as APIs adicionadas aos modelos ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="af334-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="af334-122">Consulte o arquivo [Leiame](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) para obter instruções de teste.</span><span class="sxs-lookup"><span data-stu-id="af334-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="af334-123">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af334-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="af334-124">ASP.NET Core suporte a GDPR no código gerado por modelo</span><span class="sxs-lookup"><span data-stu-id="af334-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="af334-125">Os projetos Razor Pages e MVC criados com os modelos de projeto incluem o seguinte suporte a GDPR:</span><span class="sxs-lookup"><span data-stu-id="af334-125">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="af334-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) e [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) são definidos na classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="af334-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="af334-127">A [exibição parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) *\_CookieConsentPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="af334-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="af334-128">Um botão **aceitar** está incluído neste arquivo.</span><span class="sxs-lookup"><span data-stu-id="af334-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="af334-129">Quando o usuário clica no botão **aceitar** , o consentimento para armazenar cookies é fornecido.</span><span class="sxs-lookup"><span data-stu-id="af334-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="af334-130">A página *Pages/privacy. cshtml* ou *views/Home/privacy. cshtml* fornece uma página para detalhar a política de privacidade do seu site.</span><span class="sxs-lookup"><span data-stu-id="af334-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="af334-131">O arquivo *\_CookieConsentPartial. cshtml* gera um link para a página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="af334-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="af334-132">Para aplicativos criados com contas de usuário individuais, a página Gerenciar fornece links para baixar e excluir [dados de usuário pessoal](#pd).</span><span class="sxs-lookup"><span data-stu-id="af334-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="af334-133">CookiePolicyOptions e UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="af334-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="af334-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) são inicializados no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="af334-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="af334-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) é chamado no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="af334-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="af334-136">exibição parcial de \_CookieConsentPartial. cshtml</span><span class="sxs-lookup"><span data-stu-id="af334-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="af334-137">A exibição parcial de *\_CookieConsentPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="af334-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="af334-138">Isso parcial:</span><span class="sxs-lookup"><span data-stu-id="af334-138">This partial:</span></span>

* <span data-ttu-id="af334-139">Obtém o estado do controle para o usuário.</span><span class="sxs-lookup"><span data-stu-id="af334-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="af334-140">Se o aplicativo estiver configurado para exigir consentimento, o usuário deverá consentir antes que os cookies possam ser acompanhados.</span><span class="sxs-lookup"><span data-stu-id="af334-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="af334-141">Se o consentimento for necessário, o painel de consentimento do cookie será corrigido na parte superior da barra de navegação criada pelo arquivo *layout. cshtml do\_* .</span><span class="sxs-lookup"><span data-stu-id="af334-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="af334-142">Fornece um elemento de `<p>` HTML para resumir sua política de uso de cookies e de privacidade.</span><span class="sxs-lookup"><span data-stu-id="af334-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="af334-143">Fornece um link para a página de privacidade ou a exibição em que você pode detalhar a política de privacidade do seu site.</span><span class="sxs-lookup"><span data-stu-id="af334-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="af334-144">Cookies essenciais</span><span class="sxs-lookup"><span data-stu-id="af334-144">Essential cookies</span></span>

<span data-ttu-id="af334-145">Se o consentimento para armazenar cookies não tiver sido fornecido, somente os cookies marcados como essenciais serão enviados para o navegador.</span><span class="sxs-lookup"><span data-stu-id="af334-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="af334-146">O código a seguir torna um cookie essencial:</span><span class="sxs-lookup"><span data-stu-id="af334-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a><span data-ttu-id="af334-147">Os cookies de provedor TempData e estado de sessão não são essenciais</span><span class="sxs-lookup"><span data-stu-id="af334-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="af334-148">O cookie do [provedor TempData](xref:fundamentals/app-state#tempdata) não é essencial.</span><span class="sxs-lookup"><span data-stu-id="af334-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="af334-149">Se o rastreamento estiver desabilitado, o provedor TempData não funcionará.</span><span class="sxs-lookup"><span data-stu-id="af334-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="af334-150">Para habilitar o provedor TempData quando o rastreamento estiver desabilitado, marque o cookie TempData como essencial no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="af334-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="af334-151">Os cookies de [estado de sessão](xref:fundamentals/app-state) não são essenciais.</span><span class="sxs-lookup"><span data-stu-id="af334-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="af334-152">O estado da sessão não é funcional quando o rastreamento está desabilitado.</span><span class="sxs-lookup"><span data-stu-id="af334-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="af334-153">O código a seguir torna os cookies de sessão essenciais:</span><span class="sxs-lookup"><span data-stu-id="af334-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="af334-154">Dados pessoais</span><span class="sxs-lookup"><span data-stu-id="af334-154">Personal data</span></span>

<span data-ttu-id="af334-155">ASP.NET Core aplicativos criados com contas de usuário individuais incluem código para baixar e excluir dados pessoais.</span><span class="sxs-lookup"><span data-stu-id="af334-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="af334-156">Selecione o nome de usuário e, em seguida, selecione **dados pessoais**:</span><span class="sxs-lookup"><span data-stu-id="af334-156">Select the user name and then select **Personal data**:</span></span>

![Página Gerenciar dados pessoais](gdpr/_static/pd.png)

<span data-ttu-id="af334-158">Observações:</span><span class="sxs-lookup"><span data-stu-id="af334-158">Notes:</span></span>

* <span data-ttu-id="af334-159">Para gerar o código de `Account/Manage`, consulte [identidade Scaffold](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="af334-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="af334-160">Os links de **exclusão** e **Download** só agem nos dados de identidade padrão.</span><span class="sxs-lookup"><span data-stu-id="af334-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="af334-161">Aplicativos que criam dados de usuário personalizados devem ser estendidos para excluir/baixar os dados de usuário personalizados.</span><span class="sxs-lookup"><span data-stu-id="af334-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="af334-162">Para obter mais informações, consulte [Adicionar, baixar e excluir dados de usuário personalizados para identidade](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="af334-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="af334-163">Os tokens salvos para o usuário que são armazenados na tabela de banco de dados de identidade `AspNetUserTokens` são excluídos quando o usuário é excluído por meio do comportamento de exclusão em cascata devido à [chave estrangeira](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="af334-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="af334-164">A [autenticação de provedor externo](xref:security/authentication/social/index), como o Facebook e o Google, não está disponível antes que a política de cookie seja aceita.</span><span class="sxs-lookup"><span data-stu-id="af334-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="af334-165">Criptografia em repouso</span><span class="sxs-lookup"><span data-stu-id="af334-165">Encryption at rest</span></span>

<span data-ttu-id="af334-166">Alguns bancos de dados e mecanismos de armazenamento permitem a criptografia em repouso.</span><span class="sxs-lookup"><span data-stu-id="af334-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="af334-167">Criptografia em repouso:</span><span class="sxs-lookup"><span data-stu-id="af334-167">Encryption at rest:</span></span>

* <span data-ttu-id="af334-168">Criptografa os dados armazenados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="af334-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="af334-169">Criptografa sem configuração, programação ou outro trabalho para o software que acessa os dados.</span><span class="sxs-lookup"><span data-stu-id="af334-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="af334-170">É a opção mais fácil e mais segura.</span><span class="sxs-lookup"><span data-stu-id="af334-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="af334-171">Permite que o banco de dados gerencie chaves e criptografia.</span><span class="sxs-lookup"><span data-stu-id="af334-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="af334-172">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="af334-172">For example:</span></span>

* <span data-ttu-id="af334-173">O Microsoft SQL e o Azure SQL fornecem [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="af334-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="af334-174">O SQL Azure criptografa o banco de dados por padrão</span><span class="sxs-lookup"><span data-stu-id="af334-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="af334-175">Os [BLOBs, arquivos, tabelas e armazenamento de fila do Azure são criptografados por padrão](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="af334-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="af334-176">Para bancos de dados que não fornecem criptografia interna em repouso, talvez você possa usar a criptografia de disco para fornecer a mesma proteção.</span><span class="sxs-lookup"><span data-stu-id="af334-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="af334-177">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="af334-177">For example:</span></span>

* [<span data-ttu-id="af334-178">BitLocker para Windows Server</span><span class="sxs-lookup"><span data-stu-id="af334-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="af334-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="af334-179">Linux:</span></span>
  * [<span data-ttu-id="af334-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="af334-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="af334-181">[Encfs](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="af334-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af334-182">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="af334-182">Additional resources</span></span>

* [<span data-ttu-id="af334-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="af334-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="af334-184">GDPR-adicionando um botão revogar consentimento no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af334-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
