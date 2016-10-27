# 1.Introdução
===============

## Um Breve Resumo
    Cap2 - Discute algumas metodologias de aplicação e armadilhas do Java
    Cap3 - Overview de ferramentas dispniveis para monitoramento de aplicações
    Cap4 - JIT compilation
    Cap5 e Cap6 - Garbage Collection
    Cap7 - Uso de memoria com Java Heap
    Cap8 - Uso de memoria com memoria nativa
    Cap9 - Performance de Thread
    Cap10 - JEE APIs
    Cap11 - JPA e JDBC
    Cap12 - Dicas gerais da API do Java SE
  
## Pataformas e Convenções
* Utilizado o JVM padrão HotSpot da Oracle
* Java 7 ou superior
* Implementacao do Garbage G1 - Java 7
    
## JVM Tunning Flags
* Flag boleano ligado = -XX:+FlagsName
* Flag boleano desligado = -XX:-FlagsName

 ### Client Class and Server Class
      Máquinas de 32-bit são considerado pela JVM client class
      Máquinas de 64-bit são considerado pela JVM server class
    
## A história de performance completa

### Escreva algoritmos melhores
- Não existe uma flag com a opção mágica -XX:+RunReallyFast
- Realmente a JVM otimiza seu for iterando um array para buscar um nome, mas seria melhor utilizar um HashMap
      
### Escreva menos Código
- Quanto mais código tiver para ser compilado mais tempo levará para rodar
- Quanto mais objeto tiver que ser alocado e discartado, mais trabalho o GC ira ter.
- Quanto mais objeto tiver que ser alocado e retidos, mais tempo o ciclo do GC vai demorar.
- Quanto mais classes tiver que ser carregado do disco para a JVM, mais longo irá ser a inicialização da aplicação.
      
#### Nós iremos em última analise sempre perder a guerra
- Na verdade ao decorrer do tempo a perfonce vai caindo devido a inclusão de novas features
- E para isso novos padrões são adotados
- Exemplo é o Firefox 23... ele abrirá mais lentamente um HTML padrão do que um MAC Quadra 95
          
## Oh então vamos em frente, Otimização Prematura
<<<<<<-- Donald Knuth -->>>>>>
- Otimização Prematura termo criado pelo Donald Knuth
- Termo utilizado pelos desenvolvedores para defender a idéia de que não precisa se preocupar com o código até que ele rode
- Termo diz: "Nós devemos esquecer pequenas eficiências, cerca de 97% do tempo é utilizado para isso, otimização prematura é raiz de todo mal"
- A otimização prematura deve ser evitada

     - Otimização geralmente faz com que a estrutura do código fique mais complexa e dificil de entender
     - O ideal é sempre deixar o código limpo e facil de entender e somente focar na otimização quando for detectado a real necessidade
     - Porém há algumas escolhas simples que o desenvolvedor deve considerar quando construir seu código
     
        - Nesses casos deve sim utilizar a otimização prematura
        - Sempre mantendo o código limpo e de facil compreensão

## Procure em outro lugar: O banco de dados é sempre o gargalo

- Considerar recursos externos como um gargalo de performance da sua aplicação
- Se você imaginar que o banco de dados é um gargalo na sua aplicação... Aqui vai uma dica: Ele é !!!!
- No geral se o banco de dados é um gargalo então não adianta otimizar via código o acesso a ele

    ### Bugs e Performance não são problemas limitada à JVM

    - Diante de um problema de performance utilizando o "Occam's Razor" foi identificado o problema
    - Utilizando o Apache JMeter, foi identificado que o código era o problema
    - Problemas de performance pode ser causado por qualquer parte do sistema.
    - Normalmente nas analises é considerado a parte mais nova do sistema, mais cabe a você analisar cada possivel componente do ambiente.

## Otimize para os casos comuns

- É tentador olhar para todos os aspectos de performance. Mas o foco deve ser dado nos casos e cenarios comuns
- O Principio se manifesta de diversas formas:

        * Otimize coletando informações e focando nessas operações que os dados dizem que o tempo é maior.
        * Aplique o "Occam's Razor" para diagnosticar o problema. O mais provavel causa do problema são os códigos novos do que problema de JVM ou OS.
        * Escreva algoritmos simples para as operações mais utilizadas na aplicação. Mesmo que custe um "Slow Down" para outras menos utilizadas.

## Sumario

*'O Java 7 e 8 introduziram inúmeras features e ferramentas para melhorar a performance na JVM, esse livro vai ajudar a entender todos eles*
*Lembrando que a JVM é provavelmente a figura com menos chances de ser a causadora de diminuição de performance de uma aplicação*

