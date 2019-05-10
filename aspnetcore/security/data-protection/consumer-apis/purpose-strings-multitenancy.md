---
title: Hierarquia de finalidade e a multilocação no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre multilocação e hierarquia de cadeia de caracteres de finalidade, como ele se relaciona com as APIs de proteção de dados do ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 1133d40e7b325d58b3f70e7387494dae36ff8ac9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896793"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Hierarquia de finalidade e a multilocação no ASP.NET Core

Uma vez que um `IDataProtector` também é implicitamente um `IDataProtectionProvider`, fins podem ser encadeada juntos. Nesse sentido `provider.CreateProtector([ "purpose1", "purpose2" ])` é equivalente a `provider.CreateProtector("purpose1").CreateProtector("purpose2")`.

Isso permite que algumas relações hierárquicas interessantes através do sistema de proteção de dados. No exemplo anterior de [Contoso.Messaging.SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose), o componente SecureMessage pode chamar `provider.CreateProtector("Contoso.Messaging.SecureMessage")` iniciais de uma vez e armazenar em cache o resultado em uma particular `_myProvider` campo. Protetores de futuros, em seguida, podem ser criadas por meio de chamadas para `_myProvider.CreateProtector("User: username")`, e os protetores seriam usados para proteger as mensagens individuais.

Isso também pode ser invertido. Considere um único aplicativo lógico que hospeda vários locatários (um CMS parece razoável) e cada locatário podem ser configurado com seu próprio sistema de gerenciamento de autenticação e o estado. O aplicativo guarda-chuva tem um único provedor mestre e, em seguida, chama `provider.CreateProtector("Tenant 1")` e `provider.CreateProtector("Tenant 2")` para fornecer seu próprio fatia isolada do sistema de proteção de dados de cada locatário. Os locatários, em seguida, pode derivar seus próprios protetores individuais com base em suas necessidades, mas, independentemente de como eles tentam eles não é possível criar protetores que entrem em conflito com qualquer outro locatário no sistema. Graficamente, isso é representado como abaixo.

![Com várias finalidades de aluguel](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Isso pressupõe que a cobertura do controles de aplicativo, quais APIs estão disponíveis para locatários individuais e que os locatários não podem executar código arbitrário no servidor. Se um locatário pode executar código arbitrário, eles poderiam executar a reflexão privada para quebrar as garantias de isolamento, ou pode apenas ler o material de chave mestra diretamente e derivar qualquer subchaves que desejarem.

Na verdade, o sistema de proteção de dados usa uma espécie de multilocação em sua configuração de out-of-the-box do padrão. Por padrão, o material de chave mestra é armazenado na pasta de perfil do usuário da conta de processo do operador (ou o registro, para identidades do pool de aplicativos IIS). Mas é, na verdade, bastante comum usar uma única conta para executar vários aplicativos e, portanto, todos esses aplicativos seriam acabam compartilhando a material da chave mestra. Para resolver isso, o sistema de proteção de dados insere automaticamente um identificador exclusivo por aplicativo como o primeiro elemento na cadeia de finalidade geral. Que serve para essa finalidade implícita [isolar aplicativos individuais](xref:security/data-protection/configuration/overview#per-application-isolation) uns dos outros, tratando efetivamente cada aplicativo como um locatário exclusivo no sistema e o processo de criação de protetor parece idêntico na imagem acima.
