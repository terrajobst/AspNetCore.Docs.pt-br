---
title: Introdução à autorização no ASP.NET Core
author: rick-anderson
description: Aprenda as Noções básicas de autorização e como funciona a autorização em aplicativos ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: 5465eb7875ebecd77b628376ef886db0ddd05025
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896973"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Introdução à autorização no ASP.NET Core

<a name="security-authorization-introduction"></a>

Autorização é o processo que determina o que um usuário pode fazer. Por exemplo, um usuário administrativo tem permissão para criar uma biblioteca de documentos e adicionar, editar e excluir documentos. Um usuário não administrativo trabalhando com esta biblioteca só está autorizado a ler os documentos.

A autorização é ortogonal e independente de autenticação. No entanto, a autorização requer um mecanismo de autenticação. Autenticação é o processo de verificação de quem é um usuário. A autenticação pode criar uma ou mais identidades para o usuário atual.

## <a name="authorization-types"></a>Tipos de autorização

A autorização do ASP.NET Core fornece um modelo simples de [funções](xref:security/authorization/roles) declarativas [baseado em políticas](xref:security/authorization/policies). Ela é expressa em requisitos e os manipuladores avaliam as reivindicações de um usuário em relação aos requisitos. Verificações imperativas podem ser baseadas em políticas simples ou políticas que avaliem a identidade do usuário e propriedades do recurso que o usuário está tentando acessar.

## <a name="namespaces"></a>Namespaces

Componentes de autorização, incluindo os atributo `AuthorizeAttribute` e `AllowAnonymousAttribute` , são encontrados no namespace `Microsoft.AspNetCore.Authorization`.

Consulte a documentação em [autorização simples](xref:security/authorization/simple).
