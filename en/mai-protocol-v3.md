# Mai Protocol v3: Protocolo de troca perpétua sem permissão baseado em AMM avançado

## 1. Introdução

O Mai Protocol V3 projetado por MCDEX é um protocolo de troca perpétuo baseado em AMM. O swap perpétuo é um dos derivativos mais populares que não tem data de vencimento, suporta negociação de margem e tem seu preço atrelado ao índice de preços. 

O objetivo deste protocolo é permitir que qualquer pessoa crie e negocie em qualquer mercado perpétuo. Para começar, qualquer pessoa pode criar seu próprio mercado perpétuo com o feed de preço do ativo subjacente e escolher qualquer ERC20 como garantia. Em segundo lugar, projetamos um AMM para o mercado perpétuo e este AMM também tem melhor eficiência de capital. Além disso, a AMM resolve o problema de liquidez - qualquer pessoa pode fornecer liquidez à AMM depositando ativos no pool e obter lucro razoável de mercado. Por fim, qualquer um pode negociar permutas perpétuas sem permissão. Os ativos dos traders estão no contrato inteligente de uma forma não custodiante e o processo de negociação é conduzido completamente na cadeia. 

O contrato inteligente do protocolo foi rigorosamente auditado por um terceiro, e não há chave de administrador do protocolo, maximizando a descentralização e segurança deste protocolo.

Acreditamos que o recurso sem permissão é o principal recurso deste protocolo, que pode capacitar toda a comunidade a contribuir com o ecossistema MCDEX - qualquer pessoa pode criar o mercado perpétuo de ativos sintéticos dentro ou fora da rede. Com a evolução da comunidade MCDEX, um mercado perpétuo mais diversificado será criado e o volume de negócios será gerado.

## 2. Trocas Perpétuas

### 2.1 Participantes do mercado
Este protocolo é totalmente baseado em AMM. Consulte o documento MAI v3 AMM Design para obter informações detalhadas sobre AMM. 

Existem as seguintes funções no mercado:

#### AMM

A AMM desempenha o papel de contraparte central, fornecendo liquidez para o swap perpétuo. Como um trader normal, a AMM tem sua conta de margem independente e é capaz de manter posições.. 

#### Operador

Um operador é o criador e gerente da troca perpétua.
- Como se tornar um Operador:
  - Crie um swap perpétuo e defina os parâmetros iniciais (como taxa de margem, parâmetros de risco AMM, etc.). O AMM de permuta perpétua possui um conjunto de parâmetros de risco. Ao ajustar esses parâmetros, um operador é capaz de alterar o risco de criação de mercado da AMM, a profundidade do mercado, a derrapagem e o spread, etc.
  - Pagar (ou fornecer) serviço Oracle. O protocolo define uma interface Oracle para que os Oráculos atualmente disponíveis possam ser aplicados neste protocolo. Um operador pode fornecer seus próprios dados Oracle para trocas perpétuas também.
  - Um operador pode definir uma faixa de parâmetros de risco e ajustar os parâmetros de risco dentro dessa faixa.
  - O operador pode iniciar o processo de governança para alterar a gama de parâmetros de risco. (ver 2.9 parâmetros AMM e governança)
  - O operador pode transferir sua função para outros endereços e cancelar sua função de operador. O mercado perpétuo sem operadora será regido pela LP.
- Benefícios:
- Lucre em todas as negociações cobrando uma taxa de administração definida por eles mesmos.
  - Incentivos que são distribuídos a algumas piscinas potenciais que têm bom desempenho
  - Iniciar proposta de governança de AMM

- Riscos: Não aplicável

#### Provedor de liquidez:
- Como se tornar um LP:
- depósito de garantia no pool AMM
- Benefícios:
- Taxa de negociação em uma proporção fixa
- Lucro com spread e slippage
- Pagamento de financiamento pago pelo trader
- Pena de liquidação
- Incentivo distribuído a alguns pools AMM potenciais (consulte a seção 3.2 tokenomics)
- LP pode participar da governança da AMM (consulte a seção 2.10 Os parâmetros e governança da AMM). 
- Riscos
  - Quando AMM tem posição, existe uma exposição ao risco. Se o preço do índice mudar neste momento, pode haver uma perda no AMM. Essa perda será compartilhada por todos os LP.

#### Comerciante
Os comerciantes são os principais participantes do mercado. Os comerciantes realizam o PNL negociando contra a AMM e são sempre os tomadores. Neste protocolo, todas as negociações devem passar pelo AMM, e os comerciantes não podem contornar o AMM para negociar entre si. Para cada negociação, os comerciantes precisam pagar uma certa quantia de taxa de transação. Além disso, o trader pagará ou receberá o pagamento do financiamento de acordo com a política de taxa de financiamento.

