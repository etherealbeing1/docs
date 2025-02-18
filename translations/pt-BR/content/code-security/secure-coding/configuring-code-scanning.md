---
title: Configurar a varredura do código
intro: 'Você pode configurar como o {% data variables.product.prodname_dotcom %} faz a varredura do código no seu projeto com relação a vulnerabilidades e erros.'
product: '{% data reusables.gated-features.code-scanning %}'
permissions: 'People with write permissions to a repository can configure {% data variables.product.prodname_code_scanning %} for the repository.'
miniTocMaxHeadingLevel: 4
redirect_from:
  - /github/finding-security-vulnerabilities-and-errors-in-your-code/configuring-code-scanning
versions:
  free-pro-team: '*'
  enterprise-server: '>=3.0'
  github-ae: '*'
topics:
  - Security
---

<!--For this article in earlier GHES versions, see /content/github/finding-security-vulnerabilities-and-errors-in-your-code-->

{% data reusables.code-scanning.beta %}
{% data reusables.code-scanning.enterprise-enable-code-scanning-actions %}

### Sobre a configuração do {% data variables.product.prodname_code_scanning %}

Você pode executar {% data variables.product.prodname_code_scanning %} em {% data variables.product.product_name %}, usando {% data variables.product.prodname_actions %} ou a partir do seu sistema de integração contínua (CI). Para obter mais informações, consulte "[Sobre {% data variables.product.prodname_actions %}](/actions/getting-started-with-github-actions/about-github-actions)" ou
{%- if currentVersion == "free-pro-team@latest" or currentVersion ver_gt "enterprise-server@3.0" or currentVersion == "github-ae@next" %}
"[Sobre {% data variables.product.prodname_codeql %} {% data variables.product.prodname_code_scanning %} no seu sistema de CI](/code-security/secure-coding/about-codeql-code-scanning-in-your-ci-system)".
{%- else %}
"[Executar {% data variables.product.prodname_codeql_runner %} no seu sistema de CI](/code-security/secure-coding/running-codeql-runner-in-your-ci-system)."
{% endif %}

Este artigo é sobre a execução de {% data variables.product.prodname_code_scanning %} em {% data variables.product.product_name %} usando ações.

Antes de poder configurar o {% data variables.product.prodname_code_scanning %} para um repositório, você deve configurar {% data variables.product.prodname_code_scanning %} adicionando um fluxo de trabalho do {% data variables.product.prodname_actions %} ao repositório. Para obter mais informações, consulte "[Configurar {% data variables.product.prodname_code_scanning %} para um repositório](/code-security/secure-coding/setting-up-code-scanning-for-a-repository)".

{% data reusables.code-scanning.edit-workflow %}

A análise de {% data variables.product.prodname_codeql %} é apenas um tipo de {% data variables.product.prodname_code_scanning %} que você pode fazer em {% data variables.product.prodname_dotcom %}. {% data variables.product.prodname_marketplace %}{% if currentVersion ver_gt "enterprise-server@2.21" %} em  {% data variables.product.prodname_dotcom_the_website %}{% endif %} contém outros fluxos de trabalho de {% data variables.product.prodname_code_scanning %}  que você pode usar. {% if currentVersion == "free-pro-team@latest" %}Você pode encontrar uma seleção destes na página "Começar com {% data variables.product.prodname_code_scanning %}" que você pode acessar na aba **{% octicon "shield" aria-label="The shield symbol" %} Segurança** .{% endif %} Os exemplos específicos apresentados neste artigo estão relacionados ao arquivo de {% data variables.product.prodname_codeql_workflow %}.

### Editing a code scanning workflow

O {% data variables.product.prodname_dotcom %} salva arquivos de fluxo de trabalho no diretório _.github/workflows_ do seu repositório. Você pode encontrar um fluxo de trabalho que você adicionou procurando o nome do seu arquivo. For example, the default workflow file for CodeQL code scanning is called `codeql-analysis.yml`.

