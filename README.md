# FormaCerta — análise de execução de exercícios por vídeo

App web (arquivo único) que analisa um vídeo curto de um exercício da biblioteca **openGym** (1.324 exercícios) e avalia postura, amplitude, tempo de execução, balanço do corpo e sinais de carga excessiva.

## Usar
Abra `dist/index.html` no Chrome/Edge/Safari (precisa de internet na 1ª vez: biblioteca MediaPipe via jsDelivr e modelo de pose via Google Storage; URLs no topo do script, trocáveis para hospedagem própria). O vídeo é processado no navegador e não é enviado a servidor.

## Como funciona
1. `tools/build_catalog.py` classifica cada exercício em um de 36 **padrões biomecânicos** (rosca, agachamento, remada, press...). ~90% com alta confiança; o usuário pode trocar o padrão na interface.
2. MediaPipe Pose extrai 33 pontos do corpo a 15 quadros/s.
3. `src/engine.js` detecta repetições no sinal-guia de cada padrão e aplica checagens: balanço de tronco/quadril, cotovelo fugindo, amplitude, tempo da descida, arrancada, simetria, valgo de joelho, calcanhar, inclinação de tronco, coluna, ombro subindo, linha do corpo, fadiga. "Carga excessiva" é inferida de combinações (balanço + arrancada + queda de amplitude etc.).

## Desenvolver
```
python3 tools/build_catalog.py <exercises.json> data/catalog.json
node test/test_engine.mjs && node test/test_patterns.mjs
python3 tools/bundle.py     # gera dist/index.html
```

## Limitações
- Estimativa 2D: precisa de câmera parada, corpo inteiro e ângulo correto (lado ou frente conforme o exercício). Profundidade de agachamento de frente e coluna só de lado.
- Limiares são heurísticas gerais (não validadas clinicamente) e foram testados com poses sintéticas, não com vídeos reais.
- Padrões de baixa confiança (~10%) e movimentos complexos/olímpicos/cíclicos têm análise mais genérica; alongamentos só medem estabilidade.
- Não substitui profissional de educação física/fisioterapia.
