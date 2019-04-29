---
title: Introdução ao Blazor no ASP.NET Core
author: guardrex
description: Explore o Blazor no ASP.NET Core, uma maneira de criar a IU da Web interativa do lado do cliente com o .NET em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/18/2019
uid: blazor/index
ms.openlocfilehash: 74eeb003c249fc9ff8267ac855455f875806ccd9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982991"
---
# <a name="introduction-to-blazor"></a>Introdução ao Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Bem-vindo ao Blazor!

Crie a interface do usuário da Web interativa do lado do cliente com o .NET:

* Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.
* Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada com o .NET.
* Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.

O Blazor é compatível com cenários centrais exigidos pela maioria dos aplicativos:

* Parâmetros
* Manipulação de eventos
* Associação de dados
* Roteamento
* Injeção de dependência
* Layouts
* Modelos
* Valores em cascata

## <a name="components"></a>Componentes

Um *componente* no Blazor é um elemento da interface do usuário, como uma página, uma caixa de diálogo ou um formulário de entrada de dados. Os componentes manuseiam eventos de usuário e definem a lógica flexível de renderização de interface do usuário. Os componentes podem ser aninhados e reutilizados.

Os componentes são classes do .NET compiladas em assemblies do .NET que podem ser compartilhados e distribuídos como pacotes NuGet. A classe do componente geralmente é gravada na forma de uma página de marcação do Razor com uma extensão de arquivo *.razor*. Componentes no Blazor às vezes são chamados de componentes do Razor.

O [Razor](xref:mvc/views/razor) é uma sintaxe para a combinação da marcação HTML com o código C#. O Razor foi projetado visando a produtividade do desenvolvedor, permitindo que ele mude entre a marcação e o C# no mesmo arquivo com o suporte do [IntelliSense](/visualstudio/ide/using-intellisense). As Razor Pages e as exibições MVC também usam o Razor. Ao contrário das Razor Pages e das exibições MVC, que são criadas ao redor de um modelo de solicitação/resposta, os componentes são usados especificamente para manusear a composição da interface do usuário. Os componentes Razor podem ser usados especificamente para composição e lógica de interface do usuário do lado do cliente.

A marcação a seguir é um exemplo de um componente de caixa de diálogo personalizada:

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

