# Simulador Didático de Rede ART-1 (Teoria da Ressonância Adaptativa)

Aplicação interativa em **Single File HTML/JS** desenvolvida para simulação visual, análise matemática e exploração pedagógica da rede neural **ART-1** (*Adaptive Resonance Theory* para padrões binários), abordando o clássico **dilema da estabilidade-plasticidade**.

O simulador é integralmente fundamentado no **Capítulo 10** do livro:
> **Redes Neurais Artificiais para Engenharia e Ciências Aplicadas**  
> Ivan Nunes da Silva, Danilo Hernane Spatti & Rogério Andrade Flauzino.

---

## 🎯 Visão Geral e Recursos

O aplicativo é 100% *client-side* e autocontido (funciona diretamente no navegador, sem dependências externas, npm, compilação ou backend) e oferece:

1. **Arquitetura em Duas Camadas ($F_1$ e $F_2$) e Subsistema de Orientação:**
   - **Camada de Comparação $F^{(1)}$:** Recebe o vetor de entrada $\mathbf{x}$ e a expectativa *top-down* $\mathbf{W}^{(b)}$.
   - **Camada de Reconhecimento $F^{(2)}$:** Neurônios competitivos que disputam a ativação pelo sinal *bottom-up* $u_j$.
   - **Mecanismo de Reset e Vigilância ($\rho$):** Avaliação dinâmica da similaridade $R$ e bloqueio temporário de neurônios que falharem no teste de vigilância.

2. **Presets de Aplicação e Benchmarks Embutidos:**
   - **★ Livro: Manutenção Industrial (Tabela 10.3, 16 bits):** Estudo de caso da Seção 10.8 envolvendo 10 situações operacionais de um processo industrial com 16 variáveis binárias (apresentadas em grade $4 \times 4$). Permite reproduzir os 4 experimentos de sensibilidade do parâmetro de vigilância ($\rho = 0{,}5; 0{,}8; 0{,}9; 0{,}99$).
   - **Letras 5×5 com Ruído (25 bits):** 16 amostras de 4 caracteres geométricos (`X`, `+`, `O`, `L`) com ruído de inversão aleatório (6%), permitindo visualizar o protótipo *top-down* $\mathbf{W}^{(b)}$ como a interseção binária lógica (porta AND) dos membros da categoria.
   - **Exemplo Pequeno (8 bits):** Padrões didáticos simplificados para rastreamento manual e validação das contas do livro.

3. **Etapas do Algoritmo ART-1 (Conforme o Capítulo 10):**
   - **I · Inicialização dos Pesos:**
     - Pesos *bottom-up* (ascendentes):
       $$W^{(f)}_{ji} = \frac{1}{1 + n} \quad \text{(Equação 10.1)}$$
     - Pesos *top-down* (descendentes):
       $$W^{(b)}_{ij} = 1 \quad \text{(Equação 10.2)}$$
   - **II · Reconhecimento / Competição em $F^{(2)}$:**
     - Potencial de ativação ascendente:
       $$u_j = \sum_{i=1}^n W^{(f)}_{ji} \cdot x_i \quad \text{(Equação 10.3)}$$
     - Seleção do neurônio vencedor:
       $$k = \arg\max_j u_j \quad \text{(Equação 10.4)}$$
   - **III e IV · Comparação e Busca de Ressonância:**
     - Projeção descendente e cálculo do grau de similaridade $R$:
       $$R = \frac{\sum_{j} W^{(b)}_{jk} \cdot x_j}{\sum_{j} x_j} = \frac{\|\mathbf{W}^{(b)}_k \cap \mathbf{x}\|_1}{\|\mathbf{x}\|_1} \quad \text{(Equação 10.6)}$$
     - **Teste de Vigilância:**
       - Se $R > \rho$: ocorre a **ressonância adaptativa** e a rede prossegue para a atualização.
       - Se $R \le \rho$: disparo do **sinal de reset**, inativação temporária do neurônio $k$ ($u_k = 0$) e repetição da busca entre os neurônios restantes.
       - Se todos os neurônios existentes forem rejeitados pelo critério de vigilância, um **novo neurônio é alocado** em $F^{(2)}$.
   - **V · Atualização dos Pesos (Aprendizado):**
     - Pesos descendentes (operação lógica AND):
       $$W^{(b)}_{jk}(t+1) = W^{(b)}_{jk}(t) \cdot x_j \quad \text{(Equação 10.9)}$$
     - Pesos ascendentes (normalização pelo tamanho do protótipo):
       $$W^{(f)}_{kj}(t+1) = \frac{W^{(b)}_{jk}(t) \cdot x_j}{\frac{1}{2} + \sum_{i=1}^n W^{(b)}_{ik}(t) \cdot x_i} \quad \text{(Equação 10.10)}$$

