# <a name="custom-model-binding-demo"></a>Demonstração de model binding personalizado

Teste o `ByteArrayModelBinder` executando o aplicativo e postando uma cadeia de caracteres codificada em Base64 no ponto de extremidade `ImageController` (`/api/image/`). Especifique as propriedades de arquivo e de nome de arquivo no corpo da solicitação como dados de formulário (usando o [Postman](https://www.getpostman.com/) ou uma ferramenta semelhante). Use [esta cadeia de caracteres de exemplo](Base64String.txt). O resultado é salvo na pasta *wwwroot/images/upload* com o nome do arquivo especificado.

Para testar o exemplo de associação personalizada, experimente os seguintes pontos de extremidade:

* /api/authors/1
* /api/authors/2 (NOT FOUND)
* /api/boundauthors/1
* /api/boundauthors/2 (NOT FOUND)
* /api/boundauthors/get/1
* /api/boundauthors/get/2 (NO CONTENT) &ndash; Essa ação não verifica se há nulos e retorna um *404 Não Encontrado*.
