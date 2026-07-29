# loCS001 — Painel de Licenças & Sistemas

Painel web estático para monitoramento de licenças e status de sistemas de impressão (ex: PaperCut MF, NDD Print) em um portfólio de clientes/instalações. Feito para rodar como página interna (intranet), sem backend, sem banco de dados e sem dependências externas de build.

## Motivação

Ambientes de outsourcing de impressão costumam gerenciar dezenas de instalações, cada uma com sua própria licença, versão de software e prazo de validade. Este painel consolida esses dados em uma única tela, com destaque visual para licenças próximas do vencimento.

> **Sobre os dados neste repositório:** o CSV real com dados de clientes **não é versionado** (veja `.gitignore`). O arquivo `loCS001_Base_.exemplo.csv` incluído aqui tem dados fictícios, apenas para ilustrar o formato esperado — use-o como modelo para gerar seu próprio CSV de produção, que deve ficar apenas no servidor onde o painel está publicado.

## Como funciona

- HTML + CSS + JavaScript puro — sem frameworks, sem etapa de build, sem `node_modules`.
- Os dados vêm de um arquivo `.csv` (separado por `;`) publicado na mesma pasta do site.
- Ao carregar, a página busca o CSV automaticamente (`fetch`) na mesma origem. Se o arquivo não for encontrado (ex: ao abrir o HTML localmente, fora de um servidor), a página usa um snapshot de dados embutido no momento da geração como fallback.
- Basta sobrescrever o CSV no servidor para que o painel reflita a atualização — não é necessário reconstruir ou reimplantar o HTML.
- Botão de recarregar força uma nova busca do CSV sem precisar dar F5 na página.

## Estrutura do CSV esperado

Cabeçalho e colunas (delimitador `;`):

```
system;version_system;multiverse;agent;releaser;host;client;crn;date_license;ricoh;epson;accounting;embedeed;embedeed_license;description;infomation;ativo
```

Exemplo (dados fictícios, apenas ilustrativo):

```
PaperCut MF;24.0.2 (69746);Y;N/A;N/A;N/A;Cliente Exemplo Ltda;C-XXXXXX;01/12/2026;3;1;Y;3;3;uso padrão;;Y
NDD;N/A;N/A;5.24.18;5.30.1;5.63.5;Outro Cliente S.A.;00-00-00-00-00-00;N/A;1;0;Y;1;N/A;;;Y
```

| Coluna | Descrição |
|---|---|
| `system` | Sistema de impressão (`PaperCut MF` ou `NDD`) |
| `version_system` | Versão do PaperCut (quando aplicável) |
| `agent` / `releaser` / `host` | Versões dos componentes do NDD (quando aplicável) |
| `client` | Nome do cliente/instalação |
| `crn` | Código de licença (PaperCut) ou identificador do dispositivo (NDD) |
| `date_license` | Validade da licença no formato `dd/mm/aaaa` (não se aplica ao NDD) |
| `ricoh` / `epson` | Contagem de dispositivos por marca |
| `accounting` | Uso de accounting/rateio de custos (`Y`/`N`) |
| `embedeed` / `embedeed_license` | Terminais embarcados em uso / licenciados |
| `description` / `infomation` | Observações livres |
| `ativo` | Status do cliente (`Y`/`N`/`?`) |

> O painel diferencia automaticamente o modelo de licenciamento: PaperCut é avaliado por data de validade; NDD é tratado como licenciamento próprio, sem data de expiração.

## Publicação (intranet via IIS)

1. Copie `index.html` e o CSV de dados para a mesma pasta, ex: `C:\inetpub\wwwroot\loCS001\`.
2. No IIS Manager, crie um site (ou virtual directory) apontando para essa pasta.
3. Libere a porta usada no firewall do Windows Server.
4. Acesse via `http://<ip-ou-hostname-do-servidor>/`.

Qualquer servidor HTTP estático (IIS, Nginx, Apache, `python -m http.server`, etc.) funciona — o projeto não depende de nenhuma tecnologia de backend específica.

## Estrutura do projeto

```
/
├── index.html                    # painel (HTML + CSS + JS embutidos)
├── loCS001_Base_.exemplo.csv     # exemplo fictício do formato de dados (versionado)
├── loCS001_Base_.csv             # base real de produção (NÃO versionado — ver .gitignore)
└── .gitignore
```

## Limitações conhecidas

- Não há autenticação — controle de acesso deve ser feito na camada de rede/servidor.
- É uma leitura direta do CSV a cada carregamento; não há histórico ou versionamento dos dados dentro do painel.
- Fontes (IBM Plex Sans/Mono) são carregadas via Google Fonts; sem acesso à internet, o navegador usa a fonte padrão do sistema como alternativa.

## Autor

Desenvolvido por Rafael Zonta.
