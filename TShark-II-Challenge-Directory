# Relatório de Investigação: Desafio TShark II - TryHackMe

Este repositório documenta a resolução do desafio **TShark II** da plataforma **TryHackMe**. [cite_start]A investigação foca na análise forense de um arquivo de captura de rede (`.pcap`) utilizando o **TShark** para identificar infraestrutura maliciosa, comportamentos de rede e exfiltração de artefatos[cite: 1, 2, 3].

---

## 1. Identificação de Infraestrutura Maliciosa
[cite_start]O objetivo inicial foi filtrar consultas de rede para identificar domínios com baixa reputação ou comportamentos suspeitos de Comando e Controle (C2)[cite: 3].

* [cite_start]**Metodologia**: Investigação de consultas DNS e validação de domínios via VirusTotal[cite: 5].
* [cite_start]**Domínio Identificado**: `jx2-bavuong.com`[cite: 6].
* [cite_start]**Endereço IP Associado**: `141.164.41.174`[cite: 13, 15].
* [cite_start]**Informações do Servidor**: O host utiliza **Apache/2.2.11 (Win32)** com suporte a PHP 5.2.9 e OpenSSL 0.9.8i[cite: 17, 19].

**Comando utilizado para extração de domínios:**
```bash
tshark -r directory-curiosity.pcap -Y "dns" -T fields -e "dns.qry.name"