# 2. Uma abordagem ao teste de desempenho

*Nesse cápitulo é abordado os 4 Principios de obtenção de teste de performance. A ciência da Engenharia de Performance é coberta por esses Principios.*

## Teste uma aplicação real
- O primeiro Principio consiste em que o teste deve ser feito no produto atual e da forma que o produto vai ser utilizado. E é dividido em 3 categorias

### Microbenchmarks

O primeiro dessa categorias é o Microbenchmarks. O Microbenchmarks é um teste designado para mensurar uma unidade muito pequena de performance exemplo: implementações de chamadas de métodos sincronizado versus o não sincronizado, o overhead na criação de uma thread versus utilizando um pool de thread etc.

Microbenchmarks pode parecer uma boa idéia, mas é muito dificil escrever corretamente.
Considere o código a seguir, que testa um algoritmo de Fibonachi
   
```
public void doTest() {
// Main Loop
double l;
long then = System.currentTimeMillis();

for (int i = 0; i < nLoops; i++) {
l = fibImpl1(50);
}
long now = System.currentTimeMillis();
System.out.println("Elapsed time: " + (now - then));
}
...
private double fibImpl1(int n) {
if (n < 0) throw new IllegalArgumentException("Must be > 0");
if (n == 0) return 0d;
if (n == 1) return 1d;
double d = fibImpl1(n - 2) + fibImpl(n - 1);
if (Double.isInfinite(d)) throw new ArithmeticException("Overflow");
return d;
}

```

Esse código parece simples porém possui vários problemas

####Microbenchmarks deve usar seus resultados

O problema do código acima é que nunca representa verdadeiramente o estado do programa.
O calculo de Fibonnaci nunca é usado para a medição do tempo pois a JVM é livre para discartar-lo em tempo de execução
No final o compilador irá executar esse código:

```
long then = System.currentTimeMillis();
long now = System.currentTimeMillis();
System.out.println("Elapsed time: " + (now - then));

```

Assim o tempo medido será de milesegundos idependentemente da implementação do calculo do Fibonnaci. 
Detalhes de como a JVM elimina os loops no Capitulo 4

O problema acima é facilmente resolvido modificando a variavel local "l" para variavel de instância com (declarado como volatile), permitindo a performance do método seja mensurado.
(Detalhes da razão da variavel deve ser volatile pode ser encontrado no capitulo 9)


####Theread Microbenchmarks
```
A necessidade de utilizar uma variável volátil neste exemplo aplica-se mesmo quando o microbenchmark
é single-threaded.

Seja cuidadoso quando codificar MicroBenchmarks 'Thredado', pois quando varias threads são executadas em um pequeno trecho de código sincronizado a chance de existir um gargalo de performance é muito alta.

Assim a validação no final será feita sobre como a JVM trata a contenção das threads e não no microbenchmark

```

####Microbenchmarks não deve incluir operações irrelevantes

Mesmo assim existem outras amardilhas em potencial. Esse codigo executa somente uma função: Calculo de Fibonacci.
Porém um compilador inteligente irá executar 1 vez o loop ou até mesmo discartar alguns loops caso ache redundante.
Além disso a execução do método *fibImpl(1000)* pode ser muito diferente que *fibImpl(1)* pois o compilador poderá retirar alguns passos do loop (por o valor ser fixo) , por isso os diferentes inputs devem ser considerados nesse tipo de analise.
Para melhor analise o parametro passado deve ser ramdomico, mas deve ser usado com cautela.

Uma forma fácil de codificar o uso de numero randomico para um processo de loop pode ser da seguinte maneira:

```
for (int i = 0; i < nLoops; i++) {
l = fibImpl1(random.nextInteger());
}

```

Porém usando o código acima iremos adicionar mais tempo de processamento para gerar os números ramdomicos. E não é isso que queremos. 

No microbanchmarks o valores de entrada devem ser calculadas previamente, conforme a seguir:

```
int[] input = new int[nLoops];
for (int i = 0; i < nLoops; i++) {
input[i] = random.nextInt();
}
long then = System.currentTimeMillis();
for (int i = 0; i < nLoops; i++) {
try {
l = fibImpl1(input[i]);
} catch (IllegalArgumentException iae) {
}
}
long now = System.currentTimeMillis();

```

####Microbenchmarks deve medir a entrada correta

A terceira armadilha é range de valores, pois valores gerados arbitrariamente não serão necessariamente a representação real de como o código sera usado.
Um exemplo disso é se o valor for negativo ou maior que 1476 (Valor máximo que o calculo de Fibbonacci conseguirá armazenar o resultado em um double).
Assim uma excessão será lançada na metade do método e assim a medição não será tão efetiva.
Uma implementação alternativa seria algo como:

```
public double fibImplSlow(int n) {
if (n < 0) throw new IllegalArgumentException("Must be > 0");
if (n > 1476) throw new ArithmeticException("Must be < 1476");
return verySlowImpl(n);
}

```

Dessa maneira o código sera mais rapido simplesmente pelo fato da vericação ser antes do método de calculo.


####Que tal um periodo de aquecimento?
```
Uma das caracteristicas de performance do Java é a melhoria de performance a cada execução do mesmo código. 
Por esse motivo o Microbenchmark deve ter um tempo de aquecimento, para que a JVM faça um código mais otimizado.
Caso não tenha esse tempo o Microbenchmark não será avaliado pela o seu código mais sim pela performance de sua compilação.

```

Juntando todos as informações o código do Microbenchmark será algo como:

```
package net.sdo;

import java.util.Random;

public class FibonacciTest {

private volatile double l;
private int nLoops;
private int[] input;

public static void main(String[] args) {
FibonacciTest ft = new FibonacciTest(Integer.parseInt(args[0]));
ft.doTest(true);
ft.doTest(false);
}

private FibonacciTest(int n) {
nLoops = n;
input = new int[nLoops];
Random r = new Random();
for (int i = 0; i < nLoops; i++) {
input[i] = r.nextInt(100);
}
}

private void doTest(boolean isWarmup) {
long then = System.currentTimeMillis();
for (int i = 0; i < nLoops; i++) {
l = fibImpl1(input[i]);
}
if (!isWarmup) {
long now = System.currentTimeMillis();
System.out.println("Elapsed time: " + (now - then));
}
}

private double fibImpl1(int n) {
if (n < 0) throw new IllegalArgumentException("Must be > 0");
if (n == 0) return 0d;  
if (n == 1) return 1d;
double d = fibImpl1(n - 2) + fibImpl(n - 1);
if (Double.isInfinite(d)) throw new ArithmeticException("Overflow");
return d;
}

}

```

Mesmo este MicroBenchmarks mede algumas coisas que não são pertinentes à aplicação Fibonacci: há uma certa quantidade sobrecarga na criação das chamadas para o método *fibImpl1()*, e a necessidade de escrever cada resultado para um variável volátil é uma sobrecarga adicional. 
Lembrando que a otimização da JVM se baseia em "Profile Feedback", onde a otimização depende de quantas vezes o método foi executado, tipos de variaveis, tamanho da pilha etc
Também vale ressaltar que a otimização pode ser diferente dependendo em qual classe foi implementado a chamada (Devido ao compilador).
Finalmente, há a questão do MicroBenchmark ser analisado globalmente (Toda a operação) em segundos mas é muito comum também analisar por exemplo a diferença por iteração em nanosegundos. Aí entra a teoria da "morte por 1000 cortes", pois cada nanosegundo somados em uma coleção que será acessado milhões de vezes realmente faz sentido analisar.

Escrever microbanchmarks é dificil pois depende muito do contexto do problema,  e devera ser usado em uma situação realmente que seja útil a sua abordagem, pois dependendo será necessário utilizar um conceito mas Macro de testes.


###Macrobenchmarks
A melhor coisa a usar para medir o desempenho de uma aplicação é a própria aplicação, em conjunto com os recursos externos que utiliza
Imagine o seguinte em uma estrutura onde a aplicação tem a capacidade de processar 500 RPS (Requisição por segundo) não necessariamente significa que essa será o desempenho real dela, pois depende de outros recursos externos que envolve a aplicação como por exemplo um Banco de Dados que só tenha a  capacidade de 100 RPS. Independente da aplicação o "Macro" só vai suportar apenas 100 RPS

###Teste do Sistema completo com vários JVMs
```
Um caso particularmente importante de testar uma aplicação inteira ocorre quando vários aplicativos são executados ao mesmo tempo no mesmo hardware.
Pois quando uma JVM esta rodando sozinha no hardware o desempenho dela será melhor pois ela assumirá que todos os recursos do hardware estão disponiveis.
Por exemplo uma JVM rodando na maquina sozinha ao executar um GarbageCollector ela usará 100% da CPU pois não haverá outra aplicação dividindo o recurso com ela.
Já uma JVM que esta rodando em uma maquina com outras aplicações (Banco de dados, Antivirus e até mesmo outras JVMs) ela não conseguirá atingir a os 100% da CPU ao executar o GC reduzindo o desempenho da aplicação.
```
###Mesobenchmarks
