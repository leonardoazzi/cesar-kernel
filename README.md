# CESAR16i Kernel

Kernel de controle dos periféricos para o processador hipotético CESAR16i, baseado no PDP-11.

---
- Para isso, você deverá desenvolver funções para controlar:
– Visor, Teclado e Timer
- Além disso, você deve implementar a inicialização do processador (procedimentos de reset), que envolve
– Inicialização dos periféricos e sistemas de interrupção
– Inicialização de quaisquer outras variáveis necessárias para as funcionalidades que você está desenvolvendo
- Finalmente, você deverá implementar o tratador de interrupção dos periféricos

Para o desenvolvimento do trabalho, será colocado à disposição um arquivo fonte que, sugerimos, deve ser usado como base para o desenvolvimento do kernel. O nome do arquivo fornecido é “KERNEL_REF.CED”.

Também serão disponibilizados arquivos com a implementação de aplicações, para facilitar o teste de sua implementação. Esse arquivo deverá ser carregado no simulador, usando a carga parcial para os endereços H8000 até HFFBF, logo após ter sido feita a carga normal de seu kernel.

O kernel deverá ser construído de maneira a suportar as interrupções de teclado e timer, assim como as funções de sistema descritas no arquivo “FuncoesDoKernel.pdf” que acompanha esta especificação.

Após encerrados todos os procedimentos de inicialização do kernel, deverá ser executado um desvio (JMP) para o endereço onde está o programa de aplicação, de maneira a iniciar sua execução. Sugere-se que seja executada pelo kernel a seguinte instrução, onde “_APP” é um símbolo definido no “KERNEL_REF.CED” e corresponde ao início da aplicação: JMP _APP

No arquivo de referência (KERNEL_REF.CED), além do símbolo “_APP”, estão definidos outros símbolos que correspondem à endereços da memória úteis ao desenvolvimento do kernel.

---

- Função para teclado
– kbhit e getchar
- Função para visor
– putchar, putmsg e clr_visor
- Funções para o timer
– get_timer, clr_timer, get_timer_on e set_timer_on
- Função para a velocidade
– get_speed

A descrição de cada função encontra-se no arquivo “FuncoesDoKernel.pdf

---

# **Espaço de Endereçamento**

Forma como o espaço de endereçamento deve ser dividido

- Espaço do kernel (H0000 até H7FFF)
– Essa parte pode ser desenvolvida a partir do arquivo “KERNEL_REF.CED”
    - O que manipular
        - Vetores de entrada (H0100)
        - Área de código, dados e pilha
- Espaço da aplicação (H8000 até HFFBF)
– Essa parte está nos arquivos de aplicação
- Espaço de Periféricos (HFFC0 até HFFFF)

| Espaço | Faixa de Endereços | Descrição |
| --- | --- | --- |
| Kernel | (H0000 a H7FFF)  | Essa área é de uso exclusivo de seu kernel. Nessa área você deverá colocar TODO o código de
seu kernel, incluindo variáveis locais. Seu kernel será composto por:
• Inicialização do kernel (primeiras instruções a serem executadas pelo CESAR)
• Inicialização de variáveis internas do kernel
• Inicialização do sistema de interrupções
• Implementação dos tratadores de interrupção
• Implementação das funções de sistema (ver arquivo “FuncoesDoKernel.pdf” que
acompanha essa especificação)
• Definição da tabela de vetores (a partir do endereço H0100 até H01FF)
• Definição da área de pilha (stack) |
| Kernel: Vetores de Entrada | (H0100 a H01FF) | Área onde serão colocados os vetores das funções do kernel. (ver detalhes a seguir) |
| Aplicação | (H8000 a HFF7F) | Área para carregar o programa de aplicação, que será fornecido pelo professor, para
possibilitar o teste do kernel. |
| Periféricos | (HFF80 a HFFFF) | Área dos periféricos. |

# **Vetores de Entrada**

Nessa área estão os endereços que apontam para o início das funções de sistema, que você deverá implementar. Cada vetor, por se tratar de um endereço, ocupa dois bytes. O primeiro vetor é o vetor[0], o segundo o vetor[1], e assim por diante.

O programa de aplicação que estiver carregado nos endereços H8000 até HFF7F da memória poderá chamar as funções de sistema através desses vetores. Assim, pode-se usar diferentes implementações do kernel com o mesmo programa de aplicação.

