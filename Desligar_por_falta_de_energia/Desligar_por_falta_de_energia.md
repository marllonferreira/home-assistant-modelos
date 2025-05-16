# Automação Home Assistant: Notificação e Desligamento em Queda de Energia

Este documento descreve uma automação para Home Assistant que monitora a disponibilidade de dispositivos conectados à energia (como lâmpadas e tomadas inteligentes) para detectar uma possível queda de energia. Se a falta de energia persistir por um período, a automação envia notificações e, por fim, desliga o host do Home Assistant para preservar a carga de um nobreak (UPS).

## Código YAML da Automação

Abaixo está o código YAML completo da automação. Você pode copiá-lo e colá-lo no editor de automações do Home Assistant (Modo YAML) ou no seu arquivo `automations.yaml`.

```yaml
alias: sem_energia - notificar e desligar
description: Avisa de uma possivel queda de energia e desliga o HA após 25 min
triggers:
  - entity_id:
      - switch.lampada_do_quarto_local_switch_1
      - switch.lampada_da_frente_local_switch_1
      - switch.lampada_da_cozinha_local_switch_1
      - switch.lampada_da_sala_local_switch_1
      - switch.lampada_do_quintal_local_switch_1
      - switch.tomada_escritorio_local_switch_1
      - switch.tomada_quarto_local_switch_1
    to: unavailable
    trigger: state
    for:
      hours: 0
      minutes: 1
      seconds: 0
conditions: []
actions:
  # Espera que todos os dispositivos especificados fiquem indisponíveis (confirmação inicial da falta de energia)
  - wait_template: >-
      {% set entities = [
          'switch.lampada_do_quarto_local_switch_1',
          'switch.lampada_da_frente_local_switch_1',
          'switch.lampada_da_cozinha_local_switch_1',
          'switch.lampada_da_sala_local_switch_1',
          'switch.lampada_do_quintal_local_switch_1',
          'switch.tomada_escritorio_local_switch_1',
          'switch.tomada_quarto_local_switch_1',
      ] %} {% set state_count = entities | select('is_state', 'unavailable') |
      list | count %} {{ state_count == entities | count }}
    timeout: "00:02:00" # Dá 2 minutos para todos os dispositivos relatarem indisponibilidade
    continue_on_timeout: false # Para a automação se nem todos ficarem indisponíveis dentro do tempo limite

  # Adiciona um atraso de 1 minuto antes de verificar a condição novamente e notificar
  - delay:
      hours: 0
      minutes: 1
      seconds: 0
      milliseconds: 0

  # Verificação final antes de enviar a notificação inicial
  - condition: template
    value_template: >-
      {% set entities = [
          'switch.lampada_do_quarto_local_switch_1',
          'switch.lampada_da_frente_local_switch_1',
          'switch.lampada_da_cozinha_local_switch_1',
          'switch.lampada_da_sala_local_switch_1',
          'switch.lampada_do_quintal_local_switch_1',
          'switch.tomada_escritorio_local_switch_1',
          'switch.tomada_quarto_local_switch_1',
      ] %} {% set state_count = entities | select('is_state', 'unavailable') |
      list | count %} {{ state_count == entities | count }}

  # Envia a notificação inicial para o bot com a hora
  - data:
      message: Possível queda de energia detectada às {{ states('sensor.time') }}
      title: "*Atenção!*"
    action: notify.bot

  # Envia A MESMA notificação inicial para o celular com a hora
  - data:
      message: Possível queda de energia detectada às {{ states('sensor.time') }}
      title: "*Atenção!*"
    action: notify.mobile_app_celular

  # ---- Início da sequência de desligamento ----

  # Espera 22 minutos
  - delay:
      hours: 0
      minutes: 22
      seconds: 0
      milliseconds: 0

  # Verifica NOVAMENTE se TODOS os dispositivos ainda estão indisponíveis após o atraso
  # Se a energia tiver voltado, esta condição falhará e a automação parará aqui.
  - condition: template
    value_template: >-
      {% set entities = [
          'switch.lampada_do_quarto_local_switch_1',
          'switch.lampada_da_frente_local_switch_1',
          'switch.lampada_da_cozinha_local_switch_1',
          'switch.lampada_da_sala_local_switch_1',
          'switch.lampada_do_quintal_local_switch_1',
          'switch.tomada_escritorio_local_switch_1',
          'switch.tomada_quarto_local_switch_1',
      ] %} {% set state_count = entities | select('is_state', 'unavailable') |
      list | count %} {{ state_count == entities | count }}

  # Envia a notificação de que o Home Assistant será desligado para o bot com a hora
  - data:
      message: >-
        Às {{ states('sensor.time') }}, a energia ainda está desligada. Home
        Assistant será desligado por segurança em 2 minutos.
      title: "*Desligando Home Assistant*"
    action: notify.bot

  # Envia A MESMA notificação de que o Home Assistant será desligado para o celular com a hora
  - data:
      message: >-
        Às {{ states('sensor.time') }}, a energia ainda está desligada. Home
        Assistant será desligado por segurança em 2 minutos.
      title: "*Desligando Home Assistant*"
    action: notify.mobile_app_celular

  # Espera 20 segundos antes de desligar
  - delay:
      hours: 0
      minutes: 0
      seconds: 20
      milliseconds: 0

  # Desliga o Home Assistant (e o sistema hospedeiro)
  - action: hassio.host_shutdown # Sintaxe que a UI pode gerar/manter para este serviço
    data: {} # Incluído conforme a observação da UI

mode: single # Garante que apenas uma instância desta automação seja executada por vez

```


