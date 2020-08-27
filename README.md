# Funcionalidades do Git

# Git Rebase

Descrição
Se <branch> for especificado, o git rebase executará um automático antes de fazer qualquer outra coisa. Caso contrário, permanece no ramo atual.git switch <branch>

Se <upstream> não for especificado, o upstream configurado em branch.<name>.remote e branch.<name>.merge options será usado (ver git-config[1] para detalhes) e a opção será assumida. Se você não estiver atualmente em nenhuma filial ou se a filial atual não tiver uma corrente de ar acima configurada, a rebase abortará.--fork-point

Todas as alterações feitas por compromissos na filial atual, mas que não estão em <upstream> são salvas em uma área temporária. Este é o mesmo conjunto de compromissos que seria mostrado por; ou por , se estiver ativo (veja a descrição abaixo); ou por , se a opção for especificada.git log <upstream>..HEADgit log 'fork_point'..HEAD--fork-point--fork-pointgit log HEAD--root

A filial atual é redefinida para <upstream>, ou <newbase> se a opção -on foi fornecida. Isso tem exatamente o mesmo efeito de (ou <newbase>). ORIG_HEAD é definido para apontar na ponta do ramo antes do reset.git reset --hard <upstream>

Os compromissos que foram previamente salvos na área temporária são então reaplicados ao ramo atual, um por um, em ordem. Observe que quaisquer compromissos no HEAD que introduzem as mesmas alterações textuais como um compromisso no HEAD.. <upstream> são omitidos (ou seja, um patch já aceito upstream com uma mensagem de compromisso diferente ou omp de tempo será ignorado).

É possível que uma falha de fusão impeça que esse processo seja completamente automático. Você terá que resolver qualquer falha de fusão e execução. Outra opção é contornar o compromisso que causou a falha de fusão com . Para verificar o original <branch> e remover os arquivos de trabalho .git/rebase-apply, use o comando em vez disso.git rebase --continuegit rebase --skipgit rebase --abort

Assuma que existe o seguinte histórico e que o ramo atual é "tópico":

          A---B---C topic
         /
    D---E---F---G master
A partir deste ponto, o resultado de qualquer um dos seguintes comandos:

git rebase master
git rebase master topic
Seria:

                  A'--B'--C' topic
                 /
    D---E---F---G master
NOTA: Esta última forma é apenas uma mão curta de seguido por . Quando as saídas de rebase continuarão a ser o ramo verificado.git checkout topicgit rebase mastertopic

Se o ramo upstream já contiver uma alteração que você fez (por exemplo, porque você enviou um patch que foi aplicado rio acima), então esse compromisso será ignorado. Por exemplo, executar no histórico a seguir (no qual e introduzir o mesmo conjunto de alterações, mas tem informações diferentes de compromisso):git rebase masterA'A

          A---B---C topic
         /
    D---E---A'---F master
resultará em:

                   B'---C' topic
                  /
    D---E---A'---F master
Aqui está como você transplantaria um ramo tópico baseado em um ramo para outro, para fingir que você bifurcou o ramo de tópicos a partir deste último ramo, usando .rebase --onto

Primeiro vamos supor que seu tópico é baseado em branch next. Por exemplo, um recurso desenvolvido no tópico depende de alguma funcionalidade que é encontrada no próximo.

    o---o---o---o---o  master
         \
          o---o---o---o---o  next
                           \
                            o---o---o  topic
Queremos fazer o tema bifurcado do mestrede filiais; por exemplo, porque a funcionalidade sobre qual tópico depende foi incorporada ao ramo mestre mais estável. Queremos que nossa árvore fique assim:

    o---o---o---o---o  master
        |            \
        |             o'--o'--o'  topic
         \
          o---o---o---o---o  next
Podemos obtê-lo usando o seguinte comando:

git rebase --onto master next topic
Outro exemplo de opção --on é rebasear parte de um ramo. Se tivermos a seguinte situação:

                            H---I---J topicB
                           /
                  E---F---G  topicA
                 /
    A---B---C---D  master
em seguida, o comando

git rebase --onto master topicA topicB
resultaria em:

                 H'--I'--J'  topicB
                /
                | E---F---G  topicA
                |/
    A---B---C---D  master
Isso é útil quando o tópicoB não depende do tópicoA.

Uma série de compromissos também podem ser removidos com rebase. Se tivermos a seguinte situação:

    E---F---G---H---I---J  topicA
em seguida, o comando

git rebase --onto topicA~5 topicA~3 topicA
resultaria na remoção dos compromissos F e G:

    E---H'---I'---J'  topicA
    
Isso é útil se F e G foram falhos de alguma forma, ou não devem fazer parte do tópicoA. Observe que o argumento para --on e o parâmetro <upstream> pode ser qualquer compromisso válido.

Em caso de conflito, a rebase de git vai parar no primeiro compromisso problemático e deixar marcadores de conflito na árvore. Você pode usar o git diff para localizar os marcadores (<<<<<<<)e fazer edições para resolver o conflito. Para cada arquivo que você editar, você precisa dizer ao Git que o conflito foi resolvido, normalmente isso seria feito com

git add <filename>
          
