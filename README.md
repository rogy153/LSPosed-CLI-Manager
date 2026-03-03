# LSPosed Manager CLI

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Android](https://img.shields.io/badge/Android-5.0%2B-green.svg)](https://android.com)
[![LSPosed](https://img.shields.io/badge/LSPosed-Compatible-blue.svg)](https://github.com/LSPosed/LSPosed)
[![Shell](https://img.shields.io/badge/Shell-POSIX-orange.svg)](https://en.wikipedia.org/wiki/POSIX)
[![Pixel 9](https://img.shields.io/badge/Tested%20on-Pixel%209%20Pro%20XL-4285F4.svg)](https://store.google.com)

**Gerenciamento completo de módulos LSPosed via linha de comando — sem depender da GUI que crasha.**

---

## 💥 Por que isso existe?

Se você chegou aqui, provavelmente passou pelo mesmo inferno que eu.

No **Pixel 9 Pro XL** (e possivelmente outros dispositivos com Android 14/15), clicar na notificação do LSPosed para abrir o manager resulta em **crash imediato** — sem mensagem de erro, sem fallback, sem nada. O app simplesmente morre.

Testei todas as versões disponíveis:
- ✗ LSPosed oficial (arquivado)
- ✗ Forks da comunidade
- ✗ Versões customizadas
- ✗ Diferentes versões do Magisk
- ✗ Múltiplas combinações de Zygisk

**Nada funcionou.**

A solução? Parar de depender da GUI e fazer tudo que o LSPosed Manager faz — diretamente no SQLite, via shell, com root. É exatamente isso que esse projeto faz.

> **LSPosed Manager CLI** é um substituto funcional completo para o app LSPosed Manager, operando inteiramente pela linha de comando através de escrita direta no banco de dados SQLite do framework.

---

## 🚀 Funcionalidades

- 🔧 **Gerenciamento Completo**: Habilitar, desabilitar e configurar módulos via CLI
- 🎯 **Descoberta Inteligente**: Sistema automático de descoberta e sugestão de escopos
- 📱 **Exploração de Apps**: Lista e categoriza todos os packages disponíveis no sistema
- 💾 **Backup Robusto**: Sistema de backup/restore com versionamento e verificação de integridade
- ⚡ **Operações em Lote**: Processar múltiplos módulos simultaneamente
- 👥 **Multi-usuário**: Suporte completo a Work Profiles e usuários secundários
- 🛡️ **Validação Rigorosa**: Verificações de integridade e health checks automáticos
- 📊 **Interface Rica**: Tabelas formatadas, cores e informações detalhadas
- 🔍 **Modo Preview**: Dry-run para visualizar mudanças antes de aplicar

---

## 📋 Requisitos

### Sistema
- ✅ Android 5.0+ (API 21+) — testado extensivamente no Android 14/15
- ✅ Root access (Magisk recomendado)
- ✅ LSPosed Framework instalado (mesmo que o manager crasha, o framework em si funciona)
- ✅ BusyBox ou ROM com binários completos (`sqlite3`, `awk`, `sed`)

### Conectividade
- ✅ ADB habilitado para instalação remota
- ✅ Acesso via shell (su) para execução local

### Compatibilidade
- ✅ Magisk 20.0+
- ✅ LSPosed v1.8.0+
- ✅ POSIX shell compatible

---

## 🛠️ Instalação

### Método 1: Instalação Automática (Recomendado)

```bash
# Via curl
curl -fsSL https://raw.githubusercontent.com/rogy153/LSPosed-Manager-CLI/main/install.sh | adb shell su -c 'sh'

# Via wget
wget -qO- https://raw.githubusercontent.com/rogy153/LSPosed-Manager-CLI/main/install.sh | adb shell su -c 'sh'
```

### Método 2: Instalação Manual

```bash
# 1. Clone o repositório
git clone https://github.com/rogy153/LSPosed-Manager-CLI.git
cd LSPosed-Manager-CLI

# 2. Enviar scripts para o dispositivo
adb push scripts/ /data/local/tmp/lsposed-cli/

# 3. Instalar no dispositivo
adb shell su -c '/data/local/tmp/lsposed-cli/install.sh'

# 4. Carregar aliases (opcional, mas recomendado)
adb shell su -c 'source /data/local/tmp/lsposed-cli/aliases.sh'
```

### Verificação da Instalação

```bash
adb shell su -c 'lsp-health'

# Saída esperada:
✅ Root: OK
✅ LSPosed: Banco encontrado
✅ Banco LSPosed: Integridade OK
✅ Tabelas LSPosed: OK
✅ SQLite3: 3.32.2
✅ Espaço livre: 2048MB
📊 Módulos ativos: 3
📊 Escopos configurados: 12
🎉 Validação concluída: Sistema OK
```

---

## 📚 Guia de Uso

### Comandos Essenciais

| Comando | Descrição | Exemplo |
|---------|-----------|---------|
| `lsp-health` | Diagnóstico completo do sistema | `lsp-health` |
| `lsp-list` | Listar módulos instalados | `lsp-list --enabled` |
| `lsp-available` | Explorar packages do sistema | `lsp-available --search nfc` |
| `lsp-discover` | Descobrir escopos para módulo | `lsp-discover com.module.example` |
| `lsp-enable` | Habilitar módulo com escopos | `lsp-enable --auto com.module.example` |
| `lsp-disable` | Desabilitar módulo | `lsp-disable com.module.example` |
| `lsp-scopes` | Listar escopos aplicados | `lsp-scopes --module com.module.example` |
| `lsp-backup` | Gerenciar backups | `lsp-backup --create "snapshot"` |
| `lsp-bulk` | Operações em lote | `lsp-bulk --enable module1 module2` |

### Fluxo de Trabalho Típico

```bash
# 1. Verificar sistema
lsp-health

# 2. Listar módulos disponíveis
lsp-list

# 3. Explorar apps para escopos
lsp-available --category system

# 4. Descobrir escopos recomendados
lsp-discover de.tu_darmstadt.seemoo.nfcgate

# 5. Preview da configuração
lsp-enable --dry-run --auto de.tu_darmstadt.seemoo.nfcgate

# 6. Aplicar configuração
lsp-enable --auto de.tu_darmstadt.seemoo.nfcgate

# 7. Verificar resultado
lsp-scopes --module de.tu_darmstadt.seemoo.nfcgate

# 8. Reiniciar para aplicar
adb shell su -c 'svc power reboot'
```

---

## 🎯 Exemplos Práticos

### Configuração Básica de Módulo

```bash
# Habilitar NFCGate com descoberta automática
lsp-enable --auto de.tu_darmstadt.seemoo.nfcgate

# Resultado:
📦 Módulo: de.tu_darmstadt.seemoo.nfcgate (mid=2)
🎯 Escopos aplicados:
    - com.android.nfc
    - com.samsung.android.nfc
    - com.android.systemui
💾 Backup criado: modules_config_20250905_143022.db
✅ Configuração concluída!
```

### Configuração Interativa

```bash
# Modo de seleção visual
lsp-enable --choose de.tu_darmstadt.seemoo.nfcgate

# Interface:
┌─────┬────────────────────────────┬──────────────┬────────────┐
│ ID  │ Package                    │ Tipo         │ Status     │
├─────┼────────────────────────────┼──────────────┼────────────┤
│ [1] │ com.android.nfc           │ Sistema      │ Instalado  │
│ [2] │ com.samsung.android.nfc   │ Vendor       │ Instalado  │
│ [3] │ com.android.systemui      │ Sistema      │ Instalado  │
│ [4] │ com.android.settings      │ Sistema      │ Instalado  │
└─────┴────────────────────────────┴──────────────┴────────────┘

Seleção ([a]ll, [n]one, números): 1,2,3
Multi-usuário? [y/N]: y
User IDs (0,10): 0,10
```

### Exploração de Packages

```bash
# Encontrar todos os apps relacionados a NFC
lsp-available --search nfc --with-sizes

# Detalhes de um package específico
lsp-available --details com.android.systemui

# Apps por categoria
lsp-available --category google --format json
```

### Operações em Lote

```bash
# Criar lista de módulos
cat > /sdcard/modules.txt <<EOF
de.tu_darmstadt.seemoo.nfcgate
com.ceco.pie.gravitybox
tk.wasdennnoch.androidn_ify
EOF

# Habilitar todos automaticamente
lsp-bulk --enable --scope-mode auto --file /sdcard/modules.txt

# Preview de operação em lote
lsp-bulk --enable --dry-run --file /sdcard/modules.txt
```

### Gerenciamento de Backups

```bash
# Criar backup com tag
lsp-backup --create "antes_configuracao_nfc"

# Listar backups
lsp-backup --list

# Restaurar backup específico
lsp-backup --restore 3

# Limpeza automática
lsp-backup --cleanup --keep 10
```

---

## 📖 Documentação dos Scripts

### Scripts Principais

| Script | Função | Documentação |
|--------|--------|--------------|
| [`enable_module.sh`](docs/enable_module.md) | Habilitar módulos com escopos | [📖 Docs](docs/enable_module.md) |
| [`disable_module.sh`](docs/disable_module.md) | Desabilitar módulos | [📖 Docs](docs/disable_module.md) |
| [`list_modules.sh`](docs/list_modules.md) | Listar módulos instalados | [📖 Docs](docs/list_modules.md) |
| [`list_scopes.sh`](docs/list_scopes.md) | Listar escopos aplicados | [📖 Docs](docs/list_scopes.md) |
| [`list_scopes_available.sh`](docs/list_scopes_available.md) | Explorar packages disponíveis | [📖 Docs](docs/list_scopes_available.md) |
| [`discover_scopes.sh`](docs/discover_scopes.md) | Descobrir escopos inteligentemente | [📖 Docs](docs/discover_scopes.md) |
| [`health_check.sh`](docs/health_check.md) | Diagnóstico do sistema | [📖 Docs](docs/health_check.md) |
| [`manage_backups.sh`](docs/manage_backups.md) | Gerenciar backups | [📖 Docs](docs/manage_backups.md) |
| [`bulk_operations.sh`](docs/bulk_operations.md) | Operações em lote | [📖 Docs](docs/bulk_operations.md) |

### Arquivos de Configuração

| Arquivo | Propósito | Localização |
|---------|-----------|-------------|
| `scopes_manifest.yml` | Definições de escopos por módulo | `scripts/data/` |
| `popular_modules.yml` | Banco de módulos conhecidos | `scripts/data/` |
| `aliases.sh` | Aliases para facilitar uso | `scripts/` |
| `config.sh` | Configurações do sistema | `scripts/` |

---

## 🔧 Configuração Avançada

### Personalizando o Manifesto de Escopos

Edite `scripts/data/scopes_manifest.yml` para adicionar seus próprios módulos:

```yaml
modules:
  seu.modulo.personalizado:
    description: "Descrição do seu módulo"
    defaults:
      - com.android.systemui
      - android
    patterns:
      - '^com\.android\.settings$'
      - 'launcher'
```

### Configurações Globais

Edite `scripts/config.sh`:

```bash
export AUTO_BACKUP=true
export CACHE_DURATION=3600
export LOG_LEVEL=INFO
export DEFAULT_USER_ID=0
```

### Aliases Personalizados

Adicione seus próprios aliases em `scripts/aliases.sh`:

```bash
alias lsp-status='lsp-list && echo && lsp-scopes'
alias lsp-find='lsp-available --search'
alias lsp-safe-enable='lsp-backup --create "auto" && lsp-enable'
```

---

## 🐛 Troubleshooting

### Problemas Comuns

#### ❌ "Tabelas LSPosed: Não encontradas"
```bash
adb shell su -c 'pgrep lspd'
adb shell su -c 'sqlite3 /data/adb/lspd/config/modules_config.db ".tables"'

# Se vazio, reiniciar dispositivo
adb shell su -c 'reboot'
```

#### ❌ "sqlite3: command not found"
```bash
# Instalar BusyBox via Magisk Manager
# Ou usar ROM com binários completos
```

#### ❌ "Permission denied"
```bash
adb shell su -c 'whoami'  # deve retornar 'root'
adb shell su -c 'ls -la /data/adb/lspd/'
```

#### ❌ Scripts não executam
```bash
adb shell su -c 'chmod +x /data/local/tmp/lsposed-cli/*.sh'
adb shell su -c 'chmod +x /data/local/tmp/lsposed-cli/core/*.sh'
```

### Logs e Debug

```bash
# Ver logs do sistema
lsp-health

# Logs detalhados
adb shell su -c 'cat /data/local/tmp/lsposed-cli/logs/$(date +%Y%m%d).log'

# Debug de script específico
adb shell su -c 'sh -x /data/local/tmp/lsposed-cli/enable_module.sh --help'
```

### Reset Completo

```bash
lsp-backup --create "before_reset"
lsp-bulk --disable --force $(lsp-list --enabled --format minimal)
lsp-bulk --cleanup --force
lsp-health
```

---

## 🤝 Contribuição

Contribuições são bem-vindas! Se você também sofreu com o crash do LSPosed Manager, ou encontrou outro dispositivo/versão afetada, abra uma issue ou PR.

1. **Fork** o repositório
2. Crie uma **branch** para sua feature (`git checkout -b feature/nova-funcionalidade`)
3. **Commit** suas mudanças (`git commit -am 'Adiciona nova funcionalidade'`)
4. **Push** para a branch (`git push origin feature/nova-funcionalidade`)
5. Abra um **Pull Request**

### Diretrizes

- ✅ Mantenha compatibilidade POSIX shell
- ✅ Adicione testes para novas funcionalidades
- ✅ Documente mudanças no README
- ✅ Siga o padrão de logging existente
- ✅ Teste em múltiplas versões do Android

### Reportando Bugs

Use o [issue tracker](https://github.com/rogy153/LSPosed-Manager-CLI/issues) e inclua:

- 📱 Dispositivo e versão do Android
- 🔧 Versão do LSPosed / Magisk
- 📋 Logs de erro completos (`lsp-health`)
- 🔄 Passos para reproduzir

---

## 📜 Licença

MIT License — veja [LICENSE](LICENSE) para detalhes.

---

## 🙏 Agradecimentos

- **[LSPosed Team](https://github.com/LSPosed/LSPosed)** — Framework principal
- **[Magisk](https://github.com/topjohnwu/Magisk)** — Sistema de módulos root
- **[BusyBox](https://busybox.net/)** — Utilitários Unix essenciais
- **Comunidade Xposed** — Inspiração e suporte
- **Issue [#498 — crash on click notification](https://github.com/LSPosed/LSPosed/issues/498)** — A thread que confirmou que não era só comigo

---

## 📈 Status do Projeto

- ✅ **Estável**: Core funcional testado e funcionando
- 🔄 **Ativo**: Desenvolvimento contínuo
- 📊 **Cobertura**: 90%+ dos casos de uso cobertos
- 🌍 **Compatibilidade**: Android 5.0+ e LSPosed 1.8.0+
- 📱 **Testado em**: Pixel 9 Pro XL (Android 15)

---

## 🔗 Links Úteis

- 📖 [Documentação Completa](docs/)
- 🎯 [Guia de Exemplos](EXAMPLES_GUIDE.md)
- 🐛 [Issues](https://github.com/rogy153/LSPosed-Manager-CLI/issues)
- 💬 [Discussions](https://github.com/rogy153/LSPosed-Manager-CLI/discussions)
- 📋 [Changelog](CHANGELOG.md)
- 🚀 [Releases](https://github.com/rogy153/LSPosed-Manager-CLI/releases)

### Links de Emergência 🚨
*Para quando a GUI trava e você precisa de ajuda rápida:*
- 🆘 [Guia de Recuperação Rápida](docs/emergency-recovery.md)
- 🔧 [Problemas Comuns com a GUI](docs/gui-troubleshooting.md)
- ⚡ [Comandos Rápidos One-Liner](docs/quick-commands.md)

---

<div align="center">

**⭐ Se isso te salvou de reinstalar o sistema, considera dar uma estrela!**

</div>

---

<div align="center">
<sub>Desenvolvido com raiva produtiva depois de 3 horas tentando fazer o LSPosed Manager abrir no Pixel 9 Pro XL ❤️</sub>
</div>
