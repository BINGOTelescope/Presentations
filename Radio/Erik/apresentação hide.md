# Erik H L Guimaraes
# BINGO


# Astro:

## gsm_point_source.py

gsm_point_source.py é um arquivo python plugin onde eu posso adicionar fontes pontuais de radio ao meu codigo, essas fontes vão gerar os sinais pontuais no meu mapa e adicionando-o no mapa de gsm ou global sky model, a função "add_point_sources"
adiciona as fontes pontuais listadas e configuradas como variaveis (ra dec) e as adiciona ao helpix onde calcula suas contribuições para aquela frequencia. Caso nenhuma fonte seja adicionada, o codigo irá adicionar todas as fontes do arquivo de fontes pontuais. tendo isso em mente, eu posso adicionar a fonte que eu quiser de forma customizada, ou adicionar todas as fontes do arquivo. O arquivo de fontes pontuais é um arquivo yaml que contem as fontes pontuais que eu quero adicionar ao meu mapa, e suas respectivas coordenadas ra e dec. Esse arquivo tambem define os modelos de fontes astrofisicas pontuais, modelos esses usados como parametros na hora de definir os objetos.

## gsm.py

Apenas carrega os mapas de modelo GSM presente nos dados do hide organizando para gerar os mapas baseando-se no nsize.

## hi_sky.py

Esse plugin pega os arquivos de dados do ceu no repositorio /hide/data/sky. nesse codigo é carregado o contexto ctx que carrega informações como o nome dos arquivos com mapas de sinal astronomico, nome dos arquivos com as frequencias desses sinais astronomicos e A frequência específica para a qual o mapa de sinal astronômico deve ser carregado.
carrega os fits dos mapas gsm e suas frequencias, verifica se essa frequencia fornecida está dentro dos dados carregados com "
    for i, frequency in enumerate(gsm_frequencies):
        if ctx.frequency == frequency:
            break
"
Em resumo A função load_signal carrega dados de mapas astronômicos e frequências de arquivos FITS, verifica se a frequência fornecida está dentro do intervalo disponível, encontra o mapa correspondente à frequência e retorna esse mapa.

# Beam

## Airy.py

airy_wrapper: Cria uma função que calcula o padrão de difração de uma abertura circular (função Airy) para coordenadas 
(x, y).
A função interna wrapped usa a função de Bessel de primeira ordem para calcular o padrão de difração.
Constantes e Tabela de Bessel:
_TABLERANGE: Define o tamanho da tabela de valores pré-calculados.
_BESSEL_J1_TABLE: Tabela de valores pré-calculados da função de Bessel de primeira ordem.
_C: Constante usada para escalar os valores de entrada para a tabela.
_bessel_j1_hope: Função JIT compilada que realiza a interpolação linear da função de Bessel de primeira ordem usando a tabela de valores pré-calculados.
bessel_j1: Interface para _bessel_j1_hope, que calcula os valores da função de Bessel de primeira ordem para uma array de entradas e retorna os resultados.

## Beamz.py 

Este trecho de código realiza a interpolação de um feixe (beam) usando métodos linear e cúbico, normaliza os resultados, gera visualizações e salva os resultados em arquivos FITS e PNG. A interpolação é feita usando interpolate.griddata, e as visualizações são geradas com healpy e matplotlib. A função normalization retorna o valor 1, possivelmente como um placeholder.

## gaussian_fwhm.py

Funções de Conversão: fwhm2sigma e sigma2fwhm convertem entre FWHM e sigma para uma distribuição gaussiana.
gauss_wrapper: Cria uma função "wrapper" para calcular a resposta do feixe gaussiano, normaliza os resultados, e gera visualizações e arquivos FITS.
wrapped: Função interna que calcula a resposta do feixe gaussiano para coordenadas (i, j).
Visualização e Escrita de Arquivos FITS: Se habilitado, gera visualizações do feixe e escreve os dados em arquivos FITS.
Se precisar de mais detalhes ou ajuda com outra parte do código, por favor, me avise!

# config: 

## bingo.py

rfiexponent: Seleciona o modelo de RFI (exponencial ou gaussiano).
rfienhance: Aumenta a fração do tempo ou da área afetada pela RFI.
rfiday: Define o período do dia em que a RFI é mais intensa.
rfidamping: Reduz a intensidade da RFI durante as horas fora do período de rfiday.
Esses parâmetros são usados para modelar e simular a interferência de radiofrequência em dados astronômicos, permitindo ajustar a intensidade e a distribuição temporal da RFI de acordo com diferentes cenários.

# data:

## make_fake_bingo_model_0.py

Este trecho de código gera dados simulados para as configs do "BINGO", criando arquivos de dados para ganho, fundo, ruído e interferência de radiofrequência (RFI) para diferentes cornetas do telescópio. Os dados são salvos em arquivos .dat e copiados para um diretório de destino. Os valores de ganho são definidos como 1, enquanto fundo e ruído são definidos como 0. A RFI é calculada como a média dos valores fornecidos.

# earth:

## constant.py

A função load_signal no arquivo constant.py cria e retorna um mapa HEALPix com um sinal constante. O valor do sinal constante é obtido do objeto de contexto (ctx). A função realiza os seguintes passos:

Obtém o valor de nside do contexto, que define a resolução do mapa HEALPix.
Cria um array de np.float32 preenchido com 1s, com o tamanho igual ao número de pixels no mapa HEALPix para o nside fornecido.
Multiplica cada elemento do array pelo valor do fluxo de sinal constante (earth_signal_flux) obtido do contexto.
Retorna o mapa HEALPix resultante.
Este código é útil para simulações ou testes onde um sinal constante é necessário.

## horizon.py xxxxxx

A função load_signal no arquivo horizon.py modela o efeito do horizonte em um mapa HEALPix. O processo é o seguinte:

Obtém o valor de nside do contexto, que define a resolução do mapa HEALPix.
Cria um array de índices de pixels e converte esses índices para ângulos esféricos.
Converte os ângulos theta para declinação (dec).
Cria máscaras para valores de declinação fora dos limites definidos (vmin e vmax).
Inicializa um array de zeros para armazenar o sinal da Terra.
Cria um ajuste polinomial a partir dos coeficientes fornecidos e aplica esse ajuste aos valores de declinação que não estão na máscara.
Define o sinal para valores de declinação fora dos limites usando o ajuste polinomial nos limites.
Aplica uma transformação logarítmica ao sinal.
Retorna o mapa HEALPix com o sinal da Terra modelado.
Este código é útil para simular o efeito do horizonte em observações astronômicas, ajustando o sinal da Terra com base na declinação e aplicando uma transformação logarítmica.

# plugins:

No geral esse repositorio mostra varios scripts de plugi com aplicações como adiionar fundo, map frequency, earth signal e etc, como aplicações das funções que eu citei acima.

# strategy:

A função drift_scan_strategy gera uma estratégia de varredura por deriva para um telescópio. Ela calcula as coordenadas equatoriais (RA e Dec) para cada passo de tempo, ajustando a altitude do telescópio após cada período de 24 horas. A estratégia resultante é uma lista de objetos CoordSpec que descrevem a posição do telescópio ao longo do tempo. Esta técnica permite que o telescópio observe diferentes partes do céu à medida que a Terra gira, ajustando a altitude para cobrir uma faixa maior do céu.