---
title: Comece com as APIs de proteção de dados no ASP.NET Core
author: rick-anderson
description: Saiba como usar as APIs de proteção de dados do ASP.NET Core para proteger e Desproteger dados em um aplicativo.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 25bf099a3d9edd7e6e0872725cbc3707750314e6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087633"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Comece com as APIs de proteção de dados no ASP.NET Core

<a name="security-data-protection-getting-started"></a>

Em seus dados mais simples, protegendo consiste as seguintes etapas:

1. Crie dados de um protetor de um provedor de proteção de dados.

2. Chamar o `Protect` método com os dados que você deseja proteger.

3. Chamar o `Unprotect` método com os dados que você deseja transformar de volta em texto sem formatação.

A maioria das estruturas e modelos de aplicativo, como o ASP.NET Core ou SignalR, já que configurar o sistema de proteção de dados e adicioná-lo a um contêiner de serviço que você acessa por meio da injeção de dependência. O exemplo a seguir demonstra Configurando um contêiner de serviço para injeção de dependência e registrando a pilha da proteção de dados, recebendo o provedor de proteção de dados por meio da DI, criando um protetor e desproteção em seguida, proteção de dados.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Quando você cria um protetor deve fornecer um ou mais [cadeias de caracteres de finalidade](xref:security/data-protection/consumer-apis/purpose-strings). Uma cadeia de caracteres de finalidade fornece isolamento entre os consumidores. Por exemplo, um protetor criado com uma cadeia de caracteres de finalidade de "verde" seria capaz de Desproteger dados fornecidos por um protetor, com a finalidade de "roxo".

>[!TIP]
> Instâncias do `IDataProtectionProvider` e `IDataProtector` sejam thread-safe para chamadores vários. Ele deve que depois que um componente obtém uma referência a um `IDataProtector` por meio de uma chamada para `CreateProtector`, ele usará essa referência para várias chamadas para `Protect` e `Unprotect`.
>
>Uma chamada para `Unprotect` lançará CryptographicException se o conteúdo protegido não pode ser verificado ou decifrado. Alguns componentes talvez queira ignorar erros durante a Desproteger operações; um componente que lê os cookies de autenticação pode manipular esse erro e tratar a solicitação como se ele não tinha nenhum cookie em todos os em vez de falhar a solicitação imediatamente. Componentes que desejam esse comportamento devem especificamente capturar CryptographicException em vez de assimilação de todas as exceções.
