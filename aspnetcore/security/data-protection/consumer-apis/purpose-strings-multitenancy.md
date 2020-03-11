---
title: Hierarquia de finalidade e multilocação no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a hierarquia de cadeia de caracteres de finalidade e multilocação, pois ela se relaciona com as APIs de proteção de dados ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 1133d40e7b325d58b3f70e7387494dae36ff8ac9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664748"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Hierarquia de finalidade e multilocação no ASP.NET Core

Como um `IDataProtector` também é implicitamente um `IDataProtectionProvider`, os fins podem ser encadeados juntos. Nesse sentido, `provider.CreateProtector([ "purpose1", "purpose2" ])` é equivalente a `provider.CreateProtector("purpose1").CreateProtector("purpose2")`.

Isso permite algumas relações hierárquicas interessantes por meio do sistema de proteção de dados. No exemplo anterior de [contoso. Messaging. SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose), o componente SecureMessage pode chamar `provider.CreateProtector("Contoso.Messaging.SecureMessage")` uma vez na frente e armazenar em cache o resultado em um campo de `_myProvider` particular. Os protetores futuros podem ser criados por meio de chamadas para `_myProvider.CreateProtector("User: username")`, e esses protetores seriam usados para proteger as mensagens individuais.

Isso também pode ser invertido. Considere um único aplicativo lógico que hospeda vários locatários (um CMS parece razoável) e cada locatário pode ser configurado com seu próprio sistema de gerenciamento de autenticação e estado. O aplicativo de abrangência tem um único provedor mestre e chama `provider.CreateProtector("Tenant 1")` e `provider.CreateProtector("Tenant 2")` fornecer a cada locatário sua própria fatia isolada do sistema de proteção de dados. Os locatários podem então derivar seus próprios protetores individuais com base em suas próprias necessidades, mas não importa o quão difícil eles tentam não poderem criar protetores que colidem com qualquer outro locatário no sistema. Graficamente, isso é representado como abaixo.

![Propósitos de várias locação](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Isso pressupõe que o aplicativo de proteção contra as APIs estão disponíveis para locatários individuais e que os locatários não podem executar código arbitrário no servidor. Se um locatário puder executar código arbitrário, ele poderá executar uma reflexão privada para interromper as garantias de isolamento ou simplesmente ler o material de chaveamento mestre diretamente e obter as subchaves desejadas.

O sistema de proteção de dados usa, na verdade, um tipo de multilocação em sua configuração padrão. Por padrão, o material de chave mestra é armazenado na pasta de perfil de usuário da conta de processo de trabalho (ou no registro, para identidades do pool de aplicativos do IIS). Mas, na verdade, é bastante comum usar uma única conta para executar vários aplicativos e, portanto, todos esses aplicativos acabarão compartilhando o material de chaveamento mestre. Para resolver isso, o sistema de proteção de dados insere automaticamente um identificador exclusivo por aplicativo como o primeiro elemento na cadeia de finalidade geral. Essa finalidade implícita serve para [isolar aplicativos individuais](xref:security/data-protection/configuration/overview#per-application-isolation) uns dos outros, tratando efetivamente de cada aplicativo como um locatário exclusivo no sistema, e o processo de criação de protetor é idêntico à imagem acima.
