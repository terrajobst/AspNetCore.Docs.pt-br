---
title: Introdução ao Blazor
author: guardrex
description: 'Explore o ASP.NET Core Blazor, uma nova maneira de criar aplicativos interativos do lado do cliente com o .NET que são executados no navegador com WebAssembly.'
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
uid: spa/blazor/index
---
# <a name="introduction-to-blazor"></a>Introdução ao Blazor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

O Blazor é uma estrutura de aplicativos de página única para a criação de aplicativos Web interativos do lado do cliente com o .NET. O Blazor usa padrões abertos da Web sem plug-ins ou transpilação de código. O Blazor funciona em todos os navegadores da Web modernos, incluindo os navegadores móveis.

Usar o .NET no navegador para desenvolvimento web do lado do cliente oferece muitas vantagens:

* **Linguagem C#**: escreva o código em C# em vez de JavaScript.
* **Ecossistema do .NET**: aproveite o ecossistema existente das bibliotecas do .NET.
* **Desenvolvimento de pilha completa**: compartilhe a lógica do lado do servidor e do cliente.
* **Velocidade e escalabilidade**: o .NET foi criado para desempenho, confiabilidade e segurança.
* **Ferramentas líderes do setor**: mantenha-se produtivo com o Visual Studio no Windows, Linux e macOS.
* **Estabilidade e consistência**:  Desenvolva um conjunto comum de linguagens, estruturas e ferramentas que são estáveis, com recursos avançados e fáceis de usar.

A execução do código do .NET em navegadores da Web é possibilitada por [WebAssembly](http://webassembly.org) (abreviado como *wasm*). O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins. O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.

O código do WebAssembly pode acessar a funcionalidade completa do navegador por meio da interoperabilidade do JavaScript. Ao mesmo tempo, o código .NET executado pelo WebAssembly é executado na mesma área restrita confiável que o JavaScript para impedir ações mal-intencionadas no computador cliente.

![O Blazor executa o código do .NET no navegador com o WebAssembly.](index/_static/blazor.png)

Quando um aplicativo Blazor é criado e executado em um navegador:

* os arquivos C# e os arquivos Razor são compilados em assemblies do .NET.
* os assemblies e o tempo de execução do .NET são baixados no navegador.
* O Blazor inicializa o tempo de execução do .NET e o configura para carregar os assemblies no aplicativo. A manipulação do DOM (Modelo de Objeto do Documento) e as chamadas à API do navegador são realizadas pelo tempo de execução do Blazor por meio da interoperabilidade do JavaScript.

O Blazor é compatível com instalações centrais exigidas pela maioria dos aplicativos, incluindo:

* Parâmetros
* Manipulação de eventos
* Associação de dados
* Roteamento
* Injeção de dependência
* Layouts
* Modelagem
* Valores em cascata

Para reduzir o tamanho do código não utilizado do aplicativo baixado, retirado do aplicativo quando publicado pelo [Vinculador de linguagem intermediária (IL)](xref:host-and-deploy/razor-components/configure-linker).

O Blazor é o modelo de hospedagem do lado do cliente para Componentes Razor. Como os Componentes Razor desvinculam a lógica de renderização de um componente da maneira de aplicar as atualizações da interface do usuário, há flexibilidade na maneira de hospedar os Componentes Razor. Use os Componentes Razor do ASP.NET Core para a hospedagem dos Componentes Razor no servidor, em um aplicativo ASP.NET Core, em que as atualizações da interface do usuário são realizadas por uma conexão SignalR. Para obter mais informações, consulte <xref:razor-components/hosting-models#server-side-hosting-model>. 

## <a name="components"></a>Componentes

Um *Componente Razor* é uma parte da interface do usuário, como uma página, uma caixa de diálogo ou um formulário de entrada de dados. Os componentes manuseiam eventos de usuário e definem a lógica flexível de renderização de interface do usuário. Os componentes podem ser aninhados e reutilizados.

Os componentes são classes do .NET compiladas em assemblies do .NET que podem ser compartilhados e distribuídos como pacotes NuGet. A classe pode ser escrita na forma de uma página de marcação do Razor (*.cshtml*) ou como uma classe C# (*.cs*).

O [Razor](xref:mvc/views/razor) é uma sintaxe para a combinação da marcação HTML com o código C#. O Razor foi projetado visando a produtividade do desenvolvedor, permitindo que ele mude entre a marcação e o C# no mesmo arquivo com o suporte do [IntelliSense](/visualstudio/ide/using-intellisense). As Razor Pages e as exibições MVC também usam o Razor. Ao contrário das Razor Pages e das exibições MVC, que são criadas ao redor de um modelo de solicitação/resposta, os componentes são usados especificamente para manusear a composição da interface do usuário. Os Componentes Razor podem ser usados especificamente para composição e lógica de interface do usuário do lado do cliente.

A marcação a seguir é um exemplo de um componente de caixa de diálogo personalizada em um arquivo Razor (*DialogComponent.cshtml*):

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick=@OnOK>OK</button>
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

Para aplicativos que exigem bibliotecas JavaScript e APIs do navegador de terceiros, o Blazor interopera com o JavaScript. Os componentes são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar. O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#. Para obter mais informações, confira [Interoperabilidade do JavaScript](xref:razor-components/javascript-interop).

## <a name="code-sharing-and-net-standard"></a>Compartilhamento de código e o .NET Standard

Os aplicativos podem referenciar e usar as bibliotecas [.NET Standard](/dotnet/standard/net-standard) existentes. O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET. Há suporte para o .NET standard 2.0 ou superior. As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete, threading e outros recursos) geram a <xref:System.PlatformNotSupportedException>. As bibliotecas de classes do .NET Standard podem ser compartilhadas entre o código do servidor e em aplicativos baseados em navegador.

## <a name="optimization"></a>Otimização

O tamanho do conteúdo é um fator de desempenho crítico para a usabilidade do aplicativo. O Blazor otimiza o tamanho do conteúdo para reduzir os tempos de download:

* As partes não usadas de assemblies do .NET são removidas durante o processo de compilação.
* As respostas HTTP são compactadas.
* O tempo de execução do .NET e os assemblies são armazenados em cache no navegador.

Os [Componentes Razor do lado servidor](xref:razor-components/index) fornecem um tamanho de conteúdo ainda menor do que o Blazor mantendo os assemblies do .NET, o assembly do aplicativo e o lado do servidor do tempo de execução. Os aplicativos de Componentes Razor só oferecem arquivos de marcação e ativos estáticos para os clientes.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-components/index>
* [WebAssembly](http://webassembly.org/)
* [Guia do C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
