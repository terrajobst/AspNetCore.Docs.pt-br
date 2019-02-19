---
title: Novidades do ASP.NET Core 2.1
author: isaac2004
description: Conheça os novos recursos no ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: aspnetcore-2.1
ms.openlocfilehash: 8299af819f86d3d2371650ce3d87deb817f0feb8
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248167"
---
# <a name="whats-new-in-aspnet-core-21"></a>Novidades do ASP.NET Core 2.1

Este artigo destaca as alterações mais significativas no ASP.NET Core 2.1, com links para a documentação relevante.

## <a name="signalr"></a>SignalR

O SignalR foi reescrito para ASP.NET Core 2.1. O SignalR do ASP.NET Core inclui uma série de melhorias:

* Um modelo de expansão simplificado.
* Um novo cliente JavaScript sem dependência jQuery.
* Um novo protocolo binário compacto com base em MessagePack.
* Suporte para protocolos personalizados.
* Um novo modelo de resposta de transmissão.
* Suporte para clientes com base em WebSockets básicos.

Para obter mais informações, veja [ASP.NET Core SignalR](xref:signalr/index).

## <a name="razor-class-libraries"></a>Biblioteca de classes Razor

O ASP.NET Core 2.1 torna mais fácil criar e incluir interface do usuário baseada em Razor em uma biblioteca e compartilhá-la em vários projetos. O novo SDK do Razor habilita o build de arquivos do Razor em um projeto de biblioteca de classes que podem ser empacotados em um pacote do NuGet. Exibições e páginas em bibliotecas são descobertas automaticamente e podem ser substituídas pelo aplicativo. Ao integrar a compilação do Razor ao build:

* O tempo de inicialização do aplicativo é significativamente mais rápido.
* Atualizações rápidas a modos de exibição e páginas Razor em tempo de execução ainda estão disponíveis como parte de um fluxo de trabalho de desenvolvimento iterativo.

Para obter mais informações, veja [Criar interface do usuário reutilizável usando o projeto de Biblioteca de Classes do Razor](xref:razor-pages/ui-class).

## <a name="identity-ui-library--scaffolding"></a>Scaffolding e biblioteca de interface do usuário de Identidade

O ASP.NET Core 2.1 fornece a [Identidade do ASP.NET Core](xref:security/authentication/identity) como uma [Biblioteca de Classes do Razor](xref:razor-pages/ui-class). Aplicativos que incluem Identidade podem aplicar o novo scaffolder de Identidade para adicionar seletivamente o código-fonte contido na RCL (Biblioteca de Classes do Razor) de Identidade. Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento. Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro. Código gerado tem precedência sobre o mesmo código na RCL de Identidade.

Aplicativos que **não** incluem autenticação podem aplicar o scaffolder de Identidade para adicionar o pacote de Identidade RCL. Você tem a opção de selecionar o código de Identidade a ser gerado.

