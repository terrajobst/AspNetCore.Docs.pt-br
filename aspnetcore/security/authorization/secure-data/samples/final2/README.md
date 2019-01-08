# <a name="how-to-buildrun-secure-user-data-sample"></a>Como exemplo de dados de usuário segura de build/execução

* Definir a senha com a ferramenta Secret Manager:

  `dotnet user-secrets set SeedUserPW <pw>`

* Atualize o banco de dados:

    `dotnet ef database update`

* Habilitar o HTTPS no projeto