4. **Recursos de Visualização e Análise:**
   - **Diagrama de Busca de Ressonância:** Exibição do passo a passo das tentativas, destacando em verde neurônios aceitos e em vermelho resets provocados pelo parâmetro $\rho$.
   - **Grade Visual dos Protótipos:** Representação gráfica dos protótipos armazenados em cada classe (matrizes $4\times 4$ ou $5\times 5$), mostrando a evolução do "esqueleto" binário de cada categoria.
   - **Calculadora Didática de Vigilância:** Permite simular interativamente qualquer par de vetores binários ($\mathbf{x}$ e $\mathbf{W}^{(b)}$) para entender o cálculo da similaridade $R$ e a decisão de ressonância.
   - **Varredura de Vigilância (Curva $\rho$ vs. Número de Classes):** Experimento em lote que simula a variação de $\rho$ de $0{,}1$ a $0{,}99$ mostrando graficamente o surgimento de novas categorias (de agrupamentos generalistas a classes especializadas).

---

## 🚀 Como Executar

Por ser uma aplicação autocontida em HTML5, CSS3 e JavaScript:

1. Abra diretamente o arquivo `simulador_art.html` em qualquer navegador:
   ```bash
   # No Linux / WSL:
   xdg-open simulador_art.html
   ```
   *Ou dê um duplo clique no arquivo via gerenciador de arquivos.*

2. Não requer Node.js, compilação, dependências nem servidor web dedicado.

---

## 🕹️ Guia de Uso Passo a Passo

### 1. Escolha do Problema e Vigilância ($\rho$)
- No painel lateral esquerdo (**Parâmetros do Modelo**), selecione o conjunto de dados desejado (ex.: `★ Livro: manutenção industrial`).
- Ajuste o parâmetro de vigilância $\rho$ (ex.: teste $0{,}5$ para formar poucas classes generalistas, ou $0{,}9$ para formar categorias mais rigorosas e específicas).
- Escolha a ordem de apresentação das amostras (original do livro ou embaralhada).

### 2. Execução Passo a Passo e Busca
- Clique em **Passo a Passo** para inspecionar cada apresentação:
  - Observe o potencial $u_j$ de todos os neurônios da camada $F^{(2)}$.
  - Acompanhe o cálculo da similaridade $R$ e o confronto com $\rho$.
  - Caso ocorra *reset*, veja o neurônio ser desabilitado e a busca continuar até a ressonância ou criação de uma nova classe.
- Clique em **Treinar** para execução automática contínua.

### 3. Inspeção dos Protótipos e Experimentos
- No painel central, inspecione a grade de cada classe gerada e as amostras que convergiram para ela.
- No painel direito (**Equações Conforme o Livro**), consulte a fórmula exata aplicada em cada fase e utilize a **Calculadora de Similaridade** ou o botão **Varredura de $\rho$**.

---

## 📁 Estrutura do Arquivo

- `simulador_art.html`: Arquivo único integrado contendo:
  - Interface com tema escuro e efeitos de vidro (Glassmorphism).
  - Tabela 10.3 e presets binários embutidos.
  - Implementação exata das equações 10.1 a 10.10 do livro texto.
  - Renderizador Canvas com animação de busca de ressonância e mapa de classes.

---

## 📚 Referências

- **Silva, I. N., Spatti, D. H., & Flauzino, R. A. (2010).** *Redes Neurais Artificiais para Engenharia e Ciências Aplicadas*. Editora Artliber, São Paulo. (Capítulo 10: *Redes ART e Teoria da Ressonância Adaptativa*).
- **Carpenter, G. A., & Grossberg, S. (1987).** *A massively parallel architecture for a self-organizing neural pattern recognition machine*. Computer Vision, Graphics, and Image Processing, 37(1), 54-115.
- **Grossberg, S. (1988).** *Nonlinear neural networks: Principles, mechanisms, and architectures*. Neural Networks, 1(1), 17-61.

