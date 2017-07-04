# API NF-e

API para acesso a Notas Fiscais Eletrônicas diretamente das bases da Receita Federal do Brasil através de sua chave de acesso.

A plataforma APIGOV (Plataforma que contempla todas as API's disponibilizadas e comercializadas pelo SERPRO) utiliza o protocolo Oauth2 - Client Credential Grant ([https://tools.ietf.org/html/rfc6749#section-4.4](https://tools.ietf.org/html/rfc6749#section-4.4)) para realizar a autenticação e autorização de acesso para consumo das API's contratadas, conforme figura abaixo:

<img title="Processo de autenticação e autorização APIS" src="https://raw.githubusercontent.com/devserpro/consulta-cpf/master/img/oauth.png" style="width=50%;" />

## Como fazer consultas

Para consumir a API NF-e, você deverá utilizar os dois códigos (Consumer Key e Consumer Secret) disponibilizados na Área do Cliente. Esses códigos servem para identificar o contrato e deverão ser informados sempre que uma consulta for realizada.
Exemplos de códigos:

**Consumer Key**: uldY78ZMvYm4btC0x3XZLG7ZTsYa

**Consumer Secret**: WyUeBFCUK7wu1Ko61V7bb7yB2Uoa

O APIGov utiliza o protocolo de autorização OAUTH2 para disponibilizar o acesso à API NF-e. No exemplo abaixo, estamos utilizando o GranType Cliente Credentials para requisição de token e acesso a API:

### 1 – Como solicitar o Token de Acesso
Para consultar a API, um Token deverá ser informado. O Token tem sua validade definida de acordo com a API. Para solicitar o Token, os seguintes procedimentos deverão ser realizados:

Informe ao Gateway suas Credenciais de Acesso
Faça uma requisição POST ao endereço https://apigateway.serpro.gov.br/token informando suas credenciais de acesso:
[POST] grant_type=client_credentials[HEAD] Authorization: Basic base64(Consumer Key:Consumer Secret)
Podemos, também, fazer essa chamada via cUrl da seguinte forma:

```curl
curl -k -d "grant_type=client_credentials" -H "Authorization: Basic dWxkWTc4Wk12WW00YnRDMHgzWFpMRzdaVHNZYTpXeVVlQkZDVUs3d3UxS282MVY3YmI3eUIyVW9h" https://apigateway.serpro.gov.br/token
```

A chave informada no exemplo acima "dWxkWTc4Wk12WW00YnRDMHgzWFpMRz
daVHNZYTpXeVVlQkZDVUs3d3UxS282MVY3YmI3eUIyVW9h" é resultado do BASE64 dos códigos Consumer Key e Consumer Secret separados pelo caracter “:”, conforme exemplo a seguir:

```curl
base64(uldY78ZMvYm4btC0x3XZLG7ZTsYa:WyUeBFCUK7wu1Ko61V7bb7yB2Uoa)
```


**Receba o Token**

O Gateway informará as informações do Token no seguinte padrão:

```json
{"scope":"am_application_scope default","token_type":"Bearer","expires_in":3295,"access_token":"c66a7de41c96f7008a0c397dc588b6d7"}
```

**scope**: Algumas APIs definem escopos diferentes para acessos a diferentes funcionalidades. Por exemplo: clientes com escopo de Leitura podem acessar somente funcionalidades de Leitura, escopo de escrita poderão consultar e incluir novos valores, escopo de deleção poderão consultar, incluir e deletar. Quando a API não possui esse tipo de recurso usa-se o escopo padrão.

**token_type**: Define a forma como o token será enviado. Por padrão utilizamos Bearer.

**expires_in**: Define o tempo em segundos em que o token expirará. Passado esse tempo será necessário realizar uma nova chamada.

**access_token**: O token a ser enviado durante a requisição.


**Renovação do Token de Acesso**

Sempre que o token de acesso temporário expirar, o gateway vai retornar um _HTTP CODE 401_ após realizar uma requisição para uma API. Neste caso, deve ser repetido o passo anterior (**Como solicitar o Token de Acesso**) para geração de um novo token de acesso temporário.


### 2 – Como realizar a consulta à API

De posse do Token de Acesso, faça uma requisição via GET ao gateway informando os parâmetros da API. Exemplo:

```curlBearer
curl -X GET --header "Accept: application/json" --header "Authorization: Bearer c66a7de41c96f7008a0c397dc588b6d7" "https://apigateway.serpro.gov.br/nfe/v1/12345678912345678912345678912345678912345678"
```

No exemplo acima foram utilizados os seguintes parametros:

**[HEADER] Accept: application/json** - Informamos o tipo de dados que estamos requerendo, nesse caso JSON

**[HEADER] Authorization: Bearer <span class="bearer">c66a7de41c96f7008a0c397dc588b6d7</span>** - Informamos o token de acesso recebido

**[GET] https://apigateway.serpro.gov.br/nfe/v1/12345678912345678912345678912345678912345678**: chamamos a url da API informando a chave de acesso da Nota Fiscal Eletrônica. No caso a url é "nfe/v1/{Chave de Acesso da NF-e}"

Nesse caso, espera-se que a resposta seja a seguinte:

```json
{
  "ni": "91708635203", 
  "nome": "Nome do CPF 917.086.352-03", 
  "nascimento": "01011975", 
  "situacao": {
    "codigo" : "0", 
    "descricao" : "Regular"
  }
}
```

