---
title: Trabalhar com o modelo de aplicativo no ASP.NET Core
author: ardalis
description: Saiba como ler e manipular o modelo de aplicativo para modificar como os elementos MVC se comportam no ASP.NET Core.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/application-model
ms.openlocfilehash: 4b6c978e5752eb320412a1c204df8e3d288fe4a1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666428"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a>Trabalhar com o modelo de aplicativo no ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

O ASP.NET Core MVC define um *modelo de aplicativo* que representa os componentes de um aplicativo MVC. Leia e manipule esse modelo para modificar o comportamento de elementos MVC. Por padrão, o MVC segue algumas convenções para determinar quais classes são consideradas controladores, quais métodos nessas classes são ações e qual o comportamento de parâmetros e do roteamento. Personalize esse comportamento de acordo com as necessidades do aplicativo criando suas próprias convenções e aplicando-as globalmente ou como atributos.

## <a name="models-and-providers"></a>Modelos e provedores

O modelo de aplicativo ASP.NET Core MVC inclui interfaces abstratas e classes de implementação concreta que descrevem um aplicativo MVC. Esse modelo é o resultado da descoberta do MVC de controladores, ações, parâmetros de ação, rotas e filtros do aplicativo de acordo com as convenções padrão. Trabalhando com o modelo de aplicativo, você pode modificar o aplicativo para seguir convenções diferentes do comportamento padrão do MVC. Os parâmetros, os nomes, as rotas e os filtros são todos usados como dados de configuração para ações e controladores.

O Modelo de Aplicativo ASP.NET Core MVC tem a seguinte estrutura:

* ApplicationModel
  * Controladores (ControllerModel)
    * Ações (ActionModel)
      * Parâmetros (ParameterModel)

Cada nível do modelo tem acesso a uma coleção `Properties` comum e níveis inferiores podem acessar e substituir valores de propriedade definidos por níveis mais altos na hierarquia. As propriedades são persistidas nas `ActionDescriptor.Properties` quando as ações são criadas. Em seguida, quando uma solicitação está sendo manipulada, as propriedades que uma convenção adicionou ou modificou podem ser acessadas por meio de `ActionContext.ActionDescriptor.Properties`. O uso de propriedades é uma ótima maneira de configurar filtros, associadores de modelos, etc., por ação.

> [!NOTE]
> A coleção `ActionDescriptor.Properties` não é thread-safe (para gravações) após a conclusão da inicialização do aplicativo. Convenções são a melhor maneira de adicionar dados com segurança a essa coleção.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

O ASP.NET Core MVC carrega o modelo de aplicativo usando um padrão de provedor, definido pela interface [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider). Esta seção aborda alguns dos detalhes de implementação interna de como funciona esse provedor. Este é um tópico avançado – a maioria dos aplicativos que utiliza o modelo de aplicativo deve fazer isso trabalhando com convenções.

Implementações da interface `IApplicationModelProvider` "encapsulam" umas às outras, com cada implementação chamando `OnProvidersExecuting` em ordem crescente com base em sua propriedade `Order`. O método `OnProvidersExecuted` é então chamado em ordem inversa. A estrutura define vários provedores:

Primeiro (`Order=-1000`):

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Em seguida (`Order=-990`):

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> A ordem na qual dois provedores com o mesmo valor para `Order` são chamados não é definida e, portanto, não se deve depender dela.

> [!NOTE]
> `IApplicationModelProvider` é um conceito avançado a ser estendido pelos autores da estrutura. Em geral, aplicativos devem usar convenções e estruturas devem usar provedores. A principal diferença é que os provedores sempre são executados antes das convenções.

O `DefaultApplicationModelProvider` estabelece muitos dos comportamentos padrão usados pelo ASP.NET Core MVC. Suas responsabilidades incluem:

* Adicionar filtros globais ao contexto
* Adicionar controladores ao contexto
* Adicionar métodos de controlador públicos como ações
* Adicionar parâmetros de método de ação ao contexto
* Aplicar a rota e outros atributos

Alguns comportamentos internos são implementados pelo `DefaultApplicationModelProvider`. Esse provedor é responsável pela construção do [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), que, por sua vez, referencia instâncias [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) e [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel). A classe `DefaultApplicationModelProvider` é um detalhe de implementação de estrutura interna que pode e será alterado no futuro. 

