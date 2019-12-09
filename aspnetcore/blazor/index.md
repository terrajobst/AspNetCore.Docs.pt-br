---
title: Introdução ao ASP.NET Core Blazor
author: guardrex
description: Explore ASP.NET Core Blazor, uma maneira de criar interface do usuário da Web interativa do lado do cliente com o .NET em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 4ab6af649afe66d92fb2ea07d3abed057f321bce
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943713"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a>Introdução ao ASP.NET Core Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

*Bem-vindo ao Blazor!*

Blazor é uma estrutura para a criação de interface do usuário da Web interativa do lado do cliente com o .NET:

* Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.
* Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.
* Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.

Usar o .NET para desenvolvimento web do lado do cliente oferece as seguintes vantagens:

* escreva o código em C# em vez de JavaScript.
* Aproveite o ecossistema .NET existente das bibliotecas .NET.
* Compartilhe a lógica de aplicativo entre o servidor e o cliente.
* Beneficie-se com o desempenho, confiabilidade e segurança do .NET.
* mantenha-se produtivo com o Visual Studio no Windows, Linux e macOS.
* Crie um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.

## <a name="components"></a>Componentes

Blazor aplicativos são baseados em *componentes*. Um componente no Blazor é um elemento da interface do usuário, como um formulário de página, caixa de diálogo ou entrada de dados.

Os componentes são classes do .NET incorporadas a assemblies .NET que:

* Definem a lógica de renderização da interface de usuário flexível.
* Tratam eventos do usuário.
* Podem ser aninhados e reutilizados.
* Podem ser compartilhados e distribuído como [bibliotecas de classes Razor](xref:razor-pages/ui-class) ou [pacotes do NuGet](/nuget/what-is-nuget).

A classe do componente geralmente é gravada na forma de uma página de marcação do [Razor](xref:mvc/views/razor) com uma extensão de arquivo *.razor*. Os componentes no Blazor são formalmente chamados de *componentes do Razor*. O Razor é uma sintaxe para a combinação de marcação HTML com o código C# projetada para melhorar a produtividade do desenvolvedor. O Razor permite que você alterne entre a marcação HTML e C# no mesmo arquivo com suporte [IntelliSense](/visualstudio/ide/using-intellisense). As Razor Pages e MVC também usam o Razor. Ao contrário das Razor Pages e das MVC, que são criadas ao redor de um modelo de solicitação/resposta, os componentes são usados especificamente para composição e lógica da interface do usuário do lado do cliente.

A seguinte marcação Razor demonstra um componente (*Dialog.razor*), que pode ser aninhado dentro de outro componente:

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

O conteúdo do corpo da caixa de diálogo (`ChildContent`) e o título (`Title`) são fornecidos pelo componente que usa esse componente em sua interface do usuário. `OnYes` é um método C# disparado pelo evento `onclick` do botão.

Blazor usa marcas HTML naturais para composição da interface do usuário. Os elementos HTML especificam componentes e os atributos da marcação transmitem valores para as propriedades de um componente.

No exemplo a seguir, o componente `Index` usa o componente `Dialog`. `ChildContent` e `Title` são definidos pelos atributos e pelo conteúdo do elemento `<Dialog>`.

*Index.razor*:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

A caixa de diálogo é renderizada quando o pai (*Index.razor*) é acessado em um navegador:

![Componente da caixa de diálogo renderizada no navegador](index/_static/dialog.png)

Quando esse componente é usado no aplicativo, o IntelliSense no [Visual Studio](/visualstudio/ide/using-intellisense) e no [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.

Os componentes são renderizados em uma representação na memória do Modelo de Objeto do Documento (DOM) do navegador chamada *árvore de renderização*, que é usada para atualizar a interface do usuário de maneira flexível e eficiente.

## <a name="opno-locblazor-webassembly"></a>Blazor Webassembly

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor Webassembly é uma estrutura de aplicativo de página única para criar aplicativos Web do lado do cliente interativos com o .NET. Blazor Webassembly usa padrões abertos da Web sem plug-ins ou código transpilação e trabalha em todos os navegadores da Web modernos, incluindo navegadores móveis.

A execução do código do .NET em navegadores da Web é possibilitada por [WebAssembly](https://webassembly.org) (abreviado como *wasm*). O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução. O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins.

O código WebAssembly pode acessar a funcionalidade completa do navegador por meio de JavaScript, chamado *Interoperabilidade do JavaScript* (ou *Interop do JavaScript*). O código .NET executado por meio da WebAssembly no navegador é executado na área restrita do JavaScript do navegador com as proteções que a área restrita oferece contra ações mal intencionadas no computador cliente.

![[! Parar. Não-LOC (mais alto)] o Webassembly executa o código .NET no navegador com Webassembly.](index/_static/blazor-webassembly.png)

Quando um aplicativo Webassembly Blazor é compilado e executado em um navegador:

* os arquivos C# e os arquivos Razor são compilados em assemblies do .NET.
* os assemblies e o runtime do .NET são baixados no navegador.
* Blazor Webassembly Inicializa o tempo de execução do .NET e configura o tempo de execução para carregar os assemblies para o aplicativo. O tempo de execução do Webassembly Blazor usa a interoperabilidade JavaScript para manipular as chamadas de API do navegador e a manipulação DOM.

O tamanho do aplicativo publicado, seu *tamanho de payload*, é um fator de desempenho crítico para a utilidade do aplicativo. Um aplicativo grande leva um tempo relativamente longo para baixar para um navegador, o que afeta a experiência do usuário. Blazor Webassembly otimiza o tamanho da carga para reduzir os tempos de download:

* O código não utilizado é retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:host-and-deploy/blazor/configure-linker).
* As respostas HTTP são compactadas.
* O runtime do .NET e os assemblies são armazenados em cache no navegador.

## <a name="opno-locblazor-server"></a>Servidor do Blazor

Blazor dissocia a lógica de renderização do componente de como as atualizações da interface do usuário são aplicadas. o Blazor Server fornece suporte para hospedar componentes do Razor no servidor em um aplicativo ASP.NET Core. As atualizações da interface do usuário são manipuladas por uma conexão [SignalR](xref:signalr/introduction) .

O runtime realiza o envio de eventos da interface do usuário do navegador para o servidor e executar os componentes, aplica as atualizações na interface do usuário retornadas pelo servidor ao navegador.

A conexão usada pelo Blazor Server para se comunicar com o navegador também é usada para lidar com as chamadas de interoperabilidade do JavaScript.

![[! Parar. O servidor não LOC (mais novo)] executa o código .NET no servidor e interage com o Modelo de Objeto do Documento no cliente por meio de um [! Parar. Conexão não-LOC (Signalr)]](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interoperabilidade do JavaScript

Para aplicativos que exigem bibliotecas JavaScript e acesso a APIs do navegador de terceiros, os componentes interoperam com o JavaScript. Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar. O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#. Para obter mais informações, consulte <xref:blazor/javascript-interop>.

## <a name="code-sharing-and-net-standard"></a>Compartilhamento de código e o .NET Standard

Blazor implementa [.NET Standard 2,0](/dotnet/standard/net-standard). O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET. .NET Standard bibliotecas de classe podem ser compartilhadas entre diferentes plataformas .NET, como Blazor, .NET Framework, .NET Core, Xamarin, mono e Unity.

As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete e threading) geram a <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Recursos adicionais

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* [Guia do C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* Links de comunidade do [Blazorincríveis](https://github.com/AdrienTorris/awesome-blazor)