Depois de resolver o conflito manualmente e atualizar o índice com a resolução desejada, você pode continuar o processo de rebasing com

git rebase --continue

Alternativamente, você pode desfazer a rebase git com

git rebase --abort



# Git Reverte 

basicamente reverte um commit
O comando git revert é uma operação de desfazer avançada que oferece um método seguro de desfazer alterações. Em vez de excluir ou tornar commits órfãos no histórico de commits, uma reversão vai criar um commit novo que inverte as alterações especificadas. O git revert é uma alternativa mais segura que o git reset em relação à perda de trabalho. Para demonstrar os efeitos do git revert, foram abordados outros comandos que têm uma documentação mais aprofundada nas páginas individuais: git log, git commit e git reset.

# Git Stash

Descrição
Use quando quiser registrar o estado atual do diretório de trabalho e do índice, mas quer voltar para um diretório de trabalho limpo. O comando salva suas modificações locais e reverte o diretório de trabalho para corresponder ao commit.git stashHEAD

As modificações escondidas por este comando podem ser listadas com , inspecionadas com , e restauradas (potencialmente em cima de um compromisso diferente) com . Ligar sem argumentos é equivalente a . Um estoque é por padrão listado como "WIP no nome da branch ... ", mas você pode dar uma mensagem mais descritiva na linha de comando quando você criar uma.git stash listgit stash showgit stash applygit stashgit stash push

O mais recente estoque que você criou é armazenado em; os estoques mais antigos são encontrados no refimento desta referência e podem ser nomeados usando a sintaxe de reflog usual (por exemplo. é o esconderijo mais recentemente criado, é o anterior, também é possível). Os stashes também podem ser referenciados especificando apenas o índice de estoque (por exemplo, o inteiro é equivalente a ).refs/stashstash@{0}stash@{1}stash@{2.hours.ago}nstash@{n}

Comandos

ppush [-p|--patch] [-k|--[no-]keep-index] [-u|--include-untracked] [-a|--all] [-q|--quiet] [-m|--message <message>] [--pathspec-from-file=<file> [--pathspec-file-nul]] [--] [<pathspec>…​]
Save your local modifications to a new stash entry and roll them back to HEAD (in the working tree and in the index). The <message> part is optional and gives the description along with the stashed state.

For quickly making a snapshot, you can omit "push". In this mode, non-option arguments are not allowed to prevent a misspelled subcommand from making an unwanted stash entry. The two exceptions to this are which acts as alias for and pathspec elements, which are allowed after a double hyphen for disambiguation.stash -pstash push -p--

save [-p|--patch] [-k|--[no-]keep-index] [-u|--include-untracked] [-a|--all] [-q|--quiet] [<message>]
This option is deprecated in favour of git stash push. It differs from "stash push" in that it cannot take pathspec. Instead, all non-option arguments are concatenated to form the stash message.

list [<options>]
List the stash entries that you currently have. Each stash entry is listed with its name (e.g. is the latest entry, is the one before, etc.), the name of the branch that was current when the entry was made, and a short description of the commit the entry was based on.stash@{0}stash@{1}

stash@{0}: WIP on submit: 6ebd0e2... Update git-stash documentation
stash@{1}: On master: 9cc0589... Add git-stash
The command takes options applicable to the git log command to control what is shown and how. See git-log[1].

show [<options>] [<stash>]
Show the changes recorded in the stash entry as a diff between the stashed contents and the commit back when the stash entry was first created. By default, the command shows the diffstat, but it will accept any format known to git diff (e.g., to view the second most recent entry in patch form). You can use stash.showStat and/or stash.showPatch config variables to change the default behavior.git stash show -p stash@{1}

pop [--index] [-q|--quiet] [<stash>]
Remove a single stashed state from the stash list and apply it on top of the current working tree state, i.e., do the inverse operation of . The working directory must match the index.git stash push

Applying the state can fail with conflicts; in this case, it is not removed from the stash list. You need to resolve the conflicts by hand and call manually afterwards.git stash drop

apply [--index] [-q|--quiet] [<stash>]
Like , but do not remove the state from the stash list. Unlike , may be any commit that looks like a commit created by or .poppop<stash>stash pushstash create

branch <branchname> [<stash>]
Creates and checks out a new branch named starting from the commit at which the was originally created, applies the changes recorded in to the new working tree and index. If that succeeds, and is a reference of the form , it then drops the .<branchname><stash><stash><stash>stash@{<revision>}<stash>

This is useful if the branch on which you ran has changed enough that fails due to conflicts. Since the stash entry is applied on top of the commit that was HEAD at the time was run, it restores the originally stashed state with no conflicts.git stash pushgit stash applygit stash

clear
Remove all the stash entries. Note that those entries will then be subject to pruning, and may be impossible to recover (see Examples below for a possible strategy).

drop [-q|--quiet] [<stash>]
Remove a single stash entry from the list of stash entries.

create
Create a stash entry (which is a regular commit object) and return its object name, without storing it anywhere in the ref namespace. This is intended to be useful for scripts. It is probably not the command you want to use; see "push" above.

store
Store a given stash created via git stash create (which is a dangling merge commit) in the stash ref, updating the stash reflog. This is intended to be useful for scripts. It is probably not the command you want to use; see "push" above.
