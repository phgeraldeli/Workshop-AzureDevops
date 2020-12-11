# Workshop-AzureDevops

Esse é um workshop disponibilizado pela microsoft, cujo objetivo é executar uma prova de conceito com:

  * Repositório PartsUnlimited .NET Core
  * git
  * ARM Templates
  * CI/CD on Azure Devops
  * Azure Repos Service
  * Azure Pipelines Service
  * Application Insights Service

Para mais informações, pode acessar o link [***microsoft/activate-azure-with-devops***](https://github.com/microsoft/Activate-Azure-for-DevOps/blob/master/Datasheet/Activate_Azure_with_DevOps_Datasheet.pdf)

Ps: Esse repositório é apenas um guia, para conseguir realizar as etapas com melhor conhecimento, faça os labs:

Git + Azure
https://azuredevopslabs.com/labs/azuredevops/git/#exercise-2-cloning-an-existing-repository
https://azuredevopslabs.com/labs/azuredevops/pullrequests/

CI on Azure
https://azuredevopslabs.com/labs/azuredevops/continuousintegration/

CD on Azure
https://azuredevopslabs.com/labs/azuredevops/continuousdeployment/

ARM templates
https://docs.microsoft.com/en-us/learn/modules/build-azure-vm-templates/

Agile 
https://azuredevopslabs.com/labs/azuredevops/agile/

Sonarqube + Azure
https://azuredevopslabs.com/labs/vstsextend/sonarqube/

Usando secrets no Azure Vault
https://azuredevopslabs.com/labs/vstsextend/azurekeyvault/

Monitoramento de performance de aplicações
https://azuredevopslabs.com/labs/azuredevops/appinsights/


## Utilizando repos do Azure Devops.

  - Crie o projeto no Azure Devops
  - Faça o download do repositório no [***link***](https://github.com/microsoft/Activate-Azure-for-DevOps/blob/master/PoC/Full/Assets/PartsUnlimited.zip)
  - Para adicionar o repositório no Azure Devops siga os passos abaixo:
  
    1. Vá em Repos -> Files -> New 
    2. Vá no seu projeto e digite os comandos  
    
          ```
          git init
          ```
          ```
          git remote add origin **url**
          ```
          ```
          git push -u origin --all
          ```
    
## Criando o pipeline inicial

  - Acesse o menu Pipelines -> Pipelines
  - Clique em **Create Pipeline**, selecione **classic editor mode**
  - Coloque as configurações do seu repositório, e clique em **continue**
  - Utilize o template **ASP.NET Core-CI** e clique em **save and queue**
  - Acompanhe o processo de build em *Pipelines/Pipelines*
  - Após o sucesso, acesse o pipeline novamente, e altere o nome do pacote na etapa de *publish artifacts*.
  - Crie uma nova etapa de *publish artifact* com as configurações
  ```
    Path to publish: env/Templates
    Artifact name: Nome do artefato
  ```
  - Adicione uma etapa chamada *Copy Files* com as configurações
  
      ```
         Contents: *.dacpac
         Target Folder: env/Templates
      ```
  - Mova a tarefa de Copy Files para antes da etapa de publish artifact
  - Clique novamente em **save and queue** e acompanhe o build.
  
## Adicionando grunt no pipeline

  - Acesse novamente o pipeline, e crie uma etapa **grunt** com as configurações:
  
  ```
    Grunt File Path: src/PartsUnlimitedWebsite/gruntfile.js
  ```
  - Mova a etapa **grunt** para depois da etapa de **Build**.
  
## (Opcional) Criando Tasks Groups

  - Acesse Pipelines/Pipelines e clique no pipeline criado.
  - Selecione as etapas de Build e Teste e crie um task group com o nome de Build and Test.
  - Selecione as etapas de Copy Files, Publish Artifact e crie outro task group com nome de Publish Artifacts
  
   Obs: caso não funcione, é necessário executar o **unlink all** de todas as etapas no atributo **Parameters** no Pipeline. 
   
   A funcionalidade de grupos é interessante pois permite o versionamento do grupo, logo uma modificação no grupo de Test não afetaria o grupo de Deploy etc.
   
## Criando uma conexão com o Azure Portal

  - Vá em Project settings e selecione **Service connections**
  - Selecione **Azure Resource Manager** e crie um **Service Principal** 
  - Faça o login e crie a conexão
 
## Criando uma release

  - Crie um resource group no Azure Portal
  - Volte para o AzureDevops e vá em Pipeline/Releases para criar uma Release
  - Mude o nome da fase para infraestrutura (Nessa etapa iremos criar toda a infraestrutura atraves de um template ARM
  - No artefato selecione como source o pipeline que criamos.
  - Acesse as configurações da primeira etapa na release.
  - Crie um stage chamado ARM Template utilizando a configuração:
  ```
  Template: $(System.DefaultWorkingDirectory)/_POC-ASP.NET Core-CI/arm-template/FullEnvironmentSetupMerged.json
  Template Parameters: $(System.DefaultWorkingDirectory)/_POC-ASP.NET Core-CI/arm-template/FullEnvironmentSetupMerged.param.json
```
  - Utilize a conexão criada na etapa anterior.
  - Preencha o override template parameters, lembrando que website tem que ser unico e o resource group o que foi criado.
  - Execute a release, e espere a criação de todos os recursos no azure (pode demorar bastante).
  
## (Opcional) Criando um mapa de variaveis


  - Na release, selecione a aba **Variables**.
  - Crie uma variável para cada uma utilizada no template que utilizamos na release.
  - Substitua as variaveis utilizadas no Override Template Variables por suas respectivas variaveis no mapa (por exemplo: "$(var)").
  
## Configurando o slot de ambiente

  - Volte na release no Azure Devops, cria um novo pipeline dentro da release, de o nome de Dev.
  - Adicione a etapa Deploy azure to App Service
  - Ative a opção **Deploy to Slot or App Service Environment**
  - Selecione o Resource group criado e o Slot de Dev.
  - No Pipeline **infraestrutura** crie uma nova etapa chamada **Azure SQL dacpac Task**
  - Na configuração utilize as variáveis criadas no mapa (por exemplo: Azure SQL Server: $(VPocserversql)dev.database.windows.net).
  - Execute a release novamente.
  
## Criando outras releases

  - Utilize a mesma ideia para criar as releases de staging e produção(sem slot), porém realize o dacpac nesses ambientes junto da etapa corrente, devido ao fato do dacpac atualizar o banco de dados, assim devemos apenas atualizar o banco durante a etapa de deploy daquele ambiente.
  
  
## Application Insights

  - Verifique se o application insights estão com as chaves corretas. (Como utilizamos a criação da chave automatica, já deve estar configurada)
  - Assim podemos ter alguns insights sobre a aplicação e seus usuários. 

