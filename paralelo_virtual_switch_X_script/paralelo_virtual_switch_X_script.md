# Emparelhar Script e Tomada (Modelo)

Este código é uma automação para sincronizar o estado entre um **script** e um **switch** no Home Assistant, criando um "paralelo virtual". Sempre que o estado de um dos dois mudar, o outro será atualizado automaticamente.

## **Descrição**

Este modelo realiza o emparelhamento entre um switch e um script, permitindo que suas ações sejam sincronizadas. Ele utiliza triggers para monitorar mudanças de estado e ações condicionais para atualizar o outro dispositivo.

## **Funcionamento**
1. **Trigger**: Detecta mudanças no estado do **script** ou do **switch**.
2. **Condições**: Verifica qual dispositivo originou a mudança de estado.
3. **Ação**:
   - Se a mudança for no **script**, atualiza o estado do **switch**.
   - Se a mudança for no **switch**, atualiza o estado do **script**.


## Adicionando a Automação no Home Assistant

Existem algumas maneiras de adicionar essa automação ao seu Home Assistant. Abaixo estão os métodos mais comuns:

### Método 1: Usando a Interface Gráfica (UI)

Este método é recomendado para a maioria dos usuários.

1.  **Navegue até a seção de Automações:**
    * No menu lateral do seu Home Assistant, clique em **Configurações**.
    * Dentro das configurações, clique em **Automações e Cenas**.

2.  **Crie uma nova automação:**
    * No canto inferior direito, clique no botão **"+"** (Adicionar Automação).

3.  **Mude para o modo YAML:**
    * No canto superior direito da janela de criação de automação, clique nos três pontos ("...") e selecione **Editar em YAML**.

4.  **Cole o código da automação:**
    * Apague qualquer código que possa estar na janela do editor YAML.
    * Cole o seguinte código:


```yaml
alias: Emparelhar Script e Tomada_modelo
description: Paralelo virtual entre um switch e um script
triggers:
  - entity_id: script.seu_script_aqui
    trigger: state
  - entity_id: switch.seu_switch_aqui
    trigger: state
actions:
  - choose:
      - conditions:
          - condition: template
            value_template: "{{ trigger.entity_id == 'script.seu_script_aqui' }}"
        sequence:
          - target:
              entity_id: switch.seu_switch_aqui
            action: switch.turn_{{ trigger.to_state.state }}
      - conditions:
          - condition: template
            value_template: "{{ trigger.entity_id == 'switch.seu_switch_aqui' }}"
        sequence:
          - target:
              entity_id: script.seu_script_aqui
            action: script.turn_{{ trigger.to_state.state }}
```
## 4. Ajuste os IDs das Entidades
- Substitua os placeholders no código (script.seu_script_aqui e switch.seu_switch_aqui) pelos IDs corretos das suas entidades.

## Para encontrar os IDs:

- Vá em Configurações > Dispositivos e Serviços > Entidades.

Localize o ID da entidade que corresponde ao seu script e ao seu switch.

## 5. Salve a Automação
- Após colar e ajustar o código, clique no botão Salvar.

## 6. Ative a Automação
- Certifique-se de que a automação foi habilitada.

No menu Automação e Cenas, verifique se o botão de ativação ao lado da sua automação está ligado.

## 7. Teste a Automação
- Altere manualmente o estado do script ou do switch para verificar se o outro dispositivo está sendo sincronizado corretamente.

Se a automação não funcionar como esperado:

Confira os logs do sistema em Configurações > Logs para identificar possíveis erros.

## 8. Aproveite a Automação!
Agora, sua automação deve estar funcionando como esperado, permitindo o sincronismo entre o script e o switch de forma eficiente.