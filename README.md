# 🎣 Simulação de Phishing com GoPhish

[![Security](https://img.shields.io/badge/Security-Blue%20Team-0078d4?style=flat-square&logo=shield)](https://github.com/IcaroLW)
[![MITRE ATT&CK](https://img.shields.io/badge/MITRE-T1566.002%20Spearphishing-red?style=flat-square)](https://attack.mitre.org/techniques/T1566/002/)
[![GoPhish](https://img.shields.io/badge/Tool-GoPhish-00c853?style=flat-square)](https://getgophish.com)
[![CompTIA Security+](https://img.shields.io/badge/CompTIA-Security%2B%20SY0--701-red?style=flat-square)](https://www.comptia.org/certifications/security)
[![Homelab](https://img.shields.io/badge/Ambiente-Homelab-333?style=flat-square&logo=virtualbox)](https://www.virtualbox.org)

> Campanha de phishing simulada em ambiente isolado, demonstrando o ciclo completo do ataque — da criação da infraestrutura à captura de credenciais — sob a perspectiva de quem estuda defesa.

---

## 📋 Índice

- [Objetivo](#-objetivo)
- [Ambiente](#-ambiente)
- [Técnicas Simuladas](#-técnicas-simuladas)
- [Fluxo do Ataque](#-fluxo-do-ataque)
- [Resultados](#-resultados)
- [Writeup Completo](#-writeup-completo)
- [Lições Aprendidas](#-lições-aprendidas)
- [Próximos Passos](#-próximos-passos)

---

## 🎯 Objetivo

Configurar uma infraestrutura de phishing funcional com GoPhish e MailHog, executar uma campanha simulada contra uma vítima em ambiente controlado, e documentar cada etapa do ataque com foco nas **contramedidas defensivas**.

```
Infraestrutura → Template de e-mail → Landing page falsa → Entrega → Captura de credenciais → Análise defensiva
```

---

## 🖥️ Ambiente

| Componente | Sistema | IP | Função |
|---|---|---|---|
| **GoPhish + MailHog** | Ubuntu 22.04 | `192.168.56.104` | Servidor de phishing e SMTP interno |
| **Vítima** | Windows 10 | `192.168.56.103` | Alvo da campanha |

> Rede isolada em modo **Host-Only** no VirtualBox — sem tráfego externo real. MailHog utilizado como SMTP interno para simular entrega de e-mail.

---

## ⚔️ Técnicas Simuladas

| Tática | Técnica | ID MITRE |
|---|---|---|
| Initial Access | Phishing: Spearphishing Link | [T1566.002](https://attack.mitre.org/techniques/T1566/002/) |
| Credential Access | Input Capture: Web Portal Capture | [T1056.003](https://attack.mitre.org/techniques/T1056/003/) |

---

## 🔄 Fluxo do Ataque

```
1. Sending Profile    →  SMTP via MailHog (porta 1025)
2. Email Template     →  Engenharia social com urgência + link rastreado {{.URL}}
3. Landing Page       →  Fake login page (Facebook) com captura de credenciais
4. Entrega            →  E-mail entregue à vítima via MailHog
5. Engajamento        →  Vítima clica no link e submete credenciais
6. Captura            →  GoPhish registra dados e exibe no dashboard
```

---

## 📊 Resultados

| Métrica | Resultado |
|---|---|
| E-mails enviados | 1 |
| E-mails abertos | ✅ 1 (100%) |
| Links clicados | ✅ 1 (100%) |
| Credenciais submetidas | ✅ 1 (100%) |
| E-mails reportados | ❌ 0 (0%) |

> Taxa de comprometimento: **100%** — a vítima completou todas as etapas sem identificar ou reportar o e-mail como suspeito.

---

## 📄 Writeup Completo

O writeup detalha passo a passo:
- Configuração da infraestrutura GoPhish (Sending Profile, Template, Landing Page, Grupos)
- Análise comportamental dos vetores de engenharia social utilizados
- Evidências visuais de cada etapa (dashboard, MailHog, landing page, credenciais capturadas)
- Recomendações defensivas: SPF/DKIM/DMARC, MFA, filtro de URL e treinamento de usuários

👉 **[Acesse o writeup completo →](./writeup.md)**

---

## 💡 Lições Aprendidas

- Como urgência artificial e spoofing de remetente reduzem o senso crítico da vítima
- Configuração de SMTP interno com MailHog para ambientes de teste isolados
- Por que landing pages com HTML importado diretamente do site-alvo precisam de ajuste no atributo `action` do formulário
- Como SPF, DKIM e DMARC funcionam como primeira barreira contra spoofing de domínio
- A importância do campo "Email Reported" como KPI real em simulações corporativas de phishing

---

## 🔭 Próximos Passos

- [ ] Adicionar **SPF/DKIM/DMARC** no domínio de teste e verificar bloqueio do e-mail
- [ ] Simular campanha com **múltiplos perfis de vítima** (taxa de clique vs. cargo simulado)
- [ ] Integrar alertas de clique no GoPhish com o **Wazuh SIEM** via webhook
- [ ] Documentar como um **SOC analyst** investigaria o incidente após a captura de credenciais

---

## 🔗 Referências

- [GoPhish Documentation](https://getgophish.com)
- [MailHog](https://github.com/mailhog/MailHog)
- [MITRE ATT&CK T1566.002 — Spearphishing Link](https://attack.mitre.org/techniques/T1566/002/)
- [MITRE ATT&CK T1056.003 — Web Portal Capture](https://attack.mitre.org/techniques/T1056/003/)
- [CompTIA Security+ SY0-701](https://www.comptia.org/certifications/security)

---

<p align="center">
  <sub>Projeto de homelab para fins educacionais — ambiente isolado e controlado. Nenhum sistema externo foi afetado.</sub>
</p>
