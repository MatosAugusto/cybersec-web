# cybersec-web

- Augusto Luchesi Matos - 740871
- Sebastião Venâncio - 790850

## No Way JOSE!
Escolhemos o desafio No Way JOSE da CryptoHack, o código fonte fornecido no site está no repositório.
![image](https://github.com/MatosAugusto/cybersec-web/assets/75046257/c9b3e99a-4bbe-4b6b-846a-d96c21a43350)
![image](https://github.com/MatosAugusto/cybersec-web/assets/75046257/0b07f300-831c-400c-8118-db0c693b4f85)

## Solução

Para recuperar o valor da FLAG do desafio, foi explorada a vulnerabilidade na implementação do JWT (JSON Web Token). 

O código-fonte revela duas funções-chave:
- AUTHORISE(TOKEN), que decodifica e valida um JWT dado
- CREATE_SESSION(USERNAME), que codifica um JWT para um nome de usuário dado sem privilégios de administrador.

A parte crítica do desafio está no tratamento do campo alg (algoritmo) no cabeçalho do JWT.

### Estratégia
Estrutura JWT tem três partes: 
- Cabeçalho
- Carga útil
- Assinatura
Todos sãocodificados como strings Base64Url e separados por pontos (por exemplo, xxxxx.yyyyy.zzzzz).

#### Explorando o Campo alg: 
O código verifica o campo alg do cabeçalho do token. Se for definido como "none", ele tenta decodificar o token sem verificar sua assinatura. Esta é uma vulnerabilidade bem conhecida nas implementações de JWT, onde um atacante pode forjar um token definindo alg como "none" e modificando a carga útil.

#### Forjar um Token com Privilégios de Administrador: 
Você precisa criar um JWT com um cabeçalho especificando "alg": "none" e uma carga útil definindo "admin": verdadeiro. Uma vez que a verificação da assinatura é ignorada para tokens com "alg": "none", o servidor deve aceitar este token forjado e retornar a FLAG.

### Implementação
Criar o Token Forjado:

- Cabeçalho: {"alg": "none"}
- Carga Útil: {"admin": true}
Codificando ambos como Base64Url e concatenando com um ponto. Uma vez que a assinatura não é verificada, é possível omiti-la ou incluir qualquer valor.
#### Ajustar o Token para Base64Url:
Substituir "+" por "-" e "/" por "_" no token forjado para cumprir com a codificação Base64Url usada pelos JWTs.

#### Enviar uma Solicitação para o Endpoint de Autorização:
Usando o token forjado como parte da solicitação para o endpoint "/no-way-jose/autorizar/<token>/".

### Criando o token 

O token criado é *"eyJhbGciOiAibm9uZSJ9.eyJhZG1pbiI6IHRydWV9."*
Este token tem o campo alg definido como none e a carga útil indicando "admin": verdadeiro. 

Ao enviar este token no campo de submissão foi recebida a resposta desejada:

![image](https://github.com/MatosAugusto/cybersec-web/assets/75046257/1b0c98c8-13db-4201-a3b6-85ea8b536eac)
![image](https://github.com/MatosAugusto/cybersec-web/assets/75046257/31fd0b00-43e8-4521-97fc-401d43fd9674)