#### Guardião
O guardião é um papel auxiliar. Qualquer um pode ser um detentor para liquidar contas com margem insuficiente (ver seção 2.4 liquidação).

#### Delegator
Um delegador é uma função especial. Pode haver um delegador para cada conta de margem. Um delegador pode operar sobre a conta para negociar (diretamente contra a AMM ou por meio de um corretor), mas não pode retirar fundos da conta. O objetivo do delegador é separar carteiras quentes e frias e realizar a custódia das estratégias de negociação. 

### 2.2 Pagamento de Financiamento
Semelhante ao swap perpétuo tradicional, este protocolo utiliza o pagamento de financiamento para ancorar o preço do índice.

Como todas as negociações devem passar pela AMM, o mercado atinge o equilíbrio quando a AMM não tem posição. Nesse ponto, o AMM fornecerá o melhor lance e o melhor preço de venda em torno do índice, então o preço de mercado atual está próximo ao índice.

O preço do AMM muda de acordo com sua posição. Quando a AMM está comprada, o preço da AMM será inferior ao preço do índice e vice-versa quando a AMM estiver vendida. Em outras palavras, o mercado está desequilibrado neste ponto, então o preço de mercado mudará em relação ao índice. Neste caso, o protocolo cobrará o pagamento do financiamento das posições opostas à AMM e os traders com a mesma posição da AMM também receberão o pagamento do financiamento. A AMM sempre recebe pagamento de financiamento, desde que ocupe cargos. A taxa de financiamento está positivamente correlacionada com o tamanho da posição da AMM, ou seja, quanto mais posições a AMM mantém, mais longe o preço de mercado se desvia, causando um pagamento de financiamento maior.

Por um lado, o pagamento do financiamento pode impedir que mais traders se tornem a contraparte da AMM, o que poderia levar a um novo desvio de preço. Por outro lado, uma alta taxa de financiamento atrairá mais LP para adicionar liquidez ou abrir a mesma posição com AMM. Com base no projeto AMM, tanto adicionar liquidez ao AMM quanto negociar contra AMM, o que reduz o tamanho da posição do AMM, diminuirá o desvio de preço. Dessa forma, o pagamento do financiamento empurrará o preço de mercado de volta para o índice.

### 2.3 Margem e PNL
Devido à natureza sem permissão deste protocolo, qualquer pessoa pode criar qualquer troca perpétua de diferentes níveis de risco. Para evitar a propagação do risco entre diferentes swaps perpétuos, o protocolo usa mecanismo de margem isolado - cada swap perpétuo de propriedade de um comerciante tem sua própria conta de margem independente, e o PNL desta conta não afetará outras contas de margem que eles negociam.

Quando um negociador entra em uma posição longa ou curta de contratos `ΔN` a um determinado preço de entrada` P_entry`, `ΔN> 0` indica que o negociador está comprado,` ΔN <0` indica que o negociador está vendido.

Ao abrir a posição, o saldo da margem da conta de margem do trader deve ser maior ou igual à margem inicial:

     P_mark · | ΔN | · R_im

P_mark é o preço da marca fornecido pela Oracle. `P_mark` é geralmente igual ao preço do índice` P_index` ou é um resultado TWAP de `P_index` ao usar algum Oracle descentralizado como Uniswap. `M_im` é a taxa de margem inicial do contrato perpétuo.

O PNL (Lucro e Perda) da posição é calculado da seguinte forma:

    (P_mark-P_entry) · ΔN
  
O lucro da posição perpétua do MCDEX pode ser retirado a qualquer momento, ou seja, "PNL" sempre se refere ao seu estado realizado. E a perda de posição é deduzida do saldo da margem em tempo real.

O trader pode fechar a posição a um preço de saída P_exit. O PNL depois que o trader fecha a posição é:

    (P_exit-P_entry) · ΔN

O comerciante deve garantir que o saldo da margem da conta de margem seja sempre maior ou igual à margem de manutenção:

    P_mark · ΔN · M_mm

Se o requisito de margem de manutenção não puder ser atendido, a posição será liquidada.

No final, cada troca perpétua tem um parâmetro “Keeper Gas Reward”. Quando a posição for liquidada, o guardião receberá uma determinada quantia de recompensa para pagar pelo gás. Portanto, nosso protocolo exige que, desde que o tamanho da posição da conta de margem seja maior que 0 (desconsiderando o valor da posição), o saldo da margem deve ser suficiente para a “Recompensa do Gás Keeper”. Caso contrário, a posição será liquidada.

