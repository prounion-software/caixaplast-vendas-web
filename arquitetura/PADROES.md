# Padrões adotados no projeto

## Linguagem de domínio

Um dos temas mais controversos no desenvolvimento de software é se devemos adotar inglês ou a língua falada pelos utilizadores da ferramenta, no nosso caso português (pt-BR).

O problema em usar português no projeto é lutar contra a mistura de termos que acabam tornando o código menos legivel. Por exemplo, `CriarUsuarioUseCase`, é bem mais estranho do que `CreateUserUseCase`, ou `PedidoController` em relação a `OrderController`.

Mas usar termos em inglês também gera um outro problema, pois os utilizadores da ferramenta não usam inglês para se comunicar no dia-a-dia. Para tentar contornar isso, e o usuário falar em preço (`price`) e nós não entendermos errado como custo (`cost`), iremos criar um [dicionário](DICIONARIO_TERMOS.md) de termos.

Esse [dicionário](DICIONARIO_TERMOS.md) também será usado para a representação dos elementos na interface de usuário.
