---
title: Introdução ao NSwag e ao ASP.NET Core
author: zuckerthoben
description: Saiba como usar o NSwag para gerar a documentação e as páginas de ajuda para uma API Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/18/2018
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 8af5bed1e042c4f6d83043b05084c51b3064a548
ms.sourcegitcommit: ea215df889e89db44037a6ac2f01baede0450da9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53595354"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Introdução ao NSwag e ao ASP.NET Core

Por [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](https://rsuter.com)

::: moniker range=">= aspnetcore-2.1"

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([como baixar](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([como baixar](xref:index#how-to-download-a-sample))

::: moniker-end

Registre os middlewares NSwag para:

* Gerar a especificação do Swagger para a API Web implementada.
* Oferecer a interface do usuário do Swagger para navegar e testar a API Web.

Para usar os middlewares ASP.NET Core do [NSwag](https://github.com/RSuter/NSwag), instale o pacote do NuGet [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/). Este pacote contém os middlewares para gerar e oferecer a especificação do Swagger, interface do usuário do Swagger (v2 e v3) e a [interface do usuário do ReDoc](https://github.com/Rebilly/ReDoc).

Além disso, é altamente recomendável usar as funcionalidades de geração de código do NSwag. Escolha uma das seguintes opções para usar as funcionalidades de geração de código:

* Use o [NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio), um aplicativo da área de trabalho do Windows para a geração de código do cliente em C# e em TypeScript para sua API.
* Use os pacotes do NuGet [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) ou [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) para a geração de código dentro do projeto.
* Use o NSwag na [linha de comando](https://github.com/NSwag/NSwag/wiki/CommandLine).
* Use o pacote NuGet [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild).

## <a name="features"></a>Recursos

O principal motivo para usar o NSwag é que, além de apresentar a interface do usuário do Swagger e o gerador do Swagger, também é possível usar recursos flexíveis para geração de código. Você não precisa de uma API existente. É possível usar APIs de terceiros que incorporam o Swagger e permitem que o NSwag gere uma implementação de cliente. De qualquer forma, o ciclo de desenvolvimento é acelerado e você pode se adaptar às alterações da API com mais facilidade.

## <a name="package-installation"></a>Instalação do pacote

O pacote do NuGet do NSwag pode ser adicionado com as seguintes abordagens:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Da janela **Console do Gerenciador de Pacotes**:
  * Acesse **Exibição** > **Outras Janelas** > **Console do Gerenciador de Pacotes**
  * Navegue para o diretório no qual o arquivo *TodoApi.csproj* está localizado
  * Execute o seguinte comando:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* Da caixa de diálogo **Gerenciar Pacotes NuGet**:
  * Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**
  * Defina a **Origem do pacote** para "nuget.org"
  * Insira "NSwag.AspNetCore" na caixa de pesquisa
  * Selecione o pacote "NSwag.AspNetCore" na guia **Procurar** e clique em **Instalar**

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse na pasta *Pacotes* em **Painel de Soluções** > **Adicionar Pacotes...**
* Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** para "nuget.org"
* Insira "NSwag.AspNetCore" na caixa de pesquisa
* Selecione o pacote "NSwag.AspNetCore" no painel de resultados e clique em **Adicionar Pacote**

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute o comando a seguir do **Terminal Integrado**:

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

### <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Execute o seguinte comando:

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Adicionar e configurar o middleware do Swagger

Importe os seguintes namespaces na classe `Startup`:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_StartupConfigureImports)]

No método `Startup.ConfigureServices`, registre os serviços obrigatórios do Swagger: 

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

No método `Startup.Configure`, habilite o middleware para atender à especificação do Swagger gerado e à interface do usuário do Swagger v3:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-10)]

Inicie o aplicativo. Navegue até `http://localhost:<port>/swagger` para exibir a interface do usuário do Swagger. Navegue até `http://localhost:<port>/swagger/v1/swagger.json` para exibir a especificação do Swagger.

## <a name="code-generation"></a>Geração de código

### <a name="via-nswagstudio"></a>Por meio do NSwagStudio

* Instale o NSwagStudio por meio do [repositório GitHub](https://github.com/RSuter/NSwag/wiki/NSwagStudio) oficial.
* Inicie o NSwagStudio. Insira a URL do arquivo *swagger.json* na caixa de texto **URL de Especificação do Swagger** e clique no botão **Criar Cópia local**.
* Selecione o tipo de saída do cliente **Cliente do CSharp**. Outras opções incluem **Cliente do TypeScript** e **Controlador da API Web do CSharp**. O uso de um controlador da API Web é basicamente uma geração inversa. Ele usa uma especificação de um serviço para recriar o serviço.
* Clique no botão **Gerar Saídas**. Uma implementação completa de cliente em C# do projeto *TodoApi.NSwag* é produzida. Clique na guia **Cliente do CSharp** da seção **Saídas** para ver o código de cliente gerado:

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v11.17.3.0 (NJsonSchema v9.10.46.0 (Newtonsoft.Json v9.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable // Disable all warnings

    [System.CodeDom.Compiler.GeneratedCode("NSwag",
        "11.17.3.0 (NJsonSchema v9.10.46.0 (Newtonsoft.Json v9.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "http://localhost:50499";
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient()
        {
            _settings = new System.Lazy
                <Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> O código de cliente em C# é gerado com base nas configurações definidas na guia **Configurações** da guia **Cliente do CSharp**. Modifique as configurações para executar tarefas como geração de método síncrono e renomeação de namespace padrão.

* Copie o código C# gerado para um arquivo em um projeto de cliente (por exemplo, um aplicativo [Xamarin.Forms](/xamarin/xamarin-forms/)).
* Inicie o consumo da API Web:

```csharp
var todoClient = new TodoClient();

// Gets all to-dos from the API
var allTodos = await todoClient.GetAllAsync();

// Create a new TodoItem, and save it in the API
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

> [!NOTE]
> Você pode injetar uma URL base e/ou um cliente HTTP no cliente da API. A melhor prática é sempre [reutilizar o HttpClient](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/).

### <a name="other-ways-to-generate-client-code"></a>Outras maneiras de gerar o código do cliente

Você pode gerar o código de cliente de outras maneiras mais adequadas ao seu fluxo de trabalho:

* [MSBuild](https://www.nuget.org/packages/NSwag.MSBuild/)

* [No código](https://github.com/NSwag/NSwag/wiki/SwaggerToCSharpClientGenerator)

* [Modelos T4](https://github.com/NSwag/NSwag/wiki/T4)

## <a name="customize"></a>Personalizar

O Swagger fornece opções para documentar o modelo de objeto para facilitar o consumo da API Web.

### <a name="api-info-and-description"></a>Descrição e informações da API

No método `Startup.Configure`, uma ação de configuração passada para o método `UseSwagger` adiciona informações como o autor, a licença e a descrição:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_UseSwagger)]

A interface do usuário do Swagger exibe as informações da versão:

![Interface do usuário do Swagger com informações de versão](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>comentários XML

Os comentários XML podem ser habilitados com as seguintes abordagens:

# <a name="visual-studiotabvisual-studio-xml"></a>[Visual Studio](#tab/visual-studio-xml/)

::: moniker range=">= aspnetcore-2.0"

* Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Editar <nome_do_projeto>.csproj**.
* Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Propriedades**
* Marque a caixa **Arquivo de documentação XML** na seção **Saída** da guia **Build**

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac-xml"></a>[Visual Studio para Mac](#tab/visual-studio-mac-xml/)

::: moniker range=">= aspnetcore-2.0"

* No *Painel de Soluções*, pressione **control** e clique no nome do projeto. Navegue até **Ferramentas** > **Editar arquivo**.
* Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Abra a caixa de diálogo **Opções do Projeto** > **Compilar**>**Compilador**
* Marque a caixa **Gerar documentação XML** na seção **Opções Gerais**

::: moniker-end

# <a name="visual-studio-codetabvisual-studio-code-xml"></a>[Visual Studio Code](#tab/visual-studio-code-xml/)

Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Anotações de dados

::: moniker range="<= aspnetcore-2.0"

O NSwag usa [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) e o tipo de retorno recomendado para as ações da API Web é [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult). Consequentemente, o NSwag não consegue inferir o que a sua ação está executando e o que ela retorna. Considere o exemplo a seguir:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

A ação anterior retorna `IActionResult`, mas dentro da ação, ela está retornando [CreatedAtRoute](/dotnet/api/system.web.http.apicontroller.createdatroute) ou [BadRequest](/dotnet/api/system.web.http.apicontroller.badrequest). As anotações de dados são usadas para informar os clientes de quais códigos de status HTTP essa ação deverá retornar. Decore a ação com os seguintes atributos:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

O NSwag usa [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) e o tipo de retorno recomendado para as ações da API Web é [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1). Consequentemente, o NSwag pode inferir apenas o tipo de retorno definido por `T`. Outros tipos de retorno possíveis na ação não podem ser inferidos. Considere o exemplo a seguir:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

A ação anterior retorna `ActionResult<T>`. Dentro da ação, ela está retornando [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Como o controlador está decorado com o atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute), uma resposta [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) também é possível. Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses). As anotações de dados são usadas para informar os clientes de quais códigos de status HTTP essa ação deverá retornar. Decore a ação com os seguintes atributos:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

No ASP.NET Core 2.2 ou posterior, as convenções podem ser usadas como uma alternativa para decorar explicitamente as ações individuais com `[ProducesResponseType]`. Para obter mais informações, consulte <xref:web-api/advanced/conventions>.

::: moniker-end

O gerador do Swagger agora pode descrever essa ação precisamente e os clientes gerados conseguem saber o que recebem ao chamar o ponto de extremidade. É altamente recomendável decorar todas as ações com esses atributos. Para obter diretrizes sobre quais respostas HTTP as ações da API devem retornar, confira a [Especificação RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
