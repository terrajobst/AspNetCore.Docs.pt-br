---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: Itens de dados de exibição e fornece detalhes sobre | Microsoft Docs
author: Erikre
description: Esta série de tutoriais mostrará as Noções básicas de criação de um aplicativo Web Forms do ASP.NET com o ASP.NET 4.7 e o Microsoft Visual Studio 2017
ms.author: riande
ms.date: 1/04/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: acc2f8e78375ef0455d467e2af750ecbee623224
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396214"
---
<a name="display-data-items-and-details"></a>Itens de dados de exibição e detalhes
====================
by [Erik Reitan](https://github.com/Erikre)

> Esta série de tutoriais ensina os conceitos básicos da criação de um aplicativo Web Forms do ASP.NET com o ASP.NET 4.7 e o Microsoft Visual Studio 2017.

Neste tutorial, você aprenderá como exibir itens de dados e os detalhes do item de dados com o Web Forms do ASP.NET e o Entity Framework Code First. Este tutorial se baseia no tutorial anterior de "Interface do usuário e navegação" como parte da série de tutoriais de Wingtip Toys Store. Depois de concluir este tutorial, você verá os produtos na *ProductsList.aspx* página e detalhes do produto sobre o *ProductDetails.aspx* página.

## <a name="youll-learn-how-to"></a>Você aprenderá como:

- Adicionar um controle de dados para exibir produtos do banco de dados
- Conectar um controle de dados para os dados selecionados
- Adicionar um controle de dados para exibir detalhes do produto do banco de dados
- Recupere um valor de cadeia de caracteres de consulta e use esse valor para limitar os dados recuperados do banco de dados

### <a name="features-introduced-in-this-tutorial"></a>Recursos apresentados neste tutorial:

- Model binding
- Provedores de valor

## <a name="add-a-data-control"></a>Adicionar um controle de dados

Você pode usar algumas opções diferentes para associar dados a um controle de servidor. As mais comuns incluem:

 * Adicionando um controle de fonte de dados
 * Adicionando o código manualmente
 * Usando a associação de modelos

### <a name="use-a-data-source-control-to-bind-data"></a>Usar um controle de fonte de dados para associar dados

Adicionar um controle de fonte de dados permite que você vincule o controle de fonte de dados para o controle que exibe os dados. Com essa abordagem, você pode declarativamente, em vez de programaticamente, conectar controles de servidor para fontes de dados.

### <a name="code-by-hand-to-bind-data"></a>Código manualmente para associar dados

Codificação manual envolve:

1. Ler um valor
2. Verificar se ele é nulo
3. Convertendo-o em um tipo apropriado
4. Verificando o sucesso de conversão
5. Usando o valor na consulta 

Essa abordagem permite que você tem controle total sobre a lógica de acesso a dados.

### <a name="use-model-binding-to-bind-data"></a>Usar associação de modelo para associar dados

Associação de modelos permite que você associe os resultados com muito menos código e lhe dá a capacidade de reutilizar a funcionalidade em todo o aplicativo. Ele simplifica o trabalho com a lógica de acesso a dados e focada no código enquanto ainda fornecem uma estrutura de vinculação de dados avançada.

## <a name="display-products"></a>Exibir produtos

Neste tutorial, você usará a associação de modelo para associar dados. Para configurar um controle de dados para usar a associação de modelo para selecionar dados, defina o controle `SelectMethod` propriedade para um nome de método no código da página. O controle de dados chama o método no momento apropriado no ciclo de vida da página e associa automaticamente os dados retornados. Não é necessário chamar explicitamente o `DataBind` método.

1. Na **Gerenciador de soluções**, abra *ProductList. aspx*.
2. Substitua a marcação existente com essa marcação:   

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

Esse código usa um **ListView** controle chamado `productList` para exibir produtos.

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

Com modelos e estilos, você define como o **ListView** controle exibe dados. Ele é útil para dados em qualquer estrutura de repetição. Embora isso **ListView** exemplo simplesmente exibe dados de banco de dados, você pode também, sem código, permitir que os usuários para editar, inserir e excluir dados e para classificar e dados da página.

Definindo o `ItemType` propriedade em de **ListView** controlar, a expressão de associação de dados `Item` está disponível e o controle torna-se com rigidez de tipos. Conforme mencionado no tutorial anterior, você pode selecionar os detalhes do objeto de Item com o IntelliSense, como especificar o `ProductName`:

![Exibir dados de itens e detalhes - IntelliSense](display_data_items_and_details/_static/image1.png)

Você também estiver usando a associação de modelo para especificar um `SelectMethod` valor. Esse valor (`GetProducts`) corresponde ao método que você adicionará ao código de trás para exibir produtos na próxima etapa.

### <a name="add-code-to-display-products"></a>Adicione código para exibir produtos

Nesta etapa, você adicionará código para preencher a **ListView** controle com os dados de produto do banco de dados. O código suporta mostrando todos os produtos e categoria individual.

1. Na **Gerenciador de soluções**, clique com botão direito *ProductList. aspx* e, em seguida, selecione **Exibir código**.
2. Substitua o código existente na *ProductList.aspx.cs* arquivo com este:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

Este código mostra a `GetProducts` método que o **ListView** do controle `ItemType` referências de propriedade no *ProductList. aspx* página. Para limitar os resultados a uma categoria de banco de dados específico, o código define o `categoryId` valor do valor de cadeia de caracteres de consulta passado para o *ProductList. aspx* página quando o *ProductList. aspx* é de página para onde navegar. O `QueryStringAttribute` classe de `System.Web.ModelBinding` namespace é usado para recuperar o valor da variável de cadeia de caracteres de consulta `id`. Isso instrui a ligação de modelo para tentar associar um valor da cadeia de consulta para o `categoryId` parâmetro em tempo de execução.

Quando uma categoria válida é passada como uma cadeia de caracteres de consulta para a página, os resultados da consulta são limitados aos produtos no banco de dados que correspondem a `categoryId` valor. Por exemplo, se o *ProductsList.aspx* URL da página é este:


[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

A página exibe apenas os produtos em que o `categoryId` é igual a `1`.

Todos os produtos serão exibidos se nenhuma cadeia de caracteres de consulta é incluído quando o *ProductList. aspx* página for chamada.

As fontes de valores para esses métodos são chamadas de *provedores de valor* (como *QueryString*), e os atributos de parâmetro que indicam qual provedor de valor a ser usado são denominados *atributos de provedor de valor* (como `id`). O ASP.NET inclui provedores de valor e os atributos correspondentes para todas as fontes típicas da entrada do usuário em um aplicativo Web Forms, como a cadeia de caracteres de consulta, cookies, valores de formulário, controles, estado de exibição, o estado de sessão e as propriedades de perfil. Você também pode escrever provedores de valor personalizados.

### <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo agora para exibir todos os produtos ou produtos de uma categoria.

1. Pressione **F5** enquanto estiver no Visual Studio para executar o aplicativo.  
   O navegador é aberta e mostra a *default. aspx* página.

2. Selecione **carros** no menu de navegação da categoria de produto.  
   O *ProductList. aspx* página é exibida mostrando apenas **carros** produtos da categoria. Mais tarde neste tutorial, você exibirá detalhes do produto.  

    ![Exibir dados de itens e detalhes - carros](display_data_items_and_details/_static/image2.png)

3. Selecione **produtos** no menu de navegação na parte superior.  
   Novamente, o *ProductList. aspx* página for exibida, no entanto, desta vez, ele mostra a lista completa de produtos.   

    ![Exibir dados de itens e detalhes - produtos](display_data_items_and_details/_static/image3.png)

4. Feche o navegador e retorne ao Visual Studio.

### <a name="add-a-data-control-to-display-product-details"></a>Adicionar um controle de dados para exibir detalhes do produto

Em seguida, você modificará a marcação na *ProductDetails.aspx* página que você adicionou no tutorial anterior para exibir informações de produto específico.

1. Na **Gerenciador de soluções**, abra *ProductDetails.aspx*.

2. Substitua a marcação existente com essa marcação:

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

    Esse código usa um **FormView** controle para exibir detalhes do produto específico. Essa marcação usa métodos, como os métodos usados para exibir dados na *ProductList. aspx* página. O **FormView** controle é usado para exibir um único registro por vez de uma fonte de dados. Quando você usa o **FormView** controle, você cria modelos para exibir e editar valores de associação de dados. Esses modelos contêm controles, expressões de associação, e formatação que definem a aparência e a funcionalidade do formulário.

Conectar-se a marcação anterior para o banco de dados requer código adicional.

1. Na **Gerenciador de soluções**, clique com botão direito *ProductDetails.aspx* e, em seguida, clique em **Exibir código**.  
   O *ProductDetails.aspx.cs* arquivo é exibido.

2. Substitua o código existente com este código:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

Esse código verifica se há um "`productID`" valor de cadeia de caracteres de consulta. Se um valor de cadeia de consulta válido for encontrado, o produto correspondente é exibido. Se a cadeia de consulta não for encontrada, ou seu valor não é válido, nenhum produto será exibido.

### <a name="run-the-application"></a>Executar o aplicativo

Agora você pode executar o aplicativo para ver um produto individual exibido com base na ID de produto.

1. Pressione **F5** enquanto estiver no Visual Studio para executar o aplicativo.  
   O navegador é aberta e mostra a *default. aspx* página.

2. Selecione **barcos** no menu de navegação de categoria.  
   O *ProductList. aspx* página é exibida.

3. Selecione **Paper barco** na lista de produtos.
   O *ProductDetails.aspx* página é exibida.

    ![Exibir dados de itens e detalhes - produtos](display_data_items_and_details/_static/image4.png)
    
4. Feche o navegador.


## <a name="additional-resources"></a>Recursos adicionais

[Recuperando e exibindo dados com a associação de modelos e formulários da web](../../presenting-and-managing-data/model-binding/retrieving-data.md)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou marcação e código para exibir detalhes do produto e produtos. Você aprendeu sobre controles de dados fortemente tipados, associação de modelos e provedores de valor. O próximo tutorial, você adicionará um carrinho de compras para o aplicativo de exemplo Wingtip Toys. 

> [!div class="step-by-step"]
> [Anterior](ui_and_navigation.md)
> [Próximo](shopping-cart.md)
