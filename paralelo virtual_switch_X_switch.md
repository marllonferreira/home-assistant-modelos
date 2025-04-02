# Emparelhar Tomadas (Modelo)

Este script realiza o emparelhamento de dois switches para sincronizar seus estados, funcionando como um "paralelo virtual".

## Código

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


4. **Repositório**:
   - Crie um repositório no GitHub.
   - Faça upload do arquivo `.yaml` e do `README.md`.

Dessa forma, outros usuários poderão visualizar, entender e utilizar sua automação com facilidade. Se precisar de mais assistência, é só pedir! 😊