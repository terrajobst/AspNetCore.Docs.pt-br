---
ms.openlocfilehash: a7be92adffc06ac0f25b84ea15d1a8c4896f4f9d
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54397082"
---
<!--Don't update this for 2.2, use the 2.2 version --> A chamada para [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity) define as configurações de esquema padrão. O [AddAuthentication(String)](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_String_) conjuntos de sobrecarregar os [DefaultScheme](/dotnet/api/microsoft.aspnetcore.authentication.authenticationoptions.defaultscheme) propriedade. O [AddAuthentication (ação&lt;AuthenticationOptions&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sobrecarga permite configurar opções de autenticação, que podem ser usadas para definir os esquemas de autenticação padrão para finalidades diferentes. As chamadas subsequentes para `AddAuthentication` substituição configurada anteriormente [AuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions) propriedades.

[AuthenticationBuilder](/dotnet/api/microsoft.aspnetcore.authentication.authenticationbuilder) métodos de extensão que registra um manipulador de autenticação podem ser chamados apenas uma vez por esquema de autenticação. Há sobrecargas que permitem configurar as propriedades de esquema, o nome de esquema e nome de exibição.
