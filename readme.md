# Case series temporais
Objetivo, utilizar base disponibilizada na pasta dados, para gerar projeção de crescimento para os próximos 2 anos. Tanto em valor da carteira, quanto em volume de operações. Além disso, como objetivos secundários estavam fazer uma análise básica de consistência e uma etapa de exploração dos dados.

# Consistência
Análise feita no notebook consistencia.ipynb
Foram encontrados registros nulos em variáveis Categoricas. A tabela abaixo mostra a distribuição por coluna: 

![image](https://github.com/felpcoder/case_bp/assets/74699523/3acfcf22-43c9-41d7-893d-4f4f207ef077)

## Estratégias para tratamento de valores Null
Essas estratégias dependem em geral de uma explicação mais afundo sobre o motivo do valor não estar preenchido.

Para a variável cartão por exemplo, o cliente não possui cartão? É uma falha operacional? Se for null por causa do cliente não possuir cartão é possível criar uma variável derivada indicando se o cliente possui ou não cartão.

Email, telefone e cep são são resultados de tratativas de atualização e consistência de cadastros. Os processos precisam melhorar para se ter essa informação de maneira consistente. É permitido fazer um contrato sem informar um cep? Vale a mesma pergunta para o gênero, a pessoa não precisa apresentar rg no ato de contratação de serviço? Pode-se utilizar algoritmo de classificação para tentar prever valor quando o gênero não vem informado.

Para a variável empregador ou Ocupação, tem-se a pergunta: ou está nulo pois a pessoa estava desempregada no momento da operação ser contratada? Ou a pessoal não tem ocupação? Se for campo opcional é possível criar algoritmo de classificação para dar um valor baseado em base histórica.

Idade, Cidade, Estado e Bairro são variáveis que em geral exigem ETL. Idade, não faz sentido estar vazia. Deveria ser sempre a data da operação, que está totalmente preenchida, menos a data de nascimento, que também está totalmente preenchida. Já as variáveis Cidade, Estado e Bairro integras são consequência da variável CEP integra. É possível fazer ETL através de API opensources para construir informações geoespaciais através do CEP.

Importante notar que parece ser possível preencher valores nulos, utilizando tabela de cadastrado e id.

# Exploração
Análise feita no notebook exploracao.ipynb. Foram feitas diversas análises univariadas e bivariadas. O objetivo central é entender o que é possível de se considerar como Valor da carteira e Volume de operações e entender variáveis que possam ser usadas para explicar o crescimento nos próximos dois anos.
Seguem as definições:
* Valor da carteira - O valor da carteira em um mês foi definido como a soma da variável valor_principal para todos os contratos existentes na data.
* Volume de Operações - O volume de operações em um mês foi definida como a quantidade de registros da variável contratos existentes na data.
  
Alguns insights tirados dessa seção foram:
* Idade e estado tem distribuição muito parecida para todos os valores.
* Gênero tem distribuições diferentes, e as mulheres são a maioria em valor da carteira e em volume de operações.
* As séries de Valor da carteira e do Volume de operações são altamente correlacionada.
* As séries de Valor da carteira possuí autocorrelação forte com lags quando agrupadas por operação Prod e Refin.
* As series de Valor da carteira possuem alta correlação linear quando agrupadas por operação Prod e Refin.
* A seríe de Valor da carteira Port + Refin é estacionária e não possui autocorrelação.
  
# Modelagem
Com os insights foi escolhida como metodologia o uso de regressões lineares associada a variáveis como data_operacao, genero, operacao e variáveis de valor_principal lag's para que fosse possível estimar a projeção de crescimento para os próximos dois anos. Abaixo, segue gráfico com projeção estimada tanto para Valor da carteira, quanto para Volume de operações:

![image](https://github.com/felpcoder/case_bp/assets/74699523/028513ed-305b-4133-a60b-e4d94955a94c)

É possível observar que a projeção de crescimento em até 2 anos é a carteira aumentar o valor total em 3 milhões no valor_principal. Enquanto que no volume de operações deve subir de 300 mensais para pouco mais de 500. É possível observar que as série de valor e a série de volume de operações são altamente correlacionadas. As regressões se mostraram um bom instrumento para entender e modelar séries temporais, pelo menos para séries que possuem autocorrelação. A metodologia utilizando regressão deixou a desejar pra modelar a série estacionária e sem correlação, uma vez que o R2 e demais métricas ficaram bem abaixo.
