Problemas Encontrados
=====================

Em relação a API de integração com o gov.br, não houve problemas identificados. A documentação se mostrou eficiente
e satisfatória. Os *endpoints* funcionaram conforme foram definidos na documentação.

Os principais problemas encontrados foram em relação a conexões via HTTPS. Foram necessários adicionar ajustes
para que a nossa versão da JVM [#]_ pudesse executar as conexões de forma adequada, seja na forma de configuração
de certificados digitais, seja na forma de ajustes nas versões TLS [#]_.


.. rubric:: Notas

.. [#] Java Virtual Machine
.. [#] Transport Layer Security
