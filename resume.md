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
    -> Otimização Prematura termo criado pelo Donald Knuth
    -> Termo utilizado pelos desenvolvedores para defender a idéia de que não precisa se preocupar com o código até que ele rode
    -> Termo diz: "Nós devemos esquecer pequenas eficiências, cerca de 97% do tempo é utilizado para isso, otimização prematura é raiz de todo mal"
    -> A otimização prematura deve ser evitada
      -> Otimização geralmente faz com que a estrutura do código fique mais complexa e dificil de entender
      -> O ideal é sempre deixar o código limpo e facil de entender e somente focar na otimização quando for detectado a real necessidade
      -> Porém há algumas escolhas simples que o desenvolvedor deve considerar quando construir seu código
        -> Nesses casos deve sim utilizar a otimização prematura
        -> Sempre mantendo o código limpo e de facil compreensão

  ## Procure em outro lugar: O banco de dados é sempre o gargalo
    -> Considerar recursos externos como um gargalo de performance da sua aplicação
    -> Se você imaginar que o banco de dados é um gargalo na sua aplicação... Aqui vai uma dica: Ele é !!!!
    -> No geral se o banco de dados é um gargalo então não adianta otimizar via código o acesso a ele
      _Bugs e Performance não são problemas limitada à JVM
        -> Diante de um problema de performance utilizando o "Occam's Razor" foi identificado o problema
        -> Utilizando o Apache JMeter, foi identificado que o código era o problema
        -> Problemas de performance pode ser causado por qualquer parte do sistema.
        -> Normalmente nas analises é considerado a parte mais nova do sistema, mais cabe a você analisar cada possivel componente do ambiente.

  ## Otimize para os casos comuns
    -> É tentador olhar para todos os aspectos de performance. Mas o foco deve ser dado nos casos e cenarios comuns
    -> O Principio se manifesta de diversas formas:
      * Otimize coletando informações e focando nessas operações que os dados dizem que o tempo é maior.
      * Aplique o "Occam's Razor" para diagnosticar o problema. O mais provavel causa do problema são os códigos novos do que problema de JVM ou OS.
      * Escreva algoritmos simples para as operações mais utilizadas na aplicação. Mesmo que custe um "Slow Down" para outras menos utilizadas.

## Sumario
      O Java 7 e 8 introduziram inúmeras features e ferramentas para melhorar a performance na JVM, esse livro vai ajudar a entender todos eles
      Lembrando que a JVM é provavelmente a figura com menos chances de ser a causadora de diminuição de performance de uma aplicação

# 2. Uma abordagem ao teste de desempenho

*Nesse cápitulo é abordado os 4 Principios de obtenção de teste de performance. A ciência da Engenharia de Performance é coberta por esses Principios.*

  ## Teste uma aplicação real
    > O primeiro Principio consiste em que o teste deve ser feito no produto atual e da forma que o produto vai ser utilizado. E é dividido em 3 categorias
    -> Microbenchmarks
      -> O Microbenchmarks é um teste designado para mensurar uma unidade muito pequena de performance: O tempo para executar um algoritmo versus sua Implementacao alternativa
      -> 

    
  

