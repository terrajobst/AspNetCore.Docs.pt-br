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
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Suporte da UE Data Protection GDPR (regulamento geral) no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

O ASP.NET Core fornece modelos e APIs para ajudar a atender a alguns dos [regulamentação de proteção de dados geral (GDPR) da UE](https://www.eugdpr.org/) requisitos:

* Os modelos de projeto incluem pontos de extensão e a marcação de stub que você pode substituir por sua privacidade e a política de uso de cookies.
* Um recurso de consentimento do cookie permite que você pedir consentimento (e acompanhar uma) de seus usuários para armazenar informações pessoais. Se um usuário não tiver consentido para coleta de dados e o aplicativo tem [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) definido como `true`, não-essenciais cookies não são enviados para o navegador.
* Os cookies podem ser marcados como essenciais. Cookies essenciais são enviados ao navegador, mesmo quando o usuário não tiver consentido e acompanhamento está desabilitado.
* [Cookies de sessão e TempData](#tempdata) não são funcionais quando o rastreamento está desabilitado.
* O [gerenciar identidades](#pd) página fornece um link para baixar e excluir dados de usuário.

O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) permite que você teste a maioria dos pontos de extensão de GDPR e APIs adicionadas para os modelos do ASP.NET Core 2.1. Consulte a [Leiame](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) arquivo para obter instruções de teste.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>Suporte de GDPR do ASP.NET Core no código gerado pelo modelo

::: moniker range="< aspnetcore-2.2"

Páginas do Razor e MVC projetos criados com os modelos de projeto não têm suporte para consentimento GDPR ou cookie. Para adicionar o GDPR, copie o código gerado nos modelos do ASP.NET Core 2.2.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Páginas do Razor e MVC projetos criados com os modelos de projeto incluem o seguinte suporte GDPR:

::: moniker-end

* [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) e [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) são definidos `Startup` classe.
* O  *\_CookieConsentPartial.cshtml* [exibição parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper). Uma **Accept** botão está incluído nesse arquivo. Quando o usuário clica o **Accept** botão, para armazenar cookies de consentimento é fornecido.
* O *Pages/Privacy.cshtml* página ou *Views/Home/Privacy.cshtml* exibição fornece uma página para a política de privacidade do seu site de detalhe. O  *\_CookieConsentPartial.cshtml* arquivo gera um link para a página de privacidade.
* Para os aplicativos criados com as contas de usuário individual, a página Gerenciar fornece links para baixar e excluir [dados pessoais do usuário](#pd).

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions e UseCookiePolicy

[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) são inicializadas em `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) é chamado no `Startup.Configure`:

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a>\_Modo de exibição parcial CookieConsentPartial.cshtml

O  *\_CookieConsentPartial.cshtml* exibição parcial:

[!code-html[](gdpr/sample/RP/Pages/Shared/_CookieConsentPartial.cshtml)]

Essa parcial:

* Obtém o estado do controle para o usuário. Se o aplicativo estiver configurado para exigir o consentimento, o usuário deve consentir antes de cookies podem ser controlados. Se for necessário o consentimento, o painel de consentimento do cookie é fixa na parte superior da barra de navegação criada pelo  *\_layout. cshtml* arquivo.
* Fornece um HTML `<p>` elemento para resumir a sua privacidade e cookie usar a política.
* Fornece um link para a página de privacidade ou exibição onde você pode detalhar a política de privacidade do seu site.

## <a name="essential-cookies"></a>Cookies essenciais

Se dar consentimento armazenar cookies ainda não foi fornecido, somente os cookies marcados essenciais são enviados para o navegador. O código a seguir faz com que um cookie essenciais:

[!code-csharp[Main](gdpr/sample/RP/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>Cookies de estado de sessão e o provedor de TempData não são essenciais

O [provedor de TempData](xref:fundamentals/app-state#tempdata) cookie não essencial. Se o controle estiver desabilitado, o provedor de TempData não é funcional. Para habilitar o provedor de TempData quando o rastreamento está desabilitado, marcar o cookie de TempData como essencial para `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/RP/Startup.cs?name=snippet1)]

[Estado de sessão](xref:fundamentals/app-state) cookies não são essenciais. Estado de sessão não está funcionando quando o rastreamento está desabilitado. O código a seguir faz com que os cookies de sessão essenciais:

[!code-csharp[](gdpr/sample/RP/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Dados pessoais

Aplicativos ASP.NET Core criados com contas de usuário individuais incluem código para baixar e excluir dados pessoais.

Selecione o nome de usuário e, em seguida, selecione **dados pessoais**:

![Gerenciar dados pessoais de página](gdpr/_static/pd.png)

Notas:

* Para gerar a `Account/Manage` de código, consulte [Scaffold identidade](xref:security/authentication/scaffold-identity).
* O **exclua** e **baixar** links atuam somente em dados de identidade padrão. Aplicativos que criam os dados de usuário personalizada devem ser estendidos para exclusão/baixar os dados de usuário personalizada. Para obter mais informações, consulte [adicionar, baixar e excluir dados de usuário personalizada para identidade](xref:security/authentication/add-user-data).
* Salva os tokens para o usuário que são armazenados na tabela de banco de dados de identidade `AspNetUserTokens` são excluídos quando o usuário é excluído por meio do comportamento de exclusão em cascata devido ao [chave estrangeira](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* [Autenticação de provedor externo](xref:security/authentication/social/index), como Facebook e Google, não está disponível antes da política de cookie é aceito.

## <a name="encryption-at-rest"></a>Criptografia em repouso

Alguns bancos de dados e mecanismos de armazenamento permitem a criptografia em repouso. Criptografia em repouso:

* Criptografa dados armazenados automaticamente.
* Criptografa sem configuração, programação ou outro trabalho para o software que acessa os dados.
* É a opção mais segura e fácil.
* Permite que o banco de dados gerenciar chaves e criptografia.

Por exemplo:

* Microsoft SQL e SQL Azure fornecem [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [SQL Azure criptografa o banco de dados por padrão](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Blobs, arquivos, tabela e o armazenamento de filas do Azure são criptografados por padrão](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Para bancos de dados que não fornecem internos de criptografia em repouso, você poderá usar a criptografia de disco para fornecer a mesma proteção. Por exemplo:

* [BitLocker para Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [EncFS](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Recursos adicionais

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
