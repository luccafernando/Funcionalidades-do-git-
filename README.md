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
