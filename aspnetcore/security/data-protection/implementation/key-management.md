---
title: Gerenciamento de chaves no ASP.NET Core
author: rick-anderson
description: Conheça os detalhes de implementação do gerenciamento de chaves de proteção de dados do ASP.NET Core APIs.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 431bdf2d3076c83279b78f327ddb647f69e6e584
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892293"
---
# <a name="key-management-in-aspnet-core"></a>Gerenciamento de chaves no ASP.NET Core

<a name="data-protection-implementation-key-management"></a>

O sistema de proteção de dados gerencia automaticamente o tempo de vida das chaves mestras usado para proteger e Desproteger cargas. Cada chave pode existir em um dos quatro estágios:

* Criada - a chave existe no anel de chave, mas ainda não foi ativada. A chave não deve ser usada para novas operações de proteção até que haja tempo suficiente tenha decorrido a chave tem tido a oportunidade de se propagar para todos os computadores que estão consumindo esse anel de chave.

* Ativo - a chave existe no anel de chave e deve ser usado para todas as operações de proteção de novo.

* Expirou - a chave executou seu tempo de vida natural e não deve mais ser usada para novas operações de proteção.

* Revogado - a chave for comprometida e não deve ser usada para novas operações de proteção.

Todos criadas, ativas e vencidas chaves podem ser usadas para desproteger cargas de entrada. Chaves revogadas por padrão não podem ser usadas para desproteger cargas, mas o desenvolvedor de aplicativos pode [substituir esse comportamento](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) se necessário.

>[!WARNING]
> O desenvolvedor pode ser tentado a excluir uma chave do anel de chave (por exemplo, excluindo o arquivo correspondente do sistema de arquivos). Nesse ponto, todos os dados protegidos pela chave é indecifráveis permanentemente e não há nenhuma substituição de emergência como ocorre com chaves revogadas. Excluir uma chave é verdadeiramente destrutivo comportamento e, consequentemente, o sistema de proteção de dados não expõe nenhuma API de primeira classe para realizar esta operação.

## <a name="default-key-selection"></a>Seleção de chave padrão

Quando o sistema de proteção de dados lê o anel de chave do repositório de backup, ele tenta localizar uma chave de "padrão" do anel de chave. A chave padrão é usada para novas operações de proteção.

A heurística geral é que o sistema de proteção de dados escolhe a chave com a data mais recente de ativação como a chave padrão. (Há um pequeno fator para permitir o relógio do servidor para servidor distorção.) Se a chave expirou ou foi revogada, e se o aplicativo não tiver desabilitado automática da chave de geração, uma nova chave será gerada com a ativação imediata pela [expiração e sem interrupção de chave](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) política abaixo.

O motivo pelo qual o sistema de proteção de dados gera uma nova chave imediatamente em vez de fazer fallback para uma chave diferente é que a nova geração de chave deve ser tratada como uma expiração implícita de todas as chaves que foram ativados antes da nova chave. A ideia geral é que novas chaves podem ter sido configuradas com algoritmos diferentes ou mecanismos de criptografia em repouso que chaves antigos, e o sistema deve preferir a configuração atual em vez de fazer fallback.

Há uma exceção. Se o desenvolvedor do aplicativo tiver [desabilitada a geração automática de chaves](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), em seguida, o sistema de proteção de dados deve escolher algo como a chave padrão. Nesse cenário de fallback, o sistema escolherá a chave não revogado com a data de ativação mais recente, com preferência para chaves que tem tido tempo para ser propagada para outros computadores no cluster. O sistema de fallback pode acabar de escolher uma chave expirada padrão como resultado. O sistema de fallback nunca escolherá uma chave revogada como a chave padrão e se o anel de chave estiver vazio ou todas as chaves foi revogada, em seguida, o sistema produzirá um erro na inicialização.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Expiração da chave e sem interrupção

Quando uma chave é criada, ele automaticamente tenha dado uma data de ativação de {agora + 2 dias} e uma data de expiração de {agora + 90 dias}. O atraso de 2 dias antes da ativação fornece o tempo-chave para se propagar através do sistema. Ou seja, ele permite que outros aplicativos que aponta para o repositório de backup observar a chave em seu próximo período de atualização automática, além de aumentar as chances de que quando a chave de anel ativo faz tornam-se que ele ser propagado para todos os aplicativos que talvez precise usá-lo.

Se a chave padrão irá expirar dentro de 2 dias e o anel de chave ainda não tiver uma chave que estará ativa após a expiração da chave padrão, o sistema de proteção de dados persistirá automaticamente uma nova chave para o anel de chave. Essa nova chave tem uma data de ativação de {data de validade da chave padrão} e uma data de expiração de {agora + 90 dias}. Isso permite que o sistema automaticamente reverter chaves regularmente sem interrupção do serviço.

Pode haver circunstâncias em que uma chave será criada com a ativação imediata. Um exemplo seria quando o aplicativo não foi executada por um tempo e todas as chaves do anel de chave estão expiradas. Quando isso acontece, a chave é dada uma data de ativação de {agora} sem o atraso de ativação de 2 dias normal.

O tempo de vida de chave padrão é de 90 dias, embora isso seja configurável, como no exemplo a seguir.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Um administrador também pode alterar o padrão para todo o sistema, embora uma chamada explícita para `SetDefaultKeyLifetime` substituirá qualquer política de todo o sistema. O tempo de vida de chave padrão não pode ser menor do que 7 dias.

## <a name="automatic-key-ring-refresh"></a>Atualização automática de anel de chave

Quando o sistema de proteção de dados é inicializado, ele lê o anel de chave do repositório subjacente e armazena em cache na memória. Esse cache permite proteger e Desproteger operações continuar sem atingir o repositório de backup. O sistema verificará o repositório de backup para que as alterações automaticamente aproximadamente a cada 24 horas ou quando a chave padrão atual expirar, o que vier primeiro.

>[!WARNING]
> Os desenvolvedores devem muito raramente (se utilizadas) precisará usar as APIs de gerenciamento de chave diretamente. O sistema de proteção de dados executará gerenciamento automático de chaves, conforme descrito acima.

O sistema de proteção de dados expõe uma interface `IKeyManager` que pode ser usado para inspecionar e fazer alterações para o anel de chave. O sistema de injeção de dependência que forneceu a instância do `IDataProtectionProvider` também pode fornecer uma instância de `IKeyManager` para seu consumo. Como alternativa, você pode extrair os `IKeyManager` diretamente do `IServiceProvider` como no exemplo a seguir.

Qualquer operação que modifica o anel de chave (Criando uma nova chave explicitamente ou executar uma revogação) invalida o cache na memória. A próxima chamada para `Protect` ou `Unprotect` fará com que o sistema de proteção de dados releia o anel de chave e recriar o cache.

O exemplo a seguir demonstra como usar o `IKeyManager` interface para inspecionar e manipular o anel de chave, incluindo Revogando existente de chaves e gerar uma nova chave manualmente.

[!code-csharp[](key-management/samples/key-management.cs)]

## <a name="key-storage"></a>Armazenamento de chaves

O sistema de proteção de dados tem uma heurística, no qual ele tenta deduzir um mecanismo de criptografia em repouso e o local de armazenamento de chave apropriado automaticamente. O mecanismo de persistência de chave também é configurável pelo desenvolvedor do aplicativo. Os documentos a seguir discutem as implementações de caixa de entrada desses mecanismos:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
