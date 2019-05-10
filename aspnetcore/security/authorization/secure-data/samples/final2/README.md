# <a name="how-to-buildrun-secure-user-data-sample"></a><span data-ttu-id="349b6-101">Como exemplo de dados de usuário segura de build/execução</span><span class="sxs-lookup"><span data-stu-id="349b6-101">How to build/run Secure user data sample</span></span>

* <span data-ttu-id="349b6-102">Definir a senha com a ferramenta Secret Manager:</span><span class="sxs-lookup"><span data-stu-id="349b6-102">Set password with the Secret Manager tool:</span></span>

  `dotnet user-secrets set SeedUserPW <pw>`

* <span data-ttu-id="349b6-103">Atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="349b6-103">Update the database:</span></span>

  `dotnet ef database update`

* <span data-ttu-id="349b6-104">Habilitar o HTTPS no projeto</span><span class="sxs-lookup"><span data-stu-id="349b6-104">Enable HTTPS in the project</span></span>
