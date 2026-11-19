# Padrão de atualização dos aplicativos

Versão 1.0 — vale para **todos** os apps deste repositório (Meu Trampo, Meu Money, WA Capital TV).

## 1. Estrutura

```
central-atualizacoes/
├── Meu Trampo/
│   └── version.json          <- o app consulta este arquivo
├── Meu Money/
│   └── version.json
├── WA Capital TV/
│   └── version.json
└── Releases do GitHub        <- onde ficam os APKs
```

O APK **não** precisa ficar dentro da pasta. O lugar recomendado é uma **Release** do GitHub (aceita arquivos grandes e mantém histórico). A pasta guarda apenas o `version.json`.

## 2. Arquivo `version.json`

O aplicativo baixa este arquivo por HTTPS e compara com a versão instalada.

| Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `app` | texto | sim | Nome exibido do aplicativo |
| `package` | texto | sim | `applicationId` do Android. Se não bater com o app, o JSON deve ser ignorado |
| `versionCode` | inteiro | sim | Código da versão. **Sempre maior** que o anterior |
| `versionName` | texto | sim | Versão visível (ex.: `1.1.0`) |
| `minSupportedVersionCode` | inteiro | sim | Versões instaladas abaixo disso são **obrigadas** a atualizar |
| `mandatory` | booleano | sim | `true` trava o app até atualizar |
| `apkUrl` | texto | sim | Link HTTPS direto do APK (Release) |
| `sha256` | texto | sim | Hash do APK para conferir a integridade |
| `sizeBytes` | inteiro | sim | Tamanho do APK em bytes |
| `publishedAt` | texto | sim | Data em ISO 8601 UTC |
| `notes` | lista | sim | Novidades da versão (uma frase por item) |

Exemplo:

```json
{
  "app": "Meu Trampo",
  "package": "com.meutrampo.app",
  "versionCode": 2,
  "versionName": "1.1.0",
  "minSupportedVersionCode": 1,
  "mandatory": false,
  "apkUrl": "https://github.com/LifeCrazy13/central-atualizacoes/releases/download/meu-trampo-v1.1.0/meu-trampo.apk",
  "sha256": "0000000000000000000000000000000000000000000000000000000000000000",
  "sizeBytes": 0,
  "publishedAt": "2026-11-19T12:00:00Z",
  "notes": ["Correção de bugs", "Melhoria de desempenho"]
}
```

## 3. Comportamento dentro do aplicativo

### 3.1 Quando verificar

- Ao abrir o aplicativo.
- Ao voltar para o primeiro plano, se passaram mais de 6 horas desde a última verificação.
- Falha de rede **não** pode impedir o uso do app; tentar de novo na próxima abertura.

### 3.2 Atualização opcional (padrão)

Quando `versionCode` do JSON for maior que o instalado e a atualização **não** for obrigatória:

1. Mostrar um diálogo com: versão nova, tamanho do download e novidades.
2. Botões: **Atualizar agora** e **Depois**.
3. Nada é baixado sem o usuário tocar em **Atualizar agora**.

### 3.3 Atualização obrigatória (travar)

O app deve bloquear o uso quando:

- `mandatory` for `true`; **ou**
- a versão instalada for menor que `minSupportedVersionCode`.

Nesse caso:

- Mostrar diálogo sem opção de continuar usando o app.
- Botão **Atualizar agora** (e **Tentar novamente** se o download falhar).
- O app só libera depois de instalar a versão nova.

### 3.4 Se o usuário escolher "Depois"

- Fechar o diálogo e continuar usando a versão atual.
- Lembrar novamente na próxima abertura **após 24 horas**.
- No máximo **um aviso por dia** para não incomodar.
- Se depois a versão passar a ser obrigatória, o aviso volta imediatamente e trava.

### 3.5 Download e instalação

1. Baixar o APK por HTTPS com barra de progresso e opção de cancelar.
2. Conferir o `sha256`; se não bater, apagar o arquivo e avisar erro.
3. Instalar usando o instalador do Android. No Android 8+ é necessário permitir "instalar apps desconhecidos" para o aplicativo.
4. Se a instalação automática falhar, oferecer abrir o link do APK no navegador.
5. Depois de instalar, o app reinicia e a verificação passa.

### 3.6 Segurança

- Só aceitar `apkUrl` em **HTTPS**.
- Ignorar o JSON se o campo `package` não for o do aplicativo.
- Comparar sempre por `versionCode` (número inteiro), nunca por texto.
- O APK precisa ser assinado com **a mesma keystore** de sempre; caso contrário o Android recusa a atualização.

## 4. Publicação de uma versão

1. Gerar o APK assinado com a keystore oficial.
2. Criar uma Release no GitHub com a tag `<slug>-v<versão>` (ex.: `meu-trampo-v1.1.0`) e anexar o APK.
3. Calcular `sha256` e `sizeBytes` do APK.
4. Atualizar o `version.json` da pasta do app.
5. Commit e push.
6. Testar em um aparelho com a versão antiga instalada.

## 5. Requisito importante: repositório público

Para o aplicativo baixar o `version.json` e o APK **sem login**, o repositório precisa ser **público**. Tokens do GitHub nunca devem ser colocados dentro do APK (qualquer pessoa consegue extrair). Se o repositório continuar privado, a atualização automática não funciona nos aparelhos dos usuários.

## 6. Checklist rápido

- [ ] `versionCode` maior que o da versão anterior
- [ ] APK assinado com a mesma keystore
- [ ] Release criada com o APK anexado
- [ ] `sha256` e `sizeBytes` corretos
- [ ] `package` igual ao do aplicativo
- [ ] `apkUrl` abrindo no navegador
- [ ] Teste de atualização opcional e obrigatória
