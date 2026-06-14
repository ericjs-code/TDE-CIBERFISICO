Parte 2 - Threads e Semáforos

Nesta parte foi feita uma simulação de várias threads mexendo no mesmo contador.

A ideia é mostrar primeiro o problema sem sincronização, onde as threads acessam o contador ao mesmo tempo e alguns incrementos se perdem. Depois o mesmo teste é feito usando um semáforo binário, deixando apenas uma thread por vez alterar o contador.

Arquivos

contador_sem_sincronizacao.py: versão sem proteção, usada para mostrar a perda de incrementos.
contador_com_semaforo.py: versão corrigida com semáforo binário.

Parâmetros usados

T = 8 threads
M = 200000 incrementos por thread
Valor esperado = 1600000

O que é uma condição de corrida

Condição de corrida acontece quando duas ou mais threads acessam o mesmo recurso compartilhado ao mesmo tempo, e o resultado depende da ordem em que elas executam.

No caso do contador, a operação contador = contador + 1 parece uma coisa só, mas na prática tem etapas:

1. ler o valor atual;
2. somar 1;
3. gravar o novo valor.

Se duas threads leem o mesmo valor antes de uma gravar, as duas podem escrever o mesmo resultado. Assim um incremento acaba apagando o outro.

Versão sem sincronização

Na versão sem sincronização, todas as threads usam o mesmo contador global e nenhuma proteção é usada.

O incremento foi separado assim:

valor_atual = contador
time.sleep(0)
contador = valor_atual + 1

O time.sleep(0) foi colocado para facilitar a troca de contexto entre as threads. Isso ajuda a mostrar a corrida em Python, porque o GIL pode esconder o problema quando o incremento é simples demais.

Versão com semáforo

Na versão corrigida foi usado threading.Semaphore(1).

Como o semáforo começa com apenas uma permissão, ele funciona como semáforo binário. Apenas uma thread por vez entra na parte crítica.

A parte crítica é o trecho onde o contador é incrementado.

O código usa try/finally para garantir que o semáforo seja liberado mesmo se acontecer algum erro dentro da parte crítica.

Pseudocódigo

Globais:

contador = 0
semaforo = Semáforo(1)

Versão sem sincronização:

para cada thread:
    para i de 1 até M:
        valor_atual = contador
        contador = valor_atual + 1

Versão com semáforo:

para cada thread:
    para i de 1 até M:
        semaforo.adquirir()
        try:
            contador = contador + 1
        finally:
            semaforo.liberar()

Programa principal:

iniciar T threads
esperar todas terminarem
imprimir valor esperado
imprimir valor obtido
imprimir incrementos perdidos
imprimir tempo de execução

Resultados

Execução | Versão | Valor esperado | Valor obtido | Incrementos perdidos | Tempo
1 | Sem sincronização | 1600000 | 200327 | 1399673 | 2.85s
2 | Sem sincronização | 1600000 | 200459 | 1399541 | 3.28s
3 | Sem sincronização | 1600000 | 200192 | 1399808 | 3.30s
1 | Com semáforo | 1600000 | 1600000 | 0 | 2.16s
2 | Com semáforo | 1600000 | 1600000 | 0 | 2.17s
3 | Com semáforo | 1600000 | 1600000 | 0 | 2.10s

Discussão dos resultados

A versão sem sincronização perde incrementos porque várias threads podem ler o mesmo valor antigo do contador. Quando elas gravam o novo valor, uma escrita pode sobrescrever a outra.

A versão com semáforo dá o valor correto porque só uma thread por vez executa a parte crítica. Assim cada incremento termina antes de outra thread mexer no contador.

O custo disso é desempenho. A versão sem sincronização pode parecer mais rápida ou mais solta, mas o resultado final fica errado. A versão com semáforo pode demorar mais, porque as threads precisam esperar sua vez, mas o resultado fica correto.

Sobre justiça, o Semaphore do Python não garante uma fila FIFO perfeita. Mesmo assim, neste experimento todas as threads terminam e o valor final fica certo.

Sobre visibilidade e ordenação, em Java existe a garantia de happens-before entre o release de uma thread e o acquire de outra. Em Python, as primitivas do threading funcionam como barreiras práticas de sincronização, fazendo com que a parte crítica seja acessada de forma ordenada neste experimento.

Como executar

Entrar na pasta:

cd parte2-semaforo

Rodar a versão sem sincronização:

python contador_sem_sincronizacao.py

Rodar a versão com semáforo:

python contador_com_semaforo.py