O `AuthorizationApplicationModelProvider` é responsável por aplicar o comportamento associado aos atributos `AuthorizeFilter` e `AllowAnonymousFilter`. [Saiba mais sobre esses atributos](xref:security/authorization/simple).

O `CorsApplicationModelProvider` implementa o comportamento associado ao `IEnableCorsAttribute`, ao `IDisableCorsAttribute` e ao `DisableCorsAuthorizationFilter`. [Saiba mais sobre o CORS](xref:security/cors).

## <a name="conventions"></a>Convenções

O modelo de aplicativo define abstrações de convenção que fornecem uma maneira simples de personalizar o comportamento dos modelos em vez de substituir o modelo ou o provedor inteiro. Essas abstrações são a maneira recomendada para modificar o comportamento do aplicativo. As convenções fornecem uma maneira de escrever código que aplicará personalizações de forma dinâmica. Embora os [filtros](xref:mvc/controllers/filters) forneçam um meio de modificar o comportamento da estrutura, as personalizações permitem controlar como o aplicativo inteiro funciona em conjunto.

As seguintes convenções estão disponíveis:

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

As convenções são aplicadas por sua adição às opções do MVC ou pela implementação de `Attribute`s e sua aplicação a controladores, ações ou parâmetros de ação (semelhante a [`Filters`](xref:mvc/controllers/filters)). Ao contrário dos filtros, as convenções são executadas apenas quando o aplicativo é iniciado, não como parte de cada solicitação.

### <a name="sample-modifying-the-applicationmodel"></a>Amostra: modificando o ApplicationModel

A convenção a seguir é usada para adicionar uma propriedade ao modelo de aplicativo. 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

As convenções de modelo de aplicativo são aplicadas como opções quando o MVC é adicionado em `ConfigureServices` em `Startup`.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

As propriedades são acessíveis na coleção de propriedades `ActionDescriptor` nas ações do controlador:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>Amostra: modificando a descrição de ControllerModel

Como no exemplo anterior, o modelo de controlador também pode ser modificado para incluir propriedades personalizadas. Elas substituirão as propriedades existentes com o mesmo nome especificado no modelo de aplicativo. O seguinte atributo de convenção adiciona uma descrição no nível do controlador:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

Essa convenção é aplicada como um atributo em um controlador.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

A propriedade "description" é acessada da mesma maneira como nos exemplos anteriores.

### <a name="sample-modifying-the-actionmodel-description"></a>Amostra: modificando a descrição de ActionModel

Uma convenção de atributo separada pode ser aplicada a ações individuais, substituindo o comportamento já aplicado no nível do aplicativo ou do controlador.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

A aplicação disso a uma ação no controlador do exemplo anterior demonstra como ela substitui a convenção no nível do controlador:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>Amostra: modificando o ParameterModel

A convenção a seguir pode ser aplicada a parâmetros de ação para modificar seu `BindingInfo`. A convenção a seguir exige que o parâmetro seja um parâmetro de rota; outras possíveis origens da associação (como valores de cadeia de caracteres de consulta) são ignoradas.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

O atributo pode ser aplicado a qualquer parâmetro de ação:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>Amostra: modificando o nome de ActionModel

A convenção a seguir modifica o `ActionModel` para atualizar o *nome* da ação ao qual ele é aplicado. O novo nome é fornecido como um parâmetro para o atributo. Esse novo nome é usado pelo roteamento e, portanto, isso afetará a rota usada para acessar esse método de ação.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

Esse atributo é aplicado a um método de ação no `HomeController`:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

Mesmo que o nome do método seja `SomeName`, o atributo substitui a convenção do MVC de uso do nome do método e substitui o nome da ação por `MyCoolAction`. Portanto, a rota usada para acessar essa ação é `/Home/MyCoolAction`.

> [!NOTE]
> Esse exemplo é basicamente o mesmo que usar o atributo [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) interno.

### <a name="sample-custom-routing-convention"></a>Amostra: convenção de roteamento personalizada

