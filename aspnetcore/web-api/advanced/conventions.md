---
title: Usar convenções de API Web
author: pranavkm
description: Saiba mais sobre as convenções de API Web no ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: web-api/advanced/conventions
ms.openlocfilehash: d49b51d11d3f14d0c3edbe1765d74fd63e3ac061
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657748"
---
# <a name="use-web-api-conventions"></a>Usar convenções de API Web

Por [Pranav Krishnamoorthy](https://github.com/pranavkm) e [Scott Addie](https://github.com/scottaddie)

O ASP.NET Core 2.2 (e posterior) inclui uma forma de extrair a [documentação da API](xref:tutorials/web-api-help-pages-using-swagger) comum e aplicá-la a várias ações, controladores ou a todos os controladores em um assembly. As convenções de API da Web são um substituto para decorar ações individuais com [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).

Uma convenção permite que você:

* Defina os tipos de retorno mais comuns e códigos de status retornados de um tipo de ação específico.
* Identifica as ações que desviam do padrão definido.

O ASP.NET Core MVC 2.2 (e posterior) inclui um conjunto de convenções padrão em <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>. As convenções são baseadas no controlador (*ValuesController.cs*) fornecido no modelo de projeto da **API** do ASP.NET Core. Se suas ações seguem o padrão no modelo, você deve ter êxito ao usar as convenções padrão. Se as convenções padrão não atenderem às suas necessidades, consulte [Criar convenções de API Web](#create-web-api-conventions).

No runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> reconhece as convenções. `ApiExplorer` é a abstração do MVC para comunicação com geradores de documento da [OpenAPI](https://www.openapis.org/) (também conhecida como Swagger). Os atributos da convenção aplicada são associados a uma ação e estão incluídos na documentação da OpenAPI da ação. Os [Analisadores de API](xref:web-api/advanced/analyzers) também reconhecem as convenções. Se a ação for não convencional (por exemplo, ela retorna um código de status que não está documentado pela convenção aplicada), um aviso incentivará você a fazer a documentação do código de status.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="apply-web-api-conventions"></a>Aplicar convenções de API Web

As convenções não fazem composição. Cada ação pode ser associada a exatamente uma convenção. As convenções mais específicas têm precedência sobre as menos específicas. A seleção é não determinística quando duas ou mais convenções da mesma prioridade se aplicam a uma ação. As seguintes opções existem para aplicar uma convenção a uma ação, da mais específica à menos específica:

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; se aplica a ações individuais e especifica o tipo de convenção e o método de Convenção que se aplica.

    No exemplo a seguir, o método de convenção `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` do tipo de convenção padrão é aplicado à ação `Update`:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    o método de convenção `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` aplica os seguintes atributos à ação:

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    Para saber mais sobre `[ProducesDefaultResponseType]`, confira [Resposta padrão](https://swagger.io/docs/specification/describing-responses/#default).

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` aplicado a um controlador &mdash;. Aplica-se o tipo de convenção especificada a todas as ações no controlador. Um método de Convenção é marcado com dicas que determinam as ações às quais o método de Convenção se aplica. Para obter mais informações sobre dicas, consulte [Criar convenções da API Web](#create-web-api-conventions)).

    No exemplo a seguir, o conjunto padrão de convenções é aplicado a todas as ações no *ContactsConventionController*:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` aplicado a um assembly &mdash;. Aplica-se o tipo de convenção especificada a todos os controladores no assembly atual. Como recomendação, aplique atributos no nível do assembly ao arquivo *Startup.cs*.

    No exemplo a seguir, o conjunto padrão de convenções é aplicado a todos os controladores no assembly:

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a>Criar convenções de API Web

Se as convenções padrão da API não atenderem às suas necessidades, crie suas próprias convenções. Uma convenção é:

* um tipo estático com métodos.
* Com capacidade de definir [tipos de resposta](#response-types) e [requisitos de nomenclatura](#naming-requirements) em ações.

### <a name="response-types"></a>Tipos de resposta

Esses métodos são anotados com atributos `[ProducesResponseType]` ou `[ProducesDefaultResponseType]`. Por exemplo:

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

Se atributos de metadados mais específicos estão ausentes, a aplicação dessa convenção a um assembly impõe que:

* O método de convenção se aplica a qualquer ação denominada `Find`.
* Um parâmetro denominado `id` está presente na ação `Find`.

### <a name="naming-requirements"></a>Requisitos de nomenclatura

Os atributos `[ApiConventionNameMatch]` e `[ApiConventionTypeMatch]` podem ser aplicados ao método de convenção que determina as ações às quais eles são aplicáveis. Por exemplo:

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

No exemplo anterior:

* A opção `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` aplicada ao método indica que a convenção corresponde a qualquer ação desde que seja prefixada com "Find". Exemplos de ações correspondentes incluem `Find`, `FindPet` e `FindById`.
* O `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` aplicado ao parâmetro indica que a convenção corresponde a métodos com exatamente um parâmetro encerrando no identificador do sufixo. Exemplos incluem parâmetros como `id` ou `petId`. `ApiConventionTypeMatch` pode ser aplicado da mesma forma aos tipos para restringir o tipo do parâmetro. Um argumento `params[]` indica parâmetros restantes que não precisam ser explicitamente correspondidos.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
