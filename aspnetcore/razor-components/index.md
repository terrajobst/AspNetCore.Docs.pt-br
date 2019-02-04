---
title: Introdução aos Componentes Razor
author: guardrex
description: Explore o Blazor, uma estrutura da Web do .NET que usa C#/Razor e HTML executados no navegador com o WebAssembly.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/index
ms.openlocfilehash: 0f7a4b2ec404b6ceec2e643fea9e0635de6ad716
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667903"
---
# <a name="introduction-to-razor-components"></a>Introdução aos Componentes Razor

Por [Steve Sanderson](http://blog.stevensanderson.com), [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Os *Componentes Razor do ASP.NET Core* executam o lado do servidor no ASP.NET Core, enquanto o *Blazor* (Componentes Razor que executam o lado do cliente) é uma estrutura da Web experimental do .NET que usa C#/Razor e HTML executados no navegador com o WebAssembly. O Blazor fornece todos os benefícios de uma estrutura de interface do usuário da Web do lado do cliente usando o .NET no cliente.

O desenvolvimento para a Web foi aprimorado de diversas maneiras ao longo dos anos, mas a criação de aplicativos Web modernos ainda apresenta desafios. O uso do .NET no navegador oferece muitas vantagens que podem ajudar a tornar o desenvolvimento para Web mais fácil e mais produtivo:

* **Estabilidade e consistência**: o .NET fornece estruturas de programação padronizadas entre plataformas, que são estáveis, completas e fáceis de usar.
* **Linguagens modernas inovadoras**: as linguagens do .NET estão melhorando constantemente com novos recursos de linguagem inovadores.
* **Ferramentas líderes do setor**: a família de produtos do Visual Studio fornece uma experiência fantástica de desenvolvimento no .NET entre plataformas, em Windows, Linux e macOS.
* **Velocidade e escalabilidade**: o .NET tem um forte histórico de desempenho, confiabilidade e segurança para o desenvolvimento de aplicativos. O uso do .NET como uma solução de pilha completa facilita a criação de aplicativos rápidos, confiáveis e seguros.
* **Desenvolvimento de pilha completa que aproveita as habilidades existentes**: os desenvolvedores C#/Razor usam as habilidades em C#/Razor que eles já têm para escrever o código do lado do cliente e compartilhar a lógica do lado do servidor e do lado do cliente entre os aplicativos.
* **Amplo suporte a navegadores**: os Componentes Razor renderizam a interface do usuário como uma marcação e um JavaScript comuns. O Blazor é executado no .NET, no navegador, usando padrões abertos da Web sem nenhum plug-in e nenhuma transpilação de código. O Blazor funciona em todos os navegadores da Web modernos, incluindo os navegadores móveis.

## <a name="hosting-models"></a>Modelos de hospedagem

### <a name="server-side-hosting-model"></a>Modelo de hospedagem do lado do servidor

Como os Componentes Razor desvinculam a lógica de renderização de um componente da maneira de aplicar as atualizações da interface do usuário, há flexibilidade na maneira de hospedar os Componentes Razor. Os Componentes Razor do ASP.NET Core no .NET Core 3.0 adicionam suporte para a hospedagem dos Componentes Razor no servidor, em um aplicativo ASP.NET Core, em que todas as atualizações da interface do usuário são realizadas por uma conexão SignalR. O tempo de execução realiza o envio de eventos da interface do usuário do navegador para o servidor e, depois de executar os componentes, aplica as atualizações na interface do usuário retornadas pelo servidor ao navegador. A mesma conexão também é usada para realizar as chamadas de interoperabilidade do JavaScript.

![Os Componentes Razor executam o código do .NET no servidor e interagem com o Modelo de Objeto do Documento no cliente por uma conexão SignalR](index/_static/aspnet-core-razor-components.png)

Para obter mais informações, consulte <xref:razor-components/hosting-models#server-side-hosting-model>.

### <a name="client-side-hosting-model"></a>Modelo de hospedagem do lado do cliente

A execução do código do .NET em navegadores da Web é possibilitada por uma tecnologia relativamente nova, o [WebAssembly](http://webassembly.org) (abreviado como *wasm*). O WebAssembly é um padrão aberto da Web compatível com navegadores da Web sem plug-ins. O WebAssembly é um formato de código de bytes compacto, otimizado para download rápido e máxima velocidade de execução.

O código do WebAssembly pode acessar a funcionalidade completa do navegador por meio da interoperabilidade do JavaScript. Ao mesmo tempo, o código do WebAssembly é executado na mesma área restrita confiável que o JavaScript para impedir ações mal-intencionadas no computador cliente.

![O Blazor executa o código do .NET no navegador com o WebAssembly.](index/_static/blazor.png)

Quando um aplicativo Blazor é criado e executado em um navegador:

1. os arquivos C# e os arquivos Razor são compilados em assemblies do .NET.
1. os assemblies e o tempo de execução do .NET são baixados no navegador.
1. o Blazor usa o JavaScript para inicializar o tempo de execução do .NET e configura o tempo de execução para carregar as referências de assembly necessárias. A manipulação do DOM (Modelo de Objeto do Documento) e as chamadas à API do navegador são realizadas pelo tempo de execução do Blazor por meio da interoperabilidade do JavaScript.

Para dar suporte a navegadores mais antigos que não são compatíveis com o WebAssembly, você pode usar o [modelo de hospedagem do lado do servidor](#server-side-hosting-model) dos Componentes Razor do ASP.NET Core.

Para obter mais informações, consulte <xref:razor-components/hosting-models#client-side-hosting-model>.

## <a name="components"></a>Componentes

Os aplicativos são criados com *componentes*. Um componente é uma parte da interface do usuário, como uma página, uma caixa de diálogo ou um formulário de entrada de dados. Os componentes podem ser aninhados, reutilizados e compartilhados entre os projetos.

Um *componente* é uma classe do .NET. A classe também pode ser escrita diretamente, como uma classe C# (*\*.cs*) ou, com mais frequência, na forma de uma página de marcação Razor (*\*.cshtml*).

O [Razor](/aspnet/core/mvc/views/razor) é uma sintaxe para a combinação da marcação HTML com o código C#. O Razor foi projetado visando a produtividade do desenvolvedor, permitindo que ele mude entre a marcação e o C# no mesmo arquivo com o suporte do [IntelliSense](/visualstudio/ide/using-intellisense). A marcação a seguir é um exemplo de um componente básico de caixa de diálogo personalizada em um arquivo Razor (*DialogComponent.cshtml*):

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

Os componentes podem:

* ser aninhados.
* ser criados com o Razor (*\*.cshtml*) ou com o código C# (*\*.cs*).
* ser compartilhados por meio de bibliotecas de classes.
* receber um teste de unidade sem precisar de um navegador DOM.

## <a name="infrastructure"></a>Infraestrutura

Os Componentes Razor oferecem os principais recursos que a maioria dos aplicativos exige, como:

* Layouts
* Roteamento
* Injeção de dependência

Todos esses recursos são opcionais. Quando um desses recursos não é usado em um aplicativo, a implementação é eliminada do aplicativo quando ele é publicado pelo [Vinculador de IL (linguagem intermediária)](xref:host-and-deploy/razor-components/configure-linker).

## <a name="code-sharing-and-net-standard"></a>Compartilhamento de código e o .NET Standard

Os aplicativos podem referenciar e usar as bibliotecas [.NET Standard](/dotnet/standard/net-standard) existentes. O .NET Standard é uma especificação formal das APIs do .NET que são comuns entre as implementações do .NET. Há suporte para o .NET standard 2.0 ou superior. As APIs que não são aplicáveis em um navegador da Web (por exemplo, para acessar o sistema de arquivos, abrir um soquete, threading e outros recursos) geram a [PlatformNotSupportedException](/dotnet/api/system.platformnotsupportedexception). As bibliotecas de classes do .NET Standard podem ser compartilhadas entre o código do servidor e em aplicativos baseados em navegador.

## <a name="javascript-interop"></a>Interoperabilidade do JavaScript

Para aplicativos que exigem bibliotecas JavaScript e APIs do navegador de terceiros, o WebAssembly foi projetado para interoperar com o JavaScript. Os Componentes Razor são capazes de usar qualquer biblioteca ou API que o JavaScript possa usar. O código C# pode chamar o código JavaScript, e o código JavaScript pode chamar o código C#. Para obter mais informações, confira [Interoperabilidade do JavaScript](xref:razor-components/javascript-interop).

## <a name="optimization"></a>Otimização

Para aplicativos do lado do cliente, o tamanho do conteúdo é crítico. O Blazor otimiza o tamanho do conteúdo para reduzir os tempos de download. Por exemplo, as partes não usadas dos assemblies do .NET são removidas durante o processo de build, as respostas HTTP são compactadas e o tempo de execução do .NET e os assemblies são armazenados em cache no navegador.

Os Componentes Razor fornecem um tamanho de conteúdo ainda menor do que o Blazor mantendo os assemblies do .NET, o assembly do aplicativo e o lado do servidor do tempo de execução. Os aplicativos dos Componentes Razor fornecem somente marcação, scripts e folhas de estilos aos clientes.

## <a name="deployment"></a>Implantação

Use o Blazor para criar um aplicativo do lado do cliente autônomo puro ou um aplicativo do ASP.NET Core de pilha completa que contenha os aplicativos cliente e os aplicativos para servidor:

* Em um **aplicativo autônomo do lado do cliente**, o aplicativo Blazor é compilado em uma pasta *dist* que contém apenas arquivos estáticos. Os arquivos podem ser hospedados no Serviço de Aplicativo do Azure, nas páginas do GitHub, no IIS (configurado como um servidor de arquivos estático), nos servidores do Node.js e em vários outros servidores e serviços. O .NET não é necessário no servidor de produção.
* Em um **aplicativo do ASP.NET Core de pilha completa**, o código pode ser compartilhado entre os aplicativos cliente e os aplicativos para servidor. O aplicativo dos Componentes Razor do ASP.NET Core resultante, que atende à interface do usuário do lado do cliente e a outros pontos de extremidade de API do lado do servidor, pode ser criado e implantado em qualquer host de nuvem ou local compatível com o ASP.NET Core.

## <a name="suggest-a-feature-or-file-a-bug-report"></a>Sugerir um recurso ou enviar um relatório de bug

Para fazer sugestões e enviar relatórios de bug, [abra um problema](https://github.com/aspnet/AspNetCore/issues/new). Para obter ajuda geral e respostas da comunidade, participe da conversa no [Gitter](https://gitter.im/aspnet/Blazor).

## <a name="additional-resources"></a>Recursos adicionais

* [WebAssembly](http://webassembly.org/)
* [Guia do C#](/dotnet/csharp/)
* [Razor](/aspnet/core/mvc/views/razor)
* [HTML](https://www.w3.org/html/)
