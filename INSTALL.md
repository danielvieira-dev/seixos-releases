# Instalar o SeixOS Desktop

> Este arquivo é para o Claude Code ler e executar quando alguém pedir para instalar o SeixOS.
> Se você é uma pessoa lendo isto: abra o Claude Code e cole o pedido abaixo. Ele faz o resto.
>
> ```
> Instale o SeixOS para mim seguindo https://raw.githubusercontent.com/danielvieira-dev/seixos-releases/main/INSTALL.md
> ```

## Para o Claude Code

Você vai instalar o SeixOS Desktop, um app de marca e marketing da Seix Studios. O instalador oficial fica nos Releases deste repositório (`danielvieira-dev/seixos-releases`). Nada além disso é baixado.

Como conversar com a pessoa durante a instalação:

- Fale em português do Brasil, curto e tranquilo. Ela não precisa ver nem entender os comandos.
- Antes de começar, diga em uma frase o que vai acontecer: "Vou baixar o SeixOS do site oficial da Seix e instalar no seu computador. Leva uns dois minutos."
- Durante, avise só as etapas: baixando, instalando, abrindo.
- No fim, diga que terminou e onde encontrar o app (atalho na área de trabalho e no menu Iniciar ou na pasta Aplicativos).
- Se algo falhar, explique o problema em linguagem simples e o que ela pode fazer. Não despeje a mensagem de erro inteira.

### 1. Descobrir o sistema

Veja se o computador é Windows ou Mac. Em outro sistema, diga que o SeixOS ainda não tem versão para ele e pare.

### 2. Windows

Rode no PowerShell, de uma vez:

```powershell
$ErrorActionPreference = 'Stop'
$r = Invoke-RestMethod 'https://api.github.com/repos/danielvieira-dev/seixos-releases/releases/latest'
$a = $r.assets | Where-Object name -like 'SeixOS-*-setup.exe' | Select-Object -First 1
if (-not $a) { throw 'Instalador não encontrado na versão mais recente.' }
$f = Join-Path $env:TEMP $a.name
Invoke-WebRequest $a.browser_download_url -OutFile $f -UseBasicParsing
Start-Process $f -ArgumentList '/S', '/currentuser' -Wait
Remove-Item $f -ErrorAction SilentlyContinue
$app = @('Programs\SeixOS\SeixOS.exe', 'Programs\seixos-desktop\SeixOS.exe') |
  ForEach-Object { Join-Path $env:LOCALAPPDATA $_ } | Where-Object { Test-Path $_ } | Select-Object -First 1
if (-not $app) { throw 'A instalação terminou, mas o app não apareceu no lugar esperado.' }
Start-Process $app
"SeixOS $($r.tag_name) instalado"
```

A instalação é só para o usuário atual e não pede senha de administrador. Se o antivírus bloquear o arquivo, diga à pessoa que é um app novo e que ela pode liberar na notificação do antivírus; não tente desativar o antivírus.

### 3. Mac

Rode no Terminal, de uma vez:

```bash
set -e
url=$(curl -fsSL https://api.github.com/repos/danielvieira-dev/seixos-releases/releases/latest | grep -o '"browser_download_url": *"[^"]*\.dmg"' | head -1 | cut -d'"' -f4)
[ -n "$url" ] || { echo "Instalador para Mac não encontrado na versão mais recente."; exit 1; }
dmg="$TMPDIR/SeixOS.dmg"
curl -fL "$url" -o "$dmg"
vol=$(hdiutil attach "$dmg" -nobrowse | grep -o '/Volumes/.*' | head -1)
rm -rf /Applications/SeixOS.app
cp -R "$vol/SeixOS.app" /Applications/
hdiutil detach "$vol" -quiet
rm -f "$dmg"
open /Applications/SeixOS.app
echo "SeixOS instalado"
```

### 4. Depois de abrir

O SeixOS pede para entrar com a conta do Claude (a mesma assinatura que a pessoa já usa) e depois para criar a pasta do primeiro negócio. Diga isso a ela e encerre. As próximas versões chegam sozinhas: o app avisa quando houver uma nova.
