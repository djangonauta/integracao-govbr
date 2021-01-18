Parâmetros
==========

Foi adicionado um parâmetro ``ConstantesParametrosIFPA.LOGIN_GOVBR_ATIVO`` para ocultar/mostrar o botão de
login do Gov.br dependendo do ambiente em que a aplicação foi distribuída (produção ou homologação). A
seguinte inserção deve ser executada no banco de dados ``sistemas_comum``:

.. code-block:: sql
    :caption: Criação do parâmetro (sistemas_comum)
    :linenos:

    insert into comum.parametro (nome, descricao, valor, id_subsistema, id_sistema, codigo) values ('LOGIN_GOVBR_ATIVO', 'Parametro que define se o botão de login govbr está ativo', false, 11700, 2, 'IFPA_60_1');


As seguintes alterações devem ser aplicadas na tabela ``sae.bolsa_auxilio`` no banco de dados ``sigaa``:

.. code-block:: sql
    :caption: Adição de colunas na tabela sae.bolsa_auxilio do banco de dados sigaa
    :linenos:

    alter table sae.bolsa_auxilio add column link_avaliacao varchar (4000);
    alter table sae.bolsa_auxilio add column protocolo_govbr varchar (255);

Protocolo
=========

A versão **inicial** do número de protoloco é uma *string* com o seguinte formato:

    [ano][mês][dia][hora][minuto][segundo].[código do serviço].[id do objeto na base]


Exemplo: 20210110093540.4295.32

.. note::
    
    Tanto o protocolo quanto o link de avaliação são adicionados ao objeto bolsa correspondente quando
    o status desse objeto é atualizado pelos gestores do serviço de auxílio.


JBoss
=====

As seguintes propriedades foram adicionadas no arquivo ``run.sh`` do *Jboss* como parâmetros de configuração
ao invés de serem colocadas diretamente no código fonte. No momento estamos estudando outras alternativas para
configurar e carregar essas informações no sistema, de forma a desacoplar a implementação das informações
sensiveis como logins e senhas.


Credenciais de acesso para obtenção de token
--------------------------------------------

As credenciais ``clientId`` e ``clientSecret`` são obtidas a partir do |registro_app_client| junto ao GovBR
(no nosso caso, a aplicação cliente é o SIGAA [#]_). ``clientHashLogin`` é um *hash base64* obtido da *string*
"<client_id>:<client_secret>".

.. code-block:: properties

    "-DclientId=<id_do_servico>"
    "-DclientSecret=<senha_do_servico>"
    "-DclientHashLogin=<base64 de 'client_id:client_secret'>"


Credenciais de acesso para API de avaliação
-------------------------------------------

As credenciais de acesso para API [#]_ de avaliação (usuário e senha) devem ser obtidas de acordo com a
|documentacao_oficial|. A propriedade ``clientHashAvaliacao`` é um *hash base64* obtido da *string*
"<usuario:senha>".

.. code-block:: properties

    "-DclientHashAvaliacao=<base64 de 'usuario:senha'>


Protocolo TLS
-------------

Foi necessário alterar a versão do protocolo TLS [#]_ em nossa versão da JVM [#]_ para que as conexões HTTPS pudessem
ser feitas corretamente:

.. code-block:: properties

    "-Dhttps.protocols=TLSv1.2" 


.. |registro_app_client| raw:: html

    <a href="https://manual-roteiro-integracao-login-unico.servicos.gov.br/pt/stable/iniciarintegracao.html"
       target="_blank">registro da aplicação cliente</a>


.. |documentacao_oficial| raw:: html

   <a href="https://transformacao-digital.gitbook.io/tutorial-transformacao-digital/bases-tecnologicas/api-de-avaliacao-do-gov.br"
      target="_blank">documentação oficial</a>

.. rubric:: Notas
.. [#] Sistema Integrado de Gestão de Atividades Acadêmicas
.. [#] Application Programming Interface
.. [#] Transport Layer Security
.. [#] Java Virtual Machine
