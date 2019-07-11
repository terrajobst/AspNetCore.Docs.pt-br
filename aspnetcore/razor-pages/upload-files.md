---
title: Carregar arquivos para uma Página Razor no ASP.NET Core
author: guardrex
description: Saiba como carregar arquivos em uma página Razor no ASP.NET Core usando a classe FileUpload.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 11/10/2018
ms.custom: mvc, seodec18
uid: razor-pages/upload-files
ms.openlocfilehash: 14d10424951e8ec3c7909d001c6f86e5fcb45d26
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815039"
---
# <a name="upload-files-to-a-razor-page-in-aspnet-core"></a>Carregar arquivos para uma Página Razor no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Este tópico tem como base o [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) em <xref:tutorials/razor-pages/razor-pages-start>.

Este tópico mostra como usar a associação de modelos simples para carregar arquivos, o que funciona bem para carregar arquivos pequenos. Para obter informações sobre a transmissão de arquivos grandes, consulte [Carregando arquivos grandes com streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).

Nas etapas a seguir, um recurso de upload de arquivo da agenda de filmes será adicionado ao aplicativo de exemplo. Um agendamento de filmes é representado por uma classe `Schedule`. A classe inclui duas versões do agendamento. Uma versão é fornecida aos clientes, `PublicSchedule`. A outra versão é usada para os funcionários da empresa, `PrivateSchedule`. Cada versão é carregada como um arquivo separado. O tutorial demonstra como executar dois carregamentos de arquivos de uma página com um único POST para o servidor.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/upload-files/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Considerações sobre segurança

É preciso ter cuidado ao fornecer aos usuários a possibilidade de carregar arquivos em um servidor. Os invasores podem executar uma [negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) e outros ataques em um sistema. Algumas etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:

* Fazer o upload de arquivos para uma área de upload de arquivos dedicada no sistema, o que facilita a aplicação de medidas de segurança sobre o conteúdo carregado. Ao permitir os uploads de arquivos, certifique-se de que as permissões de execução estejam desabilitadas no local do upload.
* Use um nome de arquivo seguro determinado pelo aplicativo, não da entrada do usuário ou o nome do arquivo carregado.
* Permitir somente um conjunto específico de extensões de arquivo aprovadas.
* Certifique-se de que as verificações do lado do cliente sejam realizadas no servidor. As verificações do lado do cliente são fáceis de contornar.
* Verifique o tamanho do upload e evite uploads maiores do que o esperado.
* Execute um verificador de vírus/malware no conteúdo carregado.

> [!WARNING]
> Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:
> * Tomar o controle total de um sistema.
> * Sobrecarregar um sistema, fazendo com que ele falhe completamente.
> * Comprometer dados do sistema ou de usuários.
> * Aplicar pichações a uma interface pública.

## <a name="add-a-fileupload-class"></a>Adicionar uma classe FileUpload

Criar uma Página Razor para lidar com um par de carregamentos de arquivos. Adicione uma classe `FileUpload`, que é vinculada à página para obter os dados do agendamento. Clique com o botão direito do mouse na pasta *Modelos*. Selecione **Adicionar** > **Classe**. Nomeie a classe **FileUpload** e adicione as seguintes propriedades:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

A classe tem uma propriedade para o título do agendamento e uma propriedade para cada uma das duas versões do agendamento. Todas as três propriedades são necessárias e o título deve ter 3-60 caracteres.

## <a name="add-a-helper-method-to-upload-files"></a>Adicionar um método auxiliar para carregar arquivos

Para evitar duplicação de código para processar arquivos do agendamento carregados, primeiro, adicione um método auxiliar estático. Crie uma pasta de *Utilitários* no aplicativo e adicione um arquivo *FileHelpers.cs* com o seguinte conteúdo. O método auxiliar, `ProcessFormFile`, usa um [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) e [ModelStateDictionary](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) e retorna uma cadeia de caracteres que contém o tamanho e o conteúdo do arquivo. O comprimento e o tipo de conteúdo são verificados. Se o arquivo não passar em uma verificação de validação, um erro será adicionado ao `ModelState`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

### <a name="save-the-file-to-disk"></a>Salvar o arquivo no disco