### 2.4 Liquidação
Quando o saldo da margem for menor que a margem de manutenção, a posição será liquidada. O detentor inicia a liquidação contra posições com margem insuficiente. Qualquer um pode atuar como guardião. Existem duas formas de liquidação:

- Liquidação via AMM: A posição liquidada será encerrada através da AMM, o que significa que esta posição será transferida para a AMM. A penalidade de liquidação também vai para o pool de liquidez da AMM. O goleiro receberá uma certa quantidade de “Recompensa de Gás do Keeper”.
- Assumido pelo guardião: A posição liquidada será transferida para o guardião. Nesse caso, o guardião assume o risco da posição e recebe a penalidade de liquidação. 

### 2.5 Assentamento

Embora seja um swap perpétuo, pode haver deficiência de liquidez em situações extremas. Se houver perda na liquidação por deficiência de liquidez ou atraso na liquidação da AMM, o fundo de seguro da AMM priorizará a compensação da perda na liquidação. Se o fundo de seguro da AMM for insuficiente, o contrato entrará na fase de liquidação. O swap perpétuo será liquidado com o preço do índice mais recente e o ativo restante será distribuído aos negociantes de acordo com seu saldo de margem. ou seja, a perda de liquidação é assumida por todos os operadores de posição com base em seu saldo de margem. Para quem não tem cargo, não será cobrado nenhum prejuízo na liquidação. Acreditamos que em circunstâncias extremas, entrar em liquidação prontamente e permitir que os traders retirem margem protegerá todos os lados. Este mecanismo é uma forma de disjuntor.

Além disso, quando a Oracle não fornecer atualizações por mais de 24 horas, o contrato também entrará em acordo.

Existem duas fases de liquidação. O primeiro é chamado de “Emergência”, no qual o Oracle para de atualizar. Neste ponto, os responsáveis ​​irão revisar todas as contas de margem e obter a “Recompensa do Gás Keeper”. Durante a revisão, o saldo da margem será calculado com base no preço de ajuste. Quando a revisão é concluída, a liquidação entra em uma segunda etapa denominada “Compensada”. Os comerciantes podem então retirar a margem restante. 

### 2.6 Fundo de seguro

Cada swap perpétuo vem com um fundo de seguro para pagar pela perda de liquidação:

Qualquer pessoa pode doar para o fundo de seguro. Encorajamos os operadores a doar para o capital inicial e complementar o fundo de seguro à medida que o contrato é executado.

Quando a posição do trader é liquidada devido à margem insuficiente, uma certa proporção (com base nos parâmetros AMM) da penalidade de liquidação cobrada vai para o fundo de seguro. O restante vai para o liquidante (AMM ou guardião). Cada fundo de seguro tem um tamanho máximo de fundo. Quando esse tamanho máximo é atingido, o fundo recém-adicionado vai para o pool de liquidez da AMM. LP pode aumentar esse limite superior por meio da governança, mas não pode ser diminuído.

### 2.7 Limit & Stop ordens

Negociar contra a AMM é semelhante a colocar uma ordem de mercado na carteira de ordens tradicional. No caso de trocas perpétuas, as pessoas geralmente tendem a procurar oportunidades e controlar o preço de preenchimento por meio de pedidos limitados. Além disso, a ordem Stop é uma ferramenta importante para negociações altamente alavancadas. Conseqüentemente, projetamos ordens de limite e stop relativamente centralizadas. O negociante pode assinar um limite ou uma ordem de parada e enviar a ordem para um servidor “Corretor” de confiança. O servidor do Corretor observará o preço do AMM na cadeia e enviará o pedido ao contrato quando o preço do AMM atender aos requisitos do pedido. Quando o contrato inteligente receber o pedido do Corretor, ele procederá ao pedido após verificar sua validade.

Lembre-se de que o corretor não corresponderia às ordens recebidas, então todas as ordens serão negociadas contra a AMM na ordem do primeiro a entrar no serviço. O corretor cobrará dos comerciantes a taxa do gás.

### 2.8 Segurança

Compreendemos perfeitamente que a segurança é o fator chave deste tipo de protocolo. Antes de serem publicados, todos os contratos e atualizações passarão por uma auditoria rigorosa. O projeto da estrutura financeira da AMM também será verificado.

Para maximizar a característica descentralizada deste protocolo, não existe uma chave admin no código.