1. No seu repositório, pesquise o arquivo do fluxo de trabalho que você deseja editar.
1. No canto superior direito da vista do arquivo, clique em {% octicon "pencil" aria-label="The edit icon" %} para abrir o editor do fluxo de trabalho. ![Edite o botão do arquivo do fluxo de trabalho](/assets/images/help/repository/code-scanning-edit-workflow-button.png)
1. Depois de ter editado o arquivo, clique em **Iniciar commit** e preencha o formulário "Alterações do commit". Você pode escolher o "commit" diretamente no branch atual ou criar um novo branch e iniciar um pull request. ![Atualização do commit para o fluxo de trabalho do codeql.yml](/assets/images/help/repository/code-scanning-workflow-update.png)

Para obter mais informações sobre a edição de arquivos do fluxo de trabalho, consulte "[Aprenda {% data variables.product.prodname_actions %}](/actions/learn-github-actions)".

### Configurar a frequência

Você pode fazer a varredura de código de forma pré-programada ou quando ocorrerem eventos específicos em um repositório.

A varredura do código a cada push para o repositório, e toda vez que um pull request é criado, isso impede que os desenvolvedores introduzam novas vulnerabilidades e erros no código. A varredura do código de forma pré-programada informa as últimas vulnerabilidades e erros de {% data variables.product.company_short %}, que os pesquisadores de segurança e da comunidade, mesmo quando desenvolvedores não estão mantendo o repositório de forma ativa.

#### Fazer a varredura no push

Se você usar o fluxo de trabalho padrão, o {% data variables.product.prodname_code_scanning %} fará a varredura do código no repositório uma vez por semana, além das varreduras acionadas pelos eventos. Para ajustar essa programação, edite o valor `CRON` no fluxo de trabalho. Para obter mais informações, consulte "[Sintaxe de fluxo de trabalho para o {% data variables.product.prodname_actions %}](/actions/reference/workflow-syntax-for-github-actions#on)".

Se você fizer uma varredura no push, os resultados aparecerão na aba **Segurança** do repositório. Para obter mais informações, consulte "[Gerenciar alertas de varredura de código para seu repositório](/code-security/secure-coding/managing-code-scanning-alerts-for-your-repository#viewing-the-alerts-for-a-repository). "

{% note %}

**Observação**: se você deseja que os alertas de {% data variables.product.prodname_code_scanning %} sejam exibidos como verificações de pull request, você deverá usar o evento `pull_request` descrito abaixo.

{% endnote %}

#### Fazer a varredura de pull requests

O padrão {% data variables.product.prodname_codeql_workflow %} usa o evento `pull_request` para acionar uma verificação de código em pull requests direcionadas ao branch padrão. {% if currentVersion ver_gt "enterprise-server@2.21" %}O evento `pull_request` não será acionado se o pull request foi aberto através de uma bifurcação privada.{% else %}Se um pull request for de um fork privado, o evento `pull_request` só será acionado se você tiver selecionado a opção "Executar fluxos de trabalho a partir de pull requests" nas configurações do repositório. Para obter mais informações, consulte "[Desabilitando ou limitando {% data variables.product.prodname_actions %} para um repositório](/github/administering-a-repository/disabling-or-limiting-github-actions-for-a-repository#enabling-workflows-for-private-repository-forks)".{% endif %}

Para obter mais informações sobre o evento `pull_request` , consulte "[Sintaxe de fluxo de trabalho para {% data variables.product.prodname_actions %}](/actions/reference/workflow-syntax-for-github-actions#onpushpull_requestbranchestags)".

Se você realizar uma varredura de pull requests, os resultados aparecerão como alertas em uma verificação de pull request. Para obter mais informações, consulte "[Alertas de varredura de código de triagem em pull requests](/code-security/secure-coding/triaging-code-scanning-alerts-in-pull-requests)".

#### Evitar varreduras desnecessárias de pull requests

Você pode querer evitar que uma varredura de código seja acionada em pull requests específicos para o branch padrão, Independentemente de os arquivos terem sido alterados. Você pode configurar isso, especificando `no:pull_request:paths-ignore` ou `on:pull_request:paths` no fluxo de trabalho de {% data variables.product.prodname_code_scanning %}. Por exemplo, se as únicas alterações em um pull request são para arquivos com extensões de arquivo `.md` ou `.txt`, você poderá usar o seguinte array `paths-ignore`.

