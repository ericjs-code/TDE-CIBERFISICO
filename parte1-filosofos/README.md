Parte 1 - Jantar dos Filósofos

Nesta parte foi feita uma simulação simples do problema do jantar dos filósofos usando threading do Python.

A mesa tem 5 filósofos e 5 garfos. Cada filósofo alterna entre pensar, ficar com fome e comer. Para comer, ele precisa pegar dois garfos: o da esquerda e o da direita. Como os garfos são compartilhados com os vizinhos, aparece o problema de concorrência.

Arquivos

jantar_filosofos_ingenua.py: versão que mostra o risco de deadlock.
jantar_filosofos_corrigido.py: versão corrigida usando hierarquia de recursos.

Versão ingênua

Na versão ingênua, todos fazem a mesma coisa:

1. pegam o garfo da esquerda;
2. esperam um pouco;
3. tentam pegar o garfo da direita.

O problema é que pode acontecer de todos pegarem o garfo da esquerda ao mesmo tempo. Aí cada um fica segurando um garfo e esperando o garfo do vizinho. Ninguém consegue continuar, que é a situação de deadlock.

No código a tentativa de pegar o segundo garfo não tem timeout. A ideia é justamente mostrar que a versão ingênua pode travar de verdade. Se todos ficarem tentando pegar o garfo direito, ocorreu deadlock e o programa precisa ser encerrado com Ctrl+C.

Exemplo:

[Filósofo 0] pegou garfo esquerdo (0)
[Filósofo 1] pegou garfo esquerdo (1)
[Filósofo 2] pegou garfo esquerdo (2)
[Filósofo 0] tentando pegar garfo direito (1)
[Filósofo 1] tentando pegar garfo direito (2)
[Filósofo 2] tentando pegar garfo direito (3)

Condições de Coffman

Na versão ingênua aparecem as quatro condições:

1. Exclusão mútua: um garfo não pode ser usado por dois filósofos ao mesmo tempo.
2. Manter e esperar: o filósofo segura um garfo enquanto espera o outro.
3. Não preempção: um filósofo não pode tomar o garfo de outro à força.
4. Espera circular: cada filósofo pode ficar esperando o garfo que está com o próximo.

Com essas quatro condições juntas, o deadlock pode acontecer.

Versão corrigida

Na versão corrigida foi usada hierarquia de recursos.

Cada garfo tem um número de 0 a 4. O filósofo calcula quais são seus dois garfos e sempre pega primeiro o garfo de menor número. Depois pega o de maior número.

Para o filósofo p:

garfo_esquerda = p
garfo_direita = (p + 1) % N

primeiro = min(garfo_esquerda, garfo_direita)
segundo = max(garfo_esquerda, garfo_direita)

Depois ele faz:

pensar()
estado = "com fome"
pegar primeiro
pegar segundo
estado = "comendo"
comer()
liberar segundo
liberar primeiro
estado = "pensando"

Essa ordem quebra a espera circular, porque todos passam a respeitar uma ordem única para pegar os recursos. Então a condição de Coffman negada pela solução foi a espera circular.

Progresso e inanição

A solução corrigida evita deadlock porque não deixa formar um ciclo de espera entre os filósofos.

Na simulação, cada filósofo executa 3 ciclos. Também foram usados tempos pequenos e aleatórios para pensar e comer, então os filósofos não tentam sempre pegar os garfos no mesmo instante. Depois de comer, cada um libera os dois garfos corretamente.

Com isso, na execução do programa todos conseguem comer e terminar. Para uma justiça ainda mais forte em um sistema real, daria para adicionar uma fila ou um semáforo justo, mas para esta simulação a combinação de hierarquia, liberação correta e tempos alternados garante progresso e evita inanição na prática.

Como executar

Entrar na pasta:

cd parte1-filosofos

Rodar a versão ingênua:

python jantar_filosofos_ingenua.py

Rodar a versão corrigida:

python jantar_filosofos_corrigido.py

Logs

A versão ingênua deve mostrar filósofos pegando o garfo esquerdo e depois ficando parados ao tentar pegar o direito. Essa parada é o deadlock.

A versão corrigida deve terminar com:

Versão corrigida finalizada normalmente.