Finalmente, todas as funções de sistema devem encerrar sua operação usando uma instrução “RTS R7”. O motivo disso é que a chamada dessas funções será feita através de uma chamada de subrotina (JSR R7,endereço)

Estão organizados na forma de uma tabela de ponteiros. A aplicação chama as funções através dos ponteiros:

```nasm
MOV #VETOR,R0
JSR R7,(_VETTAB(R0))
```

VETOR é uma constante que depende da função a ser chamada.
• Ex: #2, para a _getchar
• Ex: #8, para a _get_clock_time

Existe um vetor para cada função a ser implementada.

Para a forma e ordem dos vetores, ver arquivo “KERNEL_REF.CED”

# Código, dados e pilha

Área de implementação.

- Funções do Kernel;
- Tratadores de interrupção;
- Variáveis necessárias para o Kernel;
- Área reservada para a pilha do sistema.

# KERNEL_REF.CED

- Definição de vários símbolos úteis (equates).
    - _KERNEL: ponto de entrada no “reset”do processador;
    - _VETTAB: definição da área de vetores;
    - _APP: início do programa de aplicação;
    - _IOAREA16 e _IOAREA8: área dos periféricos.

- Lista de procedimentos de reset
    - Função encerra com “JMP_APP”
- Protótipos das funções da API do Kernel
    - Arquivo “FuncoesDoKernel2022_1.pdf”
    - Funções encerram com “RTS R7”
    

# Rodar o programa

- Montar o Kernel com Daedalus
- Abrir no simulador
    - Desligar “Atualizar registradores”
- Carrega o Kernel e o programa de aplicação
- Resetar o processador (F10)
- Rodar o programa (F9)

**Carga Parcial**

- Carga do kernel seguida da aplicação
    - Carregar seu kernel no simulador (CTRL-C)
    - Realizar a Carga Parcial (CTRL-P) da aplicação fornecida pelo professor, com os seguintes endereços:
        - Endereço inicial da memória a copiar: 32768
        - Endereço final da memória a copiar: 65471
        - Endereço de destino: 32768
- Carga da aplicação seguida do kernel
    - Carregar a aplicação fornecida pelo professor, no simulador (CTRL-C)
    - Realizar a Carga Parcial (CTRL-P) do kernel, com os seguintes endereços:
        - Endereço inicial da memória a copiar: 0
        - Endereço final da memória a copiar: 32767
        - Endereço de destino: 0
        

**Depuração do Kernel**

- Interrupção só é chamada quando em execução
    - Durante a execução passo-a-passo, a chamada da interrupção está desabilitada.
- Para fazer o programa parar dentro da interrupção, é necessário usar “break-points”.

**Arquivos**

Outros arquivos são fornecidos para facilitar o teste de seu kernel.
Esses arquivos devem ser carregados na região que corresponde às aplicações (ver especificação do espaço de endereçamento)
- App2022_1.pdf - Fluxograma de cada uma das 3 aplicações
- app1.mem - Aplicação usada para teste de getchar, putchar, putmsg e clr_visor
- app2.mem - Aplicação usada para teste de kbhit, get_timer, clr_timer, get_timer_on, set_timer_on
- app3.mem - Aplicação usada para teste de get_speed

[App2022_1.pdf](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9e33d6aa-c2a6-44be-b608-fb62e81235aa/App2022_1.pdf)

# Avaliação

A correção do trabalho será feita através de programas de teste especialmente desenvolvido para esta finalidade.

Para realizar os testes, seu kernel deverá ser carregado na memória e, em seguida, carregado o programa de aplicação de teste, na área H8000 até HFF7F. Para isso, você deverá usar a “carga parcial” disponível no simulador do CESAR16i. Depois disso, será iniciada a execução e a verificação do comportamento correto de seu kernel.

Você deverá entregar um arquivo fonte (arquivo .CED) com a sua implementação do kernel, escrito em linguagem simbólica do CESAR16i, o qual será montado com o montador Daedalus. O código do programa fonte deverá conter comentários descritivos da implementação.

# Funções do Kernel
## Gerência do Visor

Seu kernel vai receber caracteres através das funções “putchar” e “putmsg”, e terá como responsabilidade coloca-los, corretamente, na posição do visor indicada nessas funções. Para a gestão do visor, seu kernel deve implementar a função “clr_visor”, que deve limpar, completamente, o visor. Limpar o visor significa preenche-lo caracteres SPACE (H20).

