### A Lista de Verificação de Segurança 

##### SISTEMAS DE AUTENTICAÇÃO (Registro/Login/2 Fator/Redefinição de Senha) 
- [ ] Use HTTPS em todos os lugares.
- [ ] Armazene hashes de senha usando `Bcrypt` (sem sal necessário - `Bcrypt` faz isso por você).
- [ ] Destrua o identificador de sessão após `logout`.  
- [ ] Destrua todas as sessões ativas ao redefinir a senha (ou oferecer).  
- [ ] Deve ter o parâmetro `state` no OAuth2.
- [ ] Nenhum redirecionamento aberto após o login bem-sucedido ou em qualquer outro redirecionamento intermediário.
- [ ] Ao analisar a entrada Signup/Login, limpe os caracteres javascript://, data://, CRLF. 
- [ ] Definir cookies seguros, httpOnly.
- [ ] Na verificação móvel baseada em `OTP` móvel, não envie o OTP de volta na resposta quando a API `generate OTP` ou `Resend OTP` for chamada.
- [ ] Limite as tentativas de `Login`, `Verify OTP`, `Resend OTP` e `generate OTP` APIs para um usuário específico. Tenha um conjunto de recuo exponencial ou/e algo como um desafio baseado em captcha.
- [ ] Verifique a aleatoriedade do token de redefinição de senha no link enviado por e-mail ou SMS.
- [ ] Defina uma expiração no token de redefinição de senha por um período razoável.
- [ ] Expirar o token de redefinição após ter sido usado com sucesso.


