# Solução de Equações Não Lineares — Newton-Raphson e Secante

> **Fonte:** Aulas 7 e 8 — Soluções de Equações Não Lineares — Métodos de Intervalo Aberto.
>
> **Professora:** Gisele Tessari Santos, D.Sc.
>
> **Base:** CHAPRA & CANALE, *Métodos numéricos para engenharia*.

---

## 1. O assunto da aula

Os slides apresentam dois métodos para encontrar uma raiz de uma equação não linear:

$$
f(x) = 0
$$

Os métodos estudados são:

- **Newton-Raphson**, que usa a derivada da função;
- **Secante**, que aproxima a derivada usando dois pontos da função.

A raiz é o valor de $x$ em que o gráfico cruza o eixo horizontal, isto é, o ponto em que $f(x)$ fica igual a zero.

---

## 2. Métodos de intervalo aberto

Nos métodos de intervalo fechado, como Bissecção e Falsa Posição, começamos com dois pontos que cercam a raiz e possuem sinais opostos.

Nos métodos de intervalo aberto, não precisamos manter um intervalo cercando a raiz durante todo o processo. Usamos uma ou duas estimativas iniciais e produzimos novas estimativas.

### Comparação

| Característica | Intervalo fechado | Intervalo aberto |
|---|---|---|
| Estimativas iniciais | Dois pontos que cercam a raiz | Um ou dois chutes iniciais |
| Garantia de permanência no intervalo | Sim | Não |
| Possibilidade de divergência | Menor | Maior |
| Velocidade quando converge | Geralmente menor | Geralmente maior |
| Exemplos | Bissecção e Falsa Posição | Newton-Raphson e Secante |

> **Regra de ouro:** os métodos abertos podem ser mais rápidos, mas um chute inicial ruim pode fazer a sequência se afastar da raiz ou até divergir.

---

## 3. Antes de iterar: método gráfico

Antes de aplicar um método numérico, é útil desenhar a função. O gráfico ajuda a responder:

1. Qual é a região em que a função está definida?
2. Onde a curva cruza o eixo $x$?
3. Qual chute inicial parece razoável?
4. Existem várias raízes?
5. Há pontos em que a derivada fica próxima de zero?

O gráfico não fornece necessariamente muitas casas decimais, mas ajuda a escolher um chute inicial coerente.

```python
import numpy as np
from matplotlib import pyplot as plt

f = lambda x: x**3 - 6*x**2 + 11*x - 6.1
pontosx = np.linspace(0, 4, 1000)

plt.plot(pontosx, f(pontosx), "-r", label="f(x)")
plt.axhline(0, color="black")
plt.grid()
plt.legend()
plt.xlabel("x")
plt.ylabel("f(x)")
plt.show()
```

**Saída visual:** a curva cruza o eixo $x$ perto de três regiões. O chute $x_0 = 0$ conduz à raiz próxima de $1{,}0545$.

---

## 4. Método de Newton-Raphson

### 4.1 Ideia intuitiva

Imagine que estamos em um ponto $(x_i, f(x_i))$ da curva. Em vez de seguir a curva inteira, desenhamos a **reta tangente** nesse ponto.

A reta tangente cruza o eixo $x$ em um novo ponto. Esse cruzamento é usado como uma estimativa melhorada da raiz:

