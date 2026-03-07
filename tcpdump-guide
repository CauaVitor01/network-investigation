# Análise de Tráfego de Rede com `tcpdump`

Este repositório contém a documentação técnica e guias práticos sobre a utilização do **tcpdump**, uma ferramenta essencial para Engenharia de Detecção, Resposta a Incidentes (IR) e análise forense de rede.

---

## 1. Operação e Configuração de Interface

O `tcpdump` requer a definição precisa da interface de escuta e o controle de como os dados serão processados para evitar sobrecarga de telemetria.

| Parâmetro | Função Técnica |
| --- | --- |
| `-i [interface]` | Define a interface de rede (ex: `ens5`, `eth0`). Use `any` para todas. |
| `-n` | Desativa a resolução de nomes (DNS), exibindo endereços IP numéricos. |
| `-nn` | Desativa a resolução de nomes e de portas (exibe `80` em vez de `http`). |
| `-c [count]` | Limita a captura a um número específico de pacotes. |
| `-v / -vv / -vvv` | Níveis progressivos de detalhamento (Verbose) do cabeçalho. |

---

## 2. Gestão de Arquivos de Captura (PCAP)

Para análises forenses detalhadas em ferramentas como Wireshark ou Brim, o tráfego deve ser persistido em disco.

* **Escrita:** `tcpdump -i ens5 -w captura.pcap`
* *Salva o tráfego bruto (raw) sem renderização no terminal.*


* **Leitura:** `tcpdump -r captura.pcap`
* *Processa pacotes de um arquivo existente, permitindo re-filtragem de dados históricos.*



---

## 3. Expressões de Filtragem (BPF)

A filtragem eficiente via **Berkeley Packet Filter (BPF)** é vital para isolar artefatos maliciosos em redes de alto tráfego.

### 3.1. Filtros de Escopo

* **Host:** `host 192.168.1.1` (origem ou destino).
* **Direção:** `src host [IP]` ou `dst host [IP]`.
* **Porta:** `port 53` (DNS), `port 443` (HTTPS) ou `src port [número]`.

### 3.2. Filtros de Protocolo

Permite isolar camadas específicas da pilha TCP/IP:

* `icmp`: Detecção de pings, traceroutes e tunelamento.
* `tcp` / `udp`: Focado em segmentos de transporte.
* `ip` / `ip6`: Focado em pacotes de nível de rede.

---

## 4. Operadores Lógicos e Composições

Essenciais para investigações cirúrgicas e redução de ruído:

* **`and`**: Ambas as condições devem ser verdadeiras.
* **`or`**: Uma das condições deve ser verdadeira.
* **`not`**: Exclui o padrão especificado (ex: `not port 22` para ocultar ruído de SSH).

---

## 5. Filtragem Avançada e Flags TCP

### 5.1. Comprimento de Pacote

Utilizado para detectar exfiltração de dados ou pacotes anômalos (MTU):

* `greater [LENGTH]`: Pacotes maiores ou iguais ao valor.
* `less [LENGTH]`: Pacotes menores ou iguais ao valor.

### 5.2. Lógica Binária e Flags TCP

A filtragem profunda utiliza máscaras de bits para identificar o estado da conexão TCP.

| Objetivo da Captura | Comando Sugerido |
| --- | --- |
| **Apenas pacotes SYN** | `tcpdump "tcp[tcpflags] == tcp-syn"` |
| **Qualquer pacote com SYN ativo** | `tcpdump "tcp[tcpflags] & tcp-syn != 0"` |
| **Detecção de Stealth Scan (Null)** | `tcpdump "tcp[tcpflags] == 0"` |

---

## 6. Formatação e Exibição de Dados (Output)

A forma como os dados são exibidos altera drasticamente a velocidade da análise manual.

| Opção | Nome | Descrição Técnica |
| --- | --- | --- |
| **`-q`** | Quick | Informações resumidas (IPs, Portas e Tamanho). |
| **`-e`** | Link-Level | Imprime o cabeçalho da Camada 2 (**Endereços MAC**). |
| **`-A`** | ASCII | Conteúdo em texto (Ideal para inspecionar credenciais em protocolos clear-text). |
| **`-xx`** | Hexadecimal | Dados em formato hex (Para análise de protocolos binários). |
| **`-X`** | Hex + ASCII | Renderiza hexadecimal e texto lado a lado. |


## 7. Conclusão Técnica

O domínio do `tcpdump` transita da simples observação para a **perícia cirúrgica**. A habilidade de combinar operadores lógicos com inspeção de flags TCP permite identificar comportamentos adversários como *Lateral Movement*, *Port Scanning* e *C2 Beaconing* antes mesmo da ingestão dos logs em um SIEM.
