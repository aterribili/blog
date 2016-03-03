---
layout: post
title: "Um pequeno exemplo funcional"
date: 2016-02-20 10:55:28 -0200
categories: haskell c swift
---

Fala galera, tudo bom? Bom, me chamo Abner Terribili, curto muito desenvolvimento e estou iniciando esse blog hoje :). Já possuía um [blog](http://aterribili.blogspot.com.br), porém enjoei daquela ferramenta difícil que é o blogspot e decidi montar o meu próprio, usando [Jekyll](https://jekyllrb.com). Mas vamos lá!

# Problemas Cotidianos
Bom, as vésperas de lançamento da mais nova versão do aplicativo da [Elo7](http://elo7.com.br), nós tinhamos um pequeno problema. Esqueci de mencionar acima, sou desenvolvedor no Elo7 há pouco mais de 1 ano, lá eu fico no time mobile, são apps nativos, com chats e tudo mais, temos bons desafios pela frente. Voltando ao problema, todo nosso sistema de chat é cacheado, ou seja, salvamos a conversa para o usuário não precisar ficar baixando toda conversa, toda vez que ele entra. Isso é detalhe de implementação, porém acontece que de uma versão para a outra, **x.x.x** -> **2x.2x.2x**, haveria uma quebra de compatibilidade, ou seja, precisaríamos forçar a limpeza do cache. Não vou me atentar a discutir se é a melhor solução é resolver via código ou versionamento de banco de dados, no contexto, essa era a solução (apagar via código = débito técnico), mais simples, também não vou contextualizar tanto no problema porque não é o foco do post.

# Solução Swift
```swift
if version == "1.2.0" {
    PersistenceController.clean()
}
```

Perfeito né? Não :(. Acabamos encontrando o seguinte cenário, e se o usuário pulasse da versão **1.1.0** -> **1.3.0**, o que aconteceria?
BAAAM! Quando ele fosse usar o sistema de chat e tivesse a conversa cacheada, ele certamente teria um comportamento inesperado, mas fique tranquilo, não seria um crash.

# Solução 2 Swift também
```swift
func isGreater(list: [Int], list2: [Int]) -> Bool {
    if list.count == list2.count {
        for var i = 0; i < list.count; i++ {
            if list[i] > list2[i] {
                return true
            }
        }
    }
    return false
}
```
Teste: `isGreater([1,2,3,4], list2: [0,1,2,3]) //true`

Bom, óbvio de temos formas mais elegantes de escrever esse método, ou mesmo com outras abordagens, porém para o contexto, ele resolve. A clássica forma imperativa de se resolver um problema, poderia ser recursivo? Sim, porém não conheço muito bem da performance de Swift falando em recursividade e a forma de fazer os casos base também não é tão simples.

# E daí? Solução 3 Haskell
Acordei empolgado pra pensar em algo mais simples de ler, mais simples de fazer e decidi escrever esse código em Haskell:

```haskell
is_greater :: [Int] -> [Int] -> Bool
is_greater _ [] = False
is_greater [] _ = False
is_greater [] [] = False
is_greater (x:xs) (y:ys) | x > y = True
                         | x < y = False
                         | otherwise = is_greater xs ys
```

Imagine o seguinte, talvez a primeira vista, seja dificil entender, é bem parecido com aquela matemática que tinhamos no ensino fundamental (**f(x,y) = x + y**), mas vou explicar linha por linha(ou quase isso):

```haskell
is_greater :: [Int] -> [Int] -> Bool
```

Basicamente a assinatura da [função](http://learnyouahaskell.com/starting-out#babys-first-functions), nada demais, a função `is_greater` recebe duas listas e retorna um booleano. Não se prenda a entender a declaração, o compilador infere pra você!

```haskell
is_greater _ [] = True
```

O primeiro exemplo de [Pattern Matching](http://learnyouahaskell.com/syntax-in-functions#pattern-matching), que basicamente quer dizer, caso venha lista no primeiro parâmetro, e o padrão que representa uma lista vazia (`[]`) como segundo parâmetro, o retorno é verdadeiro, pois a primeira lista é maior que a segunda. Daí todos os outros overloadings de funções que fazem pattern matching seguem a mesma ideia.

Vamos para a última linha:

```haskell
is_greater (x:xs) (y:ys) | x > y = True
                         | otherwise = is_greater xs ys
```

No último passo estamos usando pattern matching novamente, toda vez que vier 2 listas compostas por cabeça (`x`) e corpo (`xs`) esse pattern vai dar match, e esse overloading de função vai ser usado, daí a validação fica simples: `x > y`, a cabeça da primeira lista é maior que a cabeça da segunda lista? Se sim, retorna `True`, se não usamos o poder e a simplicidade da recursividade somada aos overloadings de função, para resolver esse simples problema.

Uma pequena observação, uma lista em Haskell pode ser representada assim: `[1,2,3]`, ou mesmo: `1:2:3:[]`. Por isso esses padrões dão certo :).

Daí, pra testar nossa função você pode usar o [GHC](https://www.haskell.org/ghc/), ou mais fácil ainda GHCi:

```bash
Prelude>[1,2,3] `greater` [0.2.5]
Prelude>True
```


A idéia do post é exemplificar a simplicidade que é resolver um problema de conjuntos usando uma linguagem funcional como Haskell.

Até a próxima!

