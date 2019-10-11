# <a name="upload-files-sample-app"></a>Aplicativo de exemplo de upload de arquivos

Este aplicativo de exemplo demonstra os conceitos descritos no tópico [carregar arquivos no ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads) .

## <a name="security-considerations"></a>Considerações sobre segurança

Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor. Os invasores podem executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) , tentar carregar vírus ou malware ou tentar comprometer redes e servidores de outras maneiras.

As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:

* Carregue arquivos em uma área de carregamento de arquivo dedicada no sistema, preferencialmente para uma unidade que não seja do sistema. O uso de um local dedicado torna mais fácil impor medidas de segurança em arquivos carregados. Desabilite as permissões de execução no local de carregamento do arquivo. &dagger;
* Nunca persista arquivos carregados na mesma árvore de diretório que o aplicativo. &dagger;
* Use um nome de arquivo seguro determinado pelo aplicativo. Não use um nome de arquivo fornecido pela entrada do usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger;
* Permitir apenas um conjunto específico de extensões de arquivo aprovadas. &dagger;
* Verifique a assinatura de formato de arquivo para impedir que um usuário carregue um arquivo mascarado (por exemplo, carregando um arquivo *. exe* com uma extensão *. txt* ). &dagger;
* Verifique se as verificações do lado do cliente também são executadas no servidor. Verificações no lado do cliente são fáceis de evitar. &dagger;
* Verifique o tamanho do upload e evite carregamentos maiores que o esperado. &dagger;
* Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.
* **Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**

o aplicativo de exemplo &dagger;The demonstra uma abordagem que atende aos critérios.

> [!WARNING]
> Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:
>
> * Tomar o controle total de um sistema.
> * Sobrecarregar um sistema com o resultado que o sistema falha.
> * Comprometer dados do sistema ou de usuários.
> * Aplique graffiti a uma interface do usuário pública.
>
> Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:
>
> * [Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)
> * Segurança de @no__t 0Azure: Verifique se os controles apropriados estão em vigor ao aceitar arquivos de usuários @ no__t-0

Para obter informações adicionais, consulte [carregar arquivos em ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads).

## <a name="how-to-use-the-sample"></a>Como usar o exemplo

No arquivo *appSettings. JSON* :

1. Defina o caminho para arquivos armazenados (`StoredFilesPath`).

   * O aplicativo de exemplo define o valor como `c:\\files`, o que pressupõe que uma pasta chamada *files* existe na raiz da unidade C: do sistema.
   * O caminho deve existir. Crie uma pasta *files* na unidade C: do sistema ou defina o caminho para um local adequado.
   * O processo do aplicativo requer permissões de leitura/gravação para o caminho.
   * **FUNDAMENTAL!** Desabilite as permissões de execução para todos os usuários no caminho.

1. Defina o limite de tamanho do arquivo (`FileSizeLimit`) em bytes. O valor padrão do aplicativo de exemplo de `2097152` (2.097.152 bytes) permite carregamentos de arquivo de até 2 MB.
