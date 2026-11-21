# claude-tmux

O `claude-tmux` abre conversas do [Claude Code](https://docs.claude.com/en/docs/claude-code) dentro do tmux. Assim o Claude continua trabalhando quando você fecha o terminal ou perde a conexão SSH, e você volta à conversa depois, de qualquer pasta, pelo nome que deu a ela.

## Por que usar

O `claude-tmux` parte de uma regra simples: cada pasta é um projeto em que você trabalha com o Claude. Você chama o projeto ou a conversa pelo nome, de qualquer lugar, e o script abre o tmux na pasta certa.

Isso é útil porque o `claude --resume` só encontra uma conversa quando você está na pasta em que ela foi criada. O `claude-tmux` lê o histórico do Claude Code em `~/.claude/projects`, descobre essa pasta, abre o tmux nela e retoma a conversa.

## Requisitos

- bash
- tmux
- python3 (só a biblioteca padrão)
- Claude Code (`claude`)

## Instalação

```bash
curl -fsSL https://raw.githubusercontent.com/lucianovk/claude-tmux/main/claude-tmux -o ~/.local/bin/claude-tmux
chmod +x ~/.local/bin/claude-tmux
```

Qualquer pasta que esteja no `PATH` serve no lugar de `~/.local/bin`.

## Uso

Você pode chamar um projeto pelo nome da pasta, ou dar um nome a uma conversa dentro do Claude Code com `/rename NOME` (ou `claude --name NOME`). Depois:

| Comando | O que faz |
|---|---|
| `claude-tmux iniciar [NOME]` | abre a conversa no tmux e conecta |
| `claude-tmux desconectar [NOME]` | sai do tmux; o Claude continua rodando |
| `claude-tmux conectar [NOME]` | volta à sessão tmux |
| `claude-tmux encerrar [NOME]` | fecha a sessão tmux e o Claude |
| `claude-tmux estado [NOME]` | diz se a sessão existe e se está conectada |
| `claude-tmux lista` | lista as sessões tmux |
| `claude-tmux conversas` | lista as conversas com nome, a pasta e o ID de cada uma |

NOME pode ser, nesta ordem de preferência:

| NOME | O que `iniciar` faz |
|---|---|
| nome de uma conversa (`/rename` ou `--name`) | retoma essa conversa |
| ID de uma conversa | retoma essa conversa |
| nome de uma pasta onde você já usou o Claude | retoma a conversa mais recente dessa pasta |
| caminho de uma pasta (com `/`) | o mesmo; serve também para um projeto sem conversas |

A sessão tmux recebe o NOME, ou o nome da pasta quando NOME é um caminho. Se houver nomes repetidos, o script usa o mais recente e avisa; para escolher outra conversa, passe o ID.

Sem NOME, `iniciar` usa a pasta atual e roda `claude --continue`, que retoma a conversa mais recente dessa pasta ou abre uma nova. Nesse caso a sessão tmux recebe o nome da pasta.

Dentro do tmux, Ctrl+B seguido de D também desconecta.

### Exemplo

```bash
claude-tmux iniciar ~/projetos/analise   # projeto novo: abre o Claude no tmux
# Ctrl+B, D para sair; o Claude continua rodando
claude-tmux conectar analise             # volta à sessão
claude-tmux encerrar analise             # fecha tudo
claude-tmux iniciar analise              # outro dia, de qualquer pasta: retoma a última conversa
```

## Cuidados

- Feche a conversa antes de retomá-la no tmux. Se a mesma conversa ficar aberta em dois processos, os dois gravam no mesmo histórico.
- O script depende do formato interno do histórico do Claude Code (arquivos `.jsonl` em `~/.claude/projects`), que pode mudar em versões futuras.

## Variáveis

| Variável | Efeito |
|---|---|
| `CLAUDE_TMUX_DIR` | força a pasta de trabalho do `iniciar` |
| `CLAUDE_CONFIG_DIR` | pasta de configuração do Claude Code (padrão: `~/.claude`) |

## Licença

MIT. Este é um projeto pessoal, sem relação com a Anthropic.
