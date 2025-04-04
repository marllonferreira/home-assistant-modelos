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

# Guia: Como Usar o Código de Emparelhamento de Tomadas no Home Assistant

Este passo a passo explica como configurar e usar o código YAML de emparelhamento de tomadas no Home Assistant.

## Passo a Passo

### 1. Acesse a Interface do Home Assistant
- Certifique-se de que sua instância do Home Assistant está rodando e você tem acesso à interface de administração.

### 2. Abra o Editor de Automação
- No menu lateral, clique em **Configurações**.
- Em seguida, vá para **Automação e Cenas**.
- Clique em **Nova Automação** e depois em **Editar YAML** no canto superior direito (ou, dependendo da interface, você pode acessar diretamente o editor de YAML).

### 3. Copie e Cole o Código
- Insira o seguinte código no editor de YAML:


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

