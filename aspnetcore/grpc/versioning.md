---
title: Controle de versão de serviços gRPC
author: jamesnk
description: Saiba como os serviços gRPCs de versão.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664111"
---
# <a name="versioning-grpc-services"></a>Controle de versão de serviços gRPC

Por [James Newton – King](https://twitter.com/jamesnk)

Os novos recursos adicionados a um aplicativo podem exigir que os serviços gRPCs fornecidos aos clientes mudem, às vezes de formas inesperadas e de interrupção. Quando os serviços gRPCs forem alterados:

* A consideração deve ser dada em como as alterações afetam os clientes.
* Uma estratégia de controle de versão para dar suporte a alterações deve ser implementada.

## <a name="backwards-compatibility"></a>Compatibilidade com versões anteriores

O protocolo gRPC foi projetado para dar suporte a serviços que mudam com o passar do tempo. Em geral, as adições aos serviços e métodos gRPC são não separáveis. Alterações sem interrupção permitem que os clientes existentes continuem a funcionar sem alterações. Alterar ou excluir os serviços gRPCs são alterações significativas. Quando os serviços gRPCs têm alterações significativas, os clientes que usam esse serviço precisam ser atualizados e reimplantados.

Fazer alterações não significativas em um serviço tem vários benefícios:

* Os clientes existentes continuam a ser executados.
* Evita o trabalho envolvido em notificar os clientes sobre alterações significativas e atualizá-los.
* Somente uma versão do serviço precisa ser documentada e mantida.

### <a name="non-breaking-changes"></a>Alterações não relacionadas à falha

Essas alterações não são significativas em um nível de protocolo gRPC e no nível binário do .NET.

* **Adicionando um novo serviço**
* **Adicionando um novo método a um serviço**
* A **adição de um campo a uma mensagem de solicitação** -campos adicionados a uma mensagem de solicitação são desserializados com o [valor padrão](https://developers.google.com/protocol-buffers/docs/proto3#default) no servidor quando não definido. Para ser uma alteração não significativa, o serviço deve ter sucesso quando o novo campo não é definido por clientes mais antigos.
* **Adicionar um campo a uma mensagem de resposta** -os campos adicionados a uma mensagem de resposta são desserializados na coleção de [campos desconhecidos](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) da mensagem no cliente.
* **Adicionar um valor a enum** -enums é serializado como um valor numérico. Novos valores de enumeração são desserializados no cliente para o valor de enumeração sem um nome de enumeração. Para ser uma alteração não significativa, os clientes mais antigos devem ser executados corretamente ao receber o novo valor de enumeração.

### <a name="binary-breaking-changes"></a>Alterações de quebra binária

As alterações a seguir são não separáveis em um nível de protocolo gRPC, mas o cliente precisa ser atualizado se ele atualizar para o conjunto de módulos *. proto* ou o assembly de cliente .net mais recente. A compatibilidade binária é importante se você planeja publicar uma biblioteca gRPC no NuGet.

* A **remoção de um campo** -valores de um campo removido são desserializados para os [campos desconhecidos](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)de uma mensagem. Isso não é uma alteração significativa de protocolo gRPC, mas o cliente precisa ser atualizado se ele atualizar para o contrato mais recente. É importante que um número de campo removido não seja acidentalmente reutilizado no futuro. Para garantir que isso não aconteça, especifique os números e nomes dos campos excluídos na mensagem usando a palavra-chave [reservada](https://developers.google.com/protocol-buffers/docs/proto3#reserved) do Protobuf.
* **Renomear uma mensagem** -nomes de mensagens normalmente não são enviados na rede, portanto, isso não é uma alteração de interrupção de protocolo gRPC. O cliente precisará ser atualizado se atualizar para o contrato mais recente. Uma situação em que os nomes de mensagem **são** enviados na rede é com [qualquer](https://developers.google.com/protocol-buffers/docs/proto3#any) campo, quando o nome da mensagem é usado para identificar o tipo de mensagem.
* Alterar a `csharp_namespace` de alteração de **csharp_namespace** alterará o namespace dos tipos .net gerados. Isso não é uma alteração significativa de protocolo gRPC, mas o cliente precisa ser atualizado se ele atualizar para o contrato mais recente.

### <a name="protocol-breaking-changes"></a>Alterações significativas de protocolo

Os itens a seguir são alterações de protocolo e de quebra binária:

* **Renomeando um campo** -com conteúdo Protobuf, os nomes de campo são usados somente no código gerado. O número do campo é usado para identificar campos na rede. Renomear um campo não é uma alteração significativa de protocolo para Protobuf. No entanto, se um servidor estiver usando conteúdo JSON, renomear um campo será uma alteração significativa.
* **Alterar um tipo de dados de campo** – alterar o tipo de dados de um campo para um [tipo incompatível](https://developers.google.com/protocol-buffers/docs/proto3#updating) causará erros ao desserializar a mensagem. Mesmo que o novo tipo de dados seja compatível, é provável que o cliente precise ser atualizado para dar suporte ao novo tipo se ele atualizar para o contrato mais recente.
* **Alterando um número de campo** -com cargas de Protobuf, o número do campo é usado para identificar campos na rede.
* **Renomear um pacote, serviço ou método** -gRPC usa o nome do pacote, o nome do serviço e o nome do método para criar a URL. O cliente recebe um status não *implementado* do servidor.
* **Removendo um serviço ou método** -o cliente obtém um status não *implementado* do servidor ao chamar o método removido.

### <a name="behavior-breaking-changes"></a>Alterações significativas de comportamento

Ao fazer alterações não significativas, você também deve considerar se os clientes mais antigos podem continuar trabalhando com o novo comportamento do serviço. Por exemplo, adicionar um novo campo a uma mensagem de solicitação:

* Não é uma alteração de interrupção de protocolo.
* Retornando um status de erro no servidor se o novo campo não estiver definido, ele fará uma alteração significativa para clientes antigos.

A compatibilidade de comportamento é determinada pelo seu código específico do aplicativo.

## <a name="version-number-services"></a>Serviços de número de versão

Os serviços devem se esforçar para manter a compatibilidade com os clientes antigos. Eventualmente, alterações em seu aplicativo podem exigir alterações significativas. Dividir clientes antigos e forçá-los a ser atualizados junto com seu serviço não é uma boa experiência do usuário. Uma maneira de manter a compatibilidade com versões anteriores ao fazer alterações significativas é publicar várias versões de um serviço.

o gRPC dá suporte a um especificador de [pacote](https://developers.google.com/protocol-buffers/docs/proto3#packages) opcional, que funciona de forma muito semelhante a um namespace .net. Na verdade, o `package` será usado como o namespace .NET para tipos .NET gerados se `option csharp_namespace` não estiver definido no arquivo *. proto* . O pacote pode ser usado para especificar um número de versão para seu serviço e suas mensagens:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

O nome do pacote é combinado com o nome do serviço para identificar um endereço de serviço. Um endereço de serviço permite que várias versões de um serviço sejam hospedadas lado a lado:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

As implementações do serviço com versão são registradas em *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

A inclusão de um número de versão no nome do pacote oferece a oportunidade de publicar uma versão *v2* do serviço com alterações significativas, enquanto continua a oferecer suporte a clientes mais antigos que chamam a versão *v1* . Depois que os clientes tiverem atualizado para usar o serviço *v2* , você pode optar por remover a versão antiga. Ao planejar a publicação de várias versões de um serviço:

* Evite alterações significativas, se for razoável.
* Não atualize o número de versão, a menos que faça alterações significativas.
* Atualize o número de versão ao fazer alterações significativas.

A publicação de várias versões de um serviço a duplica. Para reduzir a duplicação, considere a possibilidade de mover a lógica de negócios das implementações de serviço para um local centralizado que possa ser reutilizado pelas implementações novas e antigas:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Os serviços e as mensagens geradas com nomes de pacote diferentes são **tipos .net diferentes**. Mover a lógica de negócios para um local centralizado requer o mapeamento de mensagens para tipos comuns.
