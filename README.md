<h1>Rocket Lab 26.1: Agente de Dados GenAI (Text-to-SQL)</h1>

<p>
  Este projeto consiste em um <strong>agente inteligente de análise de dados</strong> desenvolvido para o programa Rocket Lab 2026 da Visagio. 
  O agente é capaz de converter perguntas em linguagem natural em consultas SQL executáveis para o banco de dados de um sistema de e-commerce, permitindo que usuários não técnicos analisem dados em tempo real.
</p>

<h2>Stack Tecnológica</h2>
<ul>
  <li><strong>Linguagem:</strong> Python</li>
  <li><strong>Framework de Agentes:</strong> PydanticAI</li>
  <li><strong>Modelo de Linguagem:</strong> Gemini (2.0 e 2.5 flash/lite)</li>
  <li><strong>Banco de Dados:</strong> SQLite3 (7 tabelas de e-commerce)</li>
  <li><strong>Resiliência:</strong> Tenacity (Backoff Exponencial)</li>
</ul>

<hr>

<h2>Como Executar</h2>

<h3>1. Pré-requisitos</h3>
<p>Certifique-se de ter o Python instalado e uma chave de API do <strong>Google AI Studio</strong>.</p>

<h3>2. Instalação das Dependências (Primeira célula do notebook)</h3>
<pre><code>pip install pydantic-ai google-genai tenacity</code></pre> 

<h3>3. Configuração da Chave de API</h3>
<p>
  Para segurança, a chave de API não deve ser exposta no código. No Google Colab, utilize a funcionalidade de <strong>Secrets</strong>.
</p>

<h3>4. Execução do Agente</h3>
<p>Abra o arquivo <code>Text_to_SQL.ipynb</code> e execute a célula principal. O agente iniciará um loop interativo no terminal aguardando sua pergunta.</p>

<hr>

<h2>Conceitos de GenAI Aplicados</h2>

<h3>1. Saída Estruturada (Structured Output)</h3>
<p>
  Utilizei o <code>BaseModel</code> do Pydantic para forçar o modelo a retornar um objeto validado contendo a query SQL e a conclusão. 
  Isso evita alucinações de formato e garante que a aplicação consuma os dados de forma segura.
</p>

<h3>2. Schema Linking (Seleção de Esquema)</h3>
<p>
  Em vez de injetar o esquema inteiro no prompt (abordagem ingênua), o agente usa a ferramenta <code>ver_schema</code> para identificar apenas as tabelas e colunas relevantes. 
  Isso mitiga o fenômeno <i>Lost in the Middle</i> e tenta economizar tokens.
</p>

<h3>3. Information Retrieval e Desambiguação</h3>
<p>
  Para evitar erros de where, implementei a tool <code>buscar_valores_distintos</code>. 
  O agente consulta exemplos reais de dados no banco antes de construir a query final.
</p>

<h3>4. Ciclo de Autocorreção (Self-Correction)</h3>
<p>
  O agente opera em um loop <strong>ReAct (Reason + Act)</strong>. Caso o banco retorne um erro de sintaxe SQL, o erro é devolvido para a IA, que analisa a falha e gera uma nova consulta corrigida automaticamente.
</p>

<h3>5. Guardrails e Segurança</h3>
<p>
  Implementei verificações na ferramenta <code>executar_sql</code> para impedir <strong>Prompt Injections</strong> que tentem executar comandos destrutivos como <code>DROP</code> ou <code>DELETE</code>. 
  O acesso é restrito apenas a operações de leitura (<code>SELECT</code>).
</p>

<h3>6. Resiliência com Backoff Exponencial</h3>
<p>
  Para contornar os limites de taxa (Rate Limits) do <i>Free Tier</i> do Gemini erro 429, utilizei a biblioteca <strong>Tenacity</strong>. 
  O agente aguarda tempos progressivamente maiores antes de tentar uma nova requisição se o limite for atingido.
</p>

<hr>

<p align="center">
  <i>Desenvolvido por Daniel Ramos Mendonça como parte do Rocket Lab 2026 - Visagio.</i>
</p>