Embora os operadores tenham privilégios limitados sobre as trocas perpétuas, que até certo ponto aumentam a segurança, os comerciantes precisam escolher cuidadosamente as trocas perpétuas que gostariam de negociar e negociar por sua própria conta e risco. Nós encorajamos os operadores a escolherem Oráculos descentralizados e limitar os parâmetros de risco em uma faixa menor (ou definir parâmetros fixos), para que a credibilidade possa ser aumentada. 

### 2.9 Referência

A taxa de referência é suportada neste protocolo. O referenciador receberá uma certa quantia da taxa de referência da taxa do operador e da taxa LP quando o seu árbitro negociar contra a AMM ou por meio de um corretor. A proporção dessa taxa de referência será definida pela governança da AMM. Dessa forma, os investidores institucionais podem lucrar com a indicação de traders para AMM, executando seu próprio front-end. 

### 2.10 Os parâmetros e governança da AMM

Os parâmetros AMM têm duas categorias: os alteráveis ​​e os inalteráveis. O operador e o LP podem ajustar os parâmetros alteráveis ​​votando. AMM tem um grupo de parâmetros de risco e cada um tem uma faixa efetiva. De acordo com o mercado, o operador pode ajustar livremente os parâmetros de risco dentro da faixa efetiva. Um procedimento de votação é necessário se houver necessidade de alterar o intervalo.

Um operador pode iniciar uma proposta. Caso não haja operadora na permuta perpétua, LP com mais de 1% de participação também pode iniciar a proposta. Cada proposta prosseguirá por votação entre o LP que possui o token do LP antes da proposta ser iniciada. O quorum de votos deve ser superior a 10% da participação total da LP. O período de votação dura 72 horas e a resolução pode entrar em vigor após um bloqueio de tempo de 24 horas. Ao votar, o LP precisa apostar seu token LP. Se a proposta for aprovada, o token LP que votou sim será desbloqueado após 72 horas desde a execução da proposta; o token LP que votou não será desbloqueado imediatamente. Se a proposta falhar, todos os tokens LP serão desbloqueados logo após o período de votação. Quando um LP inicia uma proposta, o sistema registra automaticamente seu voto como “sim” e bloqueia seu token de LP.

| Parâmetros AMM | Definição | Alterável / inalterável |
| ---------------- | ------------ | ------------------- ---- |
| Ativo subjacente | Uma string que identifica o ativo subjacente | Inalterável |
Endereço de token colateral | Endereço de token colateral ERC20 | Inalterável |
Endereço do operador | Endereço do operador | Alterável pelo operador |
| Endereço do adaptador Oracle | Endereço do adaptador compatível para Mai3 Oracle | Inalterável
Taxa de margem inicial | Determina a alavancagem máxima quando a posição aberta | Alterável pela governança do LP (apenas decrementos são permitidos)
Taxa de margem de manutenção | Determina a alavancagem quando a posição é liquidada; Menor do que a taxa de margem inicial | Alterável por LP Governance (apenas decrementos são permitidos) |
| Taxa do Vault | A taxa de taxa de negociação que entra no DAO Vault | Alterável por MCDEX DAO Governance |
Taxa de operador | A taxa de negociação que vai para o operador; Menos de 1% | Alterável pela LP Governance |
Taxa LP | A taxa de negociação que vai para LP; Menos de 1% | Alterável pela LP Governance |
Taxa de referência | A taxa de comissão de referência da Taxa de Operador e Taxa de LP | Alterável pela LP Governance |
Taxa de penalidade de liquidação | A taxa de penalidade de liquidação. Penalidade de Liquidação = Valor da Posição * Penalidade de Liquidação; Menor que a taxa de margem de manutenção | Alterável pela LP Governance |
Taxa do fundo de seguro | A proporção da pena que vai para o fundo de seguro | Alterável pela LP Governance |
| Fundo de seguro máximo | O limite superior do fundo de seguro | Alterável por LP Governance (apenas incrementos são permitidos) |
Recompensa do gás Keeper | Quando o detentor executa a liquidação ou revisa as contas durante a liquidação, eles recebem um valor fixo de recompensa para pagar pelo gás. Alterável pela LP Governance |
| Parâmetros de risco AMM | Um conjunto de parâmetros que auxiliam na gestão de risco da AMM como market maker | O alcance efetivo pode ser alterado pela governança e o operador se ajusta dentro do alcance.


Preste atenção ao fato de que todos os parâmetros de risco são eficazes dentro de uma faixa designada. Os operadores podem ajustar os parâmetros dentro desta faixa sem passar pelos procedimentos de governança. Dessa forma, a LP pode autorizar o operador a ajustar os parâmetros de risco de acordo com o mercado, o que é significativo em um estágio inicial. Após o protocolo estar em execução por um tempo e os parâmetros de risco gradualmente se estabilizarem, o LP pode cortar a faixa efetiva dos parâmetros de risco por meio da governança (ou mesmo corrigir os parâmetros) para fortalecer ainda mais a característica descentralizada do AMM.