O aplicativo de exemplo salva os arquivos carregados em campos de banco de dados. Para salvar um arquivo no disco, use um [FileStream](/dotnet/api/system.io.filestream). O exemplo a seguir copia um arquivo mantido por `FileUpload.UploadPublicSchedule` para um `FileStream` em um método `OnPostAsync`. O `FileStream` grava o arquivo no disco no `<PATH-AND-FILE-NAME>` fornecido:

```csharp
public async Task<IActionResult> OnPostAsync()
{
    // Perform an initial check to catch FileUpload class attribute violations.
    if (!ModelState.IsValid)
    {
        return Page();
    }

    var filePath = "<PATH-AND-FILE-NAME>";

    using (var fileStream = new FileStream(filePath, FileMode.Create))
    {
        await FileUpload.UploadPublicSchedule.CopyToAsync(fileStream);
    }

    return RedirectToPage("./Index");
}
```

O processo de trabalho deve ter permissões de gravação para o local especificado por `filePath`.

> [!NOTE]
> O `filePath` *precisa* incluir o nome do arquivo. Se o nome do arquivo não for fornecido, uma [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) será gerada no tempo de execução.

> [!WARNING]
> Nunca persista os arquivos carregados na mesma árvore de diretório que o aplicativo.
>
> O exemplo de código não oferece proteção do lado do servidor contra carregamentos de arquivos mal-intencionados. Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:
>
> * [Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)
> * [Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

### <a name="save-the-file-to-azure-blob-storage"></a>Salvar o arquivo no Armazenamento de Blobs do Azure

Para carregar o conteúdo do arquivo para o Armazenamento de Blobs do Azure, confira [Introdução ao Armazenamento de Blobs do Azure usando o .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs). O tópico demonstra como usar [UploadFromStream](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync) para salvar um [FileStream](/dotnet/api/system.io.filestream) para armazenamento de blobs.

## <a name="add-the-schedule-class"></a>Adicionar a classe de Agendamento

Clique com o botão direito do mouse na pasta *Modelos*. Selecione **Adicionar** > **Classe**. Nomeie a classe **Agendamento** e adicione as seguintes propriedades:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

Usa a classe usa os atributos `Display` e `DisplayFormat`, que produzem formatação e títulos fáceis quando os dados de agendamento são renderizados.

::: moniker range=">= aspnetcore-2.1"

## <a name="update-the-razorpagesmoviecontext"></a>Atualizar o RazorPagesMovieContext

Especifique um `DbSet` no `RazorPagesMovieContext` (*Data/RazorPagesMovieContext.cs*) para os agendamentos:

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Data/RazorPagesMovieContext.cs?highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="update-the-moviecontext"></a>Atualizar o MovieContext

Especifique um `DbSet` no `MovieContext` (*Models/MovieContext.cs*) para os agendamentos:

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

::: moniker-end

## <a name="add-the-schedule-table-to-the-database"></a>Adicione a tabela de Agendamento ao banco de dados

Abra o Console do Gerenciador de pacote (PMC): **Ferramentas** > **Gerenciador de pacotes NuGet** > **Package Manager Console**.

![Menu do PMC](upload-files/_static/pmc.png)

No PMC, execute os seguintes comandos. Estes comandos adicionam uma tabela `Schedule` ao banco de dados:

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-file-upload-razor-page"></a>Adicionar uma página do Razor de upload de arquivo

Na pasta *Páginas*, crie uma pasta *Agendamentos*. Na pasta *Agendamentos*, crie uma página chamada *Index.cshtml* para carregar um agendamento com o seguinte conteúdo:

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

Cada grupo de formulário inclui um **\<rótulo>** que exibe o nome de cada propriedade de classe. Os atributos `Display` no modelo `FileUpload` fornecem os valores de exibição para os rótulos. Por exemplo, o nome de exibição da propriedade `UploadPublicSchedule` é definido com `[Display(Name="Public Schedule")]` e, portanto, exibe "Agendamento público" no rótulo quando o formulário é renderizado.

Cada grupo de formulário inclui uma validação **\<span>** . Se a entrada do usuário não atender aos atributos de propriedade definidos na classe `FileUpload` ou se qualquer uma das verificações de validação do arquivo de método `ProcessFormFile` falhar, o modelo não será validado. Quando a validação do modelo falha, uma mensagem de validação útil é renderizada para o usuário. Por exemplo, a propriedade `Title` é anotada com `[Required]` e `[StringLength(60, MinimumLength = 3)]`. Se o usuário não fornecer um título, ele receberá uma mensagem indicando que um valor é necessário. Se o usuário inserir um valor com menos de três caracteres ou mais de sessenta, ele receberá uma mensagem indicando que o valor tem um comprimento incorreto. Se um arquivo que não tem nenhum conteúdo for fornecido, uma mensagem aparecerá indicando que o arquivo está vazio.

## <a name="add-the-page-model"></a>Adicionar o modelo de página

Adicione o modelo de página (*Index.cshtml.cs*) à pasta *Schedules*:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

O modelo de página (`IndexModel` no *Index.cshtml.cs*) associa a classe `FileUpload`:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

O modelo também usa uma lista dos agendamentos (`IList<Schedule>`) para exibir os agendamentos armazenados no banco de dados na página:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

Quando a página for carregada com `OnGetAsync`, `Schedules` é preenchido com o banco de dados e usado para gerar uma tabela HTML de agendamentos carregados:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

Quando o formulário é enviado para o servidor, o `ModelState` é verificado. Se for inválido, `Schedule` é recriado e a página é renderizada com uma ou mais mensagens de validação informando por que a validação de página falhou. Se for válido, as propriedades `FileUpload` serão usadas em *OnPostAsync* para concluir o upload do arquivo para as duas versões do agendamento e criar um novo objeto `Schedule` para armazenar os dados. O agendamento, em seguida, é salvo no banco de dados:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

## <a name="link-the-file-upload-razor-page"></a>Vincular a página do Razor de upload de arquivo

Abra *Pages/Shared/_Layout.cshtml* e adicione um link para a barra de navegação para acessar a página de Agendas:

```cshtml
<div class="navbar-collapse collapse">
    <ul class="nav navbar-nav">
        <li><a asp-page="/Index">Home</a></li>
        <li><a asp-page="/Schedules/Index">Schedules</a></li>
        <li><a asp-page="/About">About</a></li>
        <li><a asp-page="/Contact">Contact</a></li>
    </ul>
</div>
```

## <a name="add-a-page-to-confirm-schedule-deletion"></a>Adicionar uma página para confirmar a exclusão de agendamento

Quando o usuário clica para excluir um agendamento, é oferecida uma oportunidade de cancelar a operação. Adicione uma página de confirmação de exclusão (*Delete.cshtml*) à pasta *Agendamentos*:

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

O modelo de página (*Delete.cshtml.cs*) carrega um único agendamento identificado por `id` nos dados de rota da solicitação. Adicione o arquivo *Delete.cshtml.cs* à pasta *Agendamentos*:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

O método `OnPostAsync` lida com a exclusão do agendamento pelo seu `id`:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

Após a exclusão com êxito do agendamento, o `RedirectToPage` envia o usuário para a página *Index.cshtml* dos agendamentos.

## <a name="the-working-schedules-razor-page"></a>A página de Razor de agendamentos de trabalho

Quando a página é carregada, os rótulos e entradas para o título do agendamento, o agendamento público e o agendamento privado são renderizados com um botão de envio:

![Agenda a página de Razor como visto no carregamento inicial sem erros de validação e campos vazios](upload-files/_static/browser1.png)

Selecionar o botão **Upload** sem preencher nenhum dos campos viola os atributos `[Required]` no modelo. O `ModelState` é inválido. As mensagens de erro de validação são exibidas para o usuário:

![As mensagens de erro de validação aparecem ao lado de cada controle de entrada](upload-files/_static/browser2.png)

Digite duas letras no campo de **Título**. A mensagem de validação muda para indicar que o título deve ter entre 3 e 60 caracteres:

![Mensagem de validação de título alterada](upload-files/_static/browser3.png)

Quando um ou mais agendamentos são carregados, a seção **Agendamentos carregados** renderiza os agendamentos carregados:

![Tabela de agendamentos carregados, mostrando o título de cada agendamento, dados carregados em UTC, tamanho do arquivo de versão pública e tamanho do arquivo de versão privada](upload-files/_static/browser4.png)

O usuário pode clicar no link **Excluir** para chegar à exibição de confirmação de exclusão, na qual ele tem a oportunidade de confirmar ou cancelar a operação de exclusão.

## <a name="troubleshooting"></a>Solução de problemas

Para solucionar problemas de informações com `IFormFile` carregar, consulte [uploads de arquivos no ASP.NET Core: Solução de problemas](xref:mvc/models/file-uploads#troubleshooting).
