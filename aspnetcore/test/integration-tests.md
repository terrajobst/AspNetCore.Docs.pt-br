---
title: Testes de integração no ASP.NET Core
author: guardrex
description: Saiba como testes de integração garantem que os componentes do aplicativo funcionem corretamente no nível de infraestrutura, incluindo o banco de dados, o sistema de arquivos e a rede.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: test/integration-tests
ms.openlocfilehash: 053713e148df70b0be6bb567b55b2381a78d6c3e
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833573"
---
# <a name="integration-tests-in-aspnet-core"></a>Testes de integração no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com/)

Testes de integração Certifique-se de que os componentes do aplicativo funcionam corretamente em um nível que inclui a infraestrutura de suporte do aplicativo, como o banco de dados, o sistema de arquivos e a rede. ASP.NET Core dá suporte a testes de integração usando uma estrutura de teste de unidade com um host de teste da web e um servidor de teste na memória.

Este tópico pressupõe uma compreensão básica dos testes de unidade. Se estiver familiarizado com os conceitos de teste, consulte o [testes de unidade no .NET Core e .NET Standard](/dotnet/core/testing/) tópico e seu conteúdo vinculado.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo é um aplicativo páginas Razor e pressupõe uma compreensão básica de páginas do Razor. Se estiver familiarizado com as páginas Razor, consulte os tópicos a seguir:

