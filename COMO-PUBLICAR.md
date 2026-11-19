# Como publicar uma nova versão

## 1. O que enviar no chat

- O **APK** (arraste o arquivo para o chat ou informe o caminho completo)
- Nome do app: **Meu Trampo**, **Meu Money** ou **WA Capital TV**
- `versionName` (ex.: `1.1.0`) e `versionCode` (número inteiro, sempre maior que o anterior)
- Notas da versão (o que mudou)
- A atualização é obrigatória? (sim/não)
- Versão mínima suportada (opcional)

## 2. Prompt pronto (copie e cole)

```text
Siga o padrão em PADRAO-DE-ATUALIZACAO.md do repositório LifeCrazy13/central-atualizacoes.

Publique uma nova versão do app "<NOME>".
- APK: <caminho do arquivo ou arquivo anexado>
- versionName: <x.y.z>
- versionCode: <inteiro>
- Notas: <o que mudou>
- Atualização obrigatória: <sim/não>
- Versão mínima suportada: <versionCode ou "manter a atual">

Execute:
1. Validar o package, o versionCode/versionName e a assinatura do APK.
2. Criar a Release com a tag <slug>-v<versionName> e anexar o APK.
3. Calcular o SHA-256 e o tamanho em bytes.
4. Atualizar o version.json da pasta do app conforme o padrão.
5. Commitar e enviar para o GitHub.
6. Testar o link do APK e informar o link da Release e o resultado.
```

Slugs das tags: `meu-trampo`, `meu-money`, `wa-capital-tv`.

## 3. O que acontece depois

1. O `version.json` é atualizado no repositório.
2. Na próxima verificação, o app mostra o aviso de nova versão.
3. O usuário escolhe **Atualizar agora** ou **Depois**.
4. Se a atualização for obrigatória, o app fica bloqueado até atualizar.
5. Quem escolher **Depois** recebe um novo lembrete após 24 horas.

## 4. Regras para não quebrar a atualização

- **Nunca** diminuir ou repetir o `versionCode`.
- **Sempre** assinar o APK com a mesma keystore.
- Nunca enviar keystore, senha ou token para o repositório.
- APK acima de ~50 MB: usar Release (não colocar na pasta).
- Testar o link do APK no navegador antes de divulgar.

## 5. Onde está o padrão completo

Leia [PADRAO-DE-ATUALIZACAO.md](PADRAO-DE-ATUALIZACAO.md) para os campos do JSON, regras de comportamento e segurança.
