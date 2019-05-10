---
title: Visão geral do ASP.NET Core MVC
author: ardalis
description: Saiba como o ASP.NET Core MVC é uma estrutura avançada para a criação de aplicativos Web e APIs usando o padrão de design Model-View-Controller.
ms.author: riande
ms.date: 01/08/2018
uid: mvc/overview
ms.openlocfilehash: eb636616ab952f506993ae5f4692e5e966bb20f4
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087314"
---
# <a name="overview-of-aspnet-core-mvc"></a>Visão geral do ASP.NET Core MVC

Por [Steve Smith](https://ardalis.com/)

O ASP.NET Core MVC é uma estrutura avançada para a criação de aplicativos Web e APIs usando o padrão de design Model-View-Controller.

## <a name="what-is-the-mvc-pattern"></a>O que é o padrão MVC?

O padrão de arquitetura MVC (Model-View-Controller) separa um aplicativo em três grupos de componentes principais: Modelos, Exibições e Controladores. Esse padrão ajuda a obter a [separação de interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Usando esse padrão, as solicitações de usuário são encaminhadas para um Controlador, que é responsável por trabalhar com o Modelo para executar as ações do usuário e/ou recuperar os resultados de consultas. O Controlador escolhe a Exibição a ser exibida para o usuário e fornece-a com os dados do Modelo solicitados.

O seguinte diagrama mostra os três componentes principais e quais deles referenciam os outros:

![Padrão MVC](overview/_static/mvc.png)

Essa descrição das responsabilidades ajuda você a dimensionar o aplicativo em termos de complexidade, porque é mais fácil de codificar, depurar e testar algo (modelo, exibição ou controlador) que tem um único trabalho. É mais difícil atualizar, testar e depurar um código que tem dependências distribuídas em duas ou mais dessas três áreas. Por exemplo, a lógica da interface do usuário tende a ser alterada com mais frequência do que a lógica de negócios. Se o código de apresentação e a lógica de negócios forem combinados em um único objeto, um objeto que contém a lógica de negócios precisa ser modificado sempre que a interface do usuário é alterada. Isso costuma introduzir erros e exige um novo teste da lógica de negócios após cada alteração mínima da interface do usuário.

> [!NOTE]
> A exibição e o controlador dependem do modelo. No entanto, o modelo não depende da exibição nem do controlador. Esse é um dos principais benefícios da separação. Essa separação permite que o modelo seja criado e testado de forma independente da apresentação visual.

### <a name="model-responsibilities"></a>Responsabilidades do Modelo

O Modelo em um aplicativo MVC representa o estado do aplicativo e qualquer lógica de negócios ou operação que deve ser executada por ele. A lógica de negócios deve ser encapsulada no modelo, juntamente com qualquer lógica de implementação, para persistir o estado do aplicativo. As exibições fortemente tipadas normalmente usam tipos ViewModel criados para conter os dados a serem exibidos nessa exibição. O controlador cria e popula essas instâncias de ViewModel com base no modelo.

### <a name="view-responsibilities"></a>Responsabilidades da Exibição

As exibições são responsáveis por apresentar o conteúdo por meio da interface do usuário. Elas usam o [mecanismo de exibição do Razor](#razor-view-engine) para inserir o código .NET em uma marcação HTML. Deve haver uma lógica mínima nas exibições e qualquer lógica contida nelas deve se relacionar à apresentação do conteúdo. Se você precisar executar uma grande quantidade de lógica em arquivos de exibição para exibir dados de um modelo complexo, considere o uso de um [Componente de Exibição](views/view-components.md), ViewModel ou um modelo de exibição para simplificar a exibição.

### <a name="controller-responsibilities"></a>Responsabilidades do Controlador

Os controladores são os componentes que cuidam da interação do usuário, trabalham com o modelo e, em última análise, selecionam uma exibição a ser renderizada. Em um aplicativo MVC, a exibição mostra apenas informações; o controlador manipula e responde à entrada e à interação do usuário. No padrão MVC, o controlador é o ponto de entrada inicial e é responsável por selecionar quais tipos de modelo serão usados para o trabalho e qual exibição será renderizada (daí seu nome – ele controla como o aplicativo responde a determinada solicitação).

> [!NOTE]
> Os controladores não devem ser excessivamente complicados por muitas responsabilidades. Para evitar que a lógica do controlador se torne excessivamente complexa, efetue push da lógica de negócios para fora do controlador e insira-a no modelo de domínio.

>[!TIP]
> Se você achar que as ações do controlador executam com frequência os mesmos tipos de ações, mova essas ações comuns para [filtros](#filters).

## <a name="what-is-aspnet-core-mvc"></a>O que é ASP.NET Core MVC

A estrutura do ASP.NET Core MVC é uma estrutura de apresentação leve, de software livre e altamente testável, otimizada para uso com o ASP.NET Core.

ASP.NET Core MVC fornece uma maneira com base em padrões para criar sites dinâmicos que habilitam uma separação limpa de preocupações. Ele lhe dá controle total sobre a marcação, dá suporte ao desenvolvimento amigável a TDD e usa os padrões da web mais recentes.

## <a name="features"></a>Recursos

ASP.NET Core MVC inclui o seguinte:

* [Roteamento](#routing)
* [Model binding](#model-binding)
* [Validação de modelo](#model-validation)
* [Injeção de dependência](../fundamentals/dependency-injection.md)
* [Filtros](#filters)
* [Áreas](#areas)
* [APIs Web](#web-apis)
* [Capacidade de teste](#testability)
* [Mecanismo de exibição do Razor](#razor-view-engine)
* [Exibições fortemente tipadas](#strongly-typed-views)
* [Auxiliares de marcação](#tag-helpers)
* [Componentes da exibição](#view-components)

### <a name="routing"></a>Roteamento

O ASP.NET Core MVC baseia-se no [roteamento do ASP.NET Core](../fundamentals/routing.md), um componente de mapeamento de URL avançado que permite criar aplicativos que têm URLs compreensíveis e pesquisáveis. Isso permite que você defina padrões de nomenclatura de URL do aplicativo que funcionam bem para SEO (otimização do mecanismo de pesquisa) e para a geração de links, sem levar em consideração como os arquivos no servidor Web estão organizados. Defina as rotas usando uma sintaxe de modelo de rota conveniente que dá suporte a restrições de valor de rota, padrões e valores opcionais.

O *roteamento baseado em convenção* permite definir globalmente os formatos de URL aceitos pelo aplicativo e como cada um desses formatos é mapeado para um método de ação específico em determinado controlador. Quando uma solicitação de entrada é recebida, o mecanismo de roteamento analisa a URL e corresponde-a a um dos formatos de URL definidos. Em seguida, ele chama o método de ação do controlador associado.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

O *roteamento de atributo* permite que você especifique as informações de roteamento decorando os controladores e as ações com atributos que definem as rotas do aplicativo. Isso significa que as definições de rota são colocadas ao lado do controlador e da ação aos quais estão associadas.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
  [HttpGet("{id}")]
  public IActionResult GetProduct(int id)
  {
    ...
  }
}
```

### <a name="model-binding"></a>Model binding

ASP.NET Core MVC [model binding](models/model-binding.md) converte dados de solicitação de cliente (valores de formulário, os dados de rota, parâmetros de cadeia de caracteres de consulta, os cabeçalhos HTTP) em objetos que o controlador pode manipular. Como resultado, a lógica de controlador não precisa fazer o trabalho de descobrir os dados de solicitação de entrada; ele simplesmente tem os dados como parâmetros para os métodos de ação.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a>Validação de modelo

O ASP.NET Core MVC dá suporte à [validação](models/validation.md) pela decoração do objeto de modelo com atributos de validação de anotação de dados. Os atributos de validação são verificados no lado do cliente antes que os valores sejam postados no servidor, bem como no servidor antes que a ação do controlador seja chamada.

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

Uma ação do controlador:

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

A estrutura manipula a validação dos dados de solicitação no cliente e no servidor. A lógica de validação especificada em tipos de modelo é adicionada às exibições renderizados como anotações não invasivas e é imposta no navegador com o [jQuery Validation](https://jqueryvalidation.org/).

### <a name="dependency-injection"></a>Injeção de dependência

O ASP.NET Core tem suporte interno para [DI (injeção de dependência)](../fundamentals/dependency-injection.md). No ASP.NET Core MVC, os [controladores](controllers/dependency-injection.md) podem solicitar serviços necessários por meio de seus construtores, possibilitando o acompanhamento do [princípio de dependências explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

O aplicativo também pode usar a [injeção de dependência em arquivos no exibição](views/dependency-injection.md), usando a diretiva `@inject`:

```cshtml
@inject SomeService ServiceName
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>Filtros

Os [filtros](controllers/filters.md) ajudam os desenvolvedores a encapsular interesses paralelos, como tratamento de exceção ou autorização. Os filtros permitem a execução de uma lógica pré e pós-processamento personalizada para métodos de ação e podem ser configurados para execução em determinados pontos no pipeline de execução de uma solicitação específica. Os filtros podem ser aplicados a controladores ou ações como atributos (ou podem ser executados globalmente). Vários filtros (como `Authorize`) são incluídos na estrutura. `[Authorize]` é o atributo usado para criar filtros de autorização do MVC.

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a>Áreas

As [áreas](controllers/areas.md) fornecem uma maneira de particionar um aplicativo Web ASP.NET Core MVC grande em agrupamentos funcionais menores. Uma área é uma estrutura MVC dentro de um aplicativo. Em um projeto MVC, componentes lógicos como Modelo, Controlador e Exibição são mantidos em pastas diferentes e o MVC usa convenções de nomenclatura para criar a relação entre esses componentes. Para um aplicativo grande, pode ser vantajoso particionar o aplicativo em áreas de nível alto separadas de funcionalidade. Por exemplo, um aplicativo de comércio eletrônico com várias unidades de negócios, como check-out, cobrança e pesquisa, etc. Cada uma dessas unidades têm suas próprias exibições de componente lógico, controladores e modelos.

### <a name="web-apis"></a>APIs da Web

Além de ser uma ótima plataforma para a criação de sites, o ASP.NET Core MVC tem um excelente suporte para a criação de APIs Web. Crie serviços que alcançam uma ampla gama de clientes, incluindo navegadores e dispositivos móveis.

A estrutura inclui suporte para a negociação de conteúdo HTTP com suporte interno para [formatar dados](xref:web-api/advanced/formatting) como JSON ou XML. Escreva [formatadores personalizados](xref:web-api/advanced/custom-formatters) para adicionar suporte para seus próprios formatos.

Use a geração de links para habilitar o suporte para hipermídia. Habilite o suporte para o [CORS (Compartilhamento de Recursos Entre Origens)](http://www.w3.org/TR/cors/) com facilidade, de modo que as APIs Web possam ser compartilhadas entre vários aplicativos Web.

### <a name="testability"></a>Capacidade de teste

O uso pela estrutura da injeção de dependência e de interfaces a torna adequada para teste de unidade. Além disso, a estrutura inclui recursos (como um provedor TestHost e InMemory para o Entity Framework) que também agiliza e facilita a execução de [testes de integração](xref:test/integration-tests). Saiba mais sobre [como testar a lógica do controlador](controllers/testing.md).

### <a name="razor-view-engine"></a>Mecanismo de exibição do Razor

As [exibições do ASP.NET Core MVC](views/overview.md) usam o [mecanismo de exibição do Razor](views/razor.md) para renderizar exibições. Razor é uma linguagem de marcação de modelo compacta, expressiva e fluida para definir exibições usando um código C# inserido. O Razor é usado para gerar o conteúdo da Web no servidor de forma dinâmica. Você pode combinar o código do servidor com o código e o conteúdo do lado cliente de maneira limpa.

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

Usando o mecanismo de exibição do Razor, você pode definir [layouts](views/layout.md), [exibições parciais](views/partial.md) e seções substituíveis.

### <a name="strongly-typed-views"></a>Exibições fortemente tipadas

As exibições do Razor no MVC podem ser fortemente tipadas com base no modelo. Os controladores podem passar um modelo fortemente tipado para as exibições, permitindo que elas tenham a verificação de tipo e o suporte do IntelliSense.

Por exemplo, a seguinte exibição renderiza um modelo do tipo `IEnumerable<Product>`:

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>Auxiliares de Marca

Os [Auxiliares de Marca](views/tag-helpers/intro.md) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor. Use auxiliares de marca para definir marcas personalizadas (por exemplo, `<environment>`) ou para modificar o comportamento de marcas existentes (por exemplo, `<label>`). Os Auxiliares de Marca associam a elementos específicos com base no nome do elemento e seus atributos. Eles oferecem os benefícios da renderização do lado do servidor, enquanto preservam uma experiência de edição de HTML.

Há muitos Auxiliares de Marca internos para tarefas comuns – como criação de formulários, links, carregamento de ativos e muito mais – e ainda outros disponíveis em repositórios GitHub públicos e como NuGet. Os Auxiliares de Marca são criados no C# e são direcionados a elementos HTML de acordo com o nome do elemento, o nome do atributo ou a marca pai. Por exemplo, o LinkTagHelper interno pode ser usado para criar um link para a ação `Login` do `AccountsController`:

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

O `EnvironmentTagHelper` pode ser usado para incluir scripts diferentes nas exibições (por exemplo, bruto ou minimizado) de acordo com o ambiente de tempo de execução, como Desenvolvimento, Preparo ou Produção:

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

Os Auxiliares de Marca fornecem uma experiência de desenvolvimento amigável a HTML e um ambiente avançado do IntelliSense para a criação de HTML e marcação do Razor. A maioria dos Auxiliares de Marca internos é direcionada a elementos HTML existentes e fornece atributos do lado do servidor para o elemento.

### <a name="view-components"></a>Componentes da exibição

Os [Componentes de Exibição](views/view-components.md) permitem que você empacote a lógica de renderização e reutilize-a em todo o aplicativo. São semelhantes às [exibições parciais](views/partial.md), mas com a lógica associada.

## <a name="compatibility-version"></a>Versão de compatibilidade

O método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite que um aplicativo aceite ou recuse as possíveis alterações da falha de comportamento introduzidas no ASP.NET Core MVC 2.1 ou posteriores.

Para obter mais informações, consulte <xref:mvc/compatibility-version>.
