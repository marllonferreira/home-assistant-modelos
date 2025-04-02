Emparelhar Tomadas (modelo)
Este script realiza o emparelhamento de dois switches para que o estado de um seja automaticamente replicado no outro. Ele funciona como um "paralelo virtual", ideal para sincronizar dispositivos em sistemas de automação.

Descrição
Este modelo utiliza triggers para monitorar o estado dos switches e ações para alterar o estado do outro switch com base no evento capturado.

Estrutura
Alias: Nome da automação para facilitar sua identificação.

Triggers: Define os dispositivos que irão disparar a automação ao terem seus estados alterados. No exemplo, switch.exemplo_1 e switch.exemplo_2.

Ações: Utiliza uma lógica condicional baseada no switch que disparou o trigger (trigger.entity_id) para alterar o estado do outro switch.