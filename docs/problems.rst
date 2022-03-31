Problemas Encontrados
=====================

Em relação a API de integração com o gov.br, não houve problemas identificados. A documentação se mostrou
eficiente e satisfatória. Os *endpoints* funcionaram conforme foram definidos na documentação.

Os principais problemas encontrados foram em relação a conexões via HTTPS. Foram necessários adicionar ajustes
para que a nossa versão da JVM [#]_ pudesse executar as conexões de forma adequada, seja na forma de
configuração de certificados digitais, seja na forma de ajustes nas versões TLS [#]_.

Outro problema que surgiu futuramente foi a atualização dos servidores govbr. Essa atualização depreciou
*ciphers* menos seguros, o que impediu nosso Java 7 (que não possui os *cyphers* mais seguros e recentes) de
estabelecer conexões https com os servidores atualizados do govbr.

A solução foi desenvolver uma aplicação *proxy* (com uma versão Java mais recente) que efetivamente envia
solicitações HTTP para os *endpoints* do govbr. O SIG envia suas solicitações para essa aplicação *proxy* que
por sua vez redireciona para o govbr — a resposta do govbr chega para a aplicação *proxy* que então
redireciona para o SIG.

.. rubric:: Notas

.. [#] Java Virtual Machine
.. [#] Transport Layer Security
