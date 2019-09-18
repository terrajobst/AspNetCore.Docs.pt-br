---
title: Testes de integração no ASP.NET Core
author: guardrex
description: Saiba como testes de integração garantem que os componentes do aplicativo funcionem corretamente no nível de infraestrutura, incluindo o banco de dados, o sistema de arquivos e a rede.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: test/integration-tests
ms.openlocfilehash: 272f0f2140647dd31319f8feada0ec04c7ab4e44
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082495"
---
# <a name="integration-tests-in-aspnet-core"></a>Testes de integração no ASP.NET Core

De [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com/)

Os testes de integração garantem que os componentes de um aplicativo funcionem corretamente em um nível que inclui a infraestrutura de suporte do aplicativo, como o banco de dados, o sistema de arquivos e a rede. O ASP.NET Core dá suporte a testes de integração usando uma estrutura de teste de unidade com um host da Web de teste e um servidor de teste na memória.

Este tópico pressupõe uma compreensão básica dos testes de unidade. Se não estiver familiarizado com os conceitos de teste, consulte os [testes de unidade no .NET Core e .net Standard](/dotnet/core/testing/) tópico e seu conteúdo vinculado.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo é um aplicativo Razor Pages e assume uma compreensão básica de Razor Pages. Se não estiver familiarizado com Razor Pages, consulte os seguintes tópicos:

* [Introdução a Páginas do Razor](xref:razor-pages/index)
* [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start)
* [Testes de unidades de páginas Razor](xref:test/razor-pages-tests)

> [!NOTE]
> Para testar o SPAs, recomendamos uma ferramenta como o [Selenium](https://www.seleniumhq.org/), que pode automatizar um navegador.

## <a name="introduction-to-integration-tests"></a>Introdução aos testes de integração

Os testes de integração avaliam os componentes de um aplicativo em um nível mais amplo do que os [testes de unidade](/dotnet/core/testing/). Os testes de unidade são usados para testar componentes de software isolados, como métodos de classe individuais. Testes de integração confirmam que dois ou mais componentes de aplicativo trabalham juntos para produzir um resultado esperado, possivelmente incluindo todos os componentes necessários para processar totalmente uma solicitação.

Esses testes mais amplos são usados para testar a infraestrutura e a estrutura inteira do aplicativo, geralmente incluindo os seguintes componentes:

* Banco de Dados
* Sistema de arquivos
* Dispositivos de rede
* Pipeline de solicitação-resposta

Os testes de unidade usam componentes criei, conhecidos como *falsificações* ou *objetos fictícios*, em vez de componentes de infraestrutura.

Ao contrário dos testes de unidade, testes de integração:

* Use os componentes reais que o aplicativo usa na produção.
* Exigir mais processamento de código e de dados.
* Demore mais para executar.

Portanto, limite o uso de testes de integração para os cenários de infraestrutura mais importantes. Se um comportamento puder ser testado usando um teste de unidade ou um teste de integração, escolha o teste de unidade.

> [!TIP]
> Não escreva testes de integração para todas as permutas possíveis de dados e acesso a arquivos com bancos e sistemas de arquivos. Independentemente de quantos lugares em um aplicativo interagem com bancos de dados e sistemas de arquivos, um conjunto focado de testes de integração de leitura, gravação, atualização e exclusão geralmente é capaz de testar adequadamente os componentes do banco de dados e do sistema de arquivos. Use testes de unidade para testes de rotina da lógica de método que interagem com esses componentes. Em testes de unidade, o uso de falsificações/simulações de infraestrutura resulta em uma execução de teste mais rápida.

> [!NOTE]
> Em discussões sobre testes de integração, o projeto testado é frequentemente chamado de *sistema em teste*, ou "SUT" para curto.

## <a name="aspnet-core-integration-tests"></a>Testes de integração do ASP.NET Core

Os testes de integração no ASP.NET Core exigem o seguinte:

* Um projeto de teste é usado para conter e executar os testes. O projeto de teste tem uma referência para o projeto de ASP.NET Core testado, chamado de *sistema em teste* (SUT). _"SUT" é usado em todo este tópico para referir-se ao aplicativo testado._
* O projeto de teste cria um host Web de teste para o SUT e usa um cliente de servidor de teste para lidar com solicitações e respostas para o SUT.
* Um executor de teste é usado para executar os testes e relatar os resultados do teste.

Os testes de integração seguem uma sequência de eventos que incluem as etapas de teste de *organizar*, *Act*e *Assert* usuais:

1. O host da Web do SUT está configurado.
1. Um cliente de servidor de teste é criado para enviar solicitações ao aplicativo.
1. A etapa *organizar* teste é executada: O aplicativo de teste prepara uma solicitação.
1. A etapa de teste *Act* é executada: O cliente envia a solicitação e recebe a resposta.
1. A etapa de teste *Assert* é executada: A resposta *real* é validada como uma *passagem* ou *falha* com base em uma resposta *esperada* .
1. O processo continua até que todos os testes sejam executados.
1. Os resultados de teste são relatados.

Normalmente, o host da Web de teste é configurado de modo diferente do host da Web normal do aplicativo para as execuções de teste. Por exemplo, um banco de dados diferente ou configurações de aplicativo diferentes podem ser usadas para os testes.

Os componentes de infraestrutura, como o host da Web de teste e o[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)(servidor de teste na memória), são fornecidos ou gerenciados pelo pacote [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) . O uso desse pacote simplifica a criação e a execução de testes.

O `Microsoft.AspNetCore.Mvc.Testing` pacote lida com as seguintes tarefas:

* Copia o arquivo de dependências ( *\*. deps*) do SUT para o diretório *bin* do projeto de teste.
* Define a raiz do conteúdo como a raiz do projeto do SUT para que arquivos estáticos e páginas/exibições sejam encontrados quando os testes forem executados.
* Fornece a classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) para simplificar a inicialização do SUT com `TestServer`.