* [Introdução a Páginas do Razor](xref:razor-pages/index)
* [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Testes de unidades de páginas Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Para SPAs de teste, é recomendável uma ferramenta, como [Selenium](https://www.seleniumhq.org/), que pode automatizar um navegador.

## <a name="introduction-to-integration-tests"></a>Introdução aos testes de integração

Componentes de um aplicativo em um nível mais amplo do que avaliar a testes de integração [testes de unidade](/dotnet/core/testing/). Testes de unidade são usados para testar os componentes de software isolado, tais como métodos de classe individual. Testes de integração confirme que dois ou mais componentes de aplicativo funcionam em conjunto para produzir um resultado esperado, possivelmente incluindo todos os componentes necessários para processar totalmente uma solicitação.

Esses testes mais amplos são usados para testar a infraestrutura do aplicativo e o framework inteiro, muitas vezes, incluindo os seguintes componentes:

* Banco de Dados
* Sistema de arquivos
* Dispositivos de rede
* Pipeline de solicitação-resposta

Componentes de uso gerado, conhecidos como testes de unidade *fakes* ou *objetos fictícios*, no lugar de componentes de infraestrutura.

Em contraste com testes de unidade, testes de integração:

* Use os componentes reais usados pelo aplicativo em produção.
* Exigem mais código e processamento de dados.
* Pode levar mais tempo.

Portanto, limite o uso de testes de integração para os cenários mais importantes de infraestrutura. Se um comportamento pode ser testado usando um teste de unidade ou um teste de integração, escolha o teste de unidade.

> [!TIP]
> Não escreva testes de integração para cada permutação possíveis de acesso de dados e arquivos com sistemas de arquivos e bancos de dados. Independentemente de quantos coloca em um aplicativo de interagir com bancos de dados e sistemas de arquivos, um conjunto com foco de leitura, gravação, atualização e exclusão integração testes são geralmente capazes de banco de dados de teste adequadamente e componentes do sistema de arquivos. Testes de unidade de uso para testes de rotina da lógica do método que interagem com esses componentes. Em testes de unidade, o uso da infra-estrutura de falsificações/simulações resultado na execução de teste mais rápido.

> [!NOTE]
> Em discussões de testes de integração, o projeto testado com frequência é chamado de *sistema em teste*, ou "SUT" de forma abreviada.

## <a name="aspnet-core-integration-tests"></a>Testes de integração do ASP.NET Core

Testes de integração no ASP.NET Core requerem o seguinte:

* Um projeto de teste é usado para conter e executar os testes. O projeto de teste tem uma referência ao projeto ASP.NET Core testada, chamado de *sistema em teste* (SUT). _"SUT" é usado ao longo deste tópico para referir-se para o aplicativo testado._
* O projeto de teste cria um host da web de teste do SUT e usa um cliente do servidor de teste para lidar com solicitações e respostas para o SUT.
* Um executor de teste é usado para executar os testes e o relatório de resultados de teste.

Testes de integração seguem uma sequência de eventos que incluem o usual *organizar*, *Act*, e *Assert* etapas de teste:

1. Host de web do SUT está configurado.
1. Um cliente do servidor de teste é criado para enviar solicitações para o aplicativo.
1. O *organizar* etapa de teste é executada: O aplicativo de teste prepara uma solicitação.
1. O *Act* etapa de teste é executada: O cliente envia a solicitação e recebe a resposta.
1. O *Assert* etapa de teste é executada: O *reais* resposta é validada como um *passar* ou *falhar* com base em um *esperado* resposta.
1. O processo continua até que todos os testes são executados.
1. Os resultados do teste são relatados.

Normalmente, o host de teste da web está configurado diferentemente do host do aplicativo web normal para o teste é executado. Por exemplo, configurações de aplicativo diferente ou outro banco de dados podem ser usadas para os testes.

Componentes de infraestrutura, como o host do teste da web e o servidor de teste na memória ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), são fornecidas ou gerenciados pelo [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) pacote. O uso desse pacote simplifica a criação de teste e execução.

O `Microsoft.AspNetCore.Mvc.Testing` pacote lida com as seguintes tarefas:

* Copia o arquivo de dependências (*\*.deps*) do SUT para o projeto de teste *bin* pasta.
* Define a raiz do conteúdo raiz do projeto do SUT para que os arquivos estáticos e páginas/exibições são encontradas quando os testes são executados.
* Fornece o [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) classe para simplificar a inicialização com o SUT `TestServer`.

O [testes de unidade](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentação descreve como configurar um projeto e teste de executor de teste, junto com instruções detalhadas sobre como executar testes e recomendações sobre como a testes de nome e classes de teste.

> [!NOTE]
> Ao criar um projeto de teste para um aplicativo, separe os testes de unidade dos testes de integração em projetos diferentes. Isso ajuda a garantir que o teste componentes da infraestrutura não acidentalmente incluídos nos testes de unidade. Também permite a separação dos testes de unidade e integração de controle sobre qual conjunto de testes são executados.

Não há praticamente nenhuma diferença entre a configuração para testes de aplicativos de páginas do Razor e aplicativos MVC. A única diferença está em como os testes são nomeados. Em um aplicativo de páginas do Razor, os testes de pontos de extremidade de página geralmente são nomeadas depois a classe de modelo de página (por exemplo, `IndexPageTests` para testar a integração do componente para a página de índice). Em um aplicativo MVC, testes são geralmente organizadas por classes de controlador e os controladores de testam por eles em homenagem (por exemplo, `HomeControllerTests` para testar a integração do componente para o controlador Home).

## <a name="test-app-prerequisites"></a>Pré-requisitos de aplicativo de teste

O projeto de teste deve:

* Referenciar os seguintes pacotes:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Especifique o SDK para Web no arquivo de projeto (`<Project Sdk="Microsoft.NET.Sdk.Web">`). O SDK para Web é necessário ao fazer referência a [metapacote Microsoft](xref:fundamentals/metapackage-app).

Esses pré-requisitos podem ser vistos na [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples/). Inspecione o *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* arquivo. O aplicativo de exemplo usa o [xUnit](https://xunit.github.io/) estrutura de teste e o [AngleSharp](https://anglesharp.github.io/) biblioteca do analisador, portanto, o aplicativo de exemplo também referencia:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Ambiente do SUT

Se o SUT [ambiente](xref:fundamentals/environments) não for definido, os padrões de ambiente para desenvolvimento.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Testes básicos com o padrão WebApplicationFactory

[WebApplicationFactory&lt;TEntryPoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) é usado para criar um [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) para os testes de integração. `TEntryPoint` é a classe de ponto de entrada do SUT, geralmente o `Startup` classe.

Teste as classes implementam uma *acessório de classe* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) para indicar a classe contém testes e fornece instâncias de objeto compartilhado entre os testes na classe.

### <a name="basic-test-of-app-endpoints"></a>Teste básico de pontos de extremidade do aplicativo

O seguinte teste de classe, `BasicTests`, usa o `WebApplicationFactory` para inicializar o SUT e fornecer uma [HttpClient](/dotnet/api/system.net.http.httpclient) para um método de teste, `Get_EndpointsReturnSuccessAndCorrectContentType`. O método verifica se o código de status de resposta for bem-sucedida (códigos de status no intervalo 200-299) e o `Content-Type` cabeçalho é `text/html; charset=utf-8` para várias páginas de aplicativo.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) cria uma instância de `HttpClient` que segue redirecionamentos e manipula cookies automaticamente.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

### <a name="test-a-secure-endpoint"></a>Testar um ponto de extremidade seguro

Outro teste no `BasicTests` classe verifica que um ponto de extremidade seguro redireciona um usuário não autenticado à página de logon do aplicativo.

No SUT, o `/SecurePage` página usa um [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convenção para aplicar um [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) para a página. Para obter mais informações, consulte [convenções de autorização de páginas do Razor](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

No `Get_SecurePageRequiresAnAuthenticatedUser` testar, uma [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) está definido para não permitir redirecionamentos definindo [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) para `false`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

Não permitindo que o cliente para seguir o redirecionamento, podem ser feitas as seguintes verificações:

* O código de status retornado pelo SUT pode ser verificado em relação a esperada [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) resultado, não o código de status final após o redirecionamento para a página de logon, o que seria [Httpstatuscode](/dotnet/api/system.net.httpstatuscode).
* O `Location` valor de cabeçalho nos cabeçalhos de resposta é verificado para confirmar que ela começa com `http://localhost/Identity/Account/Login`, não a logon página resposta final, onde o `Location` cabeçalho não está presente.

Para obter mais informações sobre `WebApplicationFactoryClientOptions`, consulte o [opções de cliente](#client-options) seção.

## <a name="customize-webapplicationfactory"></a>Personalizar WebApplicationFactory

Configuração do host da Web pode ser criada independentemente das classes de teste herdando de `WebApplicationFactory` para criar um ou mais fábricas personalizadas:

1. Herdar `WebApplicationFactory` e substituir [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). O [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite a configuração da coleção com o serviço [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   A propagação do banco de dados de [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) é executada pelo `InitializeDbForTests` método. O método é descrito o [exemplo para testes de integração: Organização do aplicativo de teste](#test-app-organization) seção.

2. Usar o custom `CustomWebApplicationFactory` em classes de teste. O exemplo a seguir usa o alocador no `IndexPageTests` classe:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Cliente do aplicativo de exemplo está configurado para impedir o `HttpClient` de seguir redirecionamentos. Conforme explicado a [testar um ponto de extremidade seguro](#test-a-secure-endpoint) seção, isso permite que os testes para verificar o resultado da primeira de resposta do aplicativo. A primeira resposta é um redirecionamento em muitos desses testes com um `Location` cabeçalho.

3. Um teste típico usa a `HttpClient` e métodos auxiliares para processar a solicitação e resposta:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Todas as solicitações POST para o SUT devem satisfazer a verificação de antifalsificação é feita automaticamente pelo aplicativo do [sistema de proteção de dados antifalsificação](xref:security/data-protection/introduction). Para organizar para solicitação POST de um teste, o aplicativo de teste deve:

1. Faça uma solicitação para a página.
1. Analise o cookie antifalsificação e o token de validação de solicitação da resposta.
1. Fazer a solicitação POST com a validação antifalsificação do cookie e solicitação de token em vigor.

O `SendAsync` métodos de extensão auxiliar (*Helpers/HttpClientExtensions.cs*) e o `GetDocumentAsync` método auxiliar (*Helpers/HtmlHelpers.cs*) no [doaplicativodeexemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples/) usar o [AngleSharp](https://anglesharp.github.io/) analisador para lidar com a verificação de antiforgery com os seguintes métodos:

* `GetDocumentAsync` &ndash; Recebe o [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) e retorna um `IHtmlDocument`. `GetDocumentAsync` usa uma fábrica que prepara uma *resposta virtual* com base no original `HttpResponseMessage`. Para obter mais informações, consulte o [AngleSharp documentação](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync` métodos de extensão para o `HttpClient` compor uma [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) e chame [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) para enviar solicitações para o SUT. Sobrecargas `SendAsync` aceite o formulário HTML (`IHtmlFormElement`) e o seguinte:
  * Enviar o botão do formulário (`IHtmlElement`)
  * Coleção de valores de formulário (`IEnumerable<KeyValuePair<string, string>>`)
  * Botão enviar (`IHtmlElement`) e valores de formulário (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) é um terceiro biblioteca usada para fins de demonstração neste tópico e o aplicativo de exemplo de análise. AngleSharp não é suportado ou necessários para os testes de integração de aplicativos ASP.NET Core. Outros analisadores podem usados, como o [Pack de agilidade de Html (HAP)](http://html-agility-pack.net/). Outra abordagem é escrever código para lidar com o token de verificação de solicitação e o cookie antifalsificação o sistema antifalsificação diretamente.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personalizar o cliente com WithWebHostBuilder

Quando a configuração adicional é necessária dentro de um método de teste [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) cria uma nova `WebApplicationFactory` com um [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) que é ainda mais personalizado pela configuração.

O `Post_DeleteMessageHandler_ReturnsRedirectToRoot` método de teste a [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstra o uso de `WithWebHostBuilder`. Esse teste executa uma exclusão de registro no banco de dados ao disparar um envio de formulário no SUT.

Porque outro teste na `IndexPageTests` classe executa uma operação que exclui todos os registros no banco de dados e podem ser executadas antes do `Post_DeleteMessageHandler_ReturnsRedirectToRoot` método, o banco de dados é propagado nesse método de teste para garantir que um registro está presente para o SUT excluir. Selecionando o `deleteBtn1` botão do `messages` formulário no SUT é simulado na solicitação para o SUT:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Opções do cliente

A tabela a seguir mostra o padrão [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) disponíveis ao criar `HttpClient` instâncias.

| Opção | Descrição | Padrão |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtém ou define se `HttpClient` instâncias precisar seguir automaticamente as respostas de redirecionamento. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtém ou define o endereço básico do `HttpClient` instâncias. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtém ou define se `HttpClient` instâncias devem lidar com cookies. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtém ou define o número máximo de respostas de redirecionamento que `HttpClient` instâncias devem seguir. | 7 |

Criar o `WebApplicationFactoryClientOptions` de classe e passá-lo para o [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) método (padrão de valores são mostrados no exemplo de código):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Inserir serviços fictícios

Os serviços podem ser substituídos em um teste com uma chamada para [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) no construtor do host. **Para inserir os serviços de simulação, o SUT deve ter uma `Startup` classe com um `Startup.ConfigureServices` método.**

O exemplo SUT inclui um serviço com escopo definido que retorna uma citação. A cotação é inserida em um campo oculto na página de índice quando a página de índice é solicitada.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

A marcação a seguir é gerada quando o aplicativo SUT for executado:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Para testar a injeção de serviço e as aspas em um teste de integração, um serviço de simulação é injetado no SUT pelo teste. O serviço fictício substitui o aplicativo `QuoteService` com um serviço fornecido pelo aplicativo de teste, chamado `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` é chamado, e o serviço com escopo está registrado:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

A marcação produzida durante a execução do teste reflete o texto de cotação fornecido pelo `TestQuoteService`, portanto, os passos de asserção:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Como a infraestrutura de teste infere o caminho de raiz do conteúdo do aplicativo

O `WebApplicationFactory` construtor infere o caminho de raiz do conteúdo do aplicativo procurando por um [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) no assembly que contém os testes de integração com uma chave igual ao `TEntryPoint` assembly `System.Reflection.Assembly.FullName`. No caso de um atributo com a chave correta não for encontrado, `WebApplicationFactory` reverterá para procurar por um arquivo de solução (*\*. sln*) e anexa o `TEntryPoint` nome do assembly para o diretório da solução. O diretório raiz do aplicativo (o caminho raiz do conteúdo) é usado para descobrir os modos de exibição e arquivos de conteúdo.

Na maioria dos casos, ele não é necessário definir explicitamente a raiz do conteúdo de aplicativo, conforme a lógica de pesquisa geralmente encontra a raiz do conteúdo correta no tempo de execução. Em cenários especiais em que a raiz do conteúdo não for encontrada usando o algoritmo de pesquisa interna, o aplicativo de conteúdo raiz pode ser especificada explicitamente ou por meio de lógica personalizada. Para definir a raiz do conteúdo de aplicativo nesses cenários, chame o `UseSolutionRelativeContentRoot` método de extensão do [testhost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost) pacote. Forneça um caminho relativo da solução e o padrão de nome ou glob do arquivo de solução opcional (padrão = `*.sln`).

Chame o [UseSolutionRelativeContentRoot](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.usesolutionrelativecontentroot) usando o método extensão *um* das seguintes abordagens:

* Ao configurar classes de teste com `WebApplicationFactory`, forneça uma configuração personalizada com o [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder):

   ```csharp
   public IndexPageTests(
       WebApplicationFactory<RazorPagesProject.Startup> factory)
   {
       var _factory = factory.WithWebHostBuilder(builder =>
       {
           builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

           ...
       });
   }
   ```

* Ao configurar classes de teste com um personalizado `WebApplicationFactory`, herdam `WebApplicationFactory` e substituir [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost):

   ```csharp
   public class CustomWebApplicationFactory<TStartup>
       : WebApplicationFactory<RazorPagesProject.Startup>
   {
       protected override void ConfigureWebHost(IWebHostBuilder builder)
       {
           builder.ConfigureServices(services =>
           {
               builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

               ...
           });
       }
   }
   ```

## <a name="disable-shadow-copying"></a>Desabilitar a cópia de sombra

A cópia de sombra faz com que os testes a serem executados em uma pasta diferente que a pasta de saída. Para testes funcione corretamente, a cópia de sombra deve ser desabilitada. O [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) usa o xUnit e desabilita a cópia de sombra para xUnit, incluindo um *xunit.runner.json* arquivo com a configuração correta. Para obter mais informações, consulte [Configurando o xUnit.net com JSON](https://xunit.github.io/docs/configuring-with-json.html).

Adicione a *xunit.runner.json* arquivo raiz do projeto de teste com o seguinte conteúdo:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Descarte de objetos

Após os testes do `IClassFixture` implementação são executadas, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) e [HttpClient](/dotnet/api/system.net.http.httpclient) são descartados quando xUnit descarta o [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) . Se objetos instanciados pelo desenvolvedor exigem disposição, descartá-los no `IClassFixture` implementação. Para obter mais informações, consulte [implementando um método Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Exemplo para testes de integração

O [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) é composto de dois aplicativos:

| Aplicativo | Pasta do projeto | Descrição |
| --- | -------------- | ----------- |
| Aplicativo de mensagens (SUT) | *src/RazorPagesProject* | Permite que um usuário adicione, exclua uma, excluir todas as e analisar as mensagens. |
| Aplicativo de teste | *tests/RazorPagesProject.Tests* | Usado para teste de integração o SUT. |

Os testes podem ser executados usando os recursos de teste interno de um IDE, como [Visual Studio](https://www.visualstudio.com/vs/). Se usando [Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando na *tests/RazorPagesProject.Tests* pasta:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organização de aplicativo (SUT) da mensagem

O SUT é um sistema de mensagem de páginas do Razor com as seguintes características:

* A página de índice do aplicativo (*Pages* e *Pages/Index.cshtml.cs*) fornece uma interface do usuário e a página de métodos de modelo para controlar a adição, exclusão e análise de mensagens (palavras médias por mensagem) .
* Uma mensagem é descrita pela `Message` classe (*Data/Message.cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem). O `Text` propriedade é necessária e é limitada a 200 caracteres.
* As mensagens são armazenadas usando [banco de dados do Entity Framework na memória](/ef/core/providers/in-memory/)&#8224;.
* O aplicativo contém uma camada de acesso de dados (DAL) na sua classe de contexto do banco de dados, `AppDbContext` (*Data/AppDbContext.cs*).
* Se o banco de dados está vazio na inicialização do aplicativo, o repositório de mensagens é inicializado com três mensagens.
* O aplicativo inclui um `/SecurePage` que só pode ser acessado por um usuário autenticado.

&#8224;O tópico EF [testar com InMemory](/ef/core/miscellaneous/testing/in-memory), explica como usar um banco de dados na memória para testes com MSTest. Este tópico usa o [xUnit](https://xunit.github.io/) estrutura de teste. Conceitos de teste e teste implementações em estruturas de teste diferentes são semelhantes, mas não idênticos.

Embora o aplicativo não usa o padrão de repositório e não é um exemplo efetivação do [padrão de unidade de trabalho (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), páginas do Razor dá suporte a esses padrões de desenvolvimento. Para obter mais informações, consulte [Projetando a camada de persistência de infraestrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e [lógica do controlador de teste](/aspnet/core/mvc/controllers/testing) (o exemplo implementa o padrão de repositório).

### <a name="test-app-organization"></a>Organização de aplicativo de teste

O aplicativo de teste é um aplicativo de console dentro de *tests/RazorPagesProject.Tests* pasta.

| Pasta do aplicativo de teste | Descrição |
| --------------- | ----------- |
| *BasicTests* | *BasicTests.cs* contém métodos de teste para o roteamento, acesso a uma página segura por um usuário não autenticado e como obter um perfil de usuário do GitHub e verificação de logon do usuário do perfil. |
| *IntegrationTests* | *IndexPageTests.cs* contém os testes de integração para a página de índice usar custom `WebApplicationFactory` classe. |
| *Os auxiliares/Utilities* | <ul><li>*Utilities.CS* contém o `InitializeDbForTests` método usado para propagar o banco de dados com dados de teste.</li><li>*HtmlHelpers.cs* fornece um método para retornar um AngleSharp `IHtmlDocument` para uso pelos métodos de teste.</li><li>*HttpClientExtensions.cs* fornecem sobrecargas para `SendAsync` para enviar solicitações para o SUT.</li></ul> |

É a estrutura de teste [xUnit](https://xunit.github.io/). Testes de integração são realizados usando o [testhost](/dotnet/api/microsoft.aspnetcore.testhost), que inclui o [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Porque o [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) pacote é usado para configurar o servidor de host e o teste de teste, o `TestHost` e `TestServer` pacotes não exigem referências de pacote direto no arquivo de projeto de teste do aplicativo ou configuração de desenvolvedor do aplicativo de teste.

**A propagação do banco de dados para teste**

Testes de integração geralmente exigem um pequeno conjunto de dados no banco de dados antes da execução de teste. Por exemplo, uma exclusão chamadas de teste para uma exclusão de registros de banco de dados, portanto, o banco de dados deve ter pelo menos um registro para a solicitação de exclusão seja bem-sucedida.

O aplicativo de exemplo propaga o banco de dados com três mensagens na *Utilities.cs* que testes podem usar quando elas são executadas:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a>Recursos adicionais

* [Testes de unidade](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Testes de unidades de páginas Razor](xref:test/razor-pages-tests)
* [Middleware](xref:fundamentals/middleware/index)
* [Controladores de teste](xref:mvc/controllers/testing)
