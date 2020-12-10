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
   

  
  
  