A documentação de [testes de unidade](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) descreve como configurar um projeto de teste e o Test Runner, juntamente com instruções detalhadas sobre como executar testes e recomendações sobre como nomear testes e classes de teste.

> [!NOTE]
> Ao criar um projeto de teste para um aplicativo, separe os testes de unidade dos testes de integração em projetos diferentes. Isso ajuda a garantir que os componentes de teste de infraestrutura não sejam incluídos acidentalmente nos testes de unidade. A separação de testes de unidade e de integração também permite o controle sobre qual conjunto de testes são executados.

Praticamente não há nenhuma diferença entre a configuração para testes de aplicativos Razor Pages e aplicativos MVC. A única diferença é em como os testes são nomeados. Em um aplicativo Razor pages, os testes de pontos de extremidade de página geralmente são nomeados após a classe de modelo de `IndexPageTests` página (por exemplo, para testar a integração de componentes para a página de índice). Em um aplicativo MVC, os testes geralmente são organizados por classes de controlador e nomeados após os controladores que testam `HomeControllerTests` (por exemplo, para testar a integração de componentes para o controlador doméstico).

## <a name="test-app-prerequisites"></a>Pré-requisitos do aplicativo de teste

O projeto de teste deve:

* Faça referência aos seguintes pacotes:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Especifique o SDK da Web no arquivo de projeto`<Project Sdk="Microsoft.NET.Sdk.Web">`(). O SDK da Web é necessário ao fazer referência ao [metapacote do Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

Esses pré-requisitos podem ser vistos no aplicativo de [exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Inspecione o arquivo *tests/RazorPagesProject. Tests/RazorPagesProject. Tests. csproj* . O aplicativo de exemplo usa a estrutura de teste do [xUnit](https://xunit.github.io/) e a biblioteca do analisador [AngleSharp](https://anglesharp.github.io/) , portanto, o aplicativo de exemplo também faz referência a:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Ambiente SUT

Se o [ambiente](xref:fundamentals/environments) do SUT não estiver definido, o ambiente assume o padrão de desenvolvimento.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Testes básicos com o padrão WebApplicationFactory

[WebApplicationFactory&lt;TEntryPoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) é usado para criar um [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) para os testes de integração. `TEntryPoint`é a classe de ponto de entrada do SUT, geralmente `Startup` a classe.

Classes de teste implementam uma interface de *acessório de classe* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) para indicar que a classe contém testes e fornece instâncias de objeto compartilhado entre os testes na classe.

### <a name="basic-test-of-app-endpoints"></a>Teste básico de pontos de extremidade de aplicativo

A classe de teste a `BasicTests`seguir,, `WebApplicationFactory` usa o para inicializar o SUT e fornecer um [HttpClient](/dotnet/api/system.net.http.httpclient) para um método `Get_EndpointsReturnSuccessAndCorrectContentType`de teste,. O método verifica se o código de status de resposta é bem-sucedido (códigos de status no intervalo de 200-299 `Content-Type` ) e `text/html; charset=utf-8` o cabeçalho é para várias páginas de aplicativo.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) cria uma instância do `HttpClient` que segue automaticamente os redirecionamentos e manipula cookies.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Por padrão, os cookies não essenciais não são preservados entre solicitações quando a [política de consentimento de GDPR](xref:security/gdpr) está habilitada. Para preservar cookies não essenciais, como os usados pelo provedor TempData, marque-os como essenciais em seus testes. Para obter instruções sobre como marcar um cookie como essencial, consulte [cookies essenciais](xref:security/gdpr#essential-cookies).

### <a name="test-a-secure-endpoint"></a>Testar um ponto de extremidade seguro

Outro teste na `BasicTests` classe verifica se um ponto de extremidade seguro redireciona um usuário não autenticado para a página de logon do aplicativo.

No SUT, a `/SecurePage` página usa uma convenção [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) para aplicar um [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à página. Para obter mais informações, consulte [Razor Pages convenções de autorização](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

No teste, um [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) é definido para não permitir redirecionamentos definindo [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) como `false`: `Get_SecurePageRequiresAnAuthenticatedUser`

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

Ao não permitir que o cliente siga o redirecionamento, as seguintes verificações podem ser feitas:

* O código de status retornado pelo SUT pode ser verificado em relação ao resultado de [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) esperado, não ao código de status final após o redirecionamento para a página de logon, que seria [HttpStatusCode. ok](/dotnet/api/system.net.httpstatuscode).
* O `Location` valor do cabeçalho nos cabeçalhos de resposta é verificado para confirmar que ele começa `http://localhost/Identity/Account/Login`com, não a resposta da página final de logon `Location` , em que o cabeçalho não estaria presente.

Para obter mais informações `WebApplicationFactoryClientOptions`sobre o, consulte a seção [Opções de cliente](#client-options) .

## <a name="customize-webapplicationfactory"></a>Personalizar o WebApplicationFactory

A configuração do host Web pode ser criada independentemente das classes de teste herdando `WebApplicationFactory` de para criar uma ou mais fábricas personalizadas:

1. Herdar `WebApplicationFactory` de e substituir [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). O [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permite a configuração da coleção de serviços com [configuraservices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   A`InitializeDbForTests` propagação de banco de dados no [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) é executada pelo método. O método é descrito no exemplo [de testes de integração: Seção testar organização](#test-app-organization) do aplicativo.

2. Use as classes `CustomWebApplicationFactory` personalizadas em teste. O exemplo a seguir usa a fábrica na `IndexPageTests` classe:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   O cliente do aplicativo de exemplo está configurado para impedir `HttpClient` o dos redirecionamentos a seguir. Conforme explicado na seção [testar um ponto de extremidade seguro](#test-a-secure-endpoint) , isso permite que os testes verifiquem o resultado da primeira resposta do aplicativo. A primeira resposta é um redirecionamento em muitos desses testes com `Location` um cabeçalho.

3. Um teste típico usa os `HttpClient` métodos auxiliares e para processar a solicitação e a resposta:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Qualquer solicitação POST para o SUT deve satisfazer a verificação antifalsificação feita automaticamente pelo [sistema antifalsificação de proteção de dados](xref:security/data-protection/introduction)do aplicativo. Para organizar a solicitação POST de um teste, o aplicativo de teste deve:

1. Faça uma solicitação para a página.
1. Analise o cookie antifalsificação e solicite o token de validação da resposta.
1. Faça a solicitação POST com o cookie antifalsificação e solicite o token de validação em vigor.

Os `SendAsync` métodos de extensão auxiliar (*Helpers/HttpClientExtensions. cs*) e `GetDocumentAsync` o método auxiliar (*Helpers/HtmlHelpers. cs*) no [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usam o analisador [AngleSharp](https://anglesharp.github.io/) para lidar com o antifalsificação Verifique com os seguintes métodos:

* `GetDocumentAsync`Recebe o [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) e retorna um `IHtmlDocument`. &ndash; `GetDocumentAsync`usa uma fábrica que prepara uma *resposta virtual* com base no original `HttpResponseMessage`. Para obter mais informações, consulte a [documentação do AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`métodos de extensão para `HttpClient` compor um [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) e chamar [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) para enviar solicitações para o SUT. Sobrecargas para `SendAsync` aceitar o formulário HTML (`IHtmlFormElement`) e o seguinte:
  * Botão enviar do formulário (`IHtmlElement`)
  * Coleção de valores de`IEnumerable<KeyValuePair<string, string>>`formulário ()
  * Enviar botão (`IHtmlElement`) e valores de formulário`IEnumerable<KeyValuePair<string, string>>`()

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) é uma biblioteca de análise de terceiros usada para fins de demonstração neste tópico e no aplicativo de exemplo. AngleSharp não tem suporte ou é necessário para o teste de integração de aplicativos ASP.NET Core. Outros analisadores podem ser usados, como o [Hap (HTML agilidade Pack)](https://html-agility-pack.net/). Outra abordagem é escrever código para lidar diretamente com o token de verificação de solicitação do sistema antifalsificação e o cookie antifalsificação.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personalizar o cliente com o WithWebHostBuilder

Quando uma configuração adicional é necessária dentro de um método de teste, o [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) cria um novo `WebApplicationFactory` com um [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) que é ainda mais personalizado pela configuração.

O `Post_DeleteMessageHandler_ReturnsRedirectToRoot` método de teste do [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstra o uso `WithWebHostBuilder`de. Esse teste executa uma exclusão de registro no banco de dados disparando um envio de formulário no SUT.

Como outro teste na `IndexPageTests` classe executa uma operação que exclui todos os registros no banco de dados e pode ser executado antes do `Post_DeleteMessageHandler_ReturnsRedirectToRoot` método, o banco de dados é propagado nesse método de teste para garantir que um registro esteja presente para que o SUT seja excluído. Selecionar o `deleteBtn1` botão `messages` do formulário no SUT é simulado na solicitação para o SUT:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Opções de cliente

A tabela a seguir mostra o [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) padrão disponível ao `HttpClient` criar instâncias.

| Opção | Descrição | Padrão |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtém ou define se as instâncias `HttpClient` devem seguir ou não automaticamente as respostas de redirecionamento. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtém ou define o endereço base das `HttpClient` instâncias. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtém ou define se `HttpClient` as instâncias devem lidar com cookies. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtém ou define o número máximo de respostas de redirecionamento que `HttpClient` as instâncias devem seguir. | 7 |

Crie a classe `WebApplicationFactoryClientOptions` e passe-a para o método [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (os valores padrão são mostrados no exemplo de código):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Injetar serviços fictícios

Os serviços podem ser substituídos em um teste com uma chamada para [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) no host Builder. **Para injetar serviços fictícios, o SUT deve `Startup` ter uma classe `Startup.ConfigureServices` com um método.**

O SUT de exemplo inclui um serviço com escopo que retorna uma aspa. A cotação é inserida em um campo oculto na página de índice quando a página de índice é solicitada.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Páginas/index. cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

A marcação a seguir é gerada quando o aplicativo SUT é executado:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Para testar o serviço e a injeção de aspas em um teste de integração, um serviço fictício é injetado no SUT pelo teste. O serviço fictício substitui o aplicativo `QuoteService` por um serviço fornecido pelo aplicativo de teste, chamado: `TestQuoteService`

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`é chamado e o serviço com escopo é registrado:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

A marcação produzida durante a execução do teste reflete o texto da citação fornecido `TestQuoteService`pelo, assim, a asserção passa:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Como a infraestrutura de teste infere o caminho raiz do conteúdo do aplicativo

O `WebApplicationFactory` Construtor infere o caminho raiz do conteúdo do aplicativo pesquisando um [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) no assembly que contém os testes de integração com uma `TEntryPoint` chave igual ao assembly. `System.Reflection.Assembly.FullName` Caso um atributo com a chave correta não seja encontrado, `WebApplicationFactory` volte à pesquisa de um arquivo de solução ( *\*. sln*) e acrescente o nome `TEntryPoint` do assembly ao diretório da solução. O diretório raiz do aplicativo (o caminho raiz do conteúdo) é usado para descobrir exibições e arquivos de conteúdo.

## <a name="disable-shadow-copying"></a>Desabilitar cópia de sombra

A cópia de sombra faz com que os testes sejam executados em um diretório diferente do diretório de saída. Para que os testes funcionem corretamente, a cópia de sombra deve ser desabilitada. O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit e desabilita a cópia de sombra para xUnit, incluindo um arquivo *xUnit. Runner. JSON* com a definição de configuração correta. Para obter mais informações, consulte [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).

Adicione o arquivo *xUnit. Runner. JSON* à raiz do projeto de teste com o seguinte conteúdo:

```json
{
  "shadowCopy": false
}
```

Se estiver usando o Visual Studio, defina a propriedade **copiar para diretório de saída** do arquivo como **copiar sempre**. Se não estiver usando o Visual Studio, `Content` adicione um destino ao arquivo de projeto do aplicativo de teste:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Alienação de objetos

Depois que os testes da `IClassFixture` implementação são executados, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) e [HttpClient](/dotnet/api/system.net.http.httpclient) são descartados quando xUnit descarta o [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Se os objetos instanciados pelo desenvolvedor exigirem descarte, descarte-os na `IClassFixture` implementação. Para obter mais informações, consulte [implementando um método Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Exemplo de testes de integração

O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) é composto por dois aplicativos:

| Aplicação | Diretório do projeto | Descrição |
| --- | ----------------- | ----------- |
| Aplicativo de mensagens (o SUT) | *src/RazorPagesProject* | Permite que um usuário adicione, exclua um, exclua todos e analise as mensagens. |
| Aplicativo de teste | *tests/RazorPagesProject.Tests* | Usado para testar a integração do SUT. |

Os testes podem ser executados usando os recursos de teste internos de um IDE, como o [Visual Studio](https://visualstudio.microsoft.com). Se estiver usando [Visual Studio Code](https://code.visualstudio.com/) ou a linha de comando, execute o seguinte comando em um prompt de comando no diretório *tests/RazorPagesProject. Tests* :

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organização do aplicativo de mensagem (SUT)

O SUT é um sistema de mensagens Razor Pages com as seguintes características:

* A página de índice do aplicativo (*pages/index. cshtml* e *pages/index. cshtml. cs*) fornece uma interface do usuário e métodos de modelo de página para controlar a adição, a exclusão e a análise de mensagens (média de palavras por mensagem).
* Uma mensagem `Message` é descrita pela classe (*Data/mensagem. cs*) com duas propriedades: `Id` (chave) e `Text` (mensagem). A `Text` propriedade é necessária e limitada a 200 caracteres.
* As mensagens são armazenadas usando o banco de&#8224; [dados na memória do Entity Framework](/ef/core/providers/in-memory/).
* O aplicativo contém uma camada de acesso a dados (Dal) em sua classe de `AppDbContext` contexto de banco de dados, (*Data/AppDbContext. cs*).
* Se o banco de dados estiver vazio na inicialização do aplicativo, o repositório de mensagens será inicializado com três mensagens.
* O aplicativo inclui um `/SecurePage` que só pode ser acessado por um usuário autenticado.

&#8224;O tópico do EF, [teste com inmemory](/ef/core/miscellaneous/testing/in-memory), explica como usar um banco de dados na memória para testes com MSTest. Este tópico usa a estrutura de teste do [xUnit](https://xunit.github.io/) . Conceitos de teste e implementações de teste em diferentes estruturas de teste são semelhantes, mas não idênticos.

Embora o aplicativo não use o padrão de repositório e não seja um exemplo eficaz do [padrão de UoW (unidade de trabalho)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages dá suporte a esses padrões de desenvolvimento. Para obter mais informações, consulte [projetando a camada de persistência de infraestrutura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e a [lógica do controlador de teste](/aspnet/core/mvc/controllers/testing) (o exemplo implementa o padrão de repositório).

### <a name="test-app-organization"></a>Organização de aplicativos de teste

O aplicativo de teste é um aplicativo de console dentro do diretório *tests/RazorPagesProject. Tests* .

| Testar diretório do aplicativo | Descrição |
| ------------------ | ----------- |
| *BasicTests* | *BasicTests.cs* contém métodos de teste para roteamento, acesso a uma página segura por um usuário não autenticado e obtenção de um perfil de usuário do GitHub e verificação do logon do usuário do perfil. |
| *IntegrationTests* | *IndexPageTests.cs* contém os testes de integração para a página de índice `WebApplicationFactory` usando a classe personalizada. |
| *Auxiliares/utilitários* | <ul><li>*Utilities.cs* contém o `InitializeDbForTests` método usado para propagar o banco de dados com dado de teste.</li><li>*HtmlHelpers.cs* fornece um método para retornar um AngleSharp `IHtmlDocument` para uso pelos métodos de teste.</li><li>*HttpClientExtensions.cs* fornecem sobrecargas para `SendAsync` o enviar solicitações para o SUT.</li></ul> |

A estrutura de teste é [xUnit](https://xunit.github.io/). Os testes de integração são realizados usando o [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), que inclui o [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Como o pacote [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) é usado para configurar o host de teste e o servidor de `TestHost` teste `TestServer` , os pacotes e não exigem referências diretas de pacote no arquivo de projeto do aplicativo de teste ou desenvolvedor configuração no aplicativo de teste.

**Propagando o banco de dados para teste**

Os testes de integração geralmente exigem um pequeno conjunto de dados no banco antes da execução do teste. Por exemplo, um teste de exclusão chama uma exclusão de registro de banco de dados, de modo que o banco de dados deve ter pelo menos um registro para que a solicitação de exclusão tenha sucesso.

O aplicativo de exemplo propaga o banco de dados com três mensagens no *Utilities.cs* que os testes podem usar quando executam:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a>Recursos adicionais

* [Testes de unidade](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Testes de unidades de páginas Razor](xref:test/razor-pages-tests)
* [Middleware](xref:fundamentals/middleware/index)
* [Controladores de teste](xref:mvc/controllers/testing)
