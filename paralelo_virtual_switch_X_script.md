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

# Como Adicionar a Automação ao Home Assistant

Siga este guia passo a passo para configurar e adicionar o código YAML de automação no Home Assistant.

---

## **1. Acesse o Home Assistant**
- Certifique-se de que sua instância do Home Assistant está funcionando.
- Faça login na interface administrativa.

---

## **2. Vá para o Editor de Automação**
- No menu lateral, clique em **Configurações**.
- Em seguida, selecione **Automação e Cenas**.
- Clique no botão **Nova Automação**.
- Na nova tela, clique em **Editar YAML**, no canto superior direito, para acessar o editor avançado.

---

## **3. Cole o Código YAML**
- Copie o código de automação que deseja adicionar (substitua pelos IDs corretos, se necessário) e cole no editor de YAML.

### **Exemplo do Código**:

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