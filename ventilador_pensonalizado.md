# Ventilador Personalizado - Automação com Home Assistant

- Este código cria uma integração para um ventilador personalizado utilizando a plataforma de templates do Home Assistant. Ele permite alternar entre diferentes velocidades e modos.

# Controle de Modos via Switches

Este código foi desenvolvido para controlar o estado de três switches (`exemplo_1`, `exemplo_2` e `exemplo_3`) em um sistema automatizado, como o Home Assistant. Ele permite definir diferentes modos de operação com base nos estados desses switches.

## Funcionalidades

- **Verificação de Estados**:
  - Usa `value_template` para identificar se algum dos switches está ligado.

- **Ligar e Desligar**:
  - O comando `turn_on` ativa o `switch.exemplo_3`.
  - O comando `turn_off` desativa todos os switches (`exemplo_1`, `exemplo_2` e `exemplo_3`).

- **Modos Predefinidos**:
  - Define três modos de operação: `high` (alto), `medium` (médio) e `low` (baixo).
  - O estado atual é determinado automaticamente com base no switch ativo.

- **Alteração de Modos**:
  - Permite alternar entre os modos predefinidos ativando o switch correspondente.

## Como Funciona

O código usa templates para gerenciar os modos com base nos estados dos switches:
- **Modo Atual**: Determinado com base em qual switch está ligado.
- **Comandos**: Os modos podem ser alterados diretamente usando `set_preset_mode`.

Este script é ideal para configurar dispositivos que utilizam modos ou níveis, como ventiladores, luzes ou sistemas similares.



## Como Usar

1. Insira este código na configuração do seu Home Assistant.
2. Certifique-se de que os switches `exemplo_1`, `exemplo_2` e `exemplo_3` estão corretamente configurados e conectados ao sistema.
3. Use os comandos de ligar, desligar e alterar modos para controlar o ventilador.

## Passos para adicionar ao Home Assistant


1. No diretório do Home Assistant, navegue até a pasta onde deseja salvar o arquivo. Normalmente, fica em `/config/`.
2. Crie um arquivo chamado `fan_ventilador_quarto.yaml`.

### Cole o Código no Arquivo
1. Copie o código.

```yaml
platform: template
fans:
  ventilador_personalizado:
    friendly_name: "Ventilador_quarto"
    unique_id: "Ventilador_quarto"
    value_template: >
      {{ is_state('switch.exemplo_1', 'on') or is_state('switch.exemplo_2', 'on') or is_state('switch.exemplo_3', 'on') }}
    turn_on:
      service: switch.turn_on
      target:
        entity_id: switch.exemplo_3
    turn_off:
      service: switch.turn_off
      target:
        entity_id:
          - switch.exemplo_1
          - switch.exemplo_2
          - switch.exemplo_3
    speed_count: 3
    preset_modes:
      - high
      - medium
      - low
    preset_mode_template: >
      {% if is_state('switch.exemplo_1', 'on') %}
        high
      {% elif is_state('switch.exemplo_2', 'on') %}
        medium
      {% elif is_state('switch.exemplo_3', 'on') %}
        low
      {% else %}
        off
      {% endif %}
    set_preset_mode:
      service: switch.turn_on
      target:
        entity_id: >
          {% if preset_mode == 'high' %}
            switch.exemplo_1
          {% elif preset_mode == 'medium' %}
            switch.exemplo_2
          {% elif preset_mode == 'low' %}
            switch.exemplo_3
          {% endif %}
```

2. Cole o código no arquivo `fan_ventilador_quarto.yaml`.

### Inclua o Arquivo no `configuration.yaml`
1. Adicione uma referência ao arquivo no seu `configuration.yaml` com o seguinte comando:
   ```yaml
   fan: !include fan_ventilador_quarto.yaml
   ```

## Verifique os Switches
Certifique-se de que os switches `exemplo_1`, `exemplo_2` e `exemplo_3` estão corretamente configurados e funcionando.

## Reinicie o Home Assistant
1. Salve as alterações no arquivo `configuration.yaml`.
2. Reinicie o Home Assistant para aplicar as mudanças.

## Teste o Ventilador
1. Acesse o painel de controle do Home Assistant.
2. Procure pelo ventilador chamado **Ventilador_quarto**.
3. Teste as funcionalidades de ligar, desligar e alternar modos (alto, médio e baixo).



