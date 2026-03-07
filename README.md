# Relatório de Investigação de Rede: Detecção de Exfiltração e Varredura

## Sumário Executivo

Este relatório documenta a investigação de um incidente de rede simulado no qual o host interno **145.254.160.237** apresentou comportamento anômalo.

A análise do tráfego foi realizada utilizando a ferramenta **TShark**, permitindo identificar dois padrões de atividade maliciosa:

- **Reconhecimento de rede através de Port Scanning**
- **Exfiltração de dados através de tunelamento DNS**

Os resultados indicam uma tentativa de enumeração de serviços seguida por uma possível exfiltração de dados utilizando protocolos legítimos da rede.

---

# Metodologia de Investigação (SOC Tier 1 / Tier 2)

A investigação seguiu um fluxo estruturado de resposta a incidentes:

1. **Identificação**  
   Verificação da integridade e dos metadados do arquivo de captura (PCAP).

2. **Triagem**  
   Isolamento de tráfego suspeito através de filtros de análise.

3. **Análise de Payload**  
   Inspeção dos pacotes para identificação de **IOCs (Indicadores de Comprometimento)**.

4. **Mapeamento de Ameaça**  
   Correlação das atividades observadas com a matriz **MITRE ATT&CK**.

---

# Análise Forense e Evidências

## 1. Detecção de Reconhecimento de Rede (Port Scanning)

Durante a análise do arquivo `demo.pcapng`, foi identificada uma tentativa de reconhecimento de rede.

O padrão observado consiste em múltiplos pacotes **TCP SYN** enviados sem o correspondente **ACK**, direcionados para portas sequenciais.

Esse comportamento é característico de um **Horizontal Port Scan**, técnica utilizada para descobrir serviços ativos em um host alvo.

### Comando utilizado

```bash
### tshark -r demo.pcapng -Y "tcp.flags.syn == 1 && tcp.flags.ack == 0" | nl
Descoberta

A análise revelou diversas tentativas de conexão em portas sequenciais em um curto intervalo de tempo, indicando uma fase de enumeração realizada por um possível atacante ou ferramenta automatizada.

2. Detecção de Tunelamento e Exfiltração via DNS

A evidência mais crítica encontrada foi a presença de consultas DNS contendo subdomínios com alta entropia, compostos por sequências longas e aparentemente aleatórias de caracteres.

Esse tipo de padrão é frequentemente associado a ferramentas de Command and Control (C2) que utilizam o protocolo DNS para transmitir dados de forma encoberta.

Comando utilizado para extração das consultas DNS
tshark -r demo.pcapng -Y "dns.qry.type == 1" -T fields -e dns.qry.name | sort | uniq -c
Descoberta

Foram identificadas múltiplas consultas únicas para o mesmo domínio de topo, onde o subdomínio continha strings codificadas.

Esse padrão confirma o uso de tunelamento DNS para exfiltração de dados.

Mapeamento MITRE ATT&CK
Tática	Técnica	Observação
Discovery (TA0007)	Network Service Scanning (T1046)	Detectado através da análise de pacotes TCP SYN
Command and Control (TA0011)	Application Layer Protocol: DNS (T1102.001)	Uso do protocolo DNS como canal de comunicação
Exfiltration (TA0010)	Exfiltration Over Alternative Protocol (T1048)	Exfiltração de dados utilizando protocolo legítimo
Raciocínio de Investigação SOC
Impacto na Triagem

Esta investigação demonstra como ferramentas de análise em linha de comando podem acelerar significativamente a triagem de incidentes em ambientes SOC.

A combinação do TShark com ferramentas como:

grep

sort

uniq

permite gerar rapidamente listas de IPs, domínios suspeitos e indicadores de comprometimento, facilitando ações de bloqueio e contenção.

Recomendações de Detecção e Resposta
Regra para SIEM

Criar uma regra de correlação que gere alerta quando:

Um único host interno realizar mais de 50 consultas DNS

Para subdomínios diferentes

Dentro de um intervalo de 1 minuto

Esse comportamento é altamente indicativo de tunelamento DNS.

Medidas de Contenção

Bloquear os domínios identificados na camada de filtragem DNS corporativa

Adicionar os domínios à lista de bloqueio da organização

Isolar o host 145[.]254[.]160[.]237 para investigação forense aprofundada

Conclusão Técnica

A análise confirmou que o tráfego investigado não representa apenas ruído de rede, mas sim uma tentativa ativa de reconhecimento e exfiltração de dados.

O domínio de ferramentas de análise em linha de comando, como TShark e Capinfos, demonstrou ser fundamental para uma resposta rápida e eficaz durante a investigação.

Esse tipo de abordagem é essencial para operações de Security Operations Center (SOC) e processos de resposta a incidentes.



tunelamento DNS (DNS tunneling)
Indicadores de Comprometimento (IOCs)
Security Operations Center (SOC)
