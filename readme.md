                        Agente de Busca e Validação de Imagens (Marketing)

DESCRIÇÃO DO PROJETO
--------------------
Este projeto é uma solução de automação (RPA) desenvolvida em Python para enriquecer o cadastro de produtos da empresa. O robô identifica novos itens cadastrados no ERP sem imagens, realiza buscas automatizadas na web, utiliza Inteligência Artificial (GPT-4o) para validar visualmente se a imagem encontrada corresponde ao produto e, por fim, distribui os arquivos aprovados para os servidores de e-commerce e força de vendas.

PRINCIPAIS FUNCIONALIDADES
--------------------
- Monitoramento de Novos Produtos: Consulta diária no banco de dados Oracle para identificar novos cadastros.
- Busca Inteligente: Utiliza a API Tavily para varrer a web em busca de imagens de alta qualidade.
- Validação com IA: Uso da API OpenAI (GPT-4o Vision) para garantir que a imagem baixada é realmente do produto (filtrando banners, logotipos e concorrentes).
- Padronização: Redimensionamento automático e conversão de imagens para o padrão (JPG, 314x314, fundo branco) usando Pillow.
- Upload Multi-Plataforma: Envio automático para Object Storage (MinIO) e servidores FTP (Tablet).
- Relatórios: Envio de e-mail ao final da execução com o status de cada produto (sucesso/falha).

PRÉ-REQUISITOS
--------------------
- Python 3.8 ou superior.
- Acesso à internet (para APIs Tavily e OpenAI).
- Acesso à rede interna (Oracle, PostgreSQL, FTP).
- Executável do MinIO Client (mc.exe) configurado no caminho do sistema (padrão esperado: C:\\mc\\mc.exe).

INSTALAÇÃO E DEPENDÊNCIAS
--------------------
1\.  Clone o repositório para a máquina local.
2\.  Instale as dependências listadas (sugere-se criar um arquivo requirements.txt com: psycopg2, requests, beautifulsoup4, tavily-python, openai, Pillow, python-dotenv).
3\.  Certifique-se de que os diretórios de log e download existam ou que o script tenha permissão para criá-los.

CONFIGURAÇÃO (.ENV)
--------------------
Crie um arquivo .env na raiz do projeto e configure as seguintes variáveis de ambiente:
TAVILY\_API\_KEY=sua\_chave\_tavilyOPENAI\_API\_KEY=sua\_chave\_openaiPG\_HOST=ip\_do\_banco\_postgresPG\_PORT=5432PG\_DATABASE=nome\_do\_bancoPG\_USER=usuarioPG\_PASSWORD=senha

COMO EXECUTAR
--------------------
A execução principal é feita através do script "chamaBuscaImagens.py", que serve como ponto de entrada para o orquestrador.
Comando:python chamaBuscaImagens.py
Recomenda-se agendar esta execução via Agendador de Tarefas do Windows ou IBM RPA Launcher para rodar diariamente.

ESTRUTURA DOS ARQUIVOS
--------------------
- chamaBuscaImagens.py: Gatilho inicial da automação.
- acessaTavily.py: Núcleo da lógica de busca, validação via IA e tratamento de imagem.
- acessaBancoImagens.py: Camada de conexão com bancos de dados (Oracle/PostgreSQL).
- sobeImagensMinio.py: Script para espelhamento de imagens no MinIO Server.
- sobeImagensFTPTablet.py: Script de upload via protocolo FTP.
- moverArquivosBackup.py: Rotina de limpeza e organização de arquivos processados.

OBSERVAÇÕES IMPORTANTES E SEGURANÇA
--------------------
- Credenciais FTP: Atualmente, as credenciais do servidor FTP estão hardcoded no script. É altamente recomendado movê-las para o arquivo .env antes de colocar em produção.
- Dependência Externa: O upload para o MinIO depende do executável "mc.exe". Verifique se ele está presente na máquina host antes de executar.
- Custo de API: O processo utiliza APIs pagas (OpenAI e Tavily). O volume de buscas deve ser monitorado para controle de custos.