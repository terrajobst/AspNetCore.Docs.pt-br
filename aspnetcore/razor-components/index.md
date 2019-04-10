---
title: Introdução aos componentes do Razor no ASP.NET Core
author: guardrex
description: Explore os componentes do ASP.NET Core Razor, uma maneira de criar a IU da Web interativa do lado do cliente com o .NET em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/08/2019
uid: razor-components/index
ms.openlocfilehash: c64f40ab78036e96db154acc33588a08bbf2f2d6
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468660"
---
# <a name="introduction-to-razor-components"></a>Introdução aos Componentes Razor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

O recurso *Componentes do Razor*, introduzido na versão 3.0 do ASP.NET Core (Versão Prévia), é uma maneira de criar uma interface do usuário da Web interativa do lado do cliente com .NET:

* Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.
* Compartilhe a lógica de aplicativo do lado do cliente e do servidor toda escrita com o .NET.
* Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.

Os Componentes Razor são compatíveis com instalações centrais exigidas pela maioria dos aplicativos, incluindo:

* Parâmetros
* Manipulação de eventos
* Associação de dados
* Roteamento
* Injeção de dependência
* Layouts
* Modelagem
* Valores em cascata

Os Componentes Razor desvinculam a lógica de renderização do componente da forma como as atualizações da IU são aplicadas. Os Componentes Razor do ASP.NET Core no .NET Core 3.0 adicionam suporte à hospedagem de Componentes Razor no servidor em um aplicativo ASP.NET Core. As atualizações da interface do usuário são tratadas por uma conexão SignalR.

O tempo de execução:

* Trata do envio de eventos de interface do usuário do navegador para o servidor.
* Aplica as atualizações da interface do usuário enviadas pelo servidor de volta para o navegador depois de executar os componentes.

A conexão usada pelos Componentes Razor para se comunicarem com o navegador também é usada para manusear chamadas de interoperabilidade de JavaScript.

![Os Componentes Razor executam o código do .NET no servidor e interagem com o Modelo de Objeto do Documento no cliente por uma conexão SignalR](index/_static/aspnet-core-razor-components.png)

Para obter mais informações, consulte <xref:razor-components/hosting-models#server-side-hosting-model>.

O *Blazor* é o modelo de hospedagem experimental do lado do cliente dos Componentes Razor. O Blazor é executado em .NET no navegador usando padrões abertos da Web sem plug-ins ou transpilação de código. Para obter mais informações, consulte <xref:spa/blazor/index> e <xref:razor-components/hosting-models#client-side-hosting-model>.

## <a name="components"></a>Componentes

Um *Componente Razor* é uma parte da interface do usuário, como uma página, uma caixa de diálogo ou um formulário de entrada de dados. Os componentes manuseiam eventos de usuário e definem a lógica flexível de renderização de interface do usuário. Os componentes podem ser aninhados e reutilizados.

Os componentes são classes do .NET compiladas em assemblies do .NET que podem ser compartilhados e distribuídos como pacotes NuGet. A classe normalmente é gravada na forma de uma página de marcação do Razor com uma extensão de arquivo *.razor*.

O [Razor](xref:mvc/views/razor) é uma sintaxe para a combinação da marcação HTML com o código C#. O Razor foi projetado visando a produtividade do desenvolvedor, permitindo que ele mude entre a marcação e o C# no mesmo arquivo com o suporte do [IntelliSense](/visualstudio/ide/using-intellisense). As Razor Pages e as exibições MVC também usam o Razor. Ao contrário das Razor Pages e das exibições MVC, que são criadas ao redor de um modelo de solicitação/resposta, os componentes são usados especificamente para manusear a composição da interface do usuário. Os Componentes Razor podem ser usados especificamente para composição e lógica de interface do usuário do lado do cliente.

A marcação a seguir é um exemplo de um componente de caixa de diálogo personalizada em um arquivo Razor (*DialogComponent.razor*):

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick="@OnOK">OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

Quando esse componente é usado em outro lugar no aplicativo, o IntelliSense acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.

Os componentes são renderizados em uma representação na memória do DOM do navegador chamada *árvore de renderização*, que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.

## <a name="javascript-interop"></a>Interoperabilidade do JavaScript

Para aplicativos que exigem bibliotecas JavaScript e APIs do navegador de terceiros, os componentes interoperam com o JavaScript. Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar. O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#. Para obter mais informações, confira [Interoperabilidade do JavaScript](xref:razor-components/javascript-interop).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:spa/blazor/index>
* [WebAssembly](http://webassembly.org/)
* [Guia de C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
