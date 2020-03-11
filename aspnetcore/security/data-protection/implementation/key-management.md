---
title: Gerenciamento de chaves no ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação das APIs de gerenciamento de chaves de proteção de dados ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: c571222d734fa69183563aefa5cc6ce5a10e7612
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664706"
---
# <a name="key-management-in-aspnet-core"></a>Gerenciamento de chaves no ASP.NET Core

<a name="data-protection-implementation-key-management"></a>

O sistema de proteção de dados gerencia automaticamente o tempo de vida das chaves mestras usadas para proteger e desproteger cargas. Cada chave pode existir em um dos quatro estágios:

* Criado-a chave existe no anel de chave, mas ainda não foi ativada. A chave não deve ser usada para novas operações de proteção até que o tempo suficiente tenha decorrido que a chave tenha tido a oportunidade de se propagar para todos os computadores que estão consumindo esse anel de chave.

* Ativo-a chave existe no anel de chave e deve ser usada para todas as novas operações de proteção.

* Expirado-a chave executou seu tempo de vida natural e não deve mais ser usada para novas operações de proteção.

* Revogado-a chave está comprometida e não deve ser usada para novas operações de proteção.

As chaves criadas, ativas e expiradas podem ser usadas para desproteger as cargas de entrada. As chaves revogadas por padrão não podem ser usadas para desproteger cargas, mas o desenvolvedor do aplicativo pode [substituir esse comportamento](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) , se necessário.

>[!WARNING]
> O desenvolvedor pode ser tentado a excluir uma chave do anel de chave (por exemplo, excluindo o arquivo correspondente do sistema de arquivos). Nesse ponto, todos os dados protegidos pela chave são permanentemente indecifráveis, e não há nenhuma substituição de emergência como há com chaves revogadas. A exclusão de uma chave é um comportamento realmente destrutivo e, consequentemente, o sistema de proteção de dados não expõe nenhuma API de primeira classe para executar essa operação.

## <a name="default-key-selection"></a>Seleção de chave padrão

Quando o sistema de proteção de dados lê o anel de chave do repositório de backup, ele tentará localizar uma chave "padrão" do anel de chave. A chave padrão é usada para novas operações de proteção.

A heurística geral é que o sistema de proteção de dados escolhe a chave com a data de ativação mais recente como a chave padrão. (Há um pequeno fator de adequações para permitir a distorção de relógio de servidor para servidor.) Se a chave estiver expirada ou revogada, e se o aplicativo não tiver desabilitado a geração automática de chaves, uma nova chave será gerada com ativação imediata de acordo com a [expiração de chave e a política de interrupção](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) abaixo.

O motivo pelo qual o sistema de proteção de dados gera uma nova chave imediatamente em vez de fazer fallback para uma chave diferente é que a nova geração de chave deve ser tratada como uma expiração implícita de todas as chaves que foram ativadas antes da nova chave. A ideia geral é que as novas chaves podem ter sido configuradas com algoritmos diferentes ou mecanismos de criptografia em repouso do que as chaves antigas, e o sistema deve preferir a configuração atual ao fazer o fallback.

Há uma exceção. Se o desenvolvedor do aplicativo tiver [desabilitado a geração automática de chaves](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), o sistema de proteção de dados deverá escolher algo como a chave padrão. Nesse cenário de fallback, o sistema escolherá a chave não revogada com a data de ativação mais recente, com preferência dada às chaves que tiveram tempo para se propagar para outros computadores no cluster. O sistema de fallback pode acabar escolhendo uma chave padrão expirada como resultado. O sistema de fallback nunca escolherá uma chave revogada como a chave padrão e, se o anel de chave estiver vazio ou cada chave tiver sido revogada, o sistema produzirá um erro na inicialização.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Expiração de chave e sem interrupção

Quando uma chave é criada, ela recebe automaticamente uma data de ativação de {Now + 2 dias} e uma data de expiração de {Now + 90 dias}. O atraso de 2 dias antes da ativação dá ao momento da chave a propagação no sistema. Ou seja, ele permite que outros aplicativos apontem para o armazenamento de backup para observar a chave em seu próximo período de atualização automática, maximizando a probabilidade de quando o anel de chave se torna ativo, propagando-o para todos os aplicativos que talvez precisem usá-lo.

Se a chave padrão expirar em 2 dias e se o anel de chave ainda não tiver uma chave que estará ativa após a expiração da chave padrão, o sistema de proteção de dados manterá automaticamente uma nova chave para o anel de chave. Essa nova chave tem uma data de ativação de {data de validade da chave padrão} e uma data de expiração de {Now + 90 dias}. Isso permite que o sistema acumule automaticamente as chaves regularmente sem interrupção do serviço.

Pode haver circunstâncias em que uma chave será criada com ativação imediata. Um exemplo seria quando o aplicativo não for executado por um tempo e todas as chaves no anel de chave estiverem expiradas. Quando isso acontece, a chave recebe uma data de ativação de {Now} sem o atraso de ativação normal de 2 dias.

O tempo de vida da chave padrão é de 90 dias, embora isso seja configurável como no exemplo a seguir.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Um administrador também pode alterar o sistema padrão, embora uma chamada explícita para `SetDefaultKeyLifetime` substitua qualquer política de todo o sistema. O tempo de vida da chave padrão não pode ser menor que 7 dias.

## <a name="automatic-key-ring-refresh"></a>Atualização automática de anel de chave

Quando o sistema de proteção de dados é inicializado, ele lê o anel de chave do repositório subjacente e o armazena em cache na memória. Esse cache permite que as operações proteger e desproteger continuem sem alcançar o armazenamento de backup. O sistema verificará automaticamente o repositório de backup em busca de alterações aproximadamente a cada 24 horas ou quando a chave padrão atual expirar, o que ocorrer primeiro.

>[!WARNING]
> Os desenvolvedores devem muito raramente (se nunca) precisarem usar as APIs de gerenciamento de chaves diretamente. O sistema de proteção de dados executará o gerenciamento automático de chaves, conforme descrito acima.

O sistema de proteção de dados expõe uma interface `IKeyManager` que pode ser usada para inspecionar e fazer alterações no anel de chave. O sistema de DI que forneceu a instância do `IDataProtectionProvider` também pode fornecer uma instância de `IKeyManager` para seu consumo. Como alternativa, você pode extrair o `IKeyManager` diretamente do `IServiceProvider` como no exemplo abaixo.

Qualquer operação que modifique o anel de chave (criando uma nova chave explicitamente ou executando uma revogação) invalidará o cache na memória. A próxima chamada para `Protect` ou `Unprotect` fará com que o sistema de proteção de dados leia novamente o anel de chave e recrie o cache.

O exemplo a seguir demonstra como usar a interface `IKeyManager` para inspecionar e manipular o anel de tecla, incluindo a revogação de chaves existentes e a geração manual de uma nova chave.

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>Armazenamento de chaves

O sistema de proteção de dados tem uma heurística em que tenta deduzir automaticamente um local apropriado de armazenamento de chaves e o mecanismo de criptografia em repouso. O mecanismo de persistência de chave também é configurável pelo desenvolvedor do aplicativo. Os documentos a seguir discutem as implementações internas desses mecanismos:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