Quando o kernel passar para a execução da aplicação, o visor deve estar “limpo”. Ou seja, preenchido com caracteres SPACE (H20).

## Gerência do Teclado

Seu kernel deverá ser capaz de informar para a aplicação que o usuário do programa digitou alguma tecla. Para isso, seu kernel deve implementar as funções “kbhit” e “getchar”. Notar que a função “getchar” não coloca os caracteres digitados no visor. A única função do “getchar” é devolver para a aplicação os caracteres digitados. Ainda, a função “kbhit” informa para a aplicação se algo foi digitado. Mas, esta função não retorna nenhuma tecla nem remove a tecla digitada.

## Gerência do Timer

O kernel desenvolvido deverá dar suporte para um timer. A gestão desse timer será realizada pelas funções “clr_timer” (que zera o timer); “get_timer” (que fornece o valor atual do timer); “set_timer_on” (que faz o timer parar ou avançar); “get_timer_on” (que informa se o timer está parado ou avançando).

O timer deve ser implementado no kernel como um valor de 16 bits, sem sinal, que representa a passagem do tempo. O timer contém o valor em milissegundos (ms). O valor desse timer inicia em 0 e avança até 65535, quando deve retornar para 0. Portanto, o maior valor possível nesse timer corresponde a 65,535 segundos.

A resolução do timer deve ser de, no mínimo, 10 milisegundos. Portanto, por exemplo, se o temporizador do CESAR tiver sido programado para interromper a cada 10ms, o timer será somado com o calor 10, a cada interrupção. Outras possibilidades de valores estão apresentados na tabela abaixo.

| Periodicidade da
interrupção (ms) | Valor a ser somado no
timer, a cada interrupção |
| --- | --- |
| 10 | 10 |
| 5 | 5 |
| 2 | 2 |
| 1 | 1 |

## Gerência de Velocidade

O seu kernel deve implementar a função “get_speed”, que tem por função fornecer a velocidade de deslocamento do veículo controlado pelo seu programa. Essa velocidade é fornecida em metros por segundo (m/s) e deve ser um valor entre 0 (zero) e 100 m/s (cem metros por segundo).

O controle da velocidade é feito pelo usuário, através das teclas “+” (que aumenta a velocidade) e “-“ (que diminui a velocidade). Essas teclas devem ser capturadas pelo seu kernel e usadas para aumentar ou diminuir a velocidade. Além disso, essas teclas jamais deverão ser retornadas pelas funções “kbhit” nem “getchar”.

# Funções do Kernel

A seguir são descritas as funções da API e a forma como devem ser chamadas pelos programas de aplicação, incluindo os parâmetros de entrada e valores resultantes de saída.

Essas funções permitem que o programa de aplicação possa utilizar os periféricos disponíveis no CESAR16i (teclado, visor e timer), sem a necessidade de conhecer o funcionamento do hardware e das portas de acesso a esses periféricos.

As funções a serem implementadas deverão ser colocadas na área de memória reservada para o kernel. Iniciando no endereço H0100, você deve definir a Tabela de Vetores do Kernel. Cada vetor (ponteiro com 2 bytes) dessa tabela deve conter o endereço, no kernel, onde inicia a implementação da função correspondente ao vetor.

Observar que a ordem desses vetores deve ser obedecida, rigorosamente. Os vetores e suas funções correspondentes estão indicados abaixo:

| Vetor | Função |
| --- | --- |
| [0] | getchar |
| [1] | putchar |
| [2] | putmsg |
| [3] | clr_visor |
| [4] | Kbhit |
| [5] | get_timer |
| [6] | clr_timer |
| [7] | get_timer_on |
| [8] | set_timer_on |
| [9] | get_speed |

A seguir, você encontra a descrição das funções a serem implementadas. No título de cada função está indicado o protótipo em “C” da função. Nesse protótipo o tipo “WORD” indica um valor com 16 bits sem sinal e “BYTE” indica um valor com 8 bits sem sinal.

## getchar [0]

```c
BYTE getchar(void)
```