$$
x_{i+1} = x_i - \frac{f(x_i)}{f'(x_i)}
$$

A ideia é repetir o processo: o novo ponto vira o ponto atual, calculamos outra tangente e encontramos uma nova aproximação.

> Em uma boa região da função, Newton-Raphson se aproxima rapidamente da raiz. O método é conhecido por apresentar convergência quadrática quando as condições adequadas são satisfeitas.

### 4.2 Como aplicar

1. Escolha um chute inicial $x_i$.
2. Calcule $f(x_i)$.
3. Calcule $f'(x_i)$.
4. Substitua na fórmula de Newton-Raphson.
5. Calcule o erro.
6. Se a precisão for suficiente, pare; caso contrário, use $x_{i+1}$ como novo chute.

Um erro aproximado simples é:

$$
E_a = |x_{i+1} - x_i|
$$

Também podemos usar o erro relativo percentual:

$$
\varepsilon_a = \left|\frac{x_{i+1} - x_i}{x_{i+1}}\right| \times 100
$$

Quando conhecemos a raiz de referência $x_v$, podemos calcular o erro verdadeiro:

$$
\varepsilon_t = \left|\frac{x_v - x_{i+1}}{x_v}\right| \times 100
$$

### 4.3 Exemplo manual do slide

A função apresentada é:

$$
f(x) = x^3 - 6x^2 + 11x - 6{,}1
$$

Sua derivada é:

$$
f'(x) = 3x^2 - 12x + 11
$$

Usando $x_0 = 0$:

| Iteração | $x_i$ | $f(x_i)$ | $f'(x_i)$ | $x_{i+1}$ | $|x_{i+1}-x_i|$ |
|---:|---:|---:|---:|---:|---:|
| 1 | 0,000000 | −6,100000 | 11,000000 | 0,554545 | 0,554545 |
| 2 | 0,554545 | −1,674590 | 5,268016 | 0,872424 | 0,317879 |
| 3 | 0,872424 | −0,406055 | 2,814282 | 1,016708 | 0,144284 |
| 4 | 1,016708 | −0,067417 | 1,900591 | 1,052180 | 0,035472 |
| 5 | 1,052180 | −0,003667 | 1,695091 | 1,054343 | 0,002163 |

Por exemplo, na primeira iteração:

$$
x_1 = 0 - \frac{-6{,}1}{11} = 0{,}554545
$$

Na segunda iteração:

$$
x_2 = 0{,}554545 - \frac{-1{,}674590}{5{,}268016} = 0{,}872424
$$

Continuamos até que o erro satisfaça a precisão desejada.

### 4.4 Função Python no estilo do notebook

```python
def newton(xi):
    f = lambda x: x**3 - 6*x**2 + 11*x - 6.1
    df = lambda x: 3*x**2 - 12*x + 11
    erro = 10 # So p iniciar o loop

    while erro >= 0.001: # Enquanto o erro for maior do que a precisao
        xi_prox = xi - (f(xi) / df(xi)) # Calcula o xi+1 pela reta tangente
        erro = abs(xi_prox - xi) # Calcula o erro entre xi+1 e xi
        print(f"xi = {xi}\nxi_prox = {xi_prox}\nF(xi_prox) = {f(xi_prox)}\nErro = {erro}\n")
        xi = xi_prox # Atualiza xi para a proxima iteracao

    return xi_prox, erro

raiz = newton(0)
print(f"Raiz = {raiz[0]}")
```

| Trecho | O que faz |
|---|---|
| `def newton(xi):` | cria a função e recebe o chute inicial |
| `f = lambda x: ...` | define a função cuja raiz será procurada |
| `df = lambda x: ...` | define a derivada necessária no método |
| `erro = 10` | inicia o erro com um valor maior que a tolerância |
| `xi_prox = xi - ...` | calcula explicitamente o próximo valor, $x_{i+1}$ |
| `erro = abs(xi_prox - xi)` | calcula a diferença entre $x_{i+1}$ e $x_i$ |
| `xi = xi_prox` | atualiza o valor atual para a próxima iteração |
| `print(...)` | mostra a evolução da iteração |
| `return xi, erro` | devolve a raiz aproximada e o erro final |

**Output final aproximado:**

```text
Raiz = 1.0543428
```

### 4.5 Pontos de atenção

O método pode falhar ou ficar instável quando:

- $f'(x_i)$ é zero ou muito próximo de zero;
- o chute inicial está longe da raiz desejada;
- existe um ponto de máximo ou mínimo próximo do caminho;
- existe um ponto de inflexão próximo da raiz;
- a função possui várias raízes e o chute conduz para outra delas.

Se $f'(x_i) = 0$, a fórmula exigiria uma divisão por zero. Por isso, uma implementação mais cuidadosa deve testar o denominador antes de atualizar $x_i$.

### 4.6 Newton-Raphson com `scipy.optimize`

O método também está disponível na função `newton`. Quando informamos a derivada, a biblioteca usa Newton-Raphson.

```python
from scipy.optimize import newton

f = lambda x: x**3 - 6*x**2 + 11*x - 6.1
df = lambda x: 3*x**2 - 12*x + 11

raiz = newton(f, 0, fprime=df)
print(raiz)
```

---

## 5. Método da Secante

### 5.1 Por que usar a Secante?

Newton-Raphson precisa da derivada analítica $f'(x)$. Em muitos problemas, essa derivada é difícil de obter ou não está disponível.

A Secante contorna o problema usando dois pontos da função para aproximar a inclinação:

$$
f'(x_i) \approx \frac{f(x_i) - f(x_{i-1})}{x_i - x_{i-1}}
$$

Substituindo essa aproximação na fórmula de Newton, obtemos:

$$
x_{i+1} = x_i - \frac{f(x_i)(x_{i-1} - x_i)}{f(x_{i-1}) - f(x_i)}
$$

A fórmula também pode ser escrita como:

$$
x_{i+1} = x_i - \frac{f(x_i)(x_i - x_{i-1})}{f(x_i) - f(x_{i-1})}
$$

As duas formas são equivalentes.

### 5.2 Como aplicar

1. Escolha duas estimativas iniciais, $x_{i-1}$ e $x_i$.
2. Calcule $f(x_{i-1})$ e $f(x_i)$.
3. Calcule $x_{i+1}$ pela fórmula da Secante.
4. Calcule o erro ou o resíduo $|f(x_{i+1})|$.
5. Desloque os pontos: o antigo $x_i$ vira $x_{i-1}$ e o novo ponto vira $x_i$.
6. Repita até atingir a precisão desejada.

A Secante não exige que os dois pontos cerquem a raiz. Por isso, assim como Newton, ela pode divergir dependendo das estimativas iniciais.

### 5.3 Exemplo: equação de von Kármán

Para tubos lisos, o slide apresenta a equação:

$$
\frac{1}{\sqrt{f}} = 4\log_{10}(Re\sqrt{f}) - 0{,}4
$$

Queremos determinar o fator de atrito $f$ para:

$$
Re = 100000
$$

Reorganizando tudo para o lado esquerdo, definimos:

$$
K(f) = 4\log_{10}(Re\sqrt{f}) - 0{,}4 - \frac{1}{\sqrt{f}} = 0
$$

O slide usa os chutes iniciais $f_0 = 0{,}007$ e $f_1 = 0{,}006$.

| Iteração | $f_{i-1}$ | $f_i$ | $f_{i+1}$ | $|K(f_{i+1})|$ |
|---:|---:|---:|---:|---:|
| 1 | 0,007000 | 0,006000 | 0,003942 | 1,1357 |
| 2 | 0,006000 | 0,003942 | 0,004633 | 0,2403 |
| 3 | 0,003942 | 0,004633 | 0,004512 | 0,0222 |
| 4 | 0,004633 | 0,004512 | 0,004500 | 0,0005 |

> **Atenção:** nessa tabela, o erro mostrado no slide é o resíduo $|K(f_{i+1})|$. Ele não é o mesmo que o erro relativo percentual entre duas aproximações.

A raiz calculada com maior precisão é aproximadamente:

$$
f = 0{,}00450038
$$

### 5.4 Código do slide para o gráfico e a Secante via `newton`

O código abaixo foi transcrito do slide. A função `newton` recebe apenas a função e o chute inicial; quando a derivada não é fornecida, o SciPy usa a Secante como alternativa.

```python
from numpy import linspace, log10, sqrt
from scipy.optimize import newton
from matplotlib.pyplot import plot,legend, grid, xlabel,ylabel

def karman(f):
    Re=100000.0
    y=4.0*log10(Re*sqrt(f)) - 0.4 - (1/ sqrt(f))
    return y

x=linspace(0.002,0.008,100)
plot(x,karman(x),'r-',label='karman(f)')
xlabel('f')
ylabel('karman(f)')
x0=0.007
raiz=newton(karman,x0)
plot(raiz,karman(raiz),'ko',label='raiz')
legend()
grid()
```

**Saída visual:** o gráfico mostra a curva de $K(f)$ cruzando o eixo horizontal perto de $f=0{,}0045$. O ponto preto marca a raiz encontrada.

### 5.5 Função manual no estilo do notebook

```python
def secante(xi_1, xi):
    f = lambda x: 4*np.log10(100000*np.sqrt(x)) - 0.4 - (1/np.sqrt(x))
    erro = 10 # So p iniciar o loop

    while erro >= 0.001: # Enquanto o residuo for maior do que a precisao
        xi_novo = xi - ((f(xi) * (xi_1 - xi)) / (f(xi_1) - f(xi))) # Calcula o proximo xi
        erro = abs(f(xi_novo)) # Calcula o residuo da nova aproximacao
        print(f"xi_1 = {xi_1}\nxi = {xi}\nxi_novo = {xi_novo}\nErro = {erro}\n")
        xi_1 = xi # Move o ponto atual para a posicao anterior
        xi = xi_novo # Usa o novo ponto na proxima iteracao

    return xi, erro

raiz = secante(0.007, 0.006)
print(f"Raiz = {raiz[0]}")
```

| Trecho | O que faz |
|---|---|
| `def secante(xi_1, xi):` | cria a função com duas estimativas iniciais |
| `f = lambda x: ...` | define a equação de von Kármán na forma $K(f)=0$ |
| `xi_novo = ...` | aplica a fórmula da Secante |
| `erro = abs(f(xi_novo))` | calcula o resíduo usado como critério de parada |
| `print(...)` | mostra as estimativas da iteração |
| `xi_1 = xi` | desloca o ponto atual para a posição anterior |
| `xi = xi_novo` | coloca a nova estimativa como ponto atual |
| `return xi, erro` | devolve o fator de atrito e o resíduo final |

---

## 6. Diferença entre os métodos

| Característica | Newton-Raphson | Secante |
|---|---|---|
| Pontos iniciais | Um | Dois |
| Derivada analítica | Necessária | Não é necessária |
| Fórmula | Usa $f'(x_i)$ | Aproxima $f'(x_i)$ com dois pontos |
| Velocidade | Geralmente muito rápida | Geralmente rápida, mas inferior a Newton em boas condições |
| Risco | Pode divergir | Também pode divergir |
| Biblioteca | `newton(f, x0, fprime=df)` | `newton(f, x0)` quando a derivada é omitida |

---

## 7. Aplicação: ponto de máximo de um polinômio

O slide propõe encontrar o ponto de máximo de:

$$
f(x) = -4 + 1{,}8x^2 + 1{,}2x^3 - 0{,}3x^4
$$

Para encontrar um máximo ou mínimo, procuramos primeiro os pontos críticos, isto é, os pontos em que:

$$
f'(x) = 0
$$

A derivada é:

$$
f'(x) = 3{,}6x + 3{,}6x^2 - 1{,}2x^3
$$

A segunda derivada é:

$$
f''(x) = 3{,}6 + 7{,}2x - 3{,}6x^2
$$

Podemos usar Newton-Raphson sobre a função $f'(x)$:

$$
x_{i+1} = x_i - \frac{f'(x_i)}{f''(x_i)}
$$

Usando $x_0 = 3$:

| Iteração | $x_i$ | $f'(x_i)$ | $x_{i+1}$ | Erro absoluto |
|---:|---:|---:|---:|---:|
| 1 | 3,000000 | 10,800000 | 4,500000 | 1,500000 |
| 2 | 4,500000 | −20,250000 | 3,951220 | 0,548780 |
| 3 | 3,951220 | −3,596291 | 3,802335 | 0,148885 |
| 4 | 3,802335 | −0,231546 | 3,791346 | 0,010989 |
| 5 | 3,791346 | −0,001217 | 3,791288 | 0,000058 |
| 6 | 3,791288 | aproximadamente 0 | 3,791288 | aproximadamente 0 |

O ponto crítico encontrado é:

$$
x \approx 3{,}791288
$$

Calculando o valor da função nesse ponto:

$$
f(3{,}791288) \approx 25{,}285113
$$

Como $f''(3{,}791288) < 0$, esse ponto é um **máximo local**.

### Código Python

```python
def maximo(xi):
    f = lambda x: -4 + 1.8*x**2 + 1.2*x**3 - 0.3*x**4
    df = lambda x: 3.6*x + 3.6*x**2 - 1.2*x**3
    ddf = lambda x: 3.6 + 7.2*x - 3.6*x**2
    erro = 10 # So p iniciar o loop

    while erro >= 0.001: # Enquanto o erro for maior do que a precisao
        xi_prox = xi - (df(xi) / ddf(xi)) # Calcula o xi+1 aplicando Newton na derivada
        erro = abs(xi_prox - xi) # Calcula o erro entre xi+1 e xi
        print(f"xi = {xi}\nxi_prox = {xi_prox}\nF(xi_prox) = {df(xi_prox)}\nErro = {erro}\n")
        xi = xi_prox # Atualiza xi para a proxima iteracao

    print(f"Ponto de maximo = {xi}")
    print(f"Valor maximo = {f(xi)}")
    return xi, f(xi), erro

maximo(3)
```

**Output aproximado:**

```text
Ponto de maximo = 3.7912878
Valor maximo = 25.2851134
```

---

## 8. Erros e critérios de parada no Python

Existem três quantidades que não devem ser confundidas:

| Quantidade | Fórmula | Interpretação |
|---|---|---|
| Erro absoluto entre pontos | $\vert x_{i+1}-x_i \vert$ | mede quanto a estimativa mudou |
| Erro relativo percentual aproximado | $\left\vert\dfrac{x_{i+1}-x_i}{x_{i+1}}\right\vert 100$ | mede a mudança proporcional |
| Resíduo | $\vert f(x_{i+1}) \vert$ | mede quão perto a função está de zero |

Exemplo de cálculo no Python:

```python
erro_absoluto = abs(x_novo - x_antigo)
erro_relativo = abs((x_novo - x_antigo) / x_novo) * 100
residuo = abs(f(x_novo))
```

> **Não confunda:** uma aproximação pode mudar pouco entre duas iterações e ainda possuir um resíduo não tão pequeno. Quando possível, verifique os dois critérios.

---

## 9. Resolução passo a passo em Python

### Bloco 1 — bibliotecas e função do exemplo de Newton

```python
import numpy as np
import matplotlib.pyplot as plt

f = lambda x: x**3 - 6*x**2 + 11*x - 6.1
df = lambda x: 3*x**2 - 12*x + 11
```

| Linha | O que faz |
|---|---|
| `import numpy as np` | importa funções numéricas |
| `import matplotlib.pyplot as plt` | importa as ferramentas de gráfico |
| `f = lambda x: ...` | define a função do exemplo |
| `df = lambda x: ...` | define a derivada da função |

**Output:** nenhuma saída; apenas as funções são preparadas.

### Bloco 2 — gráfico inicial

```python
x = np.linspace(0, 4, 1000)
plt.plot(x, f(x), "r-", label="f(x)")
plt.axhline(0, color="black")
plt.grid()
plt.legend()
plt.show()
```

**Output visual:** curva de $f(x)$ e eixo $x$, permitindo observar as regiões das raízes.

### Bloco 3 — Newton acumulando as iterações

```python
xi = 0
erro = 10

while erro >= 0.001:
    xi_prox = xi - (f(xi) / df(xi))
    erro = abs(xi_prox - xi)
    print(f"xi = {xi} | xi_prox = {xi_prox} | f(xi_prox) = {f(xi_prox)} | erro = {erro}")
    xi = xi_prox

print(f"Raiz aproximada = {xi}")
```

**Output final:** raiz aproximada `1.0543428`.

### Bloco 4 — Secante no exemplo de von Kármán

```python
def karman(f):
    Re = 100000.0
    return 4*np.log10(Re*np.sqrt(f)) - 0.4 - (1/np.sqrt(f))

xi_1 = 0.007
xi = 0.006
erro = 10

while erro >= 0.001:
    xi_novo = xi - ((karman(xi) * (xi_1 - xi)) /
                    (karman(xi_1) - karman(xi)))
    erro = abs(karman(xi_novo))
    print(f"xi = {xi_novo} | residuo = {erro}")
    xi_1 = xi
    xi = xi_novo

print(f"Fator de atrito = {xi}")
```

**Output final:** fator de atrito aproximadamente `0.00450038`.

---

## 10. Conclusões importantes

- Newton-Raphson constrói uma reta tangente e usa o ponto em que ela cruza o eixo $x$.
- A fórmula é $x_{i+1}=x_i-f(x_i)/f'(x_i)$.
- Newton costuma ser muito rápido, mas depende da derivada e de um chute inicial adequado.
- A Secante substitui a derivada por uma inclinação calculada com dois pontos.
- A Secante é útil quando a derivada analítica não está disponível.
- Os dois métodos são de intervalo aberto e podem divergir.
- No exemplo do polinômio, Newton encontrou a raiz próxima de $1{,}054343$.
- No exemplo de von Kármán, a Secante encontrou $f\approx0{,}00450038$ para $Re=100000$.
- Para encontrar um máximo, aplicamos Newton à derivada $f'(x)$; a segunda derivada confirma se o ponto crítico é máximo ou mínimo.

> **Regra de ouro:** antes de confiar em um resultado de Newton ou Secante, observe o gráfico, confira o denominador da fórmula e verifique tanto o erro entre aproximações quanto o resíduo da função.

---

## 11. Referências

- CHAPRA, Steven C.; CANALE, Raymond P. *Métodos numéricos para engenharia*.
- Aula 7 e 8 — Soluções de Equações Não Lineares — Métodos de Intervalo Aberto, Prof.ª Gisele Tessari Santos, D.Sc.
- Documentação SciPy: `scipy.optimize.newton`.
