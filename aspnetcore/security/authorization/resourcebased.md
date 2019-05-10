---
title: Autorização baseada em recursos no ASP.NET Core
author: scottaddie
description: Saiba como implementar a autorização baseada em recursos em um aplicativo ASP.NET Core quando um atributo Authorize não serão suficientes.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: afc152ea677cab42d57bd642b4821159f125117e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891733"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>Autorização baseada em recursos no ASP.NET Core

Estratégia de autorização depende do recurso que está sendo acessado. Considere um documento que tem uma propriedade de autor. Somente o autor tem permissão para atualizar o documento. Consequentemente, o documento deve ser recuperado do armazenamento de dados antes que a avaliação de autorização pode ocorrer.

Avaliação de atributo ocorre antes da vinculação de dados e antes da execução do manipulador de página ou ação que carrega o documento. Por esses motivos, a autorização declarativa com um `[Authorize]` atributo não é suficiente. Em vez disso, você pode invocar um método de autorização personalizada&mdash;um estilo conhecido como *autorização imperativa*.

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([como baixar](xref:index#how-to-download-a-sample)).

[Criar um aplicativo ASP.NET Core com os dados de usuário protegidos por autorização](xref:security/authorization/secure-data) contém um aplicativo de exemplo que usa a autorização baseada em recursos.

## <a name="use-imperative-authorization"></a>Usar a autorização obrigatória

Autorização é implementada como um [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) de serviço e é registrado na coleção de serviço dentro de `Startup` classe. O serviço é disponibilizado por meio [injeção de dependência](xref:fundamentals/dependency-injection) para manipuladores de página ou de ações.

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` tem dois `AuthorizeAsync` sobrecargas de método: uma aceitando o recurso e o nome da política e a outra aceitando o recurso e uma lista de requisitos para avaliar.

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

O exemplo a seguir, o recurso a ser protegido é carregado em um personalizado `Document` objeto. Um `AuthorizeAsync` sobrecarga é invocada para determinar se o usuário atual tem permissão para editar o documento. Uma política de autorização personalizada "EditPolicy" é acrescentada a uma decisão. Ver [autorização de baseado em políticas personalizadas](xref:security/authorization/policies) para obter mais informações sobre como criar políticas de autorização.

> [!NOTE]
> O código a seguir exemplos pressupõem que a autenticação foi executada e o conjunto de `User` propriedade.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>Escrever um Gerenciador de recursos

Escrevendo um manipulador para a autorização baseada em recursos não é muito diferente [escrevendo um manipulador de requisitos simples](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler). Criar uma classe de requisito personalizado e implementar uma classe de manipulador de requisito. Para obter mais informações sobre como criar uma classe de requisito, consulte [requisitos de](xref:security/authorization/policies#requirements).

A classe de manipulador Especifica o requisito e o tipo de recurso. Por exemplo, uma utilização de manipulador uma `SameAuthorRequirement` e um `Document` recursos a seguir:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

No exemplo anterior, imagine que `SameAuthorRequirement` é um caso especial de uma mais genérica `SpecificAuthorRequirement` classe. O `SpecificAuthorRequirement` classe (não mostrado) contém um `Name` propriedade que representa o nome do autor. O `Name` propriedade pode ser definida como o usuário atual.

Registrar o requisito e o manipulador no `Startup.ConfigureServices`:

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a>Requisitos operacionais

Se você estiver fazendo decisões com base nos resultados de operações CRUD (Create, Read, Update, Delete), use o [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) classe auxiliar. Essa classe permite que você escreva um único manipulador em vez de uma classe individual para cada tipo de operação. Para usá-lo, forneça alguns nomes de operação:

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

O manipulador é implementado da seguinte maneira, usando um `OperationAuthorizationRequirement` requisito e um `Document` recursos:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

O manipulador anterior valida a operação usando o recurso, a identidade do usuário e o requisito `Name` propriedade.

Para chamar um manipulador de recursos operacionais, especifique a operação ao invocar `AuthorizeAsync` em seu manipulador de página ou ação. O exemplo a seguir determina se o usuário autenticado tem permissão para exibir o documento.

> [!NOTE]
> O código a seguir exemplos pressupõem que a autenticação foi executada e o conjunto de `User` propriedade.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Se a autorização for bem-sucedida, a página para exibir o documento é retornada. Se falhar de autorização, mas o usuário é autenticado, o retorno de `ForbidResult` informa qualquer middleware de autenticação que houve falha na autorização. Um `ChallengeResult` é retornado quando a autenticação deve ser executada. Para clientes de navegador interativo, ele pode ser apropriado redirecionar o usuário para uma página de logon.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Se a autorização for bem-sucedida, o modo de exibição para o documento é retornado. Se a autorização falhar, retornando `ChallengeResult` informa qualquer middleware de autenticação que houve falha na autorização e o middleware pode levar a resposta apropriada. Uma resposta apropriada pode estar retornando um código de status 401 ou 403. Para clientes de navegador interativo, isso poderia significar a redirecionar o usuário para uma página de logon.

::: moniker-end