##### DADOS E AUTORIZAÇÃO DO USUÁRIO
- [ ] Qualquer acesso a recursos como `my cart`, `my history` deve verificar a propriedade do usuário logado do recurso usando o ID da sessão.
- [ ] ID de recurso iterável em série deve ser evitado. Use `/me/orders` em vez de `/user/37153/orders`. Isso funciona como uma verificação de integridade caso você tenha esquecido de verificar o token de autorização.
- [ ] O recurso 'Editar e-mail/número de telefone' deve ser acompanhado por um e-mail de verificação para o proprietário da conta. 
- [ ] Qualquer recurso de upload deve higienizar o nome do arquivo fornecido pelo usuário. Além disso, por motivos gerais além da segurança, faça upload para algo como S3 (e pós-processe usando lambda) e não seu próprio servidor capaz de executar código.  
- [ ] O recurso `Upload de foto de perfil` deve limpar todas as tags `EXIF` também se não for necessário.
- [ ] Para IDs de usuário e outros IDs, use [compatível com RFC ](http://www.ietf.org/rfc/rfc4122.txt) `UUID` em vez de números inteiros. Você pode encontrar uma implementação para isso para sua linguagem no Github.
- [ ] JWT são incríveis. Use-os se necessário para seus aplicativos/APIs de página única.


##### APLICATIVO ANDROID/IOS
- [ ] `salt` dos gateways de pagamento não deve ser codificado.
- [ ] `secret` / `auth token` de SDKs de terceiros não devem ser codificados permanentemente.
- [ ] Chamadas de API destinadas a serem feitas `servidor para servidor` não devem ser feitas a partir do aplicativo.
- [ ] No Android, todas as [permissões](https://developer.android.com/guide/topics/security/permissions.html) concedidas devem ser cuidadosamente avaliadas.
- [ ] No iOS, armazene informações confidenciais (tokens de autenticação, chaves de API etc.) nas chaves do sistema. __não__ armazene esse tipo de informação nos padrões do usuário.
- [ ] [Fixação de certificado](https://en.wikipedia.org/wiki/HTTP_Public_Key_Pinning) é altamente recomendada.


##### CABEÇALHOS E CONFIGURAÇÕES DE SEGURANÇA
- [ ] `Add` [CSP](https://en.wikipedia.org/wiki/Content_Security_Policy) cabeçalho para mitigar ataques de XSS e injeção de dados. Isso é importante.
- [ ] `Add` [CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery) cabeçalho para evitar falsificação de solicitação entre sites. Adicione também os atributos [SameSite](https://tools.ietf.org/html/draft-ietf-httpbis-cookie-same-site-00) nos cookies.
- [ ] `Add` [HSTS](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security) cabeçalho para evitar o ataque de remoção de SSL.
- [ ] `Adicione` seu domínio à [lista de pré-carregamento HSTS](https://hstspreload.org/)
- [ ] `Add` [X-Frame-Options](https://en.wikipedia.org/wiki/Clickjacking#X-Frame-Options) para proteger contra Clickjacking.
- [ ] `Add` [X-XSS-Protection](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project#X-XSS-Protection) cabeçalho para mitigar ataques XSS.
- [ ] Atualize os registros DNS para adicionar o registro [SPF](https://en.wikipedia.org/wiki/Sender_Policy_Framework) para mitigar ataques de spam e phishing.
- [ ] Adicione [verificações de integridade de subrecursos](https://en.wikipedia.org/wiki/Subresource_Integrity) se estiver carregando suas bibliotecas JavaScript de uma CDN de terceiros. Para segurança extra, adicione a diretiva CSP [require-sri-for](https://w3c.github.io/webappsec-subresource-integrity/#parse-require-sri-for) para não carregar recursos que não tem um SRI sat.  
- [ ] Use tokens CSRF aleatórios e exponha APIs de lógica de negócios como solicitações HTTP POST. Não exponha tokens CSRF sobre HTTP, por exemplo, em uma fase inicial de atualização de solicitação.
- [ ] Não use dados críticos ou tokens em parâmetros de solicitação GET. A exposição de logs do servidor ou uma máquina/pilha processando-os exporia os dados do usuário por sua vez.  
  
  
##### SANITIZAÇÃO DE ENTRADA
- [ ] `Sanitize` todas as entradas do usuário ou quaisquer parâmetros de entrada expostos ao usuário para evitar [XSS](https://en.wikipedia.org/wiki/Cross-site_scripting).
- [ ] Sempre use consultas parametrizadas para evitar [SQL Injection](https://en.wikipedia.org/wiki/SQL_injection).  
- [ ] Higienize a entrada do usuário se estiver usando-a diretamente para funcionalidades como importação de CSV.
- [ ] Entrada do usuário `Sanitize` para casos especiais como robots.txt como nomes de perfil caso você esteja usando um padrão de url como coolcorp.io/username. 
- [ ] Nunca codifique manualmente ou construa JSON por concatenação de strings, não importa quão pequeno seja o objeto. Use suas bibliotecas ou estrutura definidas pela linguagem.
- [ ] Limpe as entradas que usam algum tipo de URL para evitar [SSRF](https://docs.google.com/document/d/1v1TkWZtrhzRLy0bYXBcdLUedXGb9njTNIJXa3u9akHM/edit#heading=h.t4tsk5ixehdd).
- [ ] Higienize as saídas antes de exibi-las aos usuários.

##### OPERAÇÕES
- [ ] Se você for pequeno e inexperiente, avalie usando o AWS elasticbeanstalk ou um PaaS para executar seu código.
- [ ] Use um script de provisionamento decente para criar VMs na nuvem.
- [ ] Verifique se há máquinas com `portas abertas` indesejadas publicamente.
- [ ] Verifique se não há senhas padrão para `bancos de dados` especialmente MongoDB e Redis.
- [ ] Use SSH para acessar suas máquinas; não configure uma senha, use a autenticação baseada em chave SSH.
- [ ] Instale atualizações em tempo hábil para agir em vulnerabilidades de dia zero, como Heartbleed, Shellshock.
- [ ] Modifique a configuração do servidor para usar TLS 1.2 para HTTPS e desative todos os outros esquemas. (A troca é boa.)
- [ ] Não deixe o modo DEBUG ligado. Em algumas estruturas, o modo DEBUG pode fornecer acesso completo a REPL ou shells ou expor dados críticos em rastreamentos de pilha de mensagens de erro.
- [ ] Esteja preparado para maus atores e DDOS - use um serviço de hospedagem que tenha mitigação de DDOS.
- [ ] Configure o monitoramento para seus sistemas e registre coisas (use [New Relic](https://newrelic.com/) ou algo parecido).
- [ ] Se estiver desenvolvendo para clientes corporativos, cumpra os requisitos de conformidade. Se AWS S3, considere usar o recurso para [criptografar dados](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html). Se estiver usando o AWS EC2, considere usar o recurso para usar volumes criptografados (até os volumes de inicialização podem ser criptografados agora).

##### PESSOAS
- [ ] Configure um e-mail (por exemplo, security@coolcorp.io) e uma página para pesquisadores de segurança relatarem vulnerabilidades.
- [ ] Dependendo do que você está fazendo, limite o acesso aos seus bancos de dados de usuários.
- [ ] Seja educado com os relatores de bugs.
- [ ] Tenha sua revisão de código feita por um colega desenvolvedor de uma perspectiva de codificação segura. (Mais olhos)
- [ ] Em caso de hack ou violação de dados, verifique os logs anteriores de acesso aos dados, peça às pessoas para alterar as senhas. Você pode exigir uma auditoria por agências externas, dependendo de onde você está incorporado.  
- [ ] Configure o [Netflix's Scumblr](https://github.com/Netflix/Scumblr) para ouvir sobre palestras sobre sua organização nas plataformas sociais e na pesquisa do Google.
