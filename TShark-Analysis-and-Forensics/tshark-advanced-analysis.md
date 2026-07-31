# Network Forensics & Protocol Analysis with TShark

Este repositório documenta a implementação de metodologias avançadas de análise de tráfego de rede utilizando o TShark (a interface de linha de comando do Wireshark). O foco é a extração de inteligência de rede, identificação de táticas adversárias e automação da triagem de incidentes em ambientes SOC.

---

## Sumário
* Análise Estatística e Visibilidade
* Inspeção Profunda de Protocolos (DPI)
* Filtragem Avançada e Payload Inspection
* Casos de Uso: Extração de Indicadores (IoCs)
* Impacto Operacional e Performance

---

## 1. Análise Estatística e Visibilidade

A análise de rede em larga escala exige a síntese de dados para identificar anomalias sem o overhead de interfaces gráficas.

* **Hierarquia de Protocolos (-z ptype,tree):** Identificação de volumetria anômala (ex: túneis ICMP ou tráfego HTTP em portas não padrão).
* **Mapeamento de Endpoints (-z ip_hosts,tree):** Consolidação de IPs ativos para identificação imediata de Top Talkers.
* **Direcionalidade (-z ip_srcdst,tree):** Correlação de fluxos para detetar exfiltração de dados (Inbound vs Outbound).

---

## 2. Inspeção Profunda de Protocolos (DPI)

Monitorização específica de protocolos críticos para detetar infraestruturas de Comando e Controlo (C2) e comprometimento de serviços web.

* **Auditoria DNS (-z dns,tree):** Análise de rcodes (ex: NXDOMAIN para detetar DGA) e opcodes.
* **Métricas HTTP/HTTP2 (-z http,tree):** Categorização de respostas (2xx, 3xx, 4xx, 5xx) e distribuição de carga por servidor (-z http_srv,tree).
* **Diagnósticos Expert:** Extração de retransmissões TCP e instabilidades, indicativas de ataques de DoS ou problemas de conectividade.

---

## 3. Filtragem Avançada e Payload Inspection

Para investigações onde os cabeçalhos estáticos são insuficientes, utilizo operadores lógicos para inspeção de conteúdo.

* **contains:** Busca binária e de strings (case-sensitive) para localizar assinaturas de servidores (ex: "Apache") ou extensões de ficheiros.
* **matches:** Implementação de Regex (PCRE) para detetar padrões complexos, como múltiplos métodos HTTP ou tokens de autenticação.
* **Extração Seletiva (-T fields):** Transformação de pacotes brutos em dados estruturados (CSV/JSON) focando apenas em campos de interesse como `ip.src`, `ip.dst` e `http.user_agent`.

---

## 4. Casos de Uso: Extração de Indicadores (IoCs)

Demonstração da utilização de pipelines Linux (`awk`, `sort`, `uniq`) para processar telemetria de rede.

### A. Inventário de Hostnames (DHCP)

```bash
tshark -r hostnames.pcapng -T fields -e dhcp.option.hostname | awk NF | sort -r | uniq -c | sort -r
