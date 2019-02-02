---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
title: Criar um ponto de extremidade OData v4 usando a API Web ASP.NET 2.2 | Microsoft Docs
author: MikeWasson
description: O Open Data Protocol (OData) é um protocolo de acesso de dados para a web. O OData fornece uma maneira uniforme para consultar e manipular os conjuntos de dados por meio de operações de CRUD...
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 1e1927c0-ded1-4752-80fd-a146628d2f09
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
msc.type: authoredcontent
ms.openlocfilehash: c6a4aa4eb563fd77d5afd9248175d5f5b7984d19
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667564"
---
# <a name="create-an-odata-v4-endpoint-using-aspnet-web-api"></a>Criar um ponto de extremidade OData v4 usando a API Web ASP.NET 

> O Open Data Protocol (OData) é um protocolo de acesso de dados para a web. O OData fornece uma maneira uniforme para consultar e manipular os conjuntos de dados por meio de operações de CRUD (criar, ler, atualizar e excluir).
>
> API Web ASP.NET oferece suporte a v3 e v4 do protocolo. Você pode até ter um ponto de extremidade de v4 que é executado lado a lado com um ponto de extremidade v3.
>
> Este tutorial mostra como criar um ponto de extremidade de v4 do OData que suporta operações CRUD.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Versões de software usadas no tutorial
>
> - 5.2 da API da Web
> - OData v4
> - Visual Studio 2017 (Baixe o Visual Studio 2017 [aqui](https://visualstudio.microsoft.com/downloads/))
> - Entity Framework 6
> - .NET 4.7.2
>
> ## <a name="tutorial-versions"></a>Versões de tutoriais
>
> Para o OData versão 3, consulte [criando um ponto de extremidade OData v3](../odata-v3/creating-an-odata-endpoint.md).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

No Visual Studio, do **arquivo** menu, selecione **New** &gt; **projeto**.

Expandir **Installed** &gt; **Visual C#**  &gt; **Web**e selecione o **aplicativo Web ASP.NET (.NET Framework)**  modelo. Nomeie o projeto &quot;ProductService&quot;.

[![](create-an-odata-v4-endpoint/_static/image7.png)](create-an-odata-v4-endpoint/_static/image7.png)

Selecione **OK**.



[![](create-an-odata-v4-endpoint/_static/image8.png)](create-an-odata-v4-endpoint/_static/image8.png)

Selecione o **vazio** modelo. Sob **adicionar pastas e os principais referências para:**, selecione **API da Web**. Selecione **OK**.

## <a name="install-the-odata-packages"></a>Instalar os pacotes de OData

No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** &gt; **Console do Gerenciador de Pacotes**. Na janela do Console do Gerenciador de pacotes, digite:

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample1.cmd)]

Este comando instala os últimos pacotes NuGet do OData.

## <a name="add-a-model-class"></a>Adicionar uma classe de modelo

Um *modelo* é um objeto que representa uma entidade de dados em seu aplicativo.

No Gerenciador de Soluções, clique com o botão direito na pasta de modelos (Models). No menu de contexto, selecione **Add** &gt; **classe**.

[![](create-an-odata-v4-endpoint/_static/image6.png)](create-an-odata-v4-endpoint/_static/image5.png)

> [!NOTE]
> Por convenção, as classes de modelo são colocadas na pasta modelos, mas você não precisa seguir essa convenção em seus próprios projetos.


Nomeie a classe `Product`. No arquivo Product.cs, substitua o código clichê com o seguinte:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample2.cs)]

O `Id` propriedade é a chave de entidade. Os clientes podem consultar entidades por chave. Por exemplo, para obter o produto com ID 5, o URI é `/Products(5)`. O `Id` propriedade também será a chave primária no banco de dados back-end.

## <a name="enable-entity-framework"></a>Habilitar o Entity Framework

Para este tutorial, vamos usar Code First do Entity Framework (EF) para criar o banco de dados de back-end.

> [!NOTE]
> Web API OData não exige que o EF. Use qualquer camada de acesso a dados que pode ser traduzidos a entidades de banco de dados em modelos.


Primeiro, instale o pacote do NuGet para o EF. No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** &gt; **Console do Gerenciador de Pacotes**. Na janela do Console do Gerenciador de pacotes, digite:

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample3.cmd)]

Abra o arquivo Web. config e adicione a seguinte seção dentro de **configuração** elemento após o **configSections** elemento.

