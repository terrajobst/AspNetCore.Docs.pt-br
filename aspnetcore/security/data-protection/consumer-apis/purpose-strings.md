---
title: Cadeias de caracteres de finalidade no ASP.NET Core
author: rick-anderson
description: Saiba como cadeias de caracteres de finalidade são usadas em APIs de proteção de dados de ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 4c85423f8de7e4b784ae1bb304a884541df251b6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087537"
---
# <a name="purpose-strings-in-aspnet-core"></a>Cadeias de caracteres de finalidade no ASP.NET Core

<a name="data-protection-consumer-apis-purposes"></a>

Componentes que consomem `IDataProtectionProvider` deve passar por uma única *fins* parâmetro para o `CreateProtector` método. As finalidades *parâmetro* é inerente à segurança do sistema de proteção de dados, pois ele fornece isolamento entre consumidores criptográficos, mesmo se as chaves de criptografia de raiz são os mesmos.

Quando um consumidor Especifica uma finalidade, a cadeia de caracteres de finalidade é usada junto com as chaves de criptografia de raiz para derivar subchaves criptografia exclusivos para que o consumidor. Isso isola o consumidor de todos os outros consumidores criptográficos no aplicativo: nenhum outro componente pode ler seus cargas e ele não é possível ler as cargas do qualquer outro componente. Esse isolamento também processa inviável categorias inteiras de ataque contra o componente.

![Exemplo de diagrama de finalidade](purpose-strings/_static/purposes.png)

No diagrama acima, `IDataProtector` instâncias A e B **não é possível** ler uns dos outros conteúdos, apenas seus próprios.

A cadeia de caracteres de finalidade não precisa ser secreta. Ele simplesmente deve ser exclusivo no sentido de que nenhum outro componente bem comportado nunca fornecerá a mesma cadeia de caracteres de finalidade.

>[!TIP]
> Usando o nome de namespace e o tipo do componente de consumir as APIs de proteção de dados é uma boa regra prática, prática que essas informações nunca entrará em conflito.
>
>Um componente de autoria do Contoso que é responsável por minting tokens de portador use Contoso.Security.BearerToken como sua cadeia de caracteres de finalidade. Ou - ainda melhor - ele pode usar Contoso.Security.BearerToken.v1 como sua cadeia de caracteres de finalidade. Acrescentar o número de versão permite que uma versão futura usar Contoso.Security.BearerToken.v2 como sua finalidade, e as diferentes versões seria completamente isoladas umas das outras como cargas de ir.

Desde o parâmetro fins `CreateProtector` é uma matriz de cadeia de caracteres acima poderia ter sido em vez disso, especificado como `[ "Contoso.Security.BearerToken", "v1" ]`. Isso permite o estabelecimento de uma hierarquia de finalidades e abre a possibilidade de cenários de multilocação com o sistema de proteção de dados.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Componentes não devem permitir a entrada do usuário não confiável para ser a única origem de entrada para a cadeia de fins.
>
>Por exemplo, considere um componente Contoso.Messaging.SecureMessage que é responsável por armazenar mensagens seguras. Se o componente de mensagens seguro fosse chamar `CreateProtector([ username ])`, em seguida, um usuário mal-intencionado pode criar uma conta com o nome de usuário "Contoso.Security.BearerToken" em uma tentativa de obter o componente para chamar `CreateProtector([ "Contoso.Security.BearerToken" ])`, inadvertidamente fazendo com que o serviço de mensagens seguro sistema mint cargas que poderia ser percebido como tokens de autenticação.
>
>Uma cadeia de fins melhor para o componente de mensagens seria `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, que fornece isolamento apropriado.

O isolamento fornecido pelos e comportamentos de `IDataProtectionProvider`, `IDataProtector`, e os objetivos são da seguinte maneira:

* Para um determinado `IDataProtectionProvider` objeto, o `CreateProtector` método criará um `IDataProtector` objeto vinculado exclusivamente para ambos o `IDataProtectionProvider` objeto que criou e o parâmetro de finalidades que foi passado para o método.

* O parâmetro de finalidade não deve ser nulo. (Se a fins é especificado como uma matriz, isso significa que a matriz não deve ser de comprimento zero e todos os elementos da matriz devem ser não nulo.) Finalidade de uma cadeia de caracteres vazia é tecnicamente permitida, mas não é recomendada.

* Argumentos de duas finalidades são equivalentes se e somente se eles contêm as mesmas cadeias de caracteres (usando uma comparação ordinal) na mesma ordem. Um argumento de finalidade única é equivalente à matriz de elemento único fins correspondente.

* Duas `IDataProtector` objetos forem equivalentes, se e somente se elas forem criadas de equivalente `IDataProtectionProvider` objetos com os parâmetros de fins equivalente.

* Para um determinado `IDataProtector` objeto, uma chamada a `Unprotect(protectedData)` retornará original `unprotectedData` somente se `protectedData := Protect(unprotectedData)` para um equivalente `IDataProtector` objeto.

> [!NOTE]
> Não estamos considerando o caso em que algum componente intencionalmente escolhe uma cadeia de caracteres de finalidade que é conhecida em conflito com outro componente. Esse componente essencialmente seria considerado mal-intencionado, e este sistema não é destinado a fornecer garantias de segurança que código mal-intencionado já está em execução dentro do processo de trabalho.
