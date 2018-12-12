---
title: Usar convenções de API Web
author: pranavkm
description: Saiba mais sobre as convenções de API Web no ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: pranavkm
ms.custom: mvc
ms.date: 11/13/2018
uid: web-api/advanced/conventions
ms.openlocfilehash: ede9a46c160cf6a49aa93da710af0bf0b8f59acc
ms.sourcegitcommit: c4572be5ebb301013a5698caf9b5572b76cb2e34
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710069"
---
# <a name="use-web-api-conventions"></a>Usar convenções de API Web

O ASP.NET Core 2.2 apresenta uma forma de extrair a [documentação da API](xref:tutorials/web-api-help-pages-using-swagger) comum e aplicá-la a várias ações, controladores ou a todos os controladores em um assembly. As convenções de API Web são um substituto para ambientar as ações individuais com [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute). Isso permite definir os tipos de retorno "convencionais" mais comuns e os códigos de status que você retorna de sua ação com uma maneira de selecionar o método de convenção que se aplica a uma ação.

Por padrão, o ASP.NET Core MVC 2.2 é fornecido com um conjunto de convenções padrão, `Microsoft.AspNetCore.Mvc.DefaultApiConventions`. As convenções baseiam-se no controlador ao qual o ASP.NET Core efetua o processo de scaffolding. Se suas ações seguem o padrão que o scaffolding produz, você deve ter êxito ao usar as convenções padrão.

No tempo de execução, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> reconhece as convenções. `ApiExplorer` é a abstração do MVC para comunicação com geradores de documento da API aberta. Os atributos da convenção aplicada são associados a uma ação e estão incluídos na documentação do Swagger da ação. Analisadores de API também reconhecem as convenções. Se a ação for não convencional (por exemplo, ela retorna um código de status que não está documentado pela convenção aplicada), ela produzirá um aviso, incentivando você a fazer a documentação.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="apply-web-api-conventions"></a>Aplicar convenções de API Web

Há três maneiras de aplicar uma convenção. As convenções não fazem composição. Cada ação pode ser associada a exatamente uma convenção. Convenções mais específicas (detalhadas abaixo) têm precedência sobre as menos específicas. A seleção é não determinística quando duas ou mais convenções da mesma prioridade se aplicam a uma ação. As seguintes opções existem para aplicar uma convenção a uma ação, da mais específica à menos específica:

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; aplica-se a ações individuais e especifica o tipo de convenção e o método de convenção que se aplica. No exemplo a seguir, o método de convenção `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` é aplicado à ação `Update`:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventionmethod&highlight=2-3)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` aplicado a um controlador &mdash; Aplica-se o tipo de convenção a todas as ações no controlador. Os métodos de convenção apresentam dicas que determinam quais ações seriam aplicáveis (detalhes como parte da criação de convenções). Por exemplo:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventiontypeattribute)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` aplicado a um assembly &mdash; Aplica-se o tipo de convenção a todos os controladores no assembly atual. Por exemplo:

    [!code-csharp[](conventions/sample/Startup.cs?name=apiconventiontypeattribute)]

## <a name="create-web-api-conventions"></a>Criar convenções de API Web

Uma convenção é um tipo estático com métodos. Esses métodos são anotados com atributos `[ProducesResponseType]` ou `[ProducesDefaultResponseType]`.

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(200)]
    [ProducesResponseType(404)]
    public static void Find(int id)
    {
    }
}
```

Aplicar essa convenção a um assembly resulta no método de convenção sendo aplicado a qualquer ação com o nome `Find` e tendo exatamente um parâmetro chamado `id`, desde que eles não tenham outros atributos de metadados mais específicos.

Além de `[ProducesResponseType]` e `[ProducesDefaultResponseType]`, `[ApiConventionNameMatch]` e `[ApiConventionTypeMatch]` podem ser aplicados ao método de convenção que determina os métodos aos quais eles são aplicáveis. Por exemplo:

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

* A opção `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` aplicada ao método, indica que a convenção pode corresponder a qualquer ação desde que seja prefixada com "Find". Isso inclui métodos como `Find`, `FindPet` ou `FindById`.
* O `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` aplicado ao parâmetro indica que a convenção pode corresponder a métodos com exatamente um parâmetro encerrando no identificador do sufixo. Isso inclui parâmetros como `id` ou `petId`. `ApiConventionTypeMatch` pode ser aplicado da mesma forma aos tipos para restringir o tipo do parâmetro. Um argumento `params[]` pode ser usado para indicar parâmetros restantes que não precisam ser explicitamente correspondidos.
