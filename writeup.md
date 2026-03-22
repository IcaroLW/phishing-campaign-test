# Simulação de Phishing com GoPhish

Simulação de campanha de phishing em ambiente controlado, demonstrando o fluxo completo do ataque desde a entrega do e-mail até a captura de credenciais da vítima.

---

## Objetivo

Configurar uma infraestrutura de phishing funcional, executar uma campanha simulada contra uma vítima em ambiente isolado e documentar cada etapa do ataque sob a perspectiva de quem defende.

---

## Ambiente

| Componente | Sistema | IP | Função |
|---|---|---|---|
| GoPhish Server | Ubuntu 22.04 | 192.168.56.104 | Servidor de phishing e SMTP interno |
| Vítima | Windows 10 | 192.168.56.103 | Alvo da campanha |

Toda a comunicação ocorreu em rede host-only isolada, sem acesso à internet real. O MailHog foi utilizado como servidor SMTP interno para simular a entrega de e-mails.

---

## Execução

### Infraestrutura

O GoPhish foi configurado com quatro componentes antes do lançamento da campanha:

- **Sending Profile** — SMTP apontando para o MailHog na porta 1025
- **Email Template** — mensagem com engenharia social baseada em urgência, com link rastreado via `{{.URL}}`
- **Landing Page** — página de login falsa simulando o Facebook, com captura de credenciais habilitada
- **Users & Groups** — vítima simulada `zezinho mequetrefe <zezinho@lab-local.com>`

### Campanha

O e-mail entregue à vítima continha a seguinte mensagem:

```
Por gentileza nas próximas 12hrs o Facebook irá revogar o e-mail e senha de todos os
usuários ativos na plataforma. É importante que você logue com suas credenciais e troque
de credenciais para não ser afetado por essa campanha de segurança.
Por favor clique no link abaixo.
```

A vítima acessou o e-mail pelo MailHog, clicou no link e foi direcionada à landing page falsa, onde inseriu e submeteu credenciais fictícias.

---

## Resultados

| Métrica | Resultado |
|---|---|
| Email Sent | 1 |
| Email Opened | 1 |
| Clicked Link | 1 |
| Submitted Data | 1 |
| Email Reported | 0 |

Taxa de comprometimento: **100%**. A vítima completou todas as etapas sem identificar ou reportar o e-mail como suspeito.

---

## Evidências

### Dashboard GoPhish
<img width="1919" height="1020" alt="gophish" src="https://github.com/user-attachments/assets/a8ce987e-7b30-445d-94f1-7af12d98bcfe" />


### E-mail recebido no MailHog
<img width="1919" height="1016" alt="mailhog" src="https://github.com/user-attachments/assets/fa3cb06b-44bf-4a22-8358-afea7faab526" />


### Landing page
<img width="1919" height="945" alt="tela de login falsa" src="https://github.com/user-attachments/assets/6b05ae4e-e0b2-41aa-a45d-eb44a5e72ded" />


### Credenciais capturadas
<img width="1919" height="1020" alt="gophish-resultados" src="https://github.com/user-attachments/assets/0cf2fd19-8b16-48de-9327-10f8672e0165" />


---

## Mapeamento MITRE ATT&CK

| Tática | Técnica | ID |
|---|---|---|
| Initial Access | Phishing: Spearphishing Link | [T1566.002](https://attack.mitre.org/techniques/T1566/002/) |
| Credential Access | Input Capture: Web Portal Capture | [T1056.003](https://attack.mitre.org/techniques/T1056/003/) |

---

## Análise — Perspectiva do Defensor

O ataque explorou três características comportamentais previsíveis:

**Urgência artificial.** O prazo de 12 horas induz a vítima a agir sem questionar a legitimidade do e-mail.

**Spoofing de remetente.** O campo `From` exibiu `seguranca@empresa-teste.com`, endereço com aparência legítima sem vínculo com o Facebook.

**Landing page familiar.** A reprodução visual do Facebook reduziu a desconfiança no momento da submissão.

---

## Recomendações

| Controle | Descrição |
|---|---|
| SPF / DKIM / DMARC | Impede spoofing de domínio e rejeita e-mails não autorizados |
| MFA | Torna credenciais capturadas inutilizáveis sem o segundo fator |
| Filtro de URL | Bloqueia acesso a domínios maliciosos na camada de rede |
| Treinamento de usuários | Simulações periódicas aumentam a taxa de reporte e reduzem cliques |

---

## Troubleshooting

**Instalação do MailHog via Go falhou.**
O Ubuntu 22.04 disponibiliza o Go 1.22 nos repositórios padrão, mas a versão mais recente do MailHog exige Go 1.25+. A solução foi baixar o binário pré-compilado diretamente do repositório oficial, eliminando a dependência de versão.

**Landing page do Facebook redirecionava para o site original.**
O HTML importado do Facebook continha o atributo `action` do formulário apontando para o domínio real. Com mais de 1.500 linhas minificadas, a edição manual não era viável. A solução foi substituir o HTML por uma página genérica simples com `action=""`, fazendo o formulário submeter localmente para o GoPhish.

---

## Conclusão

A campanha foi executada com sucesso, demonstrando o ciclo completo de um ataque de phishing em ambiente controlado. O projeto evidencia compreensão prática do vetor de ataque mais prevalente em ambientes corporativos e das medidas defensivas necessárias para mitigá-lo.

---

## Referências

- [GoPhish Documentation](https://getgophish.com)
- [MailHog](https://github.com/mailhog/MailHog)
- [MITRE ATT&CK T1566.002](https://attack.mitre.org/techniques/T1566/002/)
- [MITRE ATT&CK T1056.003](https://attack.mitre.org/techniques/T1056/003/)
