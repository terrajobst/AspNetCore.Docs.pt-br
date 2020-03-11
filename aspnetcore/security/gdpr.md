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
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Suporte a Regulamento Geral sobre a Proteção de Dados da UE (GDPR) no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core fornece APIs e modelos para ajudar a atender a alguns dos requisitos de [regulamento geral sobre a proteção de dados da UE (GDPR)](https://www.eugdpr.org/) :

::: moniker range=">= aspnetcore-3.0"

* Os modelos de projeto incluem pontos de extensão e marcação fragmentado que você pode substituir por sua política de uso de privacidade e cookies.
* A página *Pages/privacy. cshtml* ou *views/Home/privacy. cshtml* fornece uma página para detalhar a política de privacidade do seu site.

Para habilitar o recurso de consentimento de cookie padrão como o encontrado nos modelos ASP.NET Core 2,2 em um aplicativo gerado pelo modelo ASP.NET Core 3,0:

* Adicione `using Microsoft.AspNetCore.Http` à lista de diretivas de uso.
* Adicionar [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a `Startup.ConfigureServices` e [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) ao `Startup.Configure`:

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Adicione o consentimento do cookie parcial ao arquivo *_Layout. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Adicione o *\_arquivo CookieConsentPartial. cshtml* ao projeto:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Selecione a versão ASP.NET Core 2,2 deste artigo para ler sobre o recurso de consentimento de cookie.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Os modelos de projeto incluem pontos de extensão e marcação fragmentado que você pode substituir por sua política de uso de privacidade e cookies.
* Um recurso de consentimento de cookie permite que você solicite (e rastreie) o consentimento de seus usuários para armazenar informações pessoais. Se um usuário não tiver consentido com a coleta de dados e o aplicativo tiver [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) definido como `true`, os cookies não essenciais não serão enviados para o navegador.
* Os cookies podem ser marcados como essenciais. Os cookies essenciais são enviados ao navegador mesmo quando o usuário não consentiu e o rastreamento está desabilitado.
* [Os cookies TempData e Session](#tempdata) não são funcionais quando o rastreamento está desabilitado.
* A página [gerenciar identidade](#pd) fornece um link para baixar e excluir dados do usuário.

O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) permite que você teste a maioria dos pontos de extensão GDPR e as APIs adicionadas aos modelos ASP.NET Core 2,1. Consulte o arquivo [Leiame](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) para obter instruções de teste.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>ASP.NET Core suporte a GDPR no código gerado por modelo

Os projetos Razor Pages e MVC criados com os modelos de projeto incluem o seguinte suporte a GDPR:

* [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) e [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) são definidos na classe `Startup`.
* A [exibição parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) *\_CookieConsentPartial. cshtml* . Um botão **aceitar** está incluído neste arquivo. Quando o usuário clica no botão **aceitar** , o consentimento para armazenar cookies é fornecido.
* A página *Pages/privacy. cshtml* ou *views/Home/privacy. cshtml* fornece uma página para detalhar a política de privacidade do seu site. O arquivo *\_CookieConsentPartial. cshtml* gera um link para a página de privacidade.
* Para aplicativos criados com contas de usuário individuais, a página Gerenciar fornece links para baixar e excluir [dados de usuário pessoal](#pd).

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions e UseCookiePolicy

[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) são inicializados no `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) é chamado no `Startup.Configure`:

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a>exibição parcial de \_CookieConsentPartial. cshtml

A exibição parcial de *\_CookieConsentPartial. cshtml* :

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Isso parcial:

* Obtém o estado do controle para o usuário. Se o aplicativo estiver configurado para exigir consentimento, o usuário deverá consentir antes que os cookies possam ser acompanhados. Se o consentimento for necessário, o painel de consentimento do cookie será corrigido na parte superior da barra de navegação criada pelo arquivo *layout. cshtml do\_* .
* Fornece um elemento de `<p>` HTML para resumir sua política de uso de cookies e de privacidade.
* Fornece um link para a página de privacidade ou a exibição em que você pode detalhar a política de privacidade do seu site.

## <a name="essential-cookies"></a>Cookies essenciais

Se o consentimento para armazenar cookies não tiver sido fornecido, somente os cookies marcados como essenciais serão enviados para o navegador. O código a seguir torna um cookie essencial:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>Os cookies de provedor TempData e estado de sessão não são essenciais

O cookie do [provedor TempData](xref:fundamentals/app-state#tempdata) não é essencial. Se o rastreamento estiver desabilitado, o provedor TempData não funcionará. Para habilitar o provedor TempData quando o rastreamento estiver desabilitado, marque o cookie TempData como essencial no `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

Os cookies de [estado de sessão](xref:fundamentals/app-state) não são essenciais. O estado da sessão não é funcional quando o rastreamento está desabilitado. O código a seguir torna os cookies de sessão essenciais:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Dados pessoais

ASP.NET Core aplicativos criados com contas de usuário individuais incluem código para baixar e excluir dados pessoais.

Selecione o nome de usuário e, em seguida, selecione **dados pessoais**:

![Página Gerenciar dados pessoais](gdpr/_static/pd.png)

Observações:

* Para gerar o código de `Account/Manage`, consulte [identidade Scaffold](xref:security/authentication/scaffold-identity).
* Os links de **exclusão** e **Download** só agem nos dados de identidade padrão. Aplicativos que criam dados de usuário personalizados devem ser estendidos para excluir/baixar os dados de usuário personalizados. Para obter mais informações, consulte [Adicionar, baixar e excluir dados de usuário personalizados para identidade](xref:security/authentication/add-user-data).
* Os tokens salvos para o usuário que são armazenados na tabela de banco de dados de identidade `AspNetUserTokens` são excluídos quando o usuário é excluído por meio do comportamento de exclusão em cascata devido à [chave estrangeira](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* A [autenticação de provedor externo](xref:security/authentication/social/index), como o Facebook e o Google, não está disponível antes que a política de cookie seja aceita.

::: moniker-end

## <a name="encryption-at-rest"></a>Criptografia em repouso

Alguns bancos de dados e mecanismos de armazenamento permitem a criptografia em repouso. Criptografia em repouso:

* Criptografa os dados armazenados automaticamente.
* Criptografa sem configuração, programação ou outro trabalho para o software que acessa os dados.
* É a opção mais fácil e mais segura.
* Permite que o banco de dados gerencie chaves e criptografia.

Por exemplo:

* O Microsoft SQL e o Azure SQL fornecem [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [O SQL Azure criptografa o banco de dados por padrão](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* Os [BLOBs, arquivos, tabelas e armazenamento de fila do Azure são criptografados por padrão](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Para bancos de dados que não fornecem criptografia interna em repouso, talvez você possa usar a criptografia de disco para fornecer a mesma proteção. Por exemplo:

* [BitLocker para Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [Encfs](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Recursos adicionais

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR-adicionando um botão revogar consentimento no ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
