# SonarQube

## SonarQube Arquitetura

1. Suporta diversas linguagens
2. É possível utilizar um ou mais SonarScanners para análise dos projetos e para integração com servidores de Integração Contínua
3. Vários plugins podem ser instalados, melhorando ainda mais as necessidades
4. No banco de dados criado são armazenadas as configurações e os dados de análises dos projetos

1. Os desenvolvedores codificam na IDE de sua preferência e usam o SonarLint para executar a análise local.
2. Desenvolvedores fazem Check In/Push do código para o controle de versão (git, SVN, TFVC, …)
3. O Servidor de Integração Contínua (se configurado) aciona uma construção automática e a execução do SonarScanner necessária para executar a análise do SonarQube
4. O relatório de análise é enviado ao SonarQube Server para processamento.
5. O SonarQube Server processa e armazena os resultados do relatório de análise no banco de dados do SonarQube e exibe os resultados na interface do usuário.
6. Os desenvolvedores revisam e discutem os seus problemas, e traçam uma estratégia para gerenciar e reduzir sua dívida técnica auxiliados pela visibilidade das métricas do SonarQube.
7. Utilizando APIs do sonar é possível extrair dados, gerar relatórios e automatizar configurações.

## Configuração

Você pode fazer a análise do seu projeto utilizando o SonarQube já disponível nos nossos servidores, ou pode subir uma instância local utilizando ou não o docker, para fazer uma análise prévia.

Sonar Server