Quando esse componente é usado em outro lugar no aplicativo, o IntelliSense no [Visual Studio](https://visualstudio.microsoft.com/vs/) acelera o desenvolvimento com o preenchimento de sintaxe e de parâmetro.

Os componentes são renderizados em uma representação na memória do DOM do navegador chamada *árvore de renderização*, que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.

## <a name="blazor-server-side"></a>Blazor no lado do servidor

Os componentes Blazor desvinculam a lógica de renderização do componente da forma como as atualizações da interface do usuário são aplicadas. O Blazor no lado do servidor dá suporte à hospedagem de componentes Razor no servidor em um aplicativo ASP.NET Core. As atualizações da interface do usuário são tratadas por uma conexão SignalR.

O tempo de execução:

* Trata do envio de eventos de interface do usuário do navegador para o servidor.
* Aplica as atualizações da interface do usuário enviadas pelo servidor de volta para o navegador depois de executar os componentes.

A conexão usada pelo Blazor no lado do servidor para se comunicar com o navegador também é usada para manusear chamadas de interoperabilidade de JavaScript.

![O Blazor no lado do servidor executa o código do .NET no servidor e interage com o Modelo de Objeto do Documento no cliente por uma conexão SignalR](index/_static/blazor-server-side.png)

Para obter mais informações, consulte <xref:blazor/hosting-models#server-side>.

## <a name="blazor-client-side"></a>Blazor no lado do cliente

O Blazor no lado do cliente é uma estrutura de aplicativos de página única para a criação de aplicativos Web interativos do lado do cliente com o .NET. O Blazor no lado do cliente usa padrões abertos da Web sem plug-ins ou transpilação de código. O Blazor no lado do cliente funciona em todos os navegadores da Web modernos, incluindo os navegadores móveis.

Usar o .NET no navegador para desenvolvimento web do lado do cliente oferece muitas vantagens:

* **Linguagem C#**: escreva o código em C# em vez de JavaScript.
* **Ecossistema do .NET**: aproveite o ecossistema existente das bibliotecas do .NET.
* **Desenvolvimento de pilha completa**: compartilhe a lógica do lado do servidor e do cliente.
* **Velocidade e escalabilidade**: o .NET foi criado para desempenho, confiabilidade e segurança.
* **Ferramentas líderes do setor**: mantenha-se produtivo com o Visual Studio no Windows, Linux e macOS.
* **Estabilidade e consistência**:  Crie um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.

A execução do código do .NET em navegadores da Web é possibilitada por [WebAssembly](http://webassembly.org) (abreviado como *wasm*). O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins. O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.

O código do WebAssembly pode acessar a funcionalidade completa do navegador por meio da interoperabilidade do JavaScript. Ao mesmo tempo, o código .NET executado pelo WebAssembly é executado na mesma área restrita confiável que o JavaScript para impedir ações mal-intencionadas no computador cliente.

![O Blazor do lado do cliente executa o código do .NET no navegador com o WebAssembly.](index/_static/blazor-client-side.png)

Quando um aplicativo Blazor no lado do cliente é criado e executado em um navegador:

* os arquivos C# e os arquivos Razor são compilados em assemblies do .NET.
* os assemblies e o tempo de execução do .NET são baixados no navegador.
* O Blazor no lado do cliente inicializa o tempo de execução do .NET e o configura para carregar os assemblies no aplicativo. A manipulação do DOM (Modelo de Objeto do Documento) e as chamadas à API do navegador são realizadas pelo tempo de execução do Blazor no lado do cliente por meio da interoperabilidade do JavaScript.

Para reduzir o tamanho do aplicativo baixado, o código não utilizado é retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:host-and-deploy/blazor/configure-linker).

O lado do cliente Blazor é um modelo de hospedagem do lado do cliente. Como o Blazor desvincula uma lógica de renderização de componente da maneira de aplicar as atualizações da interface do usuário, há flexibilidade na maneira de hospedar o Blazor. Use o [Blazor no lado do servidor](#blazor-server-side) para hospedar o Blazor no servidor em um aplicativo ASP.NET Core, em que as atualizações da interface do usuário são realizadas por uma conexão SignalR. Para obter mais informações, consulte <xref:blazor/hosting-models#server-side>. 

O tamanho do conteúdo é um fator de desempenho crítico para a usabilidade do aplicativo. O Blazor no lado do cliente otimiza o tamanho do conteúdo para reduzir o tempo de download:

* As partes não usadas de assemblies do .NET são removidas durante o processo de compilação.
* As respostas HTTP são compactadas.
* O tempo de execução do .NET e os assemblies são armazenados em cache no navegador.

O [Blazor do lado do servidor](#blazor-server-side) fornece um tamanho de conteúdo menor do que o Blazor no lado do cliente, mantendo os assemblies do .NET, o assembly do aplicativo e o lado do servidor do tempo de execução. Os aplicativos do Blazor no lado do servidor só oferecem arquivos de marcação e ativos estáticos para os clientes.

## <a name="javascript-interop"></a>Interoperabilidade do JavaScript

Para aplicativos que exigem bibliotecas JavaScript e APIs do navegador de terceiros, os componentes interoperam com o JavaScript. Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar. O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#. Para obter mais informações, confira [Interoperabilidade do JavaScript](xref:blazor/javascript-interop).

## <a name="code-sharing-and-net-standard"></a>Compartilhamento de código e o .NET Standard

Os aplicativos podem referenciar e usar as bibliotecas [.NET Standard](/dotnet/standard/net-standard) existentes. O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET. O .Blazor implementa o .NET Standard 2.0. As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete, threading e outros recursos) geram a <xref:System.PlatformNotSupportedException>. As bibliotecas de classe do .NET Standard podem ser compartilhadas entre diferentes plataformas do .NET, como Blazor, .NET Framework, .NET Core, Xamarin, Mono e Unity.

## <a name="additional-resources"></a>Recursos adicionais

* [WebAssembly](http://webassembly.org/)
* [Guia do C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
