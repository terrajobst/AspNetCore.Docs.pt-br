---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: Itens de dados de exibição e fornece detalhes sobre | Microsoft Docs
author: Erikre
description: Esta série de tutoriais ensinará os conceitos básicos da criação de um aplicativo Web Forms do ASP.NET com o ASP.NET 4.7 e o Microsoft Visual Studio Community 2017 para a Web
ms.author: riande
ms.date: 1/09/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: 73ae1660f5d6e3e28c1c155e745a62936e3502df
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207428"
---
<a name="display-data-items-and-details"></a>Itens de dados de exibição e detalhes
====================
por [Erik Reitan](https://github.com/Erikre)

> Esta série de tutoriais ensina os conceitos básicos da criação de um aplicativo Web Forms do ASP.NET com o ASP.NET 4.7 e o Microsoft Visual Studio Community 2017 para a Web.

Neste tutorial, você aprenderá como exibir itens de dados e os detalhes do item de dados com o Web Forms do ASP.NET e o Entity Framework Code First. Este tutorial se baseia no tutorial anterior de "Interface do usuário e navegação" como parte da série de tutoriais de Wingtip Toys Store. No tutorial concluído, produtos na *ProductsList.aspx* página e detalhes do produto sobre o *ProductDetails.aspx* página são exibidos.

## <a name="what-you-learn"></a>O que você aprenderá

- Adicione um controle de dados para exibir os produtos de banco de dados.
- Conecte-se um controle de dados para dados selecionados.
- Adicione um controle de dados para exibir detalhes do produto.
- Analisar um valor de cadeia de caracteres de consulta e usá-lo para filtrar os dados do banco de dados recuperado.

Recursos apresentados neste tutorial incluem a associação de modelos e provedores de valor.

## <a name="add-a-data-control-to-display-products"></a>Adicionar um controle de dados para exibir produtos
 
Você tem algumas opções para associar dados a um controle de servidor. As mais comuns incluem:

 * Adicionando um controle de fonte de dados
 * Adicionando o código manualmente
 * Implementando a associação de modelos

### <a name="use-a-data-source-control-to-bind-data"></a>Usar um controle de fonte de dados para associar dados

Adicionar um controle de fonte de dados vincula o controle de fonte de dados para o controle que exibe os dados. Com essa abordagem, você pode declarativamente, em vez de programaticamente, conectar controles de servidor para fontes de dados.

### <a name="code-by-hand-to-bind-data"></a>Código manualmente para associar dados

Codificação manual envolve:

1. Ler um valor
2. Verificar se ele é nulo
3. Convertendo-o em um tipo apropriado
4. Verificando o sucesso de conversão
5. Fazer uma consulta com o valor convertido 

Com essa abordagem, você tem controle total sobre a lógica de acesso a dados.

### <a name="use-model-binding-to-bind-data"></a>Usar associação de modelo para associar dados

Com a associação de modelo, você associa os resultados com muito menos código e fornece a capacidade de reutilizar a funcionalidade em todo o aplicativo. Ele simplifica o trabalho com a lógica de acesso a dados e focada no código enquanto ainda fornecem uma estrutura de vinculação de dados avançada.

## <a name="display-products"></a>Exibir produtos

Neste tutorial, você pode usar a associação de modelo para associar dados. Para configurar um controle de dados para usar a associação de modelo para selecionar dados, defina o controle `SelectMethod` propriedade para um método no código da página. O controle de dados chama o método no momento apropriado no ciclo de vida da página e associa automaticamente os dados retornados. Não é necessário chamar explicitamente o `DataBind` método.

Trabalhar com as etapas a seguir, modifique *ProductList. aspx* marcação para exibir produtos.

1. Na **Gerenciador de soluções**, abra *ProductList. aspx*.

2. Substitua a marcação existente pela seguinte marcação: 

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

A marcação anterior usa um **ListView** controle chamado `productList` para exibir produtos.

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

Com modelos e estilos, você define como o **ListView** controle exibe dados. Ele é útil para dados em qualquer estrutura de repetição. Embora isso **ListView** exemplo simplesmente exibe dados de banco de dados, você pode também, sem código, permitir que os usuários para editar, inserir e excluir dados e para classificar e dados da página.

Quando você define o `ItemType` propriedade em de **ListView** controlar, a expressão de associação de dados `Item` está disponível e o controle torna-se com rigidez de tipos. Conforme mencionado no tutorial anterior, você pode selecionar os detalhes do objeto de Item com o IntelliSense, como especificar o `ProductName`:

![Exibir dados de itens e detalhes - IntelliSense](display_data_items_and_details/_static/image1.png)

Com a associação de modelo, você está especificando um `SelectMethod` valor (`GetProducts`). Esse é o método que você adiciona ao código de trás para exibir produtos na próxima etapa.

### <a name="add-code-to-display-products"></a>Adicione código para exibir produtos

Nesta etapa, você adiciona código para preencher a **ListView** controle com os dados de produto do banco de dados. O código suporta mostrando todos os produtos e categoria individual.

1. Na **Gerenciador de soluções**, clique com botão direito *ProductList. aspx* e, em seguida, selecione **Exibir código**.
2. Substitua o código existente na *ProductList.aspx.cs* arquivo com este:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

Este código mostra a `GetProducts` método que o **ListView** do controle `ItemType` referências de propriedade em *ProductList. aspx*. Para limitar os resultados a uma categoria de banco de dados específico, o código define a `categoryId` o valor da cadeia de consulta passada para *ProductList. aspx*. O `QueryStringAttribute` classe de `System.Web.ModelBinding` namespace é usado para recuperar a variável de cadeia de caracteres de consulta `id`do valor. Isso instrui a associação de modelo, em tempo de execução, associar um valor de cadeia de caracteres de consulta para o `categoryId` parâmetro.

Quando uma categoria válida (`categoryId`) é passado, os resultados são limitados aos produtos de banco de dados da categoria. Por exemplo, se o *ProductsList.aspx* URL da página é este:

[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

A página exibe apenas os produtos em que o `categoryId` é igual a `1`.

Todos os produtos serão exibidos se nenhuma cadeia de caracteres de consulta é passada.

As fontes de valor para esses métodos são chamadas *provedores de valor* (como `QueryString`), e os atributos de parâmetro que indicam qual provedor de valor a ser usado são chamados *atributos de provedor de valor* ( como `id`). O ASP.NET inclui provedores de valor e os atributos de todos os Web Forms aplicativo usuário entradas fontes típicas. Isso inclui a cadeia de caracteres de consulta, cookies, valores de formulário, controles, estado de exibição, o estado de sessão e as propriedades de perfil. Você também pode escrever provedores de valor personalizados.

### <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo agora para exibir todos os produtos ou produtos de uma categoria.

1. No Visual Studio, pressione **F5** para executar o aplicativo.
 O navegador é aberta e mostra a *default. aspx* página.

2. No menu de categoria do produto, selecione **carros**.

   O *ProductList. aspx* página for exibida, mostrando apenas os produtos da **carros** categoria. Posteriormente no tutorial, é possível exibir detalhes do produto.

    ![Exibir dados de itens e detalhes - carros](display_data_items_and_details/_static/image2.png)

3. Selecione **produtos** no menu superior.
 O *ProductList. aspx* página agora exibe todos os produtos. 

    ![Exibir dados de itens e detalhes - produtos](display_data_items_and_details/_static/image3.png)

4. Feche o navegador e retorne ao Visual Studio.

### <a name="add-a-data-control-to-display-product-details"></a>Adicionar um controle de dados para exibir detalhes do produto

Modificar a *ProductDetails.aspx* marcação que você adicionou no tutorial anterior para exibir informações de produto específico:

1. Na **Gerenciador de soluções**, abra *ProductDetails.aspx*.

2. Substitua a marcação existente com essa marcação:

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

Essa marcação usa um **FormView** controle para exibir detalhes do produto específico. Ele usa os métodos, como aqueles usados para exibir dados no *ProductList. aspx*. O **FormView** controle é usado para exibir um único registro por vez de uma fonte de dados. Quando você usa o **FormView** controle, você cria modelos para exibir e editar valores de associação de dados. Esses modelos contêm controles, expressões de associação, e formatação que definem a aparência e a funcionalidade do formulário.

Conectar-se a marcação anterior para o banco de dados requer código adicional.

1. Na **Gerenciador de soluções**, clique com botão direito *ProductDetails.aspx* e, em seguida, selecione **Exibir código**.  
   O *ProductDetails.aspx.cs* arquivo é exibido.

2. Substitua o código existente com este:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

Esse código verifica se há um "`productID`" valor de cadeia de caracteres de consulta. Se um valor válido for encontrado, o produto correspondente é exibido. Se a cadeia de caracteres de consulta não for encontrada, ou seu valor não é válido, nenhum produto será exibido.

### <a name="run-the-application"></a>Executar o aplicativo

Agora você pode executar o aplicativo para ver os detalhes do produto específico com base no ID do produto.

1. No Visual Studio, pressione **F5** para executar o aplicativo.  
 O navegador abre *default. aspx*.

2. No menu de categoria, selecione **barcos**.  
 O *ProductList. aspx* página é exibida.

3. Selecione **barco do papel**.  
 O *ProductDetails.aspx* página é exibida.   

    ![Exibir dados de itens e detalhes - produtos](display_data_items_and_details/_static/image4.png)

O próximo tutorial, você adiciona um carrinho de compras no aplicativo Wingtip Toys.

## <a name="additional-resources"></a>Recursos adicionais

[Recuperando e exibindo dados com a associação de modelos e formulários da web](../../presenting-and-managing-data/model-binding/retrieving-data.md)

> [!div class="step-by-step"]
> [Anterior](ui_and_navigation.md)
> [Próximo](shopping-cart.md)