**Nota:** A lista de entidades (`entity_id`) nos `triggers`, `wait_template` e `condition` templates contém os dispositivos que estavam no código original fornecido. **Você deve ajustar esta lista** para incluir os dispositivos que você deseja monitorar na sua própria instalação do Home Assistant. Certifique-se de que são entidades que realmente ficam `unavailable` quando a energia cai.

## Como Funciona

1.  **Gatilho (`triggers`):** A automação é acionada quando *qualquer* uma das entidades listadas (lâmpadas, tomadas) muda para o estado `unavailable` e permanece nesse estado por 1 minuto. Isso geralmente acontece quando um dispositivo perde a conexão com o Home Assistant, o que é um forte indicativo de falta de energia se vários dispositivos se comportam assim simultaneamente.
2.  **Ações (`actions`):**
    * **Confirmação Inicial (`wait_template`):** A automação espera por até 2 minutos para que *todos* os dispositivos listados fiquem `unavailable`. Se isso não acontecer dentro do tempo limite, a automação é interrompida (`continue_on_timeout: false`). Isso ajuda a evitar falsos positivos causados por problemas isolados de um único dispositivo.
    * **Atraso Inicial (`delay`):** Há um atraso de 1 minuto após a confirmação inicial.
    * **Nova Verificação (`condition`):** Antes de notificar, a automação verifica novamente se *todos* os dispositivos ainda estão `unavailable`. Se a energia tiver retornado rapidamente, esta condição falhará e a automação parará.
    * **Notificação de Alerta:** Se a condição passar, notificações são enviadas para `notify.bot` e `notify.mobile_app_celular` informando sobre a possível queda de energia, incluindo a hora da detecção.
    * **Espera para Desligar (`delay`):** A automação espera por mais 22 minutos.
    * **Verificação Final (`condition`):** Após a espera de 22 minutos, a automação verifica *mais uma vez* se todos os dispositivos ainda estão `unavailable`. Esta é a última chance para a automação ser cancelada caso a energia tenha sido restaurada.
    * **Notificação de Desligamento:** Se a energia ainda estiver fora, notificações são enviadas novamente para `notify.bot` e `notify.mobile_app_celular`, avisando que o Home Assistant será desligado por segurança em 2 minutos.
    * **Atraso Final (`delay`):** Um atraso de 20 segundos é adicionado antes do desligamento para dar um pequeno intervalo após a última notificação. (Nota: A soma dos atrasos após a primeira notificação e antes da segunda notificação é 1 minuto + 22 minutos = 23 minutos. O atraso final é de 20 segundos antes do desligamento. O tempo total entre a primeira notificação e o desligamento é de 23 minutos e 20 segundos, não exatamente 25 minutos como na descrição, mas segue a lógica dos atrasos definidos no código fornecido.)
    * **Desligamento do Host (`action: hassio.host_shutdown data: {}`):** O serviço `hassio.host_shutdown` é chamado. Este serviço instrui o sistema operacional onde o Home Assistant está rodando a desligar completamente a máquina. Este é o passo crucial para preservar a bateria do seu nobreak. A sintaxe `action: ... data: {}` é frequentemente usada pela interface do Home Assistant para serviços de sistema.
    * **Modo (`mode: single`):** Garante que apenas uma instância desta automação possa estar em execução por vez. Se uma nova queda de energia for detectada enquanto a automação anterior ainda estiver rodando (por exemplo, durante o longo atraso), a nova detecção será ignorada.

