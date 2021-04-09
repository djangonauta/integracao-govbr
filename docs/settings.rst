Parâmetros
==========

Foi adicionado um parâmetro ``ConstantesParametrosIFPA.LOGIN_GOVBR_ATIVO`` para ocultar/mostrar o botão de
login do Gov.br dependendo do ambiente em que a aplicação foi distribuída (produção ou homologação). A
seguinte inserção deve ser executada no banco de dados ``sistemas_comum``:

.. code-block:: sql
    :caption: Criação do parâmetro (sistemas_comum)
    :linenos:

    insert into comum.parametro (nome, descricao, valor, id_subsistema, id_sistema, codigo) values ('LOGIN_GOVBR_ATIVO', 'Parametro que define se o botão de login govbr está ativo', false, 11300, 2, 'IFPA_60_1');


As seguintes alterações devem ser aplicadas na tabela ``sae.bolsa_auxilio`` no banco de dados ``sigaa``:

.. code-block:: sql
    :caption: Adição de colunas na tabela sae.bolsa_auxilio do banco de dados sigaa
    :linenos:

    alter table sae.bolsa_auxilio add column link_avaliacao varchar (4000);
    alter table sae.bolsa_auxilio add column protocolo_govbr varchar (255);


Serviços
========

.. code-block:: sql
    :caption: Schema do modelo de serviços
    :linenos:

    create table ifpa.servico_govbr (
        id_servico_govbr integer not null,
        constraint ifpa_id_servico_govbr_pk primary key (id_servico_govbr),

        descricao varchar (255),
        codigo_producao varchar (10),
        codigo_desenvolvimento varchar (10),
        ativo boolean
    );

    alter table ifpa.servico_govbr owner sigaa;


Exemplo de inserção de dados com 3 serviços ativos (**Receber bolsa de Extensão**, **Receber bolsa de
Pesquisa** e **Obter Assistência Estudantil**) para o IFPA [#]_:

.. code-block:: sql
    :caption: Inserção de serviços
    :linenos:

    insert into ifpa.servico_govbr values (1, 'Receber bolsa de Extensão - IFPA', '5919', '4097', true); -- serviço ativo
    insert into ifpa.servico_govbr values (2, 'Participar de processo seletivo para curso de Educação Profissional Técnica (Educação de Jovens e Adultos, Integrado e Subsequente) - IFPA', '5977', '4155', false);
    insert into ifpa.servico_govbr values (3, 'Participar de processo seletivo para curso de Educação à Distância - IFPA', '6011', '4189', false);
    insert into ifpa.servico_govbr values (4, 'Receber bolsa de Pesquisa - IFPA', '6077', '4255', true); -- serviço ativo
    insert into ifpa.servico_govbr values (5, 'Obter Assistência Estudantil - IFPA', '6117', '4295', true); -- serviço ativo
    insert into ifpa.servico_govbr values (6, 'Matricular-se em curso de Educação à Distância - IFPA', '6136', '4314', false);
    insert into ifpa.servico_govbr values (7, 'Matricular-se em curso de Educação Profissional Técnica (Educação de Jovens e Adultos, Integrado e Subsequente) - IFPA', '6300', '4478', false);
    insert into ifpa.servico_govbr values (8, 'Participar de processo seletivo para curso de Formação Inicial e Continuada - IFPA', '6307', '4485', false);
    insert into ifpa.servico_govbr values (9, 'Matricular-se em curso de Educação Superior de Graduação (Licenciatura, Tecnologia e Bacharelado) - IFPA', '6318', '4496', false);
    insert into ifpa.servico_govbr values (10, 'Matricular-se em curso de Formação Inicial e Continuada - IFPA', '6538', '4716', false);
    insert into ifpa.servico_govbr values (11, 'Participar de processo seletivo para curso de Educação Superior de Graduação (Licenciatura, Tecnologia e Bacharelado) - IFPA', '6649', '4827', false);


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
    :linenos:

    -DclientId=<id_do_servico>
    -DclientSecret=<senha_do_servico>
    -DclientHashLogin=<base64 de "client_id:client_secret">


Credenciais de acesso para API de avaliação
-------------------------------------------

As credenciais de acesso para API [#]_ de avaliação (usuário e senha) devem ser obtidas de acordo com a
|documentacao_oficial|. A propriedade ``clientHashAvaliacao`` é um *hash base64* obtido da *string*
"<usuario:senha>".

.. code-block:: properties
    :linenos:

    -DclientHashAvaliacao=<base64 de "usuario:senha">


Protocolo TLS
-------------

Foi necessário alterar a versão do protocolo TLS [#]_ em nossa versão da JVM [#]_ para que as conexões HTTPS
[#]_ pudessem ser feitas corretamente:

.. code-block:: properties
    :linenos:

    -Dhttps.protocols=TLSv1.2


.. |registro_app_client| raw:: html

    <a href="https://manual-roteiro-integracao-login-unico.servicos.gov.br/pt/stable/iniciarintegracao.html"
       target="_blank">registro da aplicação cliente</a>


.. |documentacao_oficial| raw:: html

   <a href="https://transformacao-digital.gitbook.io/tutorial-transformacao-digital/bases-tecnologicas/api-de-avaliacao-do-gov.br"
      target="_blank">documentação oficial</a>


Biblioteca Jose4J
-----------------

A biblioteca :download:`jose4j-0.7.2.jar <_downloads/lib/jose4j-0.7.2.jar>` deve ser adicionada em
``$JBOSS_HOME/server/default/lib`` para que as funcionalidades relacionadas com JSON [#]_ funcionem corretamente.


.. rubric:: Notas
.. [#] Instituto Federal do Pará
.. [#] Sistema Integrado de Gestão de Atividades Acadêmicas
.. [#] Application Programming Interface
.. [#] Transport Layer Security
.. [#] Java Virtual Machine
.. [#] Hyper Text Transfer Protocol Secure
.. [#] JavaScript Object Notation