[!code-xml[Main](create-an-odata-v4-endpoint/samples/sample4.xml?highlight=6)]

Essa configuração adiciona uma cadeia de caracteres de conexão para um banco de dados LocalDB. Este banco de dados será usado quando você executa o aplicativo localmente.

Em seguida, adicione uma classe chamada `ProductsContext` na pasta modelos:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample5.cs)]

No construtor, `"name=ProductsContext"` fornece o nome da cadeia de caracteres de conexão.

## <a name="configure-the-odata-endpoint"></a>Configurar o ponto de extremidade OData

Abra o arquivo de aplicativo\_Start/WebApiConfig.cs. Adicione o seguinte **usando** instruções:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample6.cs)]

Em seguida, adicione o seguinte código para o **registrar** método:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample7.cs)]

Esse código faz duas coisas:

- Cria um modelo de dados de entidade (EDM).
- Adiciona uma rota.

Um EDM é um modelo abstrato dos dados. O EDM é usado para criar o documento de metadados de serviço. O **ODataConventionModelBuilder** classe cria um EDM usando as convenções de nomenclatura padrão. Essa abordagem requer o mínimo de código. Se você quiser mais controle sobre o EDM, você pode usar o **ODataModelBuilder** classe para criar o EDM com a adição de propriedades, chaves e propriedades de navegação explicitamente.

Um *rota* informa à API Web como rotear solicitações HTTP para o ponto de extremidade. Para criar uma rota do OData v4, chame o **MapODataServiceRoute** método de extensão.

Se seu aplicativo tiver vários pontos de extremidade OData, crie uma rota separada para cada. Dê a cada rota de um nome de rota exclusivo e um prefixo.

## <a name="add-the-odata-controller"></a>Adicionar o controlador de OData

Um *controlador* é uma classe que manipula as solicitações HTTP. Você criar um controlador separado para cada conjunto de entidades em seu serviço OData. Neste tutorial, você criará um controlador, para o `Product` entidade.

No Gerenciador de soluções, clique com botão direito na pasta controladores e selecione **Add** &gt; **classe**. Nomeie a classe `ProductsController`.

> [!NOTE]
> A versão deste tutorial para OData v3 usa o **Adicionar controlador** scaffolding. Atualmente, não há nenhum scaffolding para OData v4.


Substitua o código clichê em ProductsController.cs pelo seguinte.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample8.cs)]

O controlador usa o `ProductsContext` classe para acessar o banco de dados usando o EF. Observe que o controlador substitui o **Dispose** método descartar o **ProductsContext**.

Isso é o ponto de partida para o controlador. Em seguida, adicionaremos métodos para todas as operações CRUD.

## <a name="query-the-entity-set"></a>O conjunto de entidades de consulta

Adicione os seguintes métodos para `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample9.cs)]

A versão sem parâmetros do `Get` método retorna toda a coleção de produtos. O `Get` método com um *chave* parâmetro procura um produto por sua chave (nesse caso, o `Id` propriedade).

O **[EnableQuery]** atributo permite que os clientes modificar a consulta, usando as opções de consulta como $filter, $sort e $page. Para obter mais informações, consulte [que dão suporte a opções de consulta OData](../supporting-odata-query-options.md).

## <a name="add-an-entity-to-the-entity-set"></a>Adicionar uma entidade para o conjunto de entidades

Para habilitar clientes para adicionar um novo produto no banco de dados, adicione o seguinte método à `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample10.cs)]

## <a name="update-an-entity"></a>Atualizar uma entidade

OData dá suporte a dois semânticas diferentes para atualizar uma entidade, PATCH e PUT.

- PATCH executa uma atualização parcial. O cliente especifica apenas as propriedades a serem atualizadas.
- PUT substitui a entidade inteira.

A desvantagem de PUT é que o cliente deve enviar valores para todas as propriedades na entidade, incluindo os valores que não estão mudando. O [especificação OData](http://docs.oasis-open.org/odata/odata/v4.0/os/part1-protocol/odata-v4.0-os-part1-protocol.html#_Toc372793719) afirma que o PATCH é preferencial.

Em qualquer caso, aqui está o código para métodos PUT e PATCH:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample11.cs)]

No caso de PATCH, o controlador usa o **Delta&lt;T&gt;**  tipo para controlar as alterações.

## <a name="delete-an-entity"></a>Excluir uma entidade

Para habilitar clientes excluir um produto do banco de dados, adicione o seguinte método à `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample12.cs)]
