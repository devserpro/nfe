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
  "nfeProc": {
    "protNFe": {
      "infProt": {
        "nProt": 135170142171021,
        "dhRecbto": "2017-03-07T09:12:40-03:00",
        "Id": "Id135170142171071",
        "chNFe": "35170300776574000156550020009091941178378523",
        "xMotivo": "Autorizado o uso da NF-e",
        "cStat": 100
      }
    },
    "NFe": {
      "infNFe": {
        "infAdic": {
          "obsCont": [
            {
              "xCampo": "DevCO",
              "xTexto": "DEV"
            },
            {
              "xCampo": "SiglaERP",
              "xTexto": "ERP"
            },
            {
              "xCampo": "Logo",
              "xTexto": "LOGO"
            }
          ],
          "infCpl": "Desconto condicional concedido de        6.00 EC 87/2015 - TOTAL FECP: R$        0.00 TOTAL ICMS UF ORIGEM: R$        3.08 TOTAL ICMS UF DESTINO: R$        4.62"
        },
        "det": {
          "nItem": 1,
          "prod": {
            "cEAN": 12391112157934,
            "cProd": 123315383,
            "qCom": 1,
            "cEANTrib": 1891112157934,
            "vUnTrib": 59.99,
            "qTrib": 1,
            "vProd": 59.99,
            "nFCI": "46C5AB6C-F255-42EF-12AC-273260A2923D",
            "xProd": "JOGO FORMAS ALUMINIO 3 PCS",
            "vUnCom": 59.99,
            "indTot": 1,
            "uTrib": "PC",
            "NCM": 16151000,
            "uCom": "PC",
            "CFOP": 6108,
            "vFrete": 9.99
          },
          "imposto": {
            "vTotTrib": 24.38,
            "ICMS": {
              "ICMS00": {
                "modBC": 3,
                "orig": 0,
                "CST": "00",
                "vBC": 69.98,
                "vICMS": 4.9,
                "pICMS": 7
              }
            },
            "ICMSUFDest": {
              "pICMSInter": 7,
              "vICMSUFDest": 4.62,
              "vICMSUFRemet": 3.08,
              "pICMSInterPart": 60,
              "vFCPUFDest": 0,
              "pFCPUFDest": 0,
              "vBCUFDest": 69.98,
              "pICMSUFDest": 18
            },
            "COFINS": {
              "COFINSAliq": {
                "vCOFINS": 5.32,
                "CST": "01",
                "vBC": 69.98,
                "pCOFINS": 7.6
              }
            },
            "PIS": {
              "PISAliq": {
                "vPIS": 1.15,
                "CST": "01",
                "vBC": 69.98,
                "pPIS": 1.65
              }
            }
          }
        },
        "total": {
          "ICMSTot": {
            "vICMSUFDest": 4.62,
            "vICMSUFRemet": 3.08,
            "vCOFINS": 5.32,
            "vBCST": 0,
            "vICMSDeson": 0,
            "vFCPUFDest": 0,
            "vProd": 59.99,
            "vSeg": 0,
            "vNF": 69.98,
            "vTotTrib": 24.38,
            "vPIS": 1.15,
            "vBC": 69.98,
            "vST": 0,
            "vICMS": 4.9,
            "vII": 0,
            "vDesc": 0,
            "vOutro": 0,
            "vIPI": 0,
            "vFrete": 9.99
          }
        },
        "entrega": {
          "xCpl": "CASA",
          "UF": "DF",
          "CPF": "82771760070",
          "xLgr": "AVENIDA CENTRAL FICTICIA",
          "xMun": "BRASILIA",
          "nro": "08",
          "cMun": 5300108,
          "xBairro": "NUCLEO BANDEIRANTE"
        },
        "Id": "NFe35170300776574000156550020009091941178378523",
        "ide": {
          "tpNF": 1,
          "mod": 55,
          "indPres": 2,
          "tpImp": 1,
          "nNF": 129194,
          "cMunFG": 3534401,
          "procEmi": 0,
          "finNFe": 1,
          "dhEmi": "2017-03-07T09:12:22-03:00",
          "tpAmb": 1,
          "indFinal": 1,
          "dhSaiEnt": "2017-03-07T09:12:22-03:00",
          "idDest": 2,
          "tpEmis": 1,
          "cDV": 2,
          "cUF": 35,
          "serie": 2,
          "natOp": "Fat. Conta e Ordem N/Contrib",
          "cNF": 12337856,
          "verProc": "ELDOC",
          "indPag": 2
        },
        "emit": {
          "xNome": "Companhia Digital FICTICIA",
          "CRT": 3,
          "CNPJ": "57101771000192",
          "enderEmit": {
            "UF": "SP",
            "xPais": "BRASIL",
            "cPais": 1058,
            "xLgr": "Rua Ficticia",
            "xMun": "OSASCO",
            "nro": 123,
            "cMun": 3534401,
            "xBairro": "Presidente Ficticio",
            "CEP": "00000008"
          },
          "IE": 123413778117,
          "IEST": "1234443600757"
        },
        "dest": {
          "xNome": "Jorge Luiz Ficticio",
          "CPF": "14281245278",
          "enderDest": {
            "fone": 6155551454,
            "UF": "DF",
            "xPais": "Brasil",
            "cPais": 1058,
            "xLgr": "QUADRA ficticia",
            "xMun": "BRASILIA",
            "nro": 213,
            "cMun": 5300108,
            "xBairro": "SETOR DE HABITACOES FICTICIOS",
            "CEP": 71000000
          },
          "indIEDest": 9
        },
        "versao": 3.1,
        "transp": {
          "modFrete": 0,
          "vol": {
            "pesoL": 0.99,
            "esp": "VOLS",
            "qVol": 1,
            "pesoB": 0.99
          }
        }
      }
    },
    "versao": 3.1
  }
}
```

