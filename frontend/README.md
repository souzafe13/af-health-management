<div align="center">

# A&F Health Management · Front-end

**Aplicativo móvel para organizar remédios, consultas, exames e restrições de saúde, com confirmação do que foi feito e avisos no celular.**

![React Native](https://img.shields.io/badge/React_Native-0.88-20232A?logo=react&logoColor=61DAFB)
![Expo](https://img.shields.io/badge/Expo_SDK-58-000020?logo=expo&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-ES2022-F7DF1E?logo=javascript&logoColor=black)
![Plataformas](https://img.shields.io/badge/Plataformas-iOS_%7C_Android-1636A8)
![Status](https://img.shields.io/badge/Status-v1.0_entregue-2E7D32)

</div>

---

## Sumário

- [Sobre](#sobre)
- [Funcionalidades](#funcionalidades)
- [Tecnologias](#tecnologias)
- [Arquitetura](#arquitetura)
- [Como executar](#como-executar)
- [Estrutura de pastas](#estrutura-de-pastas)
- [Integração com a API](#integração-com-a-api)
- [Decisões técnicas](#decisões-técnicas)
- [Acessibilidade](#acessibilidade)
- [Solução de problemas](#solução-de-problemas)
- [Roteiro](#roteiro)
- [Equipe](#equipe)

## Sobre

O **A&F Health Management** ajuda pessoas idosas, cuidadores e familiares a manter sob controle os compromissos de saúde: horários de remédios, consultas, exames e restrições (alergias e proibições médicas). Uma mesma conta pode acompanhar **vários perfis**, um para cada pessoa cuidada.

Este repositório contém o **front-end mobile**. A API (Spring Boot) está em [`backend/`](../backend/health-management), e a visão geral do projeto (objetivos, público-alvo e requisitos) está no [README da raiz](../README.md).

> Projeto Integrador 4: Desenvolvimento de Sistemas para Dispositivos Móveis · Centro Universitário SENAC.

## Funcionalidades

| Área | O que o app faz |
|---|---|
| **Acesso** | Cadastro, login (JWT), escolha de perfil e saída da conta |
| **Hoje** | Linha do tempo com os horários, marcador "Agora", confirmação ("Já tomei" / "Realizado") e desfazer |
| **Remédios** | Cadastro, edição e exclusão, com dose, unidade, tarja, intervalo, início e fim |
| **Consultas e exames** | Cadastro, edição e exclusão, com médico, local e data/hora |
| **Alarme e avisos** | Notificação local que toca e vibra, ou chega em silêncio; "avisar antes" (5, 10, 30 min ou 1 h) |
| **Próximos avisos** | Tela aberta pelo sino, com o que está agendado no celular nas próximas 48 h |
| **Restrições** | Alergias e proibições por perfil, com tipo e gravidade |
| **Conta e perfil** | Edição de perfil (nome e avatar), de conta (nome e e-mail) e troca de senha |

<!--
## Capturas de tela

| Hoje | Remédios | Formulário | Próximos avisos |
|---|---|---|---|
| ![Hoje](docs/home.png) | ![Remédios](docs/medications.png) | ![Formulário](docs/form.png) | ![Avisos](docs/notifications.png) |
-->

## Tecnologias

| Camada | Tecnologia |
|---|---|
| Framework | [React Native](https://reactnative.dev/) 0.88 + [React](https://react.dev/) 19 |
| Plataforma | [Expo](https://expo.dev/) SDK 58 |
| Navegação | [React Navigation](https://reactnavigation.org/) 7 (pilha nativa + abas inferiores) |
| Notificações | `expo-notifications` (agendamento local) |
| Data e hora | `@react-native-community/datetimepicker` |
| Ícones e gráficos | `@expo/vector-icons`, `react-native-svg` |
| Linguagem | JavaScript |

## Arquitetura

O front-end é organizado em camadas com responsabilidades separadas, e a dependência vai sempre **de cima para baixo**:

```
Navegação ──▶ Telas ──▶ Services ──▶ api.js ──▶ Back-end (Spring Boot) ──▶ H2
```

| Camada | Pasta | Responsabilidade |
|---|---|---|
| Navegação | `src/navigation` | Mapa do app: pilha (login, perfil, formulários) e quatro abas |
| Telas e componentes | `src/screens`, `src/components`, `src/styles` | Interface e reação aos toques |
| Services | `src/services` | Operações do domínio (`confirmOccurrence`, `listSchedules`...) |
| Acesso à API | `src/services/api.js` | Único ponto que usa `fetch`: token, falha de rede e tradução de erros |
| Utilitários | `src/utils` | Funções puras (formatação, validação, avatares) |

As telas nunca chamam o servidor diretamente. Todo pedido passa pelo `request` do `api.js`.

## Como executar

### Pré-requisitos

- [Node.js](https://nodejs.org/) (versão LTS) e npm
- App **Expo Go** no celular, compatível com o SDK 58
- Back-end em execução ([instruções](../backend/health-management/README.md))
- Celular e computador na **mesma rede Wi-Fi**

### Passo a passo

```bash
# 1. Instalar as dependências
cd frontend
npm install
```

```js
// 2. Em src/services/api.js, apontar para o IP do computador
export const BASE_URL = 'http://SEU-IP:8080';
```

```bash
# 3. Iniciar e ler o QR code com o Expo Go
npm start
```

> O celular não enxerga o `localhost` do computador, por isso o `BASE_URL` usa o IP da rede local. **Não faça commit do seu IP.**

### Scripts

| Comando | Ação |
|---|---|
| `npm start` | Inicia o servidor do Expo (QR code) |
| `npm run android` | Inicia e abre no Android |
| `npm run ios` | Inicia e abre no iOS |
| `npm run web` | Inicia a versão web |

## Estrutura de pastas

```
frontend/
├── index.js                 ponto de entrada (registra o App)
├── App.js                   componente raiz
├── app.json                 configuração do Expo
├── package.json             dependências e scripts
└── src/
    ├── navigation/          AppNavigator
    ├── screens/             uma tela por arquivo (LoginScreen, HomeScreen...)
    ├── components/          Header, DateTimeField, AlarmFields, PasswordRules, Logo
    ├── services/            api, auth, profile, schedule, occurrence,
    │                        restriction, notification, errorMessages
    ├── styles/              theme.js (paleta de cores)
    └── utils/               format, validation, avatars, restrictions
```

**Convenção de nomes:** componentes e telas em `PascalCase` (`HomeScreen.js`); serviços e utilitários em `camelCase` (`scheduleService.js`); constantes em `UPPER_SNAKE_CASE`.

## Integração com a API

O token JWT é devolvido no cabeçalho `Authorization` do login, guardado em memória e enviado como `Bearer` em todas as chamadas. As respostas seguem `{ "data": ..., "message": ... }` em caso de sucesso e `{ "message": ... }` em caso de erro.

| Recurso | Endpoints usados |
|---|---|
| Usuário | `POST /user/signup`, `POST /user/login`, `PUT /user`, `PUT /user/password` |
| Perfis | `GET /profile`, `POST /profile`, `PUT /profile/{id}` |
| Agendamentos | `GET /schedule`, `POST /schedule`, `PUT /schedule`, `DELETE /schedule/{id}` |
| Ocorrências | `GET /occurrence` |
| Confirmações | `POST /confirmation`, `DELETE /confirmation/{id}` |
| Restrições | `GET /restriction`, `POST /restriction`, `PUT /restriction/{id}` |

As mensagens de erro do back-end (em inglês) são traduzidas para português em `errorMessages.js`.

## Decisões técnicas

- **Horários em UTC.** O back-end trabalha em UTC: o app envia com `toISOString()` e exibe no fuso do aparelho. O relógio do celular deve estar em horário automático (o back-end recusa diferenças acima de 30 minutos).
- **Notificações locais.** O app calcula os horários das próximas 48 h e agenda os avisos no próprio celular, sem servidor de push. A sincronização apaga e recria os avisos, evitando avisos antigos após edições.
- **Android.** As notificações foram validadas no iPhone pelo Expo Go. O suporte completo no Android exige uma *development build*.
- **Regra das 11 horas.** O formulário de remédio não aceita início mais de 11 horas no passado (validação apenas no front). Na edição, só é aplicada quando o horário de início muda.
- **Versionamento de agendamentos.** O back-end preserva as confirmações ao editar (id de grupo `root_id`). O front não precisa tratar isso: sempre recebe o agendamento ativo.
- **Exclusão lógica.** Excluir marca o item como inativo; o histórico de confirmações é preservado.

## Acessibilidade

- Textos grandes e contraste alto (azul `#1636A8` sobre branco: 9,9:1).
- Texto laranja usa `#B85600` (4,8:1); o laranja da marca fica restrito a elementos gráficos.
- Status sempre com **ícone, texto e cor**, nunca só cor.
- Áreas de toque de 44 px ou mais e `hitSlop` em ícones pequenos.
- Mensagens de erro claras, em português, dizendo o que fazer.

## Solução de problemas

| Sintoma | Causa provável e solução |
|---|---|
| "Não foi possível conectar ao servidor." | Back-end desligado, `BASE_URL` com IP errado ou celular em outra rede. Confira o IP atual do computador. |
| "O horário do celular está diferente do horário do servidor." | Ative o horário automático do aparelho. |
| "Erro inesperado" em todas as telas após atualizar o back-end | Banco H2 antigo incompatível com a coluna nova. Renomeie o arquivo `.mv.db` em `backend/health-management/data`, reinicie o back-end e cadastre-se de novo. |
| Avisos não aparecem | Permita notificações para o Expo Go nas configurações do celular. No Android, há limitações no Expo Go. |
| Erro ao abrir no Expo Go | Verifique se a versão do Expo Go é compatível com o SDK 58 e rode `npm install` novamente. |

## Roteiro

- [ ] "Lembrar de mim" com armazenamento seguro do token
- [ ] Tratamento de token expirado (retorno ao login)
- [ ] "Esqueci minha senha"
- [ ] Intervalos além de horas no formulário
- [ ] Foto do remédio
- [ ] Repetir o alarme até a confirmação
- [ ] Rótulos de acessibilidade (`accessibilityLabel`) nos botões só com ícone
- [ ] `BASE_URL` em arquivo de configuração fora do Git
- [ ] Testes automatizados e testes em Android
- [ ] Build nativa para alarme completo no Android

## Equipe

| Integrante | Responsabilidade | GitHub |
|---|---|---|
| Fernanda Souza | Front-end | [@souzafe13](https://github.com/souzafe13) |
| André Rodrigues | Back-end | [@oandredev](https://github.com/oandredev) |
| André Coutinho | Integrante da equipe | [@AndreCoutinhom](https://github.com/AndreCoutinhom) |

**Orientador:** [Prof. João Felipe Fernandes Coghi](https://github.com/joaocoghi)
