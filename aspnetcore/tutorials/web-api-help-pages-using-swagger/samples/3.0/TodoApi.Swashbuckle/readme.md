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
ms.openlocfilehash: d48288de90626ada83f5da1759f0057f0be46f19
ms.sourcegitcommit: f91d322f790123d41ec3271fa084ae20ed9f89a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155152"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="c37c6-102">Introdução ao Swashbuckle e ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c37c6-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="c37c6-103">Ao consumir uma API Web, entender seus vários métodos pode ser um desafio para um desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="c37c6-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="c37c6-104">O [Swagger](https://swagger.io/), também conhecido como [OpenAPI](https://www.openapis.org/), resolve o problema da geração de páginas de ajuda e de documentação úteis para APIs Web.</span><span class="sxs-lookup"><span data-stu-id="c37c6-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="c37c6-105">Ele oferece benefícios, como documentação interativa, geração de SDK de cliente e capacidade de descoberta de API.</span><span class="sxs-lookup"><span data-stu-id="c37c6-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="c37c6-106">Neste exemplo, é mostrada a implementação [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) do .NET.</span><span class="sxs-lookup"><span data-stu-id="c37c6-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="c37c6-107">Adicionar e configurar o middleware do Swagger</span><span class="sxs-lookup"><span data-stu-id="c37c6-107">Add and configure Swagger middleware</span></span>

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

<span data-ttu-id="c37c6-108">No método `Startup.Configure`, habilite o middleware para atender ao documento JSON gerado e à interface do usuário do Swagger:</span><span class="sxs-lookup"><span data-stu-id="c37c6-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

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

<span data-ttu-id="c37c6-109">A chamada do método `UseSwaggerUI` precedente habilita o [middleware de arquivos estáticos](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="c37c6-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="c37c6-110">Se você estiver direcionando ao .NET Framework ou ao .NET Core 1.x, adicione o pacote NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="c37c6-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="c37c6-111">Inicie o aplicativo e navegue até `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="c37c6-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="c37c6-112">O documento gerado que descreve os pontos de extremidade é exibido conforme é mostrado na [Especificação do Swagger (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="c37c6-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="c37c6-113">A interface do usuário do Swagger pode ser encontrada em `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="c37c6-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="c37c6-114">Explore a API por meio da interface do usuário do Swagger e incorpore-a em outros programas.</span><span class="sxs-lookup"><span data-stu-id="c37c6-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="c37c6-115">Para atender à interface do usuário do Swagger na raiz do aplicativo (`http://localhost:<port>/`), defina a propriedade `RoutePrefix` como uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="c37c6-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="c37c6-116">Se estiver usando diretórios com o IIS ou um proxy reverso, defina o ponto de extremidade do Swagger como um caminho relativo usando o prefixo `./`.</span><span class="sxs-lookup"><span data-stu-id="c37c6-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="c37c6-117">Por exemplo, `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="c37c6-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="c37c6-118">Usar o `/swagger/v1/swagger.json` instrui o aplicativo a procurar o arquivo JSON na raiz verdadeira da URL (mais o prefixo da rota, se usado).</span><span class="sxs-lookup"><span data-stu-id="c37c6-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="c37c6-119">Por exemplo, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` em vez de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="c37c6-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="c37c6-120">Personalizar e estender</span><span class="sxs-lookup"><span data-stu-id="c37c6-120">Customize and extend</span></span>

<span data-ttu-id="c37c6-121">O Swagger fornece opções para documentar o modelo de objeto e personalizar a interface do usuário para corresponder ao seu tema.</span><span class="sxs-lookup"><span data-stu-id="c37c6-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="c37c6-122">Na classe `Startup`, adicione os seguintes namespaces:</span><span class="sxs-lookup"><span data-stu-id="c37c6-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="c37c6-123">Descrição e informações da API</span><span class="sxs-lookup"><span data-stu-id="c37c6-123">API info and description</span></span>

<span data-ttu-id="c37c6-124">A ação de configuração passada para o método `AddSwaggerGen` adiciona informações como o autor, a licença e a descrição:</span><span class="sxs-lookup"><span data-stu-id="c37c6-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

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

<span data-ttu-id="c37c6-125">A interface do usuário do Swagger exibe as informações da versão:</span><span class="sxs-lookup"><span data-stu-id="c37c6-125">The Swagger UI displays the version's information:</span></span>

![A interface do usuário do Swagger com informações de versão: descrição, autor e link veja mais](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="c37c6-127">comentários XML</span><span class="sxs-lookup"><span data-stu-id="c37c6-127">XML comments</span></span>

<span data-ttu-id="c37c6-128">Comentários XML podem ser habilitados com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="c37c6-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c37c6-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c37c6-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c37c6-130">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Editar <nome_do_projeto>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="c37c6-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="c37c6-131">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="c37c6-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c37c6-132">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c37c6-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c37c6-133">No *Painel de Soluções*, pressione **control** e clique no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="c37c6-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="c37c6-134">Navegue até **Ferramentas** > **Editar arquivo**.</span><span class="sxs-lookup"><span data-stu-id="c37c6-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="c37c6-135">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="c37c6-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c37c6-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c37c6-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c37c6-137">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="c37c6-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="c37c6-138">A habilitação de comentários XML fornece informações de depuração para os membros e os tipos públicos não documentados.</span><span class="sxs-lookup"><span data-stu-id="c37c6-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="c37c6-139">Os membros e tipos não documentados são indicados por mensagem de aviso.</span><span class="sxs-lookup"><span data-stu-id="c37c6-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="c37c6-140">Por exemplo, a seguinte mensagem indica uma violação do código de aviso 1591:</span><span class="sxs-lookup"><span data-stu-id="c37c6-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="c37c6-141">Para suprimir os avisos de todo o projeto, defina uma lista separada por ponto e vírgula dos códigos de aviso a serem ignorados no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="c37c6-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="c37c6-142">Acrescentar os códigos de aviso ao `$(NoWarn);` também aplica os [valores padrão C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).</span><span class="sxs-lookup"><span data-stu-id="c37c6-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="c37c6-143">Para suprimir avisos somente para membros específicos, coloque o código nas diretivas de pré-processador [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning).</span><span class="sxs-lookup"><span data-stu-id="c37c6-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="c37c6-144">Essa abordagem é útil para o código que não deve ser exposto por meio dos documentos da API. No exemplo a seguir, o código de aviso CS1591 é ignorado para toda a classe de `Program`.</span><span class="sxs-lookup"><span data-stu-id="c37c6-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="c37c6-145">A imposição do código de aviso é restaurada no fechamento da definição de classe.</span><span class="sxs-lookup"><span data-stu-id="c37c6-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="c37c6-146">Especifique vários códigos de aviso com uma lista delimitada por vírgulas.</span><span class="sxs-lookup"><span data-stu-id="c37c6-146">Specify multiple warning codes with a comma-delimited list.</span></span>

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

<span data-ttu-id="c37c6-147">Configure o Swagger para usar o arquivo XML gerado com as instruções anteriores.</span><span class="sxs-lookup"><span data-stu-id="c37c6-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="c37c6-148">Para sistemas operacionais Linux ou que não sejam Windows, os caminhos e nomes de arquivo podem diferenciar maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="c37c6-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="c37c6-149">Por exemplo, um arquivo *TodoApi.XML* é válido no Windows, mas não no CentOS.</span><span class="sxs-lookup"><span data-stu-id="c37c6-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

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

<span data-ttu-id="c37c6-150">No código anterior, a [Reflexão](/dotnet/csharp/programming-guide/concepts/reflection) é usada para criar um nome de arquivo XML correspondente ao do projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="c37c6-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="c37c6-151">A propriedade [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) é usada para construir um caminho para o arquivo XML.</span><span class="sxs-lookup"><span data-stu-id="c37c6-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="c37c6-152">Alguns recursos do Swagger (como os esquemas de parâmetros de entrada ou dos métodos HTTP e os códigos de resposta dos respectivos atributos) funcionam sem o uso de um arquivo de documentação XML.</span><span class="sxs-lookup"><span data-stu-id="c37c6-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="c37c6-153">Para a maioria dos recursos, ou seja, resumos de método e descrições dos parâmetros e códigos de resposta, é obrigatório o uso de um arquivo XML.</span><span class="sxs-lookup"><span data-stu-id="c37c6-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="c37c6-154">Adicionar comentários de barra tripla a uma ação aprimora a interface do usuário do Swagger adicionando a descrição ao cabeçalho da seção.</span><span class="sxs-lookup"><span data-stu-id="c37c6-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="c37c6-155">Adicione um elemento [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) acima da ação `Delete`:</span><span class="sxs-lookup"><span data-stu-id="c37c6-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

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
<span data-ttu-id="c37c6-156">A interface do usuário do Swagger exibe o texto interno do elemento `<summary>` do código anterior:</span><span class="sxs-lookup"><span data-stu-id="c37c6-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![A interface do usuário do Swagger, mostrando o comentário XML 'Exclui um TodoItem específico'.](sample_images/triple-slash-comments.png)

<span data-ttu-id="c37c6-159">A interface do usuário é controlada pelo esquema JSON gerado:</span><span class="sxs-lookup"><span data-stu-id="c37c6-159">The UI is driven by the generated JSON schema:</span></span>

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
<span data-ttu-id="c37c6-160">Adicione um elemento [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) na documentação do método da ação `Create`.</span><span class="sxs-lookup"><span data-stu-id="c37c6-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="c37c6-161">Ele complementa as informações especificadas no elemento `<summary>` e fornece uma interface de usuário do Swagger mais robusta.</span><span class="sxs-lookup"><span data-stu-id="c37c6-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="c37c6-162">O conteúdo do elemento `<remarks>` pode consistir em texto, JSON ou XML.</span><span class="sxs-lookup"><span data-stu-id="c37c6-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

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
<span data-ttu-id="c37c6-163">Observe os aprimoramentos da interface do usuário com esses comentários adicionais:</span><span class="sxs-lookup"><span data-stu-id="c37c6-163">Notice the UI enhancements with these additional comments:</span></span>

![Interface do usuário do Swagger com comentários adicionais mostrados](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="c37c6-165">Anotações de dados</span><span class="sxs-lookup"><span data-stu-id="c37c6-165">Data annotations</span></span>

<span data-ttu-id="c37c6-166">Decore o modelo com atributos, encontrados no namespace [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations), para ajudar a gerar os componentes da interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="c37c6-166">Decorate the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="c37c6-167">Adicione o atributo `[Required]` à propriedade `Name` da classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="c37c6-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

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

<span data-ttu-id="c37c6-168">A presença desse atributo altera o comportamento da interface do usuário e altera o esquema JSON subjacente:</span><span class="sxs-lookup"><span data-stu-id="c37c6-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

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

<span data-ttu-id="c37c6-169">Adicione o atributo `[Produces("application/json")]` ao controlador da API.</span><span class="sxs-lookup"><span data-stu-id="c37c6-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="c37c6-170">Sua finalidade é declarar que as ações do controlador permitem o tipo de conteúdo de resposta *application/json*:</span><span class="sxs-lookup"><span data-stu-id="c37c6-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="c37c6-171">A lista suspensa **Tipo de Conteúdo de Resposta** seleciona esse tipo de conteúdo como o padrão para ações GET do controlador:</span><span class="sxs-lookup"><span data-stu-id="c37c6-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interface do usuário do Swagger com o tipo de conteúdo de resposta padrão](sample_images/json-response-content-type.png)

<span data-ttu-id="c37c6-173">À medida que aumenta o uso de anotações de dados na API Web, a interface do usuário e as páginas de ajuda da API se tornam mais descritivas e úteis.</span><span class="sxs-lookup"><span data-stu-id="c37c6-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="c37c6-174">Descrever os tipos de resposta</span><span class="sxs-lookup"><span data-stu-id="c37c6-174">Describe response types</span></span>

<span data-ttu-id="c37c6-175">Os desenvolvedores que usam uma API Web estão mais preocupados com o que é retornado&mdash;, especificamente, os tipos de resposta e os códigos de erro (se eles não forem padrão).</span><span class="sxs-lookup"><span data-stu-id="c37c6-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="c37c6-176">Os tipos de resposta e os códigos de erro são indicados nos comentários XML e nas anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="c37c6-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="c37c6-177">A ação `Create` retorna um código de status HTTP 201 em caso de sucesso.</span><span class="sxs-lookup"><span data-stu-id="c37c6-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="c37c6-178">Um código de status HTTP 400 é retornado quando o corpo da solicitação postada é nulo.</span><span class="sxs-lookup"><span data-stu-id="c37c6-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="c37c6-179">Sem a documentação adequada na interface do usuário do Swagger, o consumidor não tem conhecimento desses resultados esperados.</span><span class="sxs-lookup"><span data-stu-id="c37c6-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="c37c6-180">Corrija esse problema adicionando as linhas realçadas no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="c37c6-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="c37c6-181">A interface do usuário do Swagger agora documenta claramente os códigos de resposta HTTP esperados:</span><span class="sxs-lookup"><span data-stu-id="c37c6-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![A interface do usuário do Swagger mostra a descrição da classe de resposta POST, 'Retorna o item de tarefa pendente recém-criado' e '400 – se o item for nulo' para o código de status e o motivo em Mensagens de Resposta](sample_images/data-annotations-response-types.png)

<span data-ttu-id="c37c6-183">No ASP.NET Core 2.2 ou posterior, as convenções podem ser usadas como uma alternativa para decorar explicitamente as ações individuais com `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="c37c6-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="c37c6-184">Para obter mais informações, confira [Usar convenções da API Web](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="c37c6-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="c37c6-185">Para obter informações sobre como personalizar a interface do usuário, consulte: [Personalizar a interface do usuário](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span><span class="sxs-lookup"><span data-stu-id="c37c6-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
