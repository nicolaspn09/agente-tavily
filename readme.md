# Agente de busca e validação de imagens

Automação em **Python** que encontra imagens de produtos na web, valida com **visão computacional (OpenAI)** e padroniza o resultado para uso em catálogo, com opção de envio para **object storage (MinIO)** e **FTP**.

Projeto pensado para portfólio: demonstra integração com APIs (Tavily, OpenAI), tratamento de imagens (Pillow), orquestração de scripts e fluxo ponta a ponta de “descobrir → validar → publicar”.

---

## O que o fluxo faz

1. **Consulta a bases de dados** (PostgreSQL e, no código original, Oracle via módulos auxiliares) para identificar itens que precisam de imagem.
2. **Busca na web** com a API [Tavily](https://tavily.com/) em busca de URLs e contexto relevante.
3. **Validação com IA** usando **GPT-4o (vision)** para reduzir falsos positivos (banners, logos, produtos errados).
4. **Padronização** das imagens (ex.: redimensionamento, JPG, dimensões e fundo definidos no script).
5. **Upload** para MinIO (`mc` / cliente) e/ou FTP, além de rotinas de backup e limpeza de pastas.

---

## Stack

| Área | Tecnologia |
|------|------------|
| Linguagem | Python 3.8+ |
| Busca | Tavily (`tavily`) |
| IA | OpenAI API (`openai`) |
| Imagem | Pillow |
| Web / parsing | `requests`, BeautifulSoup |
| Config | `python-dotenv` |
| Banco | `psycopg2` (+ camada Oracle opcional, ver abaixo) |

Dependências fixadas em [`requirements.txt`](requirements.txt).

---

## Pré-requisitos

- Python **3.8 ou superior**
- Conta e chaves nas APIs **Tavily** e **OpenAI** (serviços pagos conforme uso)
- Para MinIO: cliente **`mc.exe`** no PATH ou no caminho esperado pelo script (ex.: `C:\mc\mc.exe`)
- Rede liberada para APIs e, se for usar DB/FTP, acesso aos seus próprios serviços

---

## Instalação

```bash
git clone <url-do-seu-fork-ou-repo>
cd "Agente Imagens - Github"
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

> **Playwright:** o `requirements.txt` inclui `playwright`. Se algum fluxo do projeto usar browser automatizado, pode ser necessário `playwright install` após o `pip install`.

---

## Configuração (`.env`)

Crie um arquivo `.env` na raiz (ele está no `.gitignore` — **nunca** commite chaves).

Exemplo de variáveis usadas no projeto:

```env
TAVILY_API_KEY=sua_chave_tavily
OPENAI_API_KEY=sua_chave_openai

PG_HOST=localhost
PG_PORT=5432
PG_DATABASE=seu_banco
PG_USER=usuario
PG_PASSWORD=senha
```

Ajuste host, SQL e credenciais de acordo com **seu** ambiente.

---

## Como executar

O ponto de entrada do orquestrador é:

```bash
python chamaBuscaImagens.py
```

Para uso recorrente, você pode agendar no **Agendador de Tarefas do Windows** ou com outro scheduler de sua preferência.

---

## Estrutura dos arquivos

| Arquivo | Função |
|---------|--------|
| `chamaBuscaImagens.py` | Entrada: chama o fluxo principal |
| `acessaTavily.py` | Busca na web, validação com IA, tratamento de imagem, e-mail de resumo |
| `acessaBancosImagens.py` | Conexão e atualização em PostgreSQL (e integração Oracle conforme seu setup) |
| `sobeImagensMinio.py` | Upload / espelhamento no MinIO |
| `sobeImagensFTPTablet.py` | Upload via FTP |
| `moverArquivosBackup.py` | Organização e backup de arquivos processados |

Outros scripts no repositório podem ser específicos de cenários antigos; revise antes de publicar credenciais ou endpoints.

---

## Adaptando para o seu GitHub (importante)

Este código foi extraído de um cenário com **caminhos e bibliotecas internas**. Para rodar só no seu PC ou num fork público, vale conferir:

- **`sys.path.append(r"C:\rpa\Python")`** e imports de `Classes.*` (Oracle, Postgres, e-mail): substitua por **suas** classes ou remova o que não for usar.
- **Destinos de pasta** (ex.: `DIR_TEMP`, `DIR_FINAL`) e **e-mails** em `acessaTavily.py`: ajuste para seus diretórios e destinatários.
- **Credenciais FTP**: prefira variáveis de ambiente no `.env` em vez de valores fixos no código.
- **Custo de API**: monitore chamadas Tavily + OpenAI em produção ou em testes em massa.

Assim o repositório fica claro como **portfólio / estudo**, sem parecer documentação interna de empresa.

---

## Licença

Se for publicar no GitHub, defina uma licença (por exemplo MIT) num arquivo `LICENSE` — este README não substitui termos de uso das APIs (OpenAI, Tavily, etc.).
