---
uid: signalr/overview/guide-to-the-api/working-with-groups
title: Trabalhando com grupos no SignalR | Microsoft Docs
author: bradygaster
description: Este tópico descreve como manter as informações de associação de grupo com a API do Hub.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: cd378ecd-3e9e-4236-b902-65916d85a048
msc.legacyurl: /signalr/overview/guide-to-the-api/working-with-groups
msc.type: authoredcontent
ms.openlocfilehash: 384b7e5f07fa46ea3cc32e5c18c3c2327b7aedd3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836799"
---
<a name="working-with-groups-in-signalr"></a>Trabalhando com grupos no SignalR
====================
by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tópico descreve como adicionar usuários a grupos e manter informações de associação de grupo.
>
> ## <a name="software-versions-used-in-this-topic"></a>Versões de software usadas neste tópico
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Versão 2 do SignalR
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Versões anteriores deste tópico
>
> Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Perguntas e comentários
>
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).

## <a name="overview"></a>Visão geral

Grupos no SignalR fornecem um método para mensagens de difusão para subconjuntos especificados de clientes conectados. Um grupo pode ter qualquer número de clientes e um cliente pode ser um membro de qualquer número de grupos. Você não precisa criar explicitamente grupos. Na verdade, um grupo é criado automaticamente na primeira vez em que você especifica seu nome em uma chamada para Groups.Add e é excluído quando você remove a última conexão da associação nele. Para obter uma introdução ao uso de grupos, consulte [como gerenciar a associação de grupo da classe Hub](hubs-api-guide-server.md#groupsfromhub) na API de Hubs - guia servidor.

Há uma API para obter uma lista de membros do grupo ou uma lista de grupos. O SignalR envia mensagens para os clientes e grupos com base em um modelo de pub/sub e o servidor não mantém listas de grupos ou associações de grupo. Isso ajuda a maximizar a escalabilidade, porque sempre que você adicionar um nó a uma web farm, qualquer estado que mantém o SignalR tem que ser propagadas para o novo nó.

Quando você adiciona um usuário a um grupo usando o `Groups.Add` método, o usuário recebe mensagens direcionadas a esse grupo durante a conexão atual, mas a associação do usuário ao grupo não persiste além a conexão atual. Se você quiser reter permanentemente informações sobre grupos e associação de grupo, você deve armazenar esses dados em um repositório, como um banco de dados ou armazenamento de tabelas do Azure. Em seguida, sempre que um usuário se conecta ao seu aplicativo, você recuperar do repositório de quais grupos o usuário pertence e manualmente adicione esse usuário aos grupos.

Quando voltar a ligar após uma interrupção temporária, o usuário novamente associa automaticamente os grupos atribuídos previamente. Associando automaticamente um grupo se aplica somente ao reconectar, não ao estabelecer uma nova conexão. Um token assinado digitalmente é passado do cliente que contém a lista de grupos atribuídos anteriormente. Se você quiser verificar se o usuário pertence aos grupos solicitados, você pode substituir o comportamento padrão.

Este tópico inclui as seções a seguir:

- [Adicionando e removendo usuários](#add)
- [Chamando membros de um grupo](#call)
- [Armazenamento de associação de grupo em um banco de dados](#storedatabase)
- [Associação de grupo de armazenamento no armazenamento de tabelas do Azure](#storeazuretable)
- [Verificar a associação de grupo ao reconectar](#verify)

<a id="add"></a>

## <a name="adding-and-removing-users"></a>Adicionando e removendo usuários

Para adicionar ou remover usuários de um grupo, você chama o [Add](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) ou [remover](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) métodos e passe o nome do grupo como parâmetros e o id de conexão do usuário. Não é necessário remover manualmente um usuário de um grupo quando termina a conexão.

A exemplo a seguir mostra a `Groups.Add` e `Groups.Remove` métodos usados em métodos de Hub.

[!code-csharp[Main](working-with-groups/samples/sample1.cs?highlight=5,10)]

O `Groups.Add` e `Groups.Remove` métodos são executados de forma assíncrona.

Se você quiser adicionar um cliente a um grupo e imediatamente a enviar uma mensagem para o cliente usando o grupo, você precisa certificar-se de que o método Groups.Add termina primeiro. Os exemplos de código a seguir mostram como fazer isso.

[!code-csharp[Main](working-with-groups/samples/sample2.cs?highlight=1,3)]

Em geral, você não deve incluir `await` ao chamar o `Groups.Remove` método como a id de conexão que você está tentando remover pode não estar disponível. Nesse caso, `TaskCanceledException` for gerada depois que a solicitação expira. Se seu aplicativo deve garantir que o usuário foi removido do grupo antes de enviar uma mensagem para o grupo, você pode adicionar `await` antes de `Groups.Remove`e, em seguida, capture a `TaskCanceledException` exceção que pode ser gerada.

<a id="call"></a>

## <a name="calling-members-of-a-group"></a>Chamando membros de um grupo

Você pode enviar mensagens para todos os membros de um grupo ou somente os membros especificados do grupo, conforme mostrado nos exemplos a seguir.

- **Todos os** conectados a clientes em um grupo especificado.

    [!code-css[Main](working-with-groups/samples/sample3.css)]
- Todos os clientes em um grupo especificado conectados **clientes especificados, exceto**, identificado pela ID de conexão.

    [!code-csharp[Main](working-with-groups/samples/sample4.cs)]
- Todos os clientes em um grupo especificado conectados **exceto o cliente da chamada**.

    [!code-css[Main](working-with-groups/samples/sample5.css)]

<a id="storedatabase"></a>

## <a name="storing-group-membership-in-a-database"></a>Armazenamento de associação de grupo em um banco de dados

Os exemplos a seguir mostram como reter informações de usuário e grupo em um banco de dados. Você pode usar qualquer tecnologia de acesso a dados; No entanto, o exemplo a seguir mostra como definir modelos usando o Entity Framework. Esses modelos de entidade correspondem aos campos e tabelas de banco de dados. A estrutura de dados pode variar consideravelmente dependendo dos requisitos do seu aplicativo. Este exemplo inclui uma classe chamada `ConversationRoom` que seria exclusiva de um aplicativo que permite que os usuários ingressem conversas sobre assuntos diferentes, como o ambiente ou de esportes. Este exemplo também inclui uma classe para as conexões. A classe de conexão não é absolutamente necessária para rastrear a associação de grupo, mas com frequência faz parte de uma solução robusta para acompanhamento de usuários.

[!code-csharp[Main](working-with-groups/samples/sample6.cs)]

Em seguida, no hub, você pode recuperar as informações de grupo e usuário do banco de dados e adicionar manualmente o usuário aos grupos apropriados. O exemplo inclui código para acompanhar as conexões de usuário. Neste exemplo, o `await` palavra-chave não é aplicada antes `Groups.Add` porque uma mensagem não será enviada imediatamente para membros do grupo. Se você quiser enviar uma mensagem a todos os membros do grupo logo depois de adicionar o novo membro, convém aplicar o `await` palavra-chave para garantir que a operação assíncrona foi concluída.

[!code-csharp[Main](working-with-groups/samples/sample7.cs)]

<a id="storeazuretable"></a>

## <a name="storing-group-membership-in-azure-table-storage"></a>Associação de grupo de armazenamento no armazenamento de tabelas do Azure

Usando o armazenamento de tabela do Azure para armazenar informações de usuário e grupo é semelhante ao uso de um banco de dados. O exemplo a seguir mostra uma entidade de tabela que armazena o nome de usuário e o nome do grupo.

[!code-csharp[Main](working-with-groups/samples/sample8.cs)]

No hub, você recuperar os grupos atribuídos quando o usuário se conecta.

[!code-csharp[Main](working-with-groups/samples/sample9.cs)]

<a id="verify"></a>

## <a name="verifying-group-membership-when-reconnecting"></a>Verificar a associação de grupo ao reconectar

Por padrão, o SignalR novamente atribui automaticamente um usuário aos grupos apropriados ao reconectar-se de uma interrupção temporária, como quando uma conexão será descartada e restabelecida antes que a conexão expire. Informações de grupo do usuário são passadas em um token ao reconectar e esse token é verificado no servidor. Para obter informações sobre o processo de verificação para incluir novamente grupos de usuários, consulte [incluir novamente grupos ao reconectar](../security/introduction-to-security.md#rejoingroup).

Em geral, você deve usar o comportamento padrão do automaticamente incluir novamente grupos na reconexão. Grupos de SignalR não foram projetados como um mecanismo de segurança para restringir o acesso a dados confidenciais. No entanto, se seu aplicativo precisa verificar a associação de grupo do usuário ao reconectar, você pode substituir o comportamento padrão. Alterando o comportamento padrão pode adicionar uma carga para seu banco de dados porque a associação de grupo do usuário deve ser recuperada para cada reconexão em vez de apenas quando o usuário se conecta.

Se você precisar verificar associação de grupo em reconectar, crie um novo módulo de pipeline de hub que retorna uma lista de grupos atribuídos, conforme mostrado abaixo.

[!code-csharp[Main](working-with-groups/samples/sample10.cs)]

Em seguida, adicione o módulo para o pipeline de hub, como destacado abaixo.

[!code-csharp[Main](working-with-groups/samples/sample11.cs?highlight=4)]
