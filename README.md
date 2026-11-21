# claude-tmux

O `claude-tmux` abre conversas do [Claude Code](https://docs.claude.com/en/docs/claude-code) dentro do tmux. Assim o Claude continua trabalhando quando você fecha o terminal ou perde a conexão SSH, e você volta à conversa depois, de qualquer pasta, pelo nome que deu a ela.

## Por que usar

O `claude --resume` só encontra uma conversa quando você está na pasta em que ela foi criada. O `claude-tmux` lê o histórico do Claude Code em `~/.claude/projects`, descobre essa pasta, abre o tmux nela e retoma a conversa. A sessão tmux recebe o mesmo nome da conversa.

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

Dê um nome à conversa dentro do Claude Code com `/rename NOME`, ou inicie-o com `claude --name NOME`. Depois:

| Comando | O que faz |
|---|---|
| `claude-tmux iniciar [NOME]` | abre a conversa no tmux e conecta |
| `claude-tmux desconectar [NOME]` | sai do tmux; o Claude continua rodando |
| `claude-tmux conectar [NOME]` | volta à sessão tmux |
| `claude-tmux encerrar [NOME]` | fecha a sessão tmux e o Claude |
| `claude-tmux estado [NOME]` | diz se a sessão existe e se está conectada |
| `claude-tmux lista` | lista as sessões tmux |
| `claude-tmux conversas` | lista as conversas com nome, a pasta e o ID de cada uma |

NOME pode ser o nome da conversa ou o ID dela. Se duas conversas tiverem o mesmo nome, o script usa a mais recente e avisa; para escolher outra, passe o ID.

Sem NOME, `iniciar` usa a pasta atual e roda `claude --continue`, que retoma a conversa mais recente dessa pasta ou abre uma nova. Nesse caso a sessão tmux recebe o nome da pasta.

Dentro do tmux, Ctrl+B seguido de D também desconecta.

### Exemplo

```bash
cd ~/projetos/analise
claude --name analise        # trabalha um pouco e sai
claude-tmux iniciar analise  # de qualquer pasta: retoma no tmux
# Ctrl+B, D para sair; o Claude continua rodando
claude-tmux conectar analise
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
