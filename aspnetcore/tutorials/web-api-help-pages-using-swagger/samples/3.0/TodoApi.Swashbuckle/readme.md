---
page_type: sample
description: Saiba como adicionar o Swashbuckle ao seu projeto de API Web ASP.NET Core para integrar a interface do usuário do Swagger.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: e02247325f430b0ce23dbb3f5bc344a60a1a164a
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879724"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Introdução ao Swashbuckle e ao ASP.NET Core

Ao consumir uma API Web, entender seus vários métodos pode ser um desafio para um desenvolvedor. O [Swagger](https://swagger.io/), também conhecido como [OpenAPI](https://www.openapis.org/), resolve o problema da geração de páginas de ajuda e de documentação úteis para APIs Web. Ele oferece benefícios, como documentação interativa, geração de SDK de cliente e capacidade de descoberta de API.

Neste exemplo, é mostrada a implementação [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) do .NET.

## <a name="add-and-configure-swagger-middleware"></a>Adicionar e configurar o middleware do Swagger

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

No método `Startup.Configure`, habilite o middleware para atender ao documento JSON gerado e à interface do usuário do Swagger:

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

A chamada do método `UseSwaggerUI` precedente habilita o [middleware de arquivos estáticos](https://docs.microsoft.com/aspnet/core/fundamentals/static-files). Se você estiver direcionando ao .NET Framework ou ao .NET Core 1.x, adicione o pacote NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.

Inicie o aplicativo e navegue até `http://localhost:<port>/swagger/v1/swagger.json`. O documento gerado que descreve os pontos de extremidade é exibido conforme é mostrado na [Especificação do Swagger (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).

A interface do usuário do Swagger pode ser encontrada em `http://localhost:<port>/swagger`. Explore a API por meio da interface do usuário do Swagger e incorpore-a em outros programas.

> [!TIP]
> Para atender à interface do usuário do Swagger na raiz do aplicativo (`http://localhost:<port>/`), defina a propriedade `RoutePrefix` como uma cadeia de caracteres vazia:
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

Se estiver usando diretórios com o IIS ou um proxy reverso, defina o ponto de extremidade do Swagger como um caminho relativo usando o prefixo `./`. Por exemplo, `./swagger/v1/swagger.json`. Usar o `/swagger/v1/swagger.json` instrui o aplicativo a procurar o arquivo JSON na raiz verdadeira da URL (mais o prefixo da rota, se usado). Por exemplo, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` em vez de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

## <a name="customize-and-extend"></a>Personalizar e estender

O Swagger fornece opções para documentar o modelo de objeto e personalizar a interface do usuário para corresponder ao seu tema.

Na classe `Startup`, adicione os seguintes namespaces:
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Descrição e informações da API

A ação de configuração passada para o método `AddSwaggerGen` adiciona informações como o autor, a licença e a descrição:

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

A interface do usuário do Swagger exibe as informações da versão:

![A interface do usuário do Swagger com informações de versão: descrição, autor e link veja mais](sample_images/custom-info.png)

### <a name="xml-comments"></a>comentários XML

Comentários XML podem ser habilitados com as seguintes abordagens:

#### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Editar <nome_do_projeto>.csproj**.
* Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No *Painel de Soluções*, pressione **control** e clique no nome do projeto. Navegue até **Ferramentas** > **Editar arquivo**.
* Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

A habilitação de comentários XML fornece informações de depuração para os membros e os tipos públicos não documentados. Os membros e tipos não documentados são indicados por mensagem de aviso. Por exemplo, a seguinte mensagem indica uma violação do código de aviso 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Para suprimir os avisos de todo o projeto, defina uma lista separada por ponto e vírgula dos códigos de aviso a serem ignorados no arquivo do projeto. Acrescentar os códigos de aviso ao `$(NoWarn);` também aplica os [valores padrão C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

Para suprimir avisos somente para membros específicos, coloque o código nas diretivas de pré-processador [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning). Essa abordagem é útil para o código que não deve ser exposto por meio dos documentos da API. No exemplo a seguir, o código de aviso CS1591 é ignorado para toda a classe de `Program`. A imposição do código de aviso é restaurada no fechamento da definição de classe. Especifique vários códigos de aviso com uma lista delimitada por vírgulas.

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

Configure o Swagger para usar o arquivo XML gerado com as instruções anteriores. Para sistemas operacionais Linux ou que não sejam Windows, os caminhos e nomes de arquivo podem diferenciar maiúsculas de minúsculas. Por exemplo, um arquivo *TodoApi.XML* é válido no Windows, mas não no CentOS.

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

No código anterior, a [Reflexão](/dotnet/csharp/programming-guide/concepts/reflection) é usada para criar um nome de arquivo XML correspondente ao do projeto de API Web. A propriedade [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) é usada para construir um caminho para o arquivo XML. Alguns recursos do Swagger (como os esquemas de parâmetros de entrada ou dos métodos HTTP e os códigos de resposta dos respectivos atributos) funcionam sem o uso de um arquivo de documentação XML. Para a maioria dos recursos, ou seja, resumos de método e descrições dos parâmetros e códigos de resposta, é obrigatório o uso de um arquivo XML.

Adicionar comentários de barra tripla a uma ação aprimora a interface do usuário do Swagger adicionando a descrição ao cabeçalho da seção. Adicione um elemento [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) acima da ação `Delete`:

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
A interface do usuário do Swagger exibe o texto interno do elemento `<summary>` do código anterior:

![A interface do usuário do Swagger, mostrando o comentário XML 'Exclui um TodoItem específico'. para o método DELETE](sample_images/triple-slash-comments.png)

A interface do usuário é controlada pelo esquema JSON gerado:

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```
Adicione um elemento [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) na documentação do método da ação `Create`. Ele complementa as informações especificadas no elemento `<summary>` e fornece uma interface de usuário do Swagger mais robusta. O conteúdo do elemento `<remarks>` pode consistir em texto, JSON ou XML.

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
Observe os aprimoramentos da interface do usuário com esses comentários adicionais:

![Interface do usuário do Swagger com comentários adicionais mostrados](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a>Anotações de dados

Marque o modelo com atributos, encontrado no namespace [System. ComponentModel. Annotations](/dotnet/api/system.componentmodel.dataannotations) , para ajudar a orientar os componentes da interface do usuário do Swagger.

Adicione o atributo `[Required]` à propriedade `Name` da classe `TodoItem`:

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

A presença desse atributo altera o comportamento da interface do usuário e altera o esquema JSON subjacente:

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

Adicione o atributo `[Produces("application/json")]` ao controlador da API. Sua finalidade é declarar que as ações do controlador permitem o tipo de conteúdo de resposta *application/json*:

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
A lista suspensa **Tipo de Conteúdo de Resposta** seleciona esse tipo de conteúdo como o padrão para ações GET do controlador:

![Interface do usuário do Swagger com o tipo de conteúdo de resposta padrão](sample_images/json-response-content-type.png)

À medida que aumenta o uso de anotações de dados na API Web, a interface do usuário e as páginas de ajuda da API se tornam mais descritivas e úteis.

### <a name="describe-response-types"></a>Descrever os tipos de resposta

Os desenvolvedores que usam uma API Web estão mais preocupados com o que é retornado&mdash;, especificamente, os tipos de resposta e os códigos de erro (se eles não forem padrão). Os tipos de resposta e os códigos de erro são indicados nos comentários XML e nas anotações de dados.

A ação `Create` retorna um código de status HTTP 201 em caso de sucesso. Um código de status HTTP 400 é retornado quando o corpo da solicitação postada é nulo. Sem a documentação adequada na interface do usuário do Swagger, o consumidor não tem conhecimento desses resultados esperados. Corrija esse problema adicionando as linhas realçadas no exemplo a seguir:

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

A interface do usuário do Swagger agora documenta claramente os códigos de resposta HTTP esperados:

![A interface do usuário do Swagger mostra a descrição da classe de resposta POST, 'Retorna o item de tarefa pendente recém-criado' e '400 – se o item for nulo' para o código de status e o motivo em Mensagens de Resposta](sample_images/data-annotations-response-types.png)

No ASP.NET Core 2.2 ou posterior, as convenções podem ser usadas como uma alternativa para decorar explicitamente as ações individuais com `[ProducesResponseType]`. Para obter mais informações, confira [Usar convenções da API Web](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).

Para obter informações sobre como personalizar a interface do usuário, consulte: [Personalizar a interface do usuário](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)