Para obter mais informações, veja [Identidade scaffold em projetos ASP.NET Core](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

Com o foco cada vez maior em segurança e privacidade, é importante habilitar HTTPS para aplicativos Web. A imposição de HTTPS está se tornando cada vez mais rígida na Web. Sites que não usam HTTPS são considerados inseguros. Navegadores (Chrome, Mozilla) estão começando a impor o uso de recursos Web em um contexto de seguro. O [RGPD](xref:security/gdpr) requer o uso de HTTPS para proteger a privacidade do usuário. Quando usar HTTPS em produção for fundamental, usar HTTPS no desenvolvimento pode ajudar a evitar problemas de implantação (por exemplo, links inseguros). O ASP.NET Core 2.1 inclui uma série de melhorias que tornam mais fácil usar HTTPS em desenvolvimento e configurar HTTPS em produção. Para obter mais informações, veja [Impor HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Ativo por padrão

Para facilitar o desenvolvimento de site seguro, HTTPS está habilitado por padrão. Da versão 2.1 em diante, o Kestrel escuta em `https://localhost:5001` quando um certificado de desenvolvimento local está presente. Um certificado de desenvolvimento é criado:

* Como parte da experiência de primeira execução do SDK do .NET Core, quando você usa o SDK pela primeira vez.
* Manualmente usando a nova ferramenta `dev-certs`.

Execute `dotnet dev-certs https --trust` para confiar no certificado.

### <a name="https-redirection-and-enforcement"></a>Redirecionamento e imposição de HTTPS

Aplicativos Web geralmente precisam escutar em HTTP e HTTPS, mas, então redirecionam todo o tráfego HTTP para HTTPS. Na versão 2.1, foi introduzido o middleware de redirecionamento de HTTPS especializado que redireciona de modo inteligente com base na presença de portas do servidor associado ou de configuração.

O uso de HTTPS pode ser imposto ainda mais empregando [HSTS (Protocolo de Segurança do Transporte Estrita HTTP)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts). HSTS instrui navegadores a sempre acessarem o site por meio de HTTPS. O ASP.NET Core 2.1 adiciona middleware HSTS compatível com opções para idade máxima, subdomínios e a lista de pré-carregamento de HSTS.

### <a name="configuration-for-production"></a>Configuração para produção

Em produção, HTTPS precisa ser configurado explicitamente. Na versão 2.1, foi adicionado o esquema de configuração padrão para configurar HTTPS para Kestrel. Os aplicativos podem ser configurados para usar:

* Vários pontos de extremidade incluindo as URLs. Para saber mais, confira [Implementação do servidor Web Kestrel: configuração do ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration).
* O certificado a ser usado para HTTPS de um arquivo no disco ou de um repositório de certificados.

## <a name="gdpr"></a>RGPD

O ASP.NET Core fornece APIs e modelos para ajudar a atender alguns dos requisitos do [RGPD (Regulamento de Proteção de Dados Geral) da UE](https://www.eugdpr.org/). Para obter mais informações, veja [Suporte RGPD no ASP.NET Core](xref:security/gdpr). Um [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample) mostra como usar e permite que você teste a maioria dos pontos de extensão RGPD e APIs adicionados aos modelos do ASP.NET Core 2.1.

## <a name="integration-tests"></a>Testes de integração

É introduzido um novo pacote que simplifica a criação e a execução do teste. O pacote [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) lida com as seguintes tarefas:

* Copia o arquivo de dependência (*\*.deps*) do aplicativo testado para a pasta *bin* do projeto de teste.
* Define a raiz de conteúdo para a raiz do projeto do aplicativo testado para que arquivos estáticos e páginas/exibições sejam encontrados quando os testes forem executados.
* Fornece a classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar a inicialização do aplicativo testado com [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).

O teste a seguir usa [xUnit](https://xunit.github.io/) para verificar se a página de índice é carregada com um código de status de êxito e o cabeçalho Content-Type correto:

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

Para obter mais informações, veja o tópico [Testes de integração](xref:test/integration-tests).

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

O ASP.NET Core 2.1 adiciona novas convenções de programação que facilitam o build de APIs Web limpas e descritivas. `ActionResult<T>` é um novo tipo adicionado para permitir que um aplicativo retorne um tipo de resposta ou qualquer outro resultado da ação (semelhante a IActionResult), enquanto ainda indica o tipo de resposta. O atributo `[ApiController]` também foi adicionado como a maneira de aceitar convenções e comportamentos específicos da API Web.

Para obter mais informações, veja [Criar APIs Web com ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IHttpClientFactory

O ASP.NET Core 2.1 inclui um novo serviço `IHttpClientFactory` que torna mais fácil configurar e consumir instâncias de `HttpClient` em aplicativos. O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados uns aos outros para solicitações HTTP de saída. O alocador:

* Torna o registro de instâncias de `HttpClient` por cliente nomeado mais intuitivo.
* Implementa um manipulador Polly que permite que políticas Polly sejam usadas para Retry, CircuitBreakers etc.

Para obter mais informações, veja [Iniciar solicitações de HTTP](xref:fundamentals/http-requests).

## <a name="kestrel-transport-configuration"></a>Configuração de transporte do Kestrel

Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados. Para saber mais, confira [Implementação do servidor Web Kestrel: configuração de transporte](xref:fundamentals/servers/kestrel#transport-configuration).

## <a name="generic-host-builder"></a>Construtor de host genérico

O Construtor de Host Genérico (`HostBuilder`) foi introduzido. Este construtor pode ser usado para aplicativos que não processam solicitações HTTP (mensagens, tarefas em segundo plano etc.).

Para obter mais informações, veja [Host Genérico do .NET](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Modelos do SPA atualizados

Os modelos de Aplicativo de Página Única para Angular, React e React com Redux são atualizados para usar estruturas de projeto padrão e criar sistemas para cada estrutura.

O modelo Angular se baseia na CLI Angular e os modelos React baseiam-se em create-react-app.

Para obter mais informações, consulte:

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>Pesquisa de Razor Pages para ativos Razor

Na versão 2.1, as Razor Pages pesquisam ativos do Razor (como layouts e parciais) nos seguintes diretórios na ordem listada:

1. Pasta de Páginas atual.
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="razor-pages-in-an-area"></a>Razor Pages em uma área

Razor Pages agora são compatíveis com [áreas](xref:mvc/controllers/areas). Para ver um exemplo de áreas, crie um novo aplicativo Web Razor Pages com contas de usuário individuais. Um aplicativo Web Razor Pages com contas de usuário individuais inclui */Areas/Identity/Pages*.

## <a name="mvc-compatibility-version"></a>Versão de compatibilidade do MVC

O método <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> permite que um aplicativo aceite ou recuse as possíveis alterações da falha de comportamento introduzidas no ASP.NET Core MVC 2.1 ou posteriores.

Para obter mais informações, consulte <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Migrar de 2.0 para 2.1

Veja [Migrar do ASP.NET Core 2.0 para 2.1](xref:migration/20_21).

## <a name="additional-information"></a>Informações adicionais

Para obter uma lista de alterações, vejas as [Notas de versão do ASP.NET Core 2.1](https://github.com/aspnet/Home/releases/tag/2.1.0).