## Pré-requisitos

* Home Assistant instalado (preferencialmente uma instalação que suporte o Supervisor, como Home Assistant OS ou Supervised).
* Dispositivos (lâmpadas, tomadas, etc.) que reportam o estado `unavailable` quando perdem a comunicação (geralmente dispositivos Wi-Fi ou Zigbee/Z-Wave com um coordenador que perde energia).
* Uma integração de notificação configurada (neste exemplo, `notify.bot` e `notify.mobile_app_celular`). Adapte os nomes dos serviços de notificação conforme sua configuração.
* (Opcional, mas recomendado) Um nobreak (UPS) conectado ao host do Home Assistant para permitir um desligamento seguro.

## Como Implementar

1.  No Home Assistant, vá para `Configurações` -> `Automações e Cenas`.
2.  Clique no botão `+ Adicionar Automação`.
3.  Clique em `Iniciar com uma automação vazia`.
4.  No canto superior direito, clique no menu de três pontos e selecione `Editar no editor YAML`.
5.  Apague o código existente e cole o código YAML fornecido neste documento.
6.  **Importante:** **Ajuste a lista de `entity_id`** nos `triggers`, `wait_template` e `condition` templates para incluir **os dispositivos que você deseja monitorar na sua própria instalação do Home Assistant**. Certifique-se de que são entidades que realmente ficam `unavailable` quando a energia cai.
7.  **Importante:** Ajuste os nomes dos serviços de notificação (`notify.bot` e `notify.mobile_app_celular`) para corresponder aos seus serviços de notificação configurados no Home Assistant.
8.  Clique em `Salvar` no canto inferior direito.
9.  Teste a automação (com cuidado!) simulando a indisponibilidade dos dispositivos listados.

## Considerações Adicionais

* **Falsos Positivos:** A precisão desta automação depende da confiabilidade dos seus dispositivos em reportar o estado `unavailable` durante uma falta de energia. Dispositivos com bateria interna ou que se comportam de maneira diferente podem gerar falsos positivos ou negativos.
* **Tempo de Desligamento:** Ajuste os tempos nos `delay` conforme a sua necessidade e a autonomia do seu nobreak.
* **Entidades Monitoradas:** Escolha entidades que são essenciais e que *certamente* perderão a comunicação durante uma queda de energia. Evite dispositivos que possam ficar online por outros meios (ex: dispositivos com 4G de backup).
* **Serviço `hassio.host_shutdown`:** Este serviço só está disponível em instalações do Home Assistant que incluem o Supervisor (como Home Assistant OS ou Home Assistant Supervised). Se você usa Home Assistant Container ou Core, precisará encontrar uma maneira diferente de desligar o host (geralmente via script externo ou integração específica).

Este documento e o código fornecido estão disponíveis para uso e modificação sob uma licença de código aberto (por exemplo, MIT ou similar, se desejar especificar uma).