``` yaml
on:
  push:
    branches: [main, protected]
  pull_request:
    branches: [main]
    paths-ignore:
      - '**/*.md'
      - '**/*.txt'
```

{% note %}

**Observações**

* `on:pull_request:paths-ignore` e `on:pull_request:paths` definem condições que determinam se as ações no fluxo de trabalho serão executadas em um pull request. Eles não determinam quais arquivos serão analisados quando as ações _são_ executadas. Quando uma pull request contém quaisquer arquivos não correspondidos por `on:pull_request:paths-ignore` ou `on:pull_request:paths`, o fluxo de trabalho executa as ações e faz a varredura de todos os arquivos alterados no pull request, incluindo aqueles que correspondidos por `on:pull_request:paths-ignore` ou `on:pull_request:paths`, a menos que os arquivos tenham sido excluídos. Para obter informações sobre como excluir arquivos da análise, consulte "[Especificar diretórios a serem varridos](#specifying-directories-to-scan)".
* Para arquivos do fluxo de trabalho de {% data variables.product.prodname_codeql %} {% data variables.product.prodname_code_scanning %}, não use as palavras-chave `paths-ignore` ou `paths` com o evento `on:push`, pois é provável que isso gere análises ausentes. Para resultados precisos, {% data variables.product.prodname_codeql %} {% data variables.product.prodname_code_scanning %} precisam conseguir comparar novas alterações com a análise do commit anterior.

{% endnote %}

Para mais informações sobre como usar `on:pull_request:paths-ignore` e `on:pull_request:paths` para determinar quando um fluxo de trabalho será executado para um pull request, consulte "[sintaxe de fluxo de trabalho para {% data variables.product.prodname_actions %}](/actions/reference/workflow-syntax-for-github-actions#onpushpull_requestpaths)".

#### Fazer a varredura de forma pré-programada

O fluxo de trabalho padrão do {% data variables.product.prodname_code_scanning %} usa o evento `on.push` para acionar uma varredura de código em cada push para qualquer branch que contém o arquivo de fluxo de trabalho. Para ajustar essa programação, edite o valor `CRON` no fluxo de trabalho. Para obter mais informações, consulte "[Sintaxe de fluxo de trabalho para o {% data variables.product.prodname_actions %}](/actions/reference/workflow-syntax-for-github-actions#onschedule)".

{% note %}

**Observação**: {% data variables.product.prodname_dotcom %} executa apenas trabalhos programados que estão em fluxos de trabalho no branch-padrão. Alterar a programação de um fluxo de trabalho em qualquer outro branch não terá efeito até que você mescle o branch com o branch-padrão.

{% endnote %}

#### Exemplo

O exemplo a seguir mostra um {% data variables.product.prodname_codeql_workflow %} para um repositório em particular que possui um branch-padrão denominado `principal` e um branch protegido denominado `protegido`.

``` yaml
on:
  push:
    branches: [main, protected]
  pull_request:
    branches: [main]
  schedule:
    - cron: '20 14 * * 1'
```

Este fluxo de trabalho faz a varredura:
* Cada push para o branch-padrão e o branch protegido
* Cada pull request para o branch-padrão
* O branch padrão toda segunda-feira às 14h20 UTC

### Especificar um sistema operacional

Se seu código exigir um sistema operacional específico para compilar, você poderá configurar o sistema operacional em seu {% data variables.product.prodname_codeql_workflow %}. Edite o valor de `jobs.analyze.runs-on` para especificar o sistema operacional para a máquina que executa suas ações de {% data variables.product.prodname_code_scanning %}. {% if currentVersion ver_gt "enterprise-server@2.21" %}Você especifica o sistema operacional usando uma etiqueta apropriada como segundo elemento em um array de dois elementos, após `auto-hospedado`.{% else %}