Além das propostas de revisão dos parâmetros da AMM, existem três propostas especiais que exigem um quorum de voto não inferior a 20% do total de ações da LP.
  - Atualize o código do contrato inteligente AMM
  - Faça um swap perpétuo entrar na fase de liquidação
  - Nomeie um operador. LP pode iniciar esta proposta somente quando não houver operadora.
  
### 2.11 Implantação multi-cadeia

O MCDEX acredita que várias redes públicas têm seus próprios usuários e ecossistemas. Este protocolo permanece neutro na escolha de redes públicas. Para maximizar nossa usabilidade, os contratos inteligentes deste protocolo podem ser executados em várias redes públicas. O MCDEX DAO apoiará o desenvolvimento deste protocolo nessas redes públicas, aumentando o ecossistema MCDEX.

## 3. MCDEX DAO

### 3.1 Governança

A comunidade MCDEX emitiu seu token de governança MCB e fez uma série de trabalhos de governança. Ao lançar o protocolo Mai3, estabeleceremos o MCDEX DAO baseado no MCB. MCDEX DAO será o núcleo da comunidade MCDEX. A missão da MCDAO é desenvolver continuamente o ecossistema MCDEX.

MCDEX DAO tem cofre. O ativo neste cofre vem de:
  - Parte da taxa capturada pelo ecossistema MCDEX
  - MCB recém-emitido no tokenomics MCB
  - Outros pagamentos feitos a MCDEX DAO
  
O ativo no cofre é usado para auxiliar a missão MCDEX DAO. Seu uso específico inclui, mas não se limita a:
  - Incentivo de liquidez: Incentivos para LP de AMM
  - Incentivo de governança: incentivos para titulares de MCB que participam da governança da comunidade
  - Incentivo de desenvolvimento: incentivos para iniciantes e gerentes de comunidade
  - Taxa de auditoria e outras taxas exigidas
  - Adicionar liquidez aos produtos do ecossistema MCDEX
  - Recompra MCB do mercado secundário. O MCB fará parte do ativo do cofre
  - Fornecer liquidez para o MCB (no pool de liquidez do MCB no Uniswap)

Os titulares de MCBs têm o direito de governança de MCDEX DAO. O MCDEX DAO aplica o método “Discussão fora da cadeia, governança na cadeia”. Como a governança está na cadeia, toda proposta é essencialmente um contrato inteligente executável. O MCDEX DAO deve fornecer uma fábrica de contratos inteligente para facilitar o início de propostas.

A governança MCDEX DAO inclui:
  - Gerenciar o uso específico do ativo do cofre;
  - Executar inúmeras trocas perpétuas como operador;
  - Eleger um endereço com várias assinaturas quando necessário. Este endereço de múltiplas assinaturas completará o trabalho de rotina representando MCDEX DAO como um operador;
  - Atualização do contrato inteligente MCDEX DAO

A proposta de governança do MCDEX deve ser iniciada pelos titulares do MCB, e o poder de voto do MCB do iniciador não deve ser inferior a 1% do total emitido. O quorum de votação não deve ser inferior a 10% do total emitido. O iniciador da proposta deve votar sim.

Um titular MCB pode delegar o poder de voto a outro titular.

O período de votação é de 72 horas, e há um bloqueio de tempo de 48 horas. Os participantes precisam estacar MCB. Se a proposta for aprovada, o MCB que votou sim será desbloqueado após 72 horas desde a execução desta proposta, e o MCB que votou não será desbloqueado imediatamente quando o período de votação terminar. Se a proposta falhar, todos os MCB apostados serão desbloqueados imediatamente quando o período de votação terminar.

### 3.2 Tokenomics

O tokenomics MCB original tem espaço para melhorias: O rápido aumento da inflação tem sido opressor para os titulares de MCB. Portanto, discutiremos abertamente o tokenomics MCB atualizado com a comunidade antes do lançamento do Mai 3. Os tokenomics incluem tamanho do problema, objeto do problema e velocidade do problema.

Os princípios da nova tokenomics são os seguintes:
  - O MCB emitido continua circulando.
  - MCB deve ser capturado do ecossistema MCDEX
  - Devido à importância da liquidez em derivativos, ainda precisamos emitir mais MCB como incentivos de liquidez.
  - O novo tokenomics deve levar em consideração o lucro dos atuais detentores de MCB.

