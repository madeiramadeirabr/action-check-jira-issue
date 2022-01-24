![img](https://github.com/madeiramadeirabr/action-generate-gmud-sre/blob/staging/img/action-generate-gmud-sre.svg)
# action-check-jira-issue

## Descrição:
Action que valida:
- Existência da Issue no Jira

## Contexto de negócio:
Irá compor a estrutura padrão que está sendo desenvolvida para o CI/CD da [MadeiraMadeira](https://github.com/madeiramadeirabr 'MadeiraMadeira'), sendo aplicável a todos os Projetos Novos (e "antigos").

## Squad:
[SRE-Architecture-Carpentry](https://github.com/orgs/madeiramadeirabr/teams/squad-sre-architecture-carpentry 'SRE-Architecture-Carpentry')

## Requisitos:
1. Título da PR precisa ser validado pela action [`action-check-title-pr-pattern`](https://github.com/madeiramadeirabr/action-check-title-pr-pattern 'action-check-title-pr-pattern')
> Issue precisa ser setada entre parênteses no Título da Pull Request:
> _Exemplo:_ feat(**SRE-417**): implements Swagger.

2. Secrets `BASIC_AUTH_JIRA` no Repositório 

## Exemplos de uso (da action):


```yml
name: CI
on:
  pull_request:
    branches:
      - production

jobs:
  check-existence-jira-issue:
    runs-on: ubuntu-latest     
    outputs:
      output1: ${{ steps.title.outputs.TITLE }}
    name: 'Check the jira for an issue'
    needs: check-title-pull-request
    steps:
      - name: 'Get title'
        id: title
        run: |
          text="${{ github.event.pull_request.title }}"
          IFS="("
          read -ra ADDR <<< "$text"   
          convert=${ADDR[${#ADDR[@]}-1]}
          IFS=")"
          read -ra ADDR <<< "$convert"
          echo "::set-output name=TITLE::${ADDR[0]}"
      - name: 'check jira issue'
        uses: madeiramadeirabr/action-check-jira-issue@v1
        with:
          url-jira:  'https://madeiramadeira.atlassian.net/rest/api/3/issue/${{ steps.title.outputs.TITLE }}'
          basic-auth: ${{ secrets.BASIC_AUTH_JIRA }}          
```