Se você optar por usar um executor auto-hospedado para varredura de código, você pode especificar um sistema operacional usando uma etiqueta apropriada como segundo elemento em um array de dois elementos, após `auto-hospedado`.{% endif %}

``` yaml
jobs:
  analyze:
    name: Analyze
    runs-on: [self-hosted, ubuntu-latest]
```

{% if currentVersion == "free-pro-team@latest" %}Para obter mais informações, consulte "[Sobre executores auto-hospedados](/actions/hosting-your-own-runners/about-self-hosted-runners)" e "[Adicionar executores auto-hospedados](/actions/hosting-your-own-runners/adding-self-hosted-runners)."{% endif %}

O {% data variables.product.prodname_code_scanning_capc %} é compatível com as versões mais recentes do macOS, Ubuntu, e Windows. Portanto, os valores típicos para essa configuração são `ubuntu-latest`, `windows-latest` e `macos-latest`. Para obter mais informações, consulte {% if currentVersion ver_gt "enterprise-server@2.21" %}" %}"[Sintaxe do fluxo de trabalho para o GitHub Actions](/actions/reference/workflow-syntax-for-github-actions#self-hosted-runners)" e "[Usando rótulos com executores auto-hospedados](/actions/hosting-your-own-runners/using-labels-with-self-hosted-runners){% else %}"[Sintaxe de fluxo de trabalho para o GitHub Actions](/actions/reference/workflow-syntax-for-github-actions#jobsjob_idruns-on){% endif %}."

{% if currentVersion ver_gt "enterprise-server@2.21" %}Você deve garantir que o Git esteja na variável do PATH em seus executores auto-hospedados.{% else %}Se você usa um executor auto-hospedado, você deve garantir que o Git esteja na variável de PATH.{% endif %}

### Alterar as linguagens que são analisadas

O {% data variables.product.prodname_codeql %} {% data variables.product.prodname_code_scanning %} detecta automaticamente código escrito nas linguagens compatíveis.

{% data reusables.code-scanning.codeql-languages-bullets %}

O arquivo padrão do {% data variables.product.prodname_codeql_workflow %} contém uma matriz de criação denominada `linguagem` que lista as linguagens no seu repositório que são analisadas. O {% data variables.product.prodname_codeql %} preenche automaticamente esta matriz quando você adiciona o {% data variables.product.prodname_code_scanning %} a um repositório. Usar a matriz de `linguagem` otimiza {% data variables.product.prodname_codeql %} para executar cada análise em paralelo. Recomendamos que todos os fluxos de trabalho adotem esta configuração devido aos benefícios de desempenho de criações paralelas. Para obter mais informações sobre matrizes de criação, consulte "[Gerenciar fluxos de trabalho complexos](/actions/learn-github-actions/managing-complex-workflows#using-a-build-matrix)".

{% data reusables.code-scanning.specify-language-to-analyze %}

Se o seu fluxo de trabalho usar a matriz de </code>linguagem `, o {% data variables.product.prodname_codeql %} será codificado para analisar apenas as linguagens da matriz. Para alterar as linguagens que você deseja analisar, edite o valor da variável da matriz. Você pode remover uma linguagem para evitar que ele seja analisado ou adicionar uma linguagem que não estava presente no repositório quando o {% data variables.product.prodname_code_scanning %} estava configurado. Por exemplo, se o repositório inicialmente continha apenas JavaScript quando {% data variables.product.prodname_code_scanning %} foi configurado e, posteriormente, você adicionou o código Python, você precisará adicionar o <code>python` à matriz.

```yaml
jobs:
  analyze:
    name: Analyze
    ...
    strategy:
      fail-fast: false
      matrix:
        language: ['javascript', 'python']
```

Se o seu fluxo de trabalho não contiver uma matriz denominada `linguagem`, o {% data variables.product.prodname_codeql %} será configurado para executar a análise sequencialmente. Se você não especificar as linguagens no fluxo de trabalho, o {% data variables.product.prodname_codeql %} irá detectar automaticamente e tentará analisar quaisquer linguagens compatíveis no repositório. Se você quiser escolher quais linguagens analisar sem usar uma matriz, você poderá usar o parâmetro `linguagens` na ação `init`.

```yaml
- uses: github/codeql-action/init@v1
  with:
    languages: cpp, csharp, python
```
{% if currentVersion == "free-pro-team@latest" %}
### Analisar as dependências do Python

Para executores hospedados no GitHub, que usam apenas Linux, o {% data variables.product.prodname_codeql_workflow %} tentará instalar automaticamente as dependências do Python para dar mais resultados para a análise do CodeQL. Você pode controlar este comportamento especificando o parâmetro `setup-python-dependencies` para a ação chamada pela etapa "Initialize CodeQL". Por padrão, esse parâmetro é definido como `verdadeiro`:

-  Se o repositório contiver código escrito em Python, a etapa "Initialize CodeQL" instalará as dependências necessárias no executor hospedado pelo GitHub. Se a instalação automática for bem-sucedida, a ação também definirá a variável de ambiente `CODEQL_PYTHON` para o arquivo Python executável que inclui as dependências.

- Se o repositório não tiver dependências do Python ou se as dependências forem especificadas de forma inesperada, você receberá um aviso e a ação continuará com os trabalhos restantes. A ação pode ser executada com sucesso, mesmo quando houver problemas de interpretação de dependências, mas os resultados podem estar incompletos.

Alternativamente, você pode instalar as dependências do Python manualmente em qualquer sistema operacional. Você precisará adicionar as `setup-python-dependencies` e definir como `falso`, além de definir `CODEQL_PYTHON` como o executável do Python que inclui as dependências, conforme mostrado neste trecho do fluxo de trabalho:

```yaml
jobs:
  CodeQL-Build:

    runs-on: ubuntu-latest{% if currentVersion == "free-pro-team@latest" or currentVersion ver_gt "enterprise-server@3.1" or currentVersion == "github-ae@next" %}
    permissions:
      security-events: write
      actions: read{% endif %}

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.x'
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          if [ -f requirements.txt ];
          then pip install -r requirements.txt;
          fi
          # Set the `CODEQL-PYTHON` environment variable to the Python executable
          # that includes the dependencies
          echo "CODEQL_PYTHON=$(which python)" >> $GITHUB_ENV
      - name: Initialize CodeQL
        uses: github/codeql-action/init@v1
        with:
          languages: python
          # Override the default behavior so that the action doesn't attempt
          # to auto-install Python dependencies
          setup-python-dependencies: false
```
{% endif %}

{% if currentVersion == "free-pro-team@latest" %}
### Configuring a category for the analysis

Use `category` to distinguish between multiple analyses for the same tool and commit, but performed on different languages or different parts of the code. The category you specify in your workflow will be included in the SARIF results file.

This parameter is particularly useful if you work with monorepos and have multiple SARIF files for different components of the monorepo.

{% raw %}
``` yaml
   - name: Perform CodeQL Analysis
      uses: github/codeql-action/analyze
      with:
      # Optional. Specify a category to distinguish between multiple analyses
      # for the same tool and ref. If you don't use `category` in your workflow, 
      # GitHub will generate a default category name for you
       category: "my_category"
```
{% endraw %}

If you don't specify a `category` parameter in your workflow, {% data variables.product.prodname_dotcom %} will generate a category name for you, based on the name of the workflow file triggering the action, the action name, and any matrix variables. Por exemplo:
- The `.github/workflows/codeql-analysis.yml` workflow and the `analyze` action will produce the category `.github/workflows/codeql.yml:analyze`.
- The `.github/workflows/codeql-analysis.yml` workflow, the `analyze` action, and the `{language: javascript, os: linux}` matrix variables will produce the category `.github/workflows/codeql-analysis.yml:analyze/language:javascript/os:linux`.

The `category` value will appear as the `<run>.automationDetails.id` property in SARIF v2.1.0. Para obter mais informações, consulte "[Suporte SARIF para {% data variables.product.prodname_code_scanning %}](/code-security/secure-coding/sarif-support-for-code-scanning#runautomationdetails-object)".

Your specified category will not overwrite the details of the `runAutomationDetails` object in the SARIF file, if included.

{% endif %}

### Executar consultas adicionais

{% data reusables.code-scanning.run-additional-queries %}

Para adicionar uma ou mais consultas, adicione uma entrada `with: queries:` na seção `uses: github/codeql-action/init@v1` do fluxo de trabalho. Se as consultas estiverem em um repositório privado, use o parâmetro `external-repository-token` para especificar um token que tenha acesso ao repositório privado.

{% raw %}
``` yaml
- uses: github/codeql-action/init@v1
  with:
    queries: COMMA-SEPARATED LIST OF PATHS
    # Optional. Forneça uma token para acessar repositórios privados.
    external-repository-token: ${{ secrets.ACCESS_TOKEN }}
```
{% endraw %}

Você também pode executar suítes de consultas adicionais especificando-os em um arquivo de configuração. Os suítes de consulta são coleções de consultas, geralmente agrupados por finalidade ou linguagem.

{% data reusables.code-scanning.codeql-query-suites %}

Você pode executar consultas adicionais especificando-as em um arquivo de configuração. Se você desejar executar o conjunto combinado de consultas adicionais especificadas aqui e no arquivo de configuração, determine previamente o valor de `consultas` no fluxo de trabalho com o símbolo `+`. Para obter exemplos de arquivos de configuração, consulte "[Exemplo de arquivos de configuração](#example-configuration-files)".

Para incluir um ou mais suites de consulta, adicione uma seção `consultas` ao seu arquivo de configuração.

{% raw %}
``` yaml
- uses: github/codeql-action/init@v1
  with:
    config-file: ./.github/codeql/codeql-config.yml
    queries: +security-and-quality,octo-org/python-qlpack/show_ifs.ql@main
    external-repository-token: ${{ secrets.ACCESS_TOKEN }}
```
{% endraw %}

### Usar uma ferramenta de varredura de código de terceiros

Como alternativa à especificação de quais consultas executar no arquivo de fluxo de trabalho, você poderá fazer isso em um arquivo de configuração separado. Você também pode usar um arquivo de configuração para desativar as consultas-padrão e especificar quais diretórios escanear durante a análise.

No arquivo de workflow use o parâmetro `config-file` da ação `init` para especificar o caminho para o arquivo de configuração que você deseja usar. Este exemplo carrega o arquivo de configuração _./.github/codeql/codeql-config.yml_.

``` yaml
- uses: github/codeql-action/init@v1
  with:
    config-file: ./.github/codeql/codeql-config.yml
```

{% data reusables.code-scanning.custom-configuration-file %}

Se o arquivo de configuração estiver localizado em um repositório privado externo, use o parâmetro `external-repository-token` da ação `init` para especificar um token que tenha acesso ao repositório privado.

{% raw %}
```yaml
uses: github/codeql-action/init@v1
with:
  external-repository-token: ${{ secrets.ACCESS_TOKEN }}
```
{% endraw %}

As configurações no arquivo de configuração estão escritas no formato YAML.

#### Especificar consultas adicionais

Você especifica consultas adicionais em um array de `consultas`. Cada elemento do array contém um parâmetro de `uso` com um valor que identifica um único arquivo de consulta, um diretório que contém arquivos de consulta ou um arquivo de definição do conjunto de consulta.

``` yaml
queries:
  - uses: ./my-basic-queries/example-query.ql
  - uses: ./my-advanced-queries
  - uses: ./codeql-qlpacks/complex-python-qlpack/rootAndBar.qls
```

Opcionalmente, você pode dar um nome a cada elemento do array, conforme mostrado nos exemplos de arquivos de configuração abaixo.

Para obter mais informações sobre consultas adicionais, consulte "[Executar consultas adicionais](#running-additional-queries) acima.

#### Desativar as consultas-padrão

Se você desejar apenas executar consultas personalizadas, você poderá desabilitar as consultas de segurança padrão adicionando `disable-default-queries: true` ao seu arquivo de configuração.

#### Especificar diretórios para serem varridos

Para as linguagens interpretadas com as quais {% data variables.product.prodname_codeql %} é compatível (Python e JavaScript/TypeScript), você pode restringir {% data variables.product.prodname_code_scanning %} para arquivos em diretórios específicos adicionando um array de `caminhos` para o arquivo de configuração. Você pode excluir os arquivos em diretórios específicos das análises, adicionando um array de `paths-ignore`.

``` yaml
paths:
  - src
paths-ignore:
  - src/node_modules
  - '**/*.test.js'
```

{% note %}

**Observação**:

* As palavras-chave `caminhos` e `paths-ignore`, usados no contexto do arquivo de configuração do {% data variables.product.prodname_code_scanning %}, não deve ser confundido com as mesmas palavras-chave usadas para `on.<push|pull_request>.paths` em um fluxo de trabalho. Quando estão acostumados a modificar `on.<push|pull_request>` em um fluxo de trabalho, eles determinam se as ações serão executadas quando alguém modifica o código nos diretórios especificados. Para obter mais informações, consulte "[Sintaxe de fluxo de trabalho para o {% data variables.product.prodname_actions %}](/actions/reference/workflow-syntax-for-github-actions#onpushpull_requestpaths)".
* The filter pattern characters `?`, `+`, `[`, `]`, and `!` are not supported and will be matched literally.
* `**` **Note**: `**` characters can only be at the start or end of a line, or surrounded by slashes, and you can't mix `**` and other characters. Por exemplo, `foo/**`, `**/foo` e `foo/**/bar` são todos de sintaxe permitida, mas `**foo` não é. No entanto, você pode usar estrelas únicas junto com outros caracteres, conforme mostrado no exemplo. Você precisará colocar entre aspas qualquer coisa que contenha um caractere `*`.

{% endnote %}

Para linguagens compiladas, se você deseja limitar {% data variables.product.prodname_code_scanning %} a diretórios específicos no seu projeto, você deverá especificar os passos de compilação adequados no fluxo de trabalho. Os comandos que você precisa usar para excluir um diretório da criação dependerão do seu sistema de criação. Para obter mais informações, consulte "[Configurar o fluxo de trabalho do {% data variables.product.prodname_codeql %} para linguagens compiladas](/code-security/secure-coding/configuring-the-codeql-workflow-for-compiled-languages#adding-build-steps-for-a-compiled-language)".

Você pode rapidamente analisar pequenas partes de um monorepo ao modificar o código em diretórios específicos. Você deverá excluir diretórios nas suas etapas de criação e usar as palavras-chave `paths-ignore` e `caminhos` para [`on.<push|pull_request>`](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#onpushpull_requestpaths) no seu arquivo de fluxo de trabalho.

#### Exemplo de arquivo de configuração

{% data reusables.code-scanning.example-configuration-files %}

### Configurar o {% data variables.product.prodname_code_scanning %} para linguagens compiladas

{% data reusables.code-scanning.autobuild-compiled-languages %} {% data reusables.code-scanning.analyze-go %}

{% data reusables.code-scanning.autobuild-add-build-steps %} Para obter mais informações sobre como configurar {% data variables.product.prodname_codeql %} {% data variables.product.prodname_code_scanning %} para linguagens compiladas, consulte "[Configurar o fluxo de trabalho do {% data variables.product.prodname_codeql %} para linguagens compiladas](/code-security/secure-coding/configuring-the-codeql-workflow-for-compiled-languages)".

### {% data variables.product.prodname_code_scanning_capc %} usa {% data variables.product.prodname_actions %}.

Você pode exibir análise de código de uma ferramenta de terceiros em {% data variables.product.prodname_dotcom %}, adicionando a ação de `upload-sarif` ao seu fluxo de trabalho. Você pode fazer o upload de dados de análise de código com a ação `upload-sarif`. Para obter mais informações, consulte "[Fazer o upload de um arquivo SARIF para o GitHub](/code-security/secure-coding/uploading-a-sarif-file-to-github)".