Função através da qual a aplicação solicita ao kernel que informe a tecla digitada. Caso não tenha sido digitada uma tecla, a função deve aguardar até que seja digitada uma tecla (a função é “bloqueante”). Ao ser digitada uma tecla, a função deve retornar o código ASCII dessa tecla.

- Parâmetros de entrada: nenhum.
- Parâmetro de saída: registrador R0, com a tecla digitada.

A função só retorna (só termina) se houver uma tecla já digitada ou quando o usuário digitar alguma tecla. O código ASCII da tecla digitada deve ser retornado no registrador R0.

## putchar [1]

```c
void putchar(BYTE c, WORD posicao)
```

A aplicação usa essa função para solicitar que seja colocado um caractere em determinada posição do visor.

- Parâmetros de entrada:
    - o Registrador R5, com o caractere a ser colocado no visor, codificado em ASCII. Só são aceitos valores entre H20 (SPACE) e H7A (“z”).
    - o Registrador R4, com a posição no visor onde colocar o caractere. Só são aceitos valores entre 0 (zero) e 35.

Caso algum dos parâmetros seja inválido, a função não deve apresentar qualquer informação no visor.

## putmsg [2]

```c
void putmsg(BYTE *msg, WORD posicao)
```

A aplicação usa essa função para solicitar que seja colocado no visor um string de caracteres (bytes) terminado por um byte H00(o mesmo delimitador usado em string “C”). Os caracteres ASCII (visíveis e de controle) que formam o string devem ser tratados conforme definido na função “_putchar”.

- Parâmetros de entrada:
    - Registrador R5, com o endereço de memória onde inicia o string.
    - Registrador R4, com a posição no visor onde iniciar a colocar o string. Só são aceitos valores entre 0 (zero) e 35.
    

## clr_visor [3]

```c
void clr_visor(void)
```

A aplicação vai chamar essa função sempre que desejar limpar o visor. O kernel deve preencher o visor com SPACE (H20). Essa função não tem parâmetros de entrada nem de saída.

## kbhit [4]

```c
WORD kbhit(void)
```

Função através da qual a aplicação solicita ao kernel a informação da existência de alguma tecla digitada. A função deve retornar com a informação da existência de tecla. Essa função retorna imediatamente, sem aguardar pela digitação de qualquer tecla.

- Parâmetro de saída: registrador R0, com a informação da existência de tecla.

A função retorna no registrador R0 a informação se existe tecla ou não.

- Se há tecla, o valor em R0 deverá ser zero;
- Se não há tecla, o valor em R0 será um valor qualquer diferente de zero.

## get_timer [5]

```c
WORD get_timer(void)
```

A aplicação chama essa função sempre que desejar saber qual é o valor atual do timer. O valor retornado representa o tempo em milissegundos.

- Parâmetro de saída: registrador R0, com a informação do valor atual do timer.

## clr_timer [6]

```c
void clr_timer(void)
```

A aplicação chama essa função sempre que desejar ZERAR o valor do timer.

Essa função não possui parâmetros de entrada nem de saída.

## get_timer_on [7]

A aplicação chama essa função sempre que desejar saber se o timer está parado ou avançando.

- Parâmetro de saída: registrador R0, com a informação sobre o estado atual do timer (PARADO / AVANÇANDO).

Se o timer estiver PARADO, deve ser retornado o valor 0 (zero). Se o timer estiver avançando, deve ser retornado um valor diferente de 0 (zero).

## set_timer_on [8]

A aplicação chama essa função sempre que desejar definir o estado do timer. Esse estado pode ser PARADO ou AVANÇANDO.

- Parâmetros de entrada: Registrador R5, com o estado a ser colocado no timer

Se o valor de R5 for 0 (zero), o timer deve ser PARADO. Se o valor de R5 for diferente de zero, o timer deve passar para o estado AVANÇANDO.

## get_speed [9]

A aplicação chama essa função para obter informações sobre a velocidade de deslocamento do veículo controlado pelo programa.

- Parâmetro de saída: registrador R0, com a informação atual da velocidade.

O valor retornado pode ser qualquer número entre 0 (zero) e 100, e representa a velocidade em metros por segundo.

A informação retornada por essa função representa o valor da velocidade controlado pelo usuário, através das teclas “+” e “-“.

[FuncoesDoKernel2022_1.pdf](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/73744f07-d8ff-42d3-a346-ae1ddfadc627/FuncoesDoKernel2022_1.pdf)
