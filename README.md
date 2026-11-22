# Navegação Dinâmica em Tempo Real — PWA (Next.js + Leaflet)

Repositório de estudo e prática baseado no projeto da **Aula 06.1** da disciplina de **SIG-Web**, ministrada pelo **Prof. José Rui**. O código-base foi clonado do material do professor e é usado aqui para testes e experimentação.

O aplicativo substitui a integração nativa com o ArcGIS Runtime (`LocationDisplay.AutoPanMode`) por uma solução aberta, usando **Next.js**, **Leaflet** e `navigator.geolocation.watchPosition()`.

## Funcionalidades

- **Streaming contínuo com `watchPosition`:** mantém um canal aberto com o receptor GNSS e atualiza a posição em tempo real conforme o usuário se move, sem o consumo excessivo de bateria de um `setInterval`.
- **Economia de bateria com `clearWatch`:** libera o sensor de localização ao pausar a navegação ou desmontar a página.
- **Componente `RastreadorCamera`:** reproduz o *AutoPanMode* com `map.panTo()`, integrando o fluxo declarativo do React ao motor imperativo do Leaflet.
- **Sem erros de SSR (`next/dynamic`):** os componentes do Leaflet carregam só no cliente (`ssr: false`), evitando o erro `window is not defined`.
- **Alternância de camadas:** um clique troca entre imagem de satélite (ESRI World Imagery) e mapa de ruas (OpenStreetMap).
- **PWA com suporte offline:** pode ser instalado na tela inicial do smartphone.

### HUD de telemetria ao vivo

- Velocidade instantânea em km/h
- Rumo (azimute) em graus, com ponto cardeal (N, NE, L etc.)
- Odômetro da distância total, acumulada pela fórmula de Haversine. Só entram deslocamentos maiores que `Math.max(10, 2 * precisão)` metros, para filtrar o ruído do GPS
- Círculo de incerteza com o raio da precisão horizontal (±X metros)

## Como Executar

### Requisitos

- [Node.js](https://nodejs.org/) v18 ou superior
- `npm` ou `yarn`

### Instalação

```bash
npm install
npm run dev
```

Depois, acesse [http://localhost:3000](http://localhost:3000).

## Teste no Celular

A Geolocation API exige HTTPS (ou `localhost`). Para testar o rastreamento em tempo real no smartphone:

```bash
npm run dev -- --experimental-https
```

Acesse o IP local pelo celular (ex.: `https://192.168.1.15:3000`), aceite o aviso de certificado autoassinado, toque em **Iniciar Navegação** e caminhe. O mapa acompanha seus passos, e o velocímetro e o odômetro registram o deslocamento.

## Estrutura do Projeto

```text
navegacao-dinamica-app/
├── app/
│   ├── globals.css           # Estilos globais e Tailwind CSS
│   ├── layout.tsx            # Layout raiz e Service Worker
│   └── page.tsx              # watchPosition, telemetria e HUD
├── components/
│   └── MapaNavegacao.tsx     # Leaflet, RastreadorCamera (AutoPanMode) e camadas
├── lib/
│   └── geodesia.ts           # Haversine, azimute e formatações
├── public/
│   ├── manifest.json         # Manifesto PWA
│   ├── sw.js                 # Service Worker
│   ├── offline.html          # Fallback offline
│   └── icons/
│       └── icon.svg          # Ícone do aplicativo
└── package.json
```

## Créditos

- **Projeto original e referência:** Prof. José Rui — disciplina de SIG-Web (Aula 06.1)
- **Estudo e adaptação:** Wesley Vinicius Fernandes