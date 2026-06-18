# Hércules - Gerador de site com tabela

Este projeto gera automaticamente `index.html` e faz commit/push no Git.
Uma tarefa agendada do Windows invoca o script periodicamente e também no
arranque do computador.

## Configuração automática da agenda

Um helper (`setup_task.ps1`) garante que a tarefa agendada exista e possua os
triggers corretos (início do sistema + repetição horária a cada xx:01).

Execute uma vez (como administrador, se necessário):

```powershell
powershell -ExecutionPolicy Bypass -File .\setup_task.ps1
```

ou simplesmente

```bat
RUN.bat setup
```

Após a primeira execução a tarefa fica registrada como **HerculesAutoGitPush**
(rodando sob `SYSTEM` em nível elevado). O próprio `auto_git_push.ps1` tenta
verificar a presença da tarefa sempre que é executado manualmente; se não
conseguir (por exemplo porque você não tem privilégios para consultar tarefas
`SYSTEM`) ele gravará uma mensagem no log e, se possível, chamará o helper
`setup_task.ps1` para recriá‑la.

> ⚠️ A criação/recuperação da tarefa ainda exige privilégios elevados; execute
> `RUN.bat setup` ou `setup_task.ps1` uma vez como administrador para garantir
> que tudo possa ser configurado. Após isso a verificação automática não bloqueia
> o funcionamento normal.
>
> **Nota:** qualquer alteração no helper `setup_task.ps1` (como o ajuste de
> minuto de disparo) só surtirá efeito depois de rodar novamente o script de
> configuração (`RUN.bat setup`); execuções agendadas não recriam a tarefa.

## Uso

* `RUN.bat` — chama o PowerShell que executa `gerar.py` e faz o commit/push.
* o Agendador faz o resto; não há necessidade de rodar o `batch` manualmente.

A tarefa horária é configurada para disparar **aos :01 de cada hora** (por
exemplo 10:01, 11:01, etc.). Isso evita uma execução imediata no minuto em que
`setup_task.ps1` for chamado e elimina a dupla ocorrência registrada em horas
fechadas.

Os logs são escritos em `auto_git_push.log` para facilitar a inspeção.