Use uma `IApplicationModelConvention` para personalizar como funciona o roteamento. Por exemplo, a seguinte convenção incorporará namespaces dos Controladores em suas rotas, substituindo `.` no namespace por `/` na rota:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

A convenção é adicionada como uma opção na classe Startup.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> Adicione convenções ao [middleware](xref:fundamentals/middleware/index) acessando `MvcOptions` com `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`

Esta amostra aplica essa convenção às rotas que não estão usando o roteamento de atributo, nas quais o controlador tem "Namespace" em seu nome. O seguinte controlador demonstra essa convenção:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>Uso do modelo de aplicativo em WebApiCompatShim

O ASP.NET Core MVC usa um conjunto diferente de convenções do ASP.NET Web API 2. Usando convenções personalizadas, você pode modificar o comportamento de um aplicativo ASP.NET Core MVC para que ele seja consistente com o comportamento de um aplicativo de API Web. A Microsoft fornece o [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) especificamente para essa finalidade.

> [!NOTE]
> Saiba mais sobre [migração do ASP.NET Web API](xref:migration/webapi).

Para usar o Shim de Compatibilidade de API Web, você precisa adicionar o pacote ao projeto e, em seguida, adicionar as convenções ao MVC chamando `AddWebApiConventions` em `Startup`:

```csharp
services.AddMvc().AddWebApiConventions();
```

As convenções fornecidas pelo shim são aplicadas apenas às partes do aplicativo que tiveram determinados atributos aplicados a elas. Os seguintes quatro atributos são usados para controlar quais controladores devem ter suas convenções modificadas pelas convenções do shim:

* [UseWebApiActionConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>Convenções de ação

O `UseWebApiActionConventionsAttribute` é usado para mapear o método HTTP para ações com base em seu nome (por exemplo, `Get` será mapeado para `HttpGet`). Ele se aplica somente a ações que não usam o roteamento de atributo.

### <a name="overloading"></a>Sobrecarga

O `UseWebApiOverloadingAttribute` é usado para aplicar a convenção `WebApiOverloadingApplicationModelConvention`. Essa convenção adiciona uma `OverloadActionConstraint` ao processo de seleção de ação, o que limita as ações de candidato àquelas para as quais a solicitação atende a todos os parâmetros não opcionais.

### <a name="parameter-conventions"></a>Convenções de parâmetro

O `UseWebApiParameterConventionsAttribute` é usado para aplicar a convenção de ação `WebApiParameterConventionsApplicationModelConvention`. Essa convenção especifica que tipos simples usados como parâmetros de ação são associados por meio do URI por padrão, enquanto tipos complexos são associados por meio do corpo da solicitação.

### <a name="routes"></a>Rotas

O `UseWebApiRoutesAttribute` controla se a convenção de controlador `WebApiApplicationModelConvention` é aplicada. Quando habilitada, essa convenção é usada para adicionar suporte de [áreas](xref:mvc/controllers/areas) à rota.

Além de um conjunto de convenções, o pacote de compatibilidade inclui uma classe base `System.Web.Http.ApiController` que substitui aquela fornecida pela API Web. Isso permite que os controladores escritos para a API Web e que herdam de seu `ApiController` funcionem como foram criados, enquanto são executados no ASP.NET Core MVC. Todos os atributos de `UseWebApi*` listados anteriormente são aplicados à classe de controlador base. O `ApiController` expõe propriedades, métodos e tipos de resultado compatíveis com aqueles encontrados na API Web.

## <a name="using-apiexplorer-to-document-your-app"></a>Usando o ApiExplorer para documentar o aplicativo

O modelo de aplicativo expõe uma propriedade [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) em cada nível, que pode ser usada para percorrer a estrutura do aplicativo. Isso pode ser usado para [gerar páginas da Ajuda para as APIs Web usando ferramentas como o Swagger](xref:tutorials/web-api-help-pages-using-swagger). A propriedade `ApiExplorer` expõe uma propriedade `IsVisible` que pode ser definida para especificar quais partes do modelo do aplicativo devem ser expostas. Defina essa configuração usando uma convenção:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

Usando essa abordagem (e convenções adicionais, se necessário), você pode habilitar ou desabilitar a visibilidade da API em qualquer nível no aplicativo. 
