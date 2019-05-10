---
title: Imutabilidade de chave e as configurações de chave no ASP.NET Core
author: rick-anderson
description: Conheça os detalhes de implementação de imutabilidade de chave de proteção de dados do ASP.NET Core APIs.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 7796cb102c0f6f03809704016fd36b28c7a82438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892283"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Imutabilidade de chave e as configurações de chave no ASP.NET Core

Depois que um objeto é persistido para o repositório de backup, sua representação para sempre é fixo. Novos dados podem ser adicionados ao repositório de backup, mas os dados existentes nunca podem ser modificados. A principal finalidade desse comportamento é evitar dados corrompidos.

Uma consequência deste comportamento é que, quando uma chave é gravada para o repositório de backup, é imutável. As datas de criação, ativação e expiração nunca podem ser alteradas, embora ele pode ser revogada usando `IKeyManager`. Além disso, suas informações de algorítmicos subjacentes, material de chave mestre e a criptografia em Propriedades do rest também são imutáveis.

Se o desenvolvedor altera qualquer configuração que afeta a persistência de chave, essas alterações não entrarão em vigor até a próxima vez que uma chave é gerada, por meio de uma chamada explícita para `IKeyManager.CreateNewKey` ou por meio do sistema proteção de dados próprio [chave automática geração](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) comportamento. As configurações que afetam a persistência de chave são da seguinte maneira:

* [O tempo de vida de chave padrão](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [A criptografia de chave no mecanismo de rest](xref:security/data-protection/implementation/key-encryption-at-rest)

* [As informações de algorítmicos contidas na chave do](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Se você precisar dessas configurações arranque anteriores à próxima chave automática sem interrupção de tempo, considere fazer uma chamada explícita para `IKeyManager.CreateNewKey` para forçar a criação de uma nova chave. Lembre-se de fornecer uma data de ativação explícita ({agora + 2 dias} é uma boa regra prática para dar tempo para a alteração seja propagada) e a data de expiração na chamada.

>[!TIP]
> Tocar o repositório de todos os aplicativos devem especificar as mesmas configurações com o `IDataProtectionBuilder` métodos de extensão. Caso contrário, as propriedades da chave persistente será dependentes do aplicativo específico que invocou as rotinas de geração de chave.