Para visualizar os projetos que já estão rodando ou adicionar seu projeto que está no TFS acesse: [http://qa-quali2012:9000/](http://qa-quali2012:9000/) (SonarQube 6.7 - Stable)

Sonar Local

Para subir uma instância com **Docker** :

1. No PowerShell verifique se possui o Docker instalado

docker -v

1. Caso não possua, efetue o [download](https://docs.docker.com/docker-for-windows/) e siga o passo a passo indicado para instalação.
2. Depois, baixe a imagem do sonar docker pull sonarqube
3. Execute o container utilizando a porta 9000 docker run -d --name sonarqube -p 9000:9000 sonarqube
4. Faça o login em [http://localhost:9000](http://localhost:9000) com as credenciais de administrador do sistema (_admin / admin_) e siga o tutorial para analisar seu projeto.
5. Na tela de projetos, selecione a opção Generate Token, preenchendo o campo com um nome de token de sua escolha. Guarde este token, já que ele será utilizado para utilizar a ferramenta posteriormente.
6. Para mais informações acesse: [https://hub.docker.com/\_/sonarqube](https://hub.docker.com/_/sonarqube)

Para subir uma instância localmente siga o passo-a-passo abaixo:

1. O único pré-requisito para executar o SonarQube é ter o Java instalado em sua máquina. Para maiores detalhes veja o item Java neste manual.
2. Faça o [download](https://www.sonarqube.org/downloads/)da versão community do SonarQube
3. Descompacte-o em C:\sonarqube ou no diretório desejado
4. Inicie o SonarQube Server
  1. No Windows, execute:

C:\sonarqube\bin\windows-x86-xx\StartSonar.bat

1. Na tela de projetos, selecione a opção Generate Token, preenchendo o campo com um nome de token de sua escolha. Guarde este token, já que ele será utilizado para utilizar a ferramenta posteriormente.
2. Faça o login em [http://localhost:9000](http://localhost:9000) com as credenciais de administrador do sistema (admin / admin) e siga o tutorial para analisar seu projeto.

## Analisando o Código Fonte

Após definir se seu projeto será analisado localmente ou no servidor já disponível, você estará pronto para instalar um analisador e começar a criar projetos.

Mas antes, vamos entender alguns dos parâmetros mais utilizados:

| **Chave** | **Descrição** | **Padrão** | **Obrigatório** |
| --- | --- | --- | --- |
| sonar.host.url | URL do servidor | [http://localhost:9000](http://localhost:9000/) | sim |
| sonar.projectKey  | A chave exclusiva do projeto. Os caracteres permitidos são: letras, números, -, \_,. e: |   | sim |
| sonar.projectName | Nome do projeto que será exibido na interface da web |   | não |
| sonar.login | O token de autenticação de um usuário do SonarQube com a permissão executar análise no projeto. |   | não |
| sonar.exclusions | Excluir arquivos e/ou diretórios da análise | Veja exemplos abaixo: | não |

Obs1.: Para compartilhar os parâmetros de análise entre um subconjunto de projetos, configure os valores no arquivo SonarQube.Analysis.xml encontrado no diretório do SonarScanner.

Obs 2.: Para ver mais parâmetros que podem ser utilizados, acesse a documentação oficial em: [https://docs.sonarqube.org/latest/analysis/analysis-parameters/](https://docs.sonarqube.org/latest/analysis/analysis-parameters/)



sonar.exclusions Exemplos:

# Excluir todas as classes terminadas por &quot;Bean&quot;

sonar.exclusions=\*\*/\* Bean.java, \*\*/\*DTO.java

# Excluir todas as classes no diretório &quot;src/main/java/org/sonar&quot;

sonar.exclusions=src/main/java/org/sonar/\*

# Excluir todos os arquivos no diretório &quot;dist&quot; e seus subdiretórios

sonar.exclusions=site/dist/\*\*

# Excluir todos os arquivos no diretório e subdiretórios de &quot;bank&quot; cuja extensão é .js

sonar.exclusions=bank/\*\*/\*.js

Mais detalhes e exemplos em: [https://docs.sonarqube.org/latest/project-administration/narrowing-the-focus/](https://docs.sonarqube.org/latest/project-administration/narrowing-the-focus/)

Mesmo que você não possua uma esteira de entrega contínua é possível analisar o código, desde que ele esteja atualizado na sua máquina.

Para fazer isso, você deve instalar e configurar o scanner mais adequado às suas necessidades:

- MSBuild para .NET Framework 4.6+
- MSBuild para .NET Core 2.0+
- SonarScanner para outros

## MSBuild .NET Framework 4.6+

1. Faça o [download](https://github.com/SonarSource/sonar-scanner-msbuild/releases/download/4.5.0.1761/sonar-scanner-msbuild-4.5.0.1761-net46.zip)do SonarScanner para MSBuild .Net Framework 4.6+
2. Descompacte-o em c:\sonarscanner-msbuild, por exemplo
3. No PowerShell, acesse o diretório onde consta a sua aplicação
4. Execute os comandos abaixo para executar a análise substituindo os \&lt;parâmetros\&gt; necessários:
  1. **Begin** : Conecta-se ao MSBuild, faz o download dos perfis de qualidade do SonarQube, as configurações e prepara seu projeto para a análise.

\&lt;diretório do SonarScanner.MSBuild.exe\&gt; begin /k:\&lt;&quot;chave-do-projeto&quot;\&gt; /n:&quot;Nome do projeto&quot; /d:sonar.host.url=\&lt;url do server que será gerada a análise\&gt;

1.
  1. **Build** : Entre as etapas &quot;begin&quot; e &quot;end&quot;, você precisa construir seu projeto, executar testes e gerar dados de cobertura de código. C:\Arquivos de Programas (x86)\MSBuild\14.0\Bin\MSBuild.exe \&lt;diretório da solution.sln\&gt; /t:Rebuild
  2. **End** : Ele limpa os hooks do MSBuild, coleta os dados de análise gerados pela construção, os resultados do teste, a cobertura do código e, em seguida, carrega tudo para o SonarQube.

\&lt;diretório do SonarScanner.MSBuild.exe\&gt; end

## MSBuild .NET Core 2.0+

1. Faça o [download](https://github.com/SonarSource/sonar-scanner-msbuild/releases/download/4.5.0.1761/sonar-scanner-msbuild-4.5.0.1761-netcoreapp2.0.zip)do SonarScanner para MSBuild .Net Core 2.0+
2. Descompacte-o em c:\sonarscanner-msbuild, por exemplo
3. No PowerShell, acesse o diretório onde consta a sua aplicação
4. Execute os comandos abaixo para executar a análise substituindo os \&lt;parâmetros\&gt; necessários:
  1. **Begin** : Conecta-se ao MSBuild, faz o download dos perfis de qualidade do SonarQube, as configurações e prepara seu projeto para a análise.

dotnet \&lt;diretório do SonarScanner.MSBuild.dll\&gt; begin /k:&quot;chave-do-projeto&quot; /n:&quot;Nome do projeto&quot; /d:sonar.host.url=\&lt;url do server que será gerada a análise\&gt;

1.
  1. **Build** : Entre as etapas &quot;begin&quot; e &quot;end&quot;, você precisa construir seu projeto, executar testes e gerar dados de cobertura de código.

dotnet build \&lt;diretório da solution.sln\&gt;

1.
  1. **End** : Ele limpa os hooks do MSBuild, coleta os dados de análise gerados pela construção, os resultados do teste, a cobertura do código e, em seguida, carrega tudo para o SonarQube.

dotnet \&lt;diretório do SonarScanner.MSBuild.dll\&gt; end

## SonarScanner

1. Faça o [download](https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-3.2.0.1227-windows.zip) do SonarScanner
2. Descompacte-o em c:\sonarscanner, por exemplo
3. Atualize as configurações globais para apontar para o seu servidor SonarQube editando \&lt;diretorio\_instalacao\&gt;/conf/sonar-scanner.properties:

1. Adicione o diretório \&lt;diretorio\_instalacao\&gt;/bin ao seu path.
  1. Para configurar path:
    - **■■** Localize o diretório de instalação do SonarScanner
    - **■■**** Windows 10 **- Pesquise por Variáveis de Ambiente e selecione** Editar as variáveis de ambiente do sistema**
    - **■■** Clique no botão **Variáveis de ambiente**
    - **■■** Sob **Variáveis do sistema** , encontre Path e clique em **Editar**
    - **■■** Clique em **Novo** e adicione o diretório \&lt;diretorio\_instalacao\&gt;/bin
    - **■■** Clique em **OK** e **Aplicar alterações** quando solicitado
2. Você pode verificar sua instalação abrindo um novo shell e executando o comando sonar-scanner -h (na plataforma Windows, o comando é sonar-scanner.bat -h). Você deve obter uma saída assim:

1. Crie um arquivo de configuração no diretório raiz do projeto: sonar-project.properties

**sonar-project.properties**

| # SonarQube serversonar.host.url=http://qa-quali2012:9000# must be unique in a given SonarQube instancesonar.projectKey=my:project# this is the name and version displayed in the SonarQube UI. Was mandatory prior to SonarQube 6.1.sonar.projectName=My projectsonar.projectVersion=1.0# Path is relative to the sonar-project.properties file. Replace &quot;\&quot; by &quot;/&quot; on Windows.# This property is optional if sonar.modules is set. sonar.sources=.# Encoding of the source code. Default is default system encoding#sonar.sourceEncoding=UTF-8 |
| --- |

1. Execute o seguinte comando no diretório base do projeto para ativar a análise:

sonar-scanner

## SonarLint

O SonarLint é uma extensão IDE que ajuda a detectar e corrigir problemas de qualidade enquanto você escreve o código.

Como um verificador ortográfico, o SonarLint elimina falhas para que possam ser corrigidas antes de confirmar o código.

Acesse [https://www.sonarlint.org/](https://www.sonarlint.org/) e escolha a extensão para sua IDE favorita.

## Instalação Java

1. No PowerShell verifique se possui o jdk 1.8 instalado

java -version

1. Caso não possua, efetue o [download](https://download.oracle.com/otn-pub/java/jdk/8u191-b12/2787e4a523244c269598db4e85c51e0c/jdk-8u191-windows-x64.exe)
2. Execute o instalador do Java. Anote o diretório de instalação, pois você precisará disso mais tarde.
3. Quando a instalação do Java estiver concluída, verifique se a variável de ambiente JAVA\_HOME foi configurada corretamente.
4. Abra um prompt de comando, digite

echo %JAVA\_HOME%

1.
  1. Se você vir um caminho para o diretório de instalação do Java, a variável de ambiente JAVA\_Home foi configurada corretamente.

Exemplo:

1.
  1. Se nada for exibido ou somente %JAVA\_HOME% for retornado, você precisará configurar a variável de ambiente JAVA\_HOME manualmente.
2. Para configurar a variável JAVA\_HOME:
  1. Localize o diretório de instalação Java
  2. Se você não alterou o caminho durante a instalação, ele será parecido com isso C:\Program Files\Java\jdk1.8.0\_65
  3. **Windows 10** - Pesquise por Variáveis de Ambiente e selecione **Editar as variáveis de ambiente do sistema**
  4. Clique no botão **Variáveis de ambiente**
  5. Sob **Variáveis do sistema** , clique em **Novo**
  6. No campo **Nome da variável** , insira JAVA\_HOME
  7. No campo **Valor da variável** , insira o seu caminho da instalação do JDK

1.
  1. Clique em **OK** e **Aplicar alterações** quando solicitado

Você precisará fechar e abrir novamente qualquer janela de comando que estava aberta antes de fazer estas alterações, já que não há como recarregar variáveis de ambiente de um prompt de comando ativo. Se as alterações não entrarem em vigor depois de abrir novamente a janela de comando, reinicie o Windows.



Esse documento foi baseado na documentação oficial do SonarQube que pode ser encontrada em: [https://docs.sonarqube.org/latest/](https://docs.sonarqube.org/latest/)