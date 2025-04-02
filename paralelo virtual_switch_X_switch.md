# Emparelhar Tomadas (Modelo)

Este script realiza o emparelhamento de dois switches para que o estado de um seja automaticamente replicado no outro. 
Ele funciona como um "paralelo virtual".

# Descrição
Este modelo utiliza triggers para monitorar o estado dos switches e ações para alterar o estado do outro switch com base no evento capturado.

# Estrutura
**Alias**: Nome da automação para facilitar sua identificação.

**Triggers**: Define os dispositivos que irão disparar a automação ao terem seus estados alterados. No exemplo, switch.exemplo_1 e switch.exemplo_2.

**actions**: Utiliza uma lógica condicional baseada no switch que disparou o trigger (trigger.entity_id) para alterar o estado do outro switch.

# Funcionamento
Quando o estado do switch.exemplo_1 muda, a automação altera o estado do switch.exemplo_2 para o mesmo valor.

Da mesma forma, quando o estado do switch.exemplo_2 muda, o estado do switch.exemplo_1 é sincronizado.

A lógica condicional no campo entity_id garante que o switch apropriado seja atualizado com base no que originou o evento.


## Modelo de Código

```yaml
alias: Emparelhar Tomadas_modelo
description: Modelo de emparelhamento de tomadas ou paralelo virtual
triggers:
  - entity_id: switch.exemplo_1
    trigger: state
  - entity_id: switch.exemplo_2
    trigger: state
actions:
  - target:
      entity_id: |
        {% if trigger.entity_id == 'switch.exemplo_1' %}
          switch.exemplo_2
        {% else %}
          switch.exemplo_1
        {% endif %}
    action: switch.turn_{{ trigger.to_state.state }}
```

4. **Repositório**:
   - Crie um repositório no GitHub.
   - Faça upload do arquivo `.yaml` e do `README.md`.

Dessa forma, outros usuários poderão visualizar, entender e utilizar sua automação com facilidade. Se precisar de mais assistência, é só pedir! 😊