---
title: Auxiliar de marca de script no ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra os atributos auxiliares de marca de script ASP.NET Core e a função que cada atributo desempenha ao estender o comportamento da marca de script HTML.
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 5f2fb8a45048804afa8aff2989cd53489e45a33b
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256494"
---
# <a name="script-tag-helper-in-aspnet-core"></a>Auxiliar de marca de script no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

O [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) gera um link para um arquivo de script primário ou de retorno. Normalmente, o arquivo de script primário está em uma CDN ( [rede de distribuição de conteúdo](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) ).

[!INCLUDE[](~/includes/cdn.md)]

O auxiliar de marca de script permite que você especifique uma CDN para o arquivo de script e um fallback quando a CDN não estiver disponível. O auxiliar de marca de script fornece a vantagem de desempenho de uma CDN com a robustez da hospedagem local.

A marcação Razor a seguir mostra `script` o elemento de um arquivo de layout criado com o modelo de aplicativo Web ASP.NET Core:

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

O seguinte é semelhante ao HTML renderizado do código anterior (em um ambiente que não é de desenvolvimento):

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

No código anterior, o auxiliar de marca de script gerou o segundo elemento `<script>  (window.jQuery || document.write(`script (), que testa `window.jQuery`o para. Se `window.jQuery` não for encontrado, `document.write(` o executará e criará um script 

## <a name="commonly-used-script-tag-helper-attributes"></a>Atributos auxiliares de marca de script comumente usados

Consulte [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) para todos os atributos, propriedades e métodos auxiliares de marca de script.

### <a name="href"></a>{1&gt;href&lt;1}

Endereço preferencial do recurso vinculado. O endereço é passado para o HTML gerado em todos os casos.

### <a name="asp-fallback-href"></a>ASP-fallback-href

A URL de uma folha de estilos CSS para fazer fallback no caso em que a URL primária falhar.

### <a name="asp-fallback-test-class"></a>ASP-fallback-Test-Class

O nome da classe definida na folha de estilos a ser usada para o teste de fallback. Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.

### <a name="asp-fallback-test-property"></a>ASP-fallback-Test-Property

O nome da propriedade CSS a ser usado para o teste de fallback. Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.

### <a name="asp-fallback-test-value"></a>ASP-fallback-Test-Value

O valor da propriedade CSS a ser usado para o teste de fallback. Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

### <a name="asp-fallback-test-value"></a>ASP-fallback-Test-Value

O valor da propriedade CSS a ser usado para o teste de fallback. Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
