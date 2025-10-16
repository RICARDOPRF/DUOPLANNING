<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Duoplanning</title>
  <!-- Confetti lib -->
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
  <!-- Áudio de vitória -->
  <audio id="audioWin" preload="auto">
    <source src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_0b6ce6218f.mp3?filename=success-fanfare-trumpets-6185.mp3" type="audio/mpeg">
  </audio>
  <style>
    :root{
      --bg:#0e1116; --text:#e6e6e6; --muted:#b7bec7; --primary:#3aa0ff; --accent:#ffb020; --success:#27c081; --danger:#ff5d5d; --panel:#1a222d; --card:#141a22; --border:#253142; --shadow:0 10px 30px rgba(0,0,0,.35);
    }
    *{box-sizing:border-box}
    body{margin:0;background:linear-gradient(180deg,#0a0d12,#10151d);color:var(--text);font:500 16px/1.5 Inter,system-ui,-apple-system,Segoe UI,Roboto,Ubuntu,Cantarell,Noto Sans,sans-serif}
    a{color:var(--primary);text-decoration:none}
    .container{max-width:1100px;margin-inline:auto;padding:24px}
    header{display:flex;align-items:center;gap:16px;justify-content:space-between;margin-bottom:18px}
    .brand{display:flex;align-items:center;gap:10px}
    .brand .logo{width:36px;height:36px;border-radius:12px;display:grid;place-items:center;background:linear-gradient(135deg,var(--primary),#7cc3ff)}
    .tag{font-size:12px;color:#98a1ad;border:1px solid var(--border);padding:4px 8px;border-radius:999px;background:rgba(255,255,255,.03)}
    .grid{display:grid;gap:16px}
    @media (min-width:860px){.grid.cols-3{grid-template-columns:repeat(3,1fr)}}
    .card{background:var(--card);border:1px solid var(--border);border-radius:16px;box-shadow:var(--shadow)}
    .card.pad{padding:18px}
    .card .title{font-weight:700;margin:2px 0 8px}
    .muted{color:var(--muted)}
    .btn{appearance:none;border:none;border-radius:12px;padding:10px 14px;font-weight:700;cursor:pointer;background:var(--primary);color:#0b1016;transition:transform .04s ease,opacity .2s}
    .btn:hover{transform:translateY(-1px)}
    .btn.ghost{background:transparent;color:var(--text);border:1px solid var(--border)}
    .btn.small{padding:6px 10px;border-radius:10px;font-size:13px}
    .chip{display:inline-flex;align-items:center;gap:6px;border:1px solid var(--border);border-radius:999px;padding:6px 10px;background:rgba(255,255,255,.03);font-size:13px}
    .badge{display:inline-flex;align-items:center;gap:6px;padding:4px 8px;border-radius:999px;font-size:12px;font-weight:700}
    .badge.xp{background:rgba(58,160,255,.15);color:#a8d4ff;border:1px solid rgba(58,160,255,.3)}
    .badge.missao{background:rgba(255,176,32,.12);color:#ffd590;border:1px solid rgba(255,176,32,.28)}
    .badge.streak{background:rgba(255,93,93,.12);color:#ffc8c8;border:1px solid rgba(255,93,93,.25)}
    .progress{height:10px;background:#0d1219;border:1px solid var(--border);border-radius:999px;overflow:hidden}
    .progress .bar{height:100%;background:linear-gradient(90deg,var(--success),#69f0c2);width:0;transition:width .6s ease}
    .login{max-width:520px;margin:30px auto}
    .login .field{display:flex;flex-direction:column;gap:6px;margin-bottom:12px}
    .input, select{background:var(--card);border:1px solid var(--panel);border-radius:10px;padding:10px;color:var(--text)}
    .input::placeholder{color:#8794a6}
    .input.error{border-color: var(--danger); box-shadow:0 0 0 3px rgba(255,93,93,.12)}
    .modules{display:grid;grid-template-columns:repeat(auto-fill,minmax(240px,1fr));gap:14px}
    .mod{position:relative;padding:16px;border-radius:18px;border:1px solid var(--border);background:linear-gradient(180deg,rgba(255,255,255,.02),rgba(255,255,255,.01))}
    .mod h4{margin:0 0 6px}
    .mod .meta{display:flex;gap:8px;flex-wrap:wrap}
    .mod .footer{margin-top:10px;display:flex;gap:8px;flex-wrap:wrap}
    .mod.locked{opacity:.6}
    .seal{position:absolute;top:12px;right:12px}
    .toast{position:fixed;bottom:24px;left:50%;transform:translateX(-50%);background:var(--panel);border:1px solid var(--border);padding:12px 16px;border-radius:12px;opacity:0;pointer-events:none;transition:opacity .2s}
    .toast.show{opacity:1}
    .overlay{position:fixed;inset:0;background:rgba(0,0,0,.6);display:none;align-items:center;justify-content:center;padding:24px}
    .overlay .modal{width:min(560px,96vw);background:var(--panel);border:1px solid var(--border);border-radius:18px;box-shadow:var(--shadow);padding:18px}
    .overlay.show{display:flex}
    .quiz{display:flex;flex-direction:column;gap:12px}
    .quiz .option{display:flex;gap:10px;align-items:flex-start;border:1px solid var(--border);border-radius:12px;padding:10px;cursor:pointer;background:rgba(255,255,255,.02)}
    .quiz .option.correct{border-color:rgba(39,192,129,.5)}
    .quiz .option.wrong{border-color:rgba(255,93,93,.5)}
    .quiz .exp{background:rgba(255,255,255,.04);border:1px dashed var(--border);padding:10px;border-radius:10px}
    .topbar{display:flex;flex-wrap:wrap;gap:12px;align-items:center;justify-content:space-between;margin:10px 0 18px}
    .flex{display:flex;gap:10px;align-items:center}
    .grow{flex:1}

    /* Energia (barra + cápsulas) */
    .energy {display:flex; align-items:center; gap:8px; font-size:13px; border:1px solid var(--border); background:rgba(255,255,255,.03); border-radius:999px; padding:4px 8px}
    .energy .caps {display:flex; gap:4px}
    .energy .cap {width:14px; height:14px; border-radius:4px; border:1px solid var(--border); background:#0d1219; box-shadow: inset 0 0 0 1px rgba(255,255,255,.04)}
    .energy .cap.on { background:linear-gradient(180deg, rgba(39,192,129,.8), rgba(39,192,129,.4)) }

    /* Barra de progresso semanal */
    .weekbar{height:8px;background:#0d1219;border:1px solid var(--border);border-radius:999px;overflow:hidden}
    .weekbar .w{height:100%;background:linear-gradient(90deg,var(--accent),#ffd590);width:0;transition:width .4s ease}

    /* Modal/efeito troféu */
    .trophy { text-align:center; animation: pop .4s ease }
    @keyframes pop { from{ transform:scale(.9); opacity:0 } to{ transform:scale(1); opacity:1 } }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <div class="brand">
        <div class="logo">📐</div>
        <div>
          <div style="font-weight:800;letter-spacing:.2px">Duoplanning</div>
          <div class="muted" style="font-size:12px">Planejamento & Lean</div>
        </div>
      </div>
      <div class="flex">
        <span class="tag" id="tagNivel">Nível: —</span>
        <span class="tag" id="tagXP">XP: 0</span>
        <span class="tag" id="tagStreak">🔥 Streak: 0</span>
        <!-- Energia visual -->
        <span class="energy" id="energyBox" title="Energia para responder quizzes">
          ⚡ <div class="caps" id="energyCaps"></div>
        </span>
        <button class="btn ghost small" id="toggleSound">🔊 Som: Ativado</button>
        <button class="btn ghost small" id="btnSair" style="display:none">Sair</button>
      </div>
    </header>

    <!-- LOGIN -->
    <section id="viewLogin" class="login card pad">
      <h2 class="title">Entrar</h2>
      <p class="muted" style="margin-top:-6px">Digite seu nome para começar.</p>
      <div class="field">
        <label for="inputName">Seu nome</label>
        <input id="inputName" class="input" placeholder="Ex.: Ricardo" />
      </div>
      <button class="btn" id="btnLogin">Começar</button>
    </section>

    <!-- DASHBOARD -->
    <section id="viewDash" style="display:none">
      <div class="topbar card pad">
        <div class="flex grow">
          <div>
            <div class="title" style="font-size:20px">Dashboard</div>
            <div class="muted" style="font-size:13px">Progresso, missões e módulos</div>
          </div>
        </div>
        <div class="flex">
          <span class="badge xp" id="badgeTotalXP">+0 XP</span>
          <span class="badge streak" id="badgeStreakDia">🔥 0 dias</span>
          <button class="btn small" id="btnMissaoDia">Missão do Dia</button>
        </div>
      </div>

      <div class="grid cols-3">
        <div class="card pad">
          <div class="title">Progresso Geral</div>
          <div class="muted" style="font-size:13px;margin-bottom:8px">Complete os módulos da trilha para subir de nível.</div>
          <div class="progress" aria-hidden="true"><div id="xpBar" class="bar"></div></div>
          <div class="flex" style="margin-top:8px;justify-content:space-between">
            <span class="chip">XP <strong id="xpNow">0</strong>/<span id="xpGoal">—</span></span>
            <span class="chip">Módulos <strong id="modsNow">0</strong>/<span id="modsGoal">0</span></span>
          </div>
        </div>

        <div class="card pad">
          <div class="title">Avisos</div>
          <ul id="avisos" style="margin:0;padding-left:18px"></ul>
        </div>

        <div class="card pad">
          <div class="title">Suas Conquistas</div>
          <div id="badges" class="flex" style="flex-wrap:wrap"></div>
        </div>
      </div>

      <div class="card pad" style="margin-top:16px">
        <div class="title">Missões</div>
        <div class="flex" style="flex-wrap:wrap; gap:10px">
          <div class="chip">📅 Dia: Refaça um quiz com 100% (+20 XP)</div>
          <div class="chip" id="chipSemana">🗓️ Semana: 0/3 módulos Técnicos (em progresso)</div>
          <button class="btn small" id="btnMissaoDia2">Fazer agora</button>
        </div>
        <div style="margin-top:8px">
          <div class="muted" style="font-size:12px;margin-bottom:4px">
            Progresso Semanal Técnico: <b id="weekCount">0/3</b>
          </div>
          <div class="weekbar"><div class="w" id="weekBar"></div></div>
        </div>
      </div>

      <div class="card pad" style="margin-top:16px">
        <div class="title" style="display:flex;align-items:center;gap:10px">Módulos <span class="badge missao" title="Missão automática ao atingir ≥80% no quiz">🔧 Missão (≥80% auto)</span></div>
        <div class="modules" id="modules"></div>
      </div>

      <div class="card pad" style="margin-top:16px">
        <div class="title">Seu Desempenho</div>
        <div id="perf"></div>
      </div>

      <div class="card pad" style="margin-top:16px">
        <div class="title">Ranking Local (XP)</div>
        <div id="rankList" class="flex" style="flex-direction:column; gap:6px"></div>
      </div>
    </section>

    <!-- OVERLAY: QUIZ & CONCLUSÃO -->
    <div class="overlay" id="ov">
      <div class="modal">
        <div class="flex" style="justify-content:space-between">
          <div class="title" id="ovTitle">Modal</div>
          <button class="btn ghost small" id="ovClose">Fechar</button>
        </div>
        <div id="ovBody"></div>
      </div>
    </div>

    <!-- TOAST -->
    <div class="toast" id="toast" role="status" aria-live="polite"></div>
  </div>

  <script>
  // ===== Constantes de níveis/módulos/quizzes =====
  const LEVELS = [
    { key:'Técnico', avatar:'👷‍♂️', xp:0,    reqXP:1000,  goalMods:12 , desc:'Domina o básico do LPS' },
    { key:'Analista', avatar:'👨‍💻', xp:1000, reqXP:3000,  goalMods:10 , desc:'Organiza cronogramas e indicadores' },
    { key:'Especialista', avatar:'👨‍🔧', xp:3000, reqXP:6000,  goalMods:10 , desc:'Integra Engenharia e Obra' },
    { key:'Gerente/Diretor', avatar:'👔', xp:6000, reqXP:10000, goalMods:6  , desc:'Lidera com Lean e Performance' },
    { key:'Visionário', avatar:'🔮', xp:10000, reqXP:Infinity, goalMods:0 },
  ];

  const MODULES = [
    // — Técnico (12)
    { id:1,  nivel:'Técnico', titulo:'EAP/AWP — Fundamentos', missao:true },
    { id:2,  nivel:'Técnico', titulo:'Cronograma — Básico', missao:true },
    { id:3,  nivel:'Técnico', titulo:'LPS — Lookahead', missao:false },
    { id:4,  nivel:'Técnico', titulo:'Folha Tarefa e WWP', missao:true },
    { id:5,  nivel:'Técnico', titulo:'PPC — Cálculo e Análise', missao:true },
    { id:6,  nivel:'Técnico', titulo:'Gestão Visual — Quadro de Restrições', missao:false },
    { id:7,  nivel:'Técnico', titulo:'Curva S — Introdução', missao:true },
    { id:8,  nivel:'Técnico', titulo:'Planejamento Semanal — Boas Práticas', missao:true },
    { id:9,  nivel:'Técnico', titulo:'Lookahead — Preparação de Frentes', missao:true },
    { id:10, nivel:'Técnico', titulo:'AWP — Pacotes CWP/EWP/MWP', missao:false },
    { id:11, nivel:'Técnico', titulo:'Cronograma — Predecessoras (FS/SS/FF/SF)', missao:true },
    { id:12, nivel:'Técnico', titulo:'Histograma de Recursos — Básico', missao:false },

    // — Analista (10)
    { id:13, nivel:'Analista', titulo:'Curva S & Rundown', missao:true },
    { id:14, nivel:'Analista', titulo:'Produtos Notáveis (Índices)', missao:false },
    { id:15, nivel:'Analista', titulo:'Nivelamento de Recursos', missao:true },
    { id:16, nivel:'Analista', titulo:'Cronograma Integrado (Eng/Supr/Obra)', missao:true },
    { id:17, nivel:'Analista', titulo:'PPC — Causas de Não Conclusão', missao:true },
    { id:18, nivel:'Analista', titulo:'KPIs de Planejamento', missao:false },
    { id:19, nivel:'Analista', titulo:'Planejamento de Curto × Médio × Longo Prazo', missao:false },
    { id:20, nivel:'Analista', titulo:'Análise de Caminho Crítico', missao:true },
    { id:21, nivel:'Analista', titulo:'Ritos Lean diários/semanais', missao:false },
    { id:22, nivel:'Analista', titulo:'Gestão de Mudanças no Cronograma', missao:true },

    // — Especialista (10)
    { id:23, nivel:'Especialista', titulo:'Integração Eng. × Planejamento', missao:true },
    { id:24, nivel:'Especialista', titulo:'AWP — Fluxos e Interfaces', missao:false },
    { id:25, nivel:'Especialista', titulo:'Planejamento de Comissionamento', missao:true },
    { id:26, nivel:'Especialista', titulo:'Simulação de Cenários (What-if)', missao:false },
    { id:27, nivel:'Especialista', titulo:'Curvas de Produção e Produtividade', missao:true },
    { id:28, nivel:'Especialista', titulo:'Estratégias de Sequenciamento', missao:false },
    { id:29, nivel:'Especialista', titulo:'Planejamento de Restrições Críticas', missao:true },
    { id:30, nivel:'Especialista', titulo:'Planejamento 4D — Introdução', missao:false },
    { id:31, nivel:'Especialista', titulo:'Integração Suprimentos × Obra', missao:true },
    { id:32, nivel:'Especialista', titulo:'Gestão de Riscos no Prazo', missao:true },

    // — Gerente/Diretor (6)
    { id:33, nivel:'Gerente/Diretor', titulo:'Liderança Lean no Campo', missao:false },
    { id:34, nivel:'Gerente/Diretor', titulo:'Governança de Planejamento', missao:true },
    { id:35, nivel:'Gerente/Diretor', titulo:'Portfólio e Priorização', missao:false },
    { id:36, nivel:'Gerente/Diretor', titulo:'Tomada de decisão com KPIs', missao:true },
    { id:37, nivel:'Gerente/Diretor', titulo:'Cultura de Melhoria Contínua', missao:false },
    { id:38, nivel:'Gerente/Diretor', titulo:'Contratos & Planejamento (EPC, Lump Sum)', missao:false },

    // — Visionário (2)
    { id:39, nivel:'Visionário', titulo:'BIM/IA/4D — Futuro', missao:false },
    { id:40, nivel:'Visionário', titulo:'Digital Twin & Analytics', missao:false },
  ];

  const QUIZZES = {
  // 1 — EAP/AWP — Fundamentos
  1: [
    { q: "Qual o objetivo principal da EAP (WBS)?", opts: ["Quebrar o escopo em partes gerenciáveis", "Definir o orçamento", "Emitir notas fiscais"], ok: 0, exp: "A EAP decompõe o escopo em pacotes para planejamento, custos e controle." },
    { q: "No AWP, qual pacote consolida disciplinas para construção?", opts: ["CWP", "EWP", "MWP"], ok: 0, exp: "CWP (Construction Work Package) organiza entregáveis prontos para a obra." },
    { q: "Qual benefício direto do AWP?", opts: ["Melhor preparação de frentes", "Mais retrabalho", "Menos integração"], ok: 0, exp: "AWP aumenta previsibilidade e preparação de frentes de trabalho." }
  ],

  // 2 — Cronograma — Básico
  2: [
    { q: "O que representa uma relação FS?", opts: ["Término–Início", "Início–Início", "Término–Término"], ok: 0, exp: "FS: a sucessora só inicia após o término da predecessora." },
    { q: "Qual ferramenta é mais usada para cronogramas em obra?", opts: ["MS Project", "PowerPoint", "AutoCAD"], ok: 0, exp: "MS Project é o padrão para cronogramas de obras." },
    { q: "O que é caminho crítico?", opts: ["Sequência que define o prazo do projeto", "A atividade mais cara", "A maior folga"], ok: 0, exp: "Atrasos no caminho crítico afetam diretamente o prazo final." },
    { q: "Qual boa prática ao atualizar o cronograma?", opts: ["Registrar % físico e datas reais", "Usar datas planejadas como reais", "Ignorar pendências"], ok: 0, exp: "Registre avanço real para análises confiáveis." }
  ],

  // 3 — LPS — Lookahead
  3: [
    { q: "Horizonte típico do Lookahead no LPS é de:", opts: ["3–6 semanas", "1 dia", "1 ano"], ok: 0, exp: "Lookahead atua no médio prazo, preparando frentes e removendo restrições." },
    { q: "Qual meta central do Lookahead?", opts: ["Remover restrições antes da execução", "Aumentar burocracia", "Focar só no financeiro"], ok: 0, exp: "Garantir tarefas prontas para executar com fluxo confiável." },
    { q: "Quem participa do Lookahead?", opts: ["Engenharia, planejamento e execução", "Somente o comprador", "Somente o cliente"], ok: 0, exp: "Colaboração multidisciplinar é essencial para antecipar impedimentos." }
  ],

  // 4 — Folha Tarefa e WWP
  4: [
    { q: "WWP significa:", opts: ["Weekly Work Planning", "Wide Work Pricing", "Workflow Pending"], ok: 0, exp: "Planejamento Semanal de Trabalho com compromissos claros." },
    { q: "Na folha-tarefa, é essencial registrar:", opts: ["Responsável, pré-requisitos e critério de pronto", "Somente o nome da tarefa", "Somente a disciplina"], ok: 0, exp: "Clareza de dono e condições de pronto garantem execução fluida." },
    { q: "Qual a saída semanal do WWP?", opts: ["Compromissos executáveis", "Relatório financeiro", "Lista de compras"], ok: 0, exp: "O WWP consolida compromissos factíveis para a semana." }
  ],

  // 5 — PPC — Cálculo e Análise
  5: [
    { q: "O PPC mede:", opts: ["Confiabilidade (compromissos cumpridos)", "Produtividade HH/quantidade", "Custo total"], ok: 0, exp: "É a % de compromissos cumpridos; indica aderência ao plano." },
    { q: "Periodicidade recomendada do PPC:", opts: ["Semanal", "Mensal", "Anual"], ok: 0, exp: "O PPC semanal fecha o ciclo do WWP e retroalimenta melhorias." },
    { q: "O que fazer com as não conclusões?", opts: ["Analisar causas e agir", "Ignorar", "Culpar pessoas"], ok: 0, exp: "Capturar causas raiz e agir é pilar do LPS." }
  ],

  // 6 — Gestão Visual — Quadro de Restrições
  6: [
    { q: "Exemplo de restrição típica:", opts: ["Projeto liberado", "Liberação de área pendente", "Equipe treinada"], ok: 1, exp: "Restrições de área, materiais e permissões bloqueiam a execução." },
    { q: "Boa prática no quadro de restrições:", opts: ["Dono e prazo para cada restrição", "Sem responsável", "Atualização anual"], ok: 0, exp: "Responsável e data ajudam a destravar o fluxo." },
    { q: "O quadro deve ser:", opts: ["Visível e atualizado", "Privado", "Apagado ao final do dia"], ok: 0, exp: "Transparência acelera a resolução." }
  ],

  // 7 — Curva S — Introdução
  7: [
    { q: "A Curva S compara:", opts: ["Planejado vs Real", "Custo vs Preço", "Área vs Volume"], ok: 0, exp: "Mostra aderência do avanço físico em relação ao plano." },
    { q: "Desvio negativo indica:", opts: ["Atraso no avanço real", "Adiantamento", "Erro de medição sempre"], ok: 0, exp: "Real < Planejado normalmente indica atraso." },
    { q: "Para Curva S confiável é preciso:", opts: ["Pesos físicos consistentes", "Somente horas apontadas", "Apenas quantidade de equipes"], ok: 0, exp: "Critérios de medição claros garantem comparabilidade." }
  ],

  // 8 — Planejamento Semanal — Boas Práticas
  8: [
    { q: "No planejamento semanal, compromissos devem ser:", opts: ["Executáveis (sem restrições)", "Ambiciosos mesmo com bloqueios", "Somente macro"], ok: 0, exp: "Compromissos prontos evitam quebras no PPC." },
    { q: "Reuniões semanais devem:", opts: ["Revisar PPC e causas", "Focar apenas em culpar", "Evitar dados"], ok: 0, exp: "Revisão estruturada fecha o PDCA semanal." },
    { q: "Checklist semanal inclui:", opts: ["Pré-requisitos, recursos e sequências", "Apenas RH", "Somente notas fiscais"], ok: 0, exp: "Preparação integral de frentes assegura fluxo." }
  ],

  // 9 — Lookahead — Preparação de Frentes
  9: [
    { q: "Exemplo de preparo de frente:", opts: ["Materiais, acesso e projeto liberados", "Equipe sem treinamento", "Área interditada"], ok: 0, exp: "Frentes prontas reduzem esperas e interrupções." },
    { q: "Lead time comum a considerar:", opts: ["Compra/entrega de materiais", "Aprovação de selfies", "Café"], ok: 0, exp: "Antecipar suprimentos evita bloqueios de execução." },
    { q: "Documento que auxilia no preparo:", opts: ["Lista de restrições", "Apólice de seguro", "Folha de pagamento"], ok: 0, exp: "Ajuda a rastrear e resolver impedimentos." }
  ],

  // 10 — AWP — Pacotes CWP/EWP/MWP
  10: [
    { q: "EWP está ligado a:", opts: ["Engenharia", "Suprimentos", "Construção"], ok: 0, exp: "Engineering Work Package é a base de informação técnica." },
    { q: "MWP refere-se a:", opts: ["Materiais (Material Work Package)", "Manutenção", "Medição"], ok: 0, exp: "Pacote de materiais organizado para suportar CWP." },
    { q: "CWP é consumido por:", opts: ["Execução na obra", "Financeiro", "Comercial"], ok: 0, exp: "Pacote pronto para ser executado em campo." }
  ],

  // 11 — Cronograma — Predecessoras (FS/SS/FF/SF)
  11: [
    { q: "SS significa:", opts: ["Start–Start", "Start–Finish", "Finish–Finish"], ok: 0, exp: "Início–Início: sucessora pode iniciar após a predecessora iniciar." },
    { q: "FF significa:", opts: ["Finish–Finish", "Finish–Start", "Start–Finish"], ok: 0, exp: "Término–Término: atividades devem terminar próximas." },
    { q: "SF é usada quando:", opts: ["Raro, término depende do início da outra", "Sempre", "Nunca"], ok: 0, exp: "Start–Finish é pouco comum, use com critério." }
  ],

  // 12 — Histograma de Recursos — Básico
  12: [
    { q: "O histograma mostra:", opts: ["Distribuição de recursos ao longo do tempo", "Somente custo total", "Lista de compras"], ok: 0, exp: "Ajuda a detectar picos e ociosidades." },
    { q: "Nivelar recursos significa:", opts: ["Suavizar picos respeitando restrições", "Adicionar horas extras sempre", "Remover predecessoras"], ok: 0, exp: "Nivelamento reduz variações e gargalos." },
    { q: "Dado necessário para histograma:", opts: ["Calendários e alocação por atividade", "Apenas lista de materiais", "Somente curva S"], ok: 0, exp: "Sem alocação confiável, o gráfico engana." }
  ],

  // 13 — Curva S & Rundown
  13: [
    { q: "Rundown semanal evidencia:", opts: ["Ganho de avanço por disciplina", "Meta de custo", "Quantidade de reuniões"], ok: 0, exp: "Mostra variação do avanço por período." },
    { q: "Divergência crônica real<plan indica:", opts: ["Atraso/baixa performance", "Adiantamento", "Erro de feriado"], ok: 0, exp: "Investigue causas: restrições, produtividade, sequência." },
    { q: "A integração com Curva S permite:", opts: ["Enxergar tendência e prever prazo", "Apenas horas extras", "Somente custos"], ok: 0, exp: "Tendência ajuda no replanejamento." }
  ],

  // 14 — Produtos Notáveis (Índices)
  14: [
    { q: "Índice técnico (ex.: hh/ton) serve para:", opts: ["Planejar e medir produtividade", "Definir cor da planilha", "Contar reuniões"], ok: 0, exp: "Permite estimar HH e comparar realizado vs previsto." },
    { q: "Se índice real > previsto:", opts: ["Produtividade pior que o planejado", "Produtividade melhor", "Sem impacto"], ok: 0, exp: "Mais HH por unidade indica menor produtividade." },
    { q: "Base de comparação deve ser:", opts: ["Mesma unidade e escopo", "Qualquer medida", "Apenas custo"], ok: 0, exp: "Comparabilidade é fundamental para decisões." }
  ],

  // 15 — Nivelamento de Recursos
  15: [
    { q: "Objetivo do nivelamento:", opts: ["Reduzir picos/ociosidade mantendo prazo", "Aumentar custo", "Eliminar predecessoras"], ok: 0, exp: "Suaviza carga mantendo sequência e marcos." },
    { q: "Ferramenta de apoio:", opts: ["Histograma", "Curva S", "RFI"], ok: 0, exp: "O histograma revela picos para ajuste." },
    { q: "Risco de nivelar sem critério:", opts: ["Criar novos conflitos no caminho crítico", "Melhorar tudo", "Zerar riscos"], ok: 0, exp: "Atenção à lógica e prazos contratuais." }
  ],

  // 16 — Cronograma Integrado (Eng/Supr/Obra)
  16: [
    { q: "Integração essencial:", opts: ["Engenharia–Suprimentos–Obra", "RH–Jurídico–Marketing", "AP–TI–Fiscal"], ok: 0, exp: "Fluxos integrados reduzem variações e esperas." },
    { q: "Marco de suprimentos crítico:", opts: ["Data de entrega liberada para obra", "Assinatura de e-mail", "Férias da equipe"], ok: 0, exp: "Entrega libera frentes e evita paradas." },
    { q: "Sem integração, ocorre:", opts: ["Bloqueio de frentes e retrabalho", "Maior previsibilidade", "Aumento do PPC"], ok: 0, exp: "Silagem entre áreas derruba performance." }
  ],

  // 17 — PPC — Causas de Não Conclusão
  17: [
    { q: "Exemplo de causa raiz válida:", opts: ["Projeto desatualizado", "‘Falha da equipe’ genérica", "‘Choveu’ sem plano B"], ok: 0, exp: "Causas específicas viabilizam contramedidas." },
    { q: "Próximo passo após identificar causa:", opts: ["Definir ação e dono", "Arquivar", "Repetir o erro"], ok: 0, exp: "Plano de ação e follow-up fecham o ciclo." },
    { q: "Tendência de causas ajuda a:", opts: ["Priorização de melhorias", "Aumentar burocracia", "Esconder problemas"], ok: 0, exp: "Foco nas maiores alavancas de melhoria." }
  ],

  // 18 — KPIs de Planejamento
  18: [
    { q: "KPI de confiabilidade:", opts: ["PPC", "Custo indireto", "HH por mês"], ok: 0, exp: "PPC mede aderência ao plano semanal." },
    { q: "KPI de prazo macro:", opts: ["% Físico planejado vs real", "Quantidade de e-mails", "Horas de reunião"], ok: 0, exp: "Mostra avanço acumulado vs meta." },
    { q: "Boa prática para KPIs:", opts: ["Definições claras e periodicidade", "Trocar a cada dia", "Sem dono"], ok: 0, exp: "Governança garante qualidade do indicador." }
  ],

  // 19 — Planejamento de Curto × Médio × Longo Prazo
  19: [
    { q: "Lookahead atua no:", opts: ["Médio prazo", "Curto prazo somente", "Longo prazo"], ok: 0, exp: "É a ponte entre plano mestre e semanal." },
    { q: "Curto prazo é operacionalizado por:", opts: ["WWP e compromissos executáveis", "EAP nível 0", "Contrato"], ok: 0, exp: "Onde o plano vira ação concreta." },
    { q: "Alinhamento vertical significa:", opts: ["Coerência entre planos em todos os níveis", "Somente o último nível", "Somente custo"], ok: 0, exp: "Evita contradições entre horizontes." }
  ],

  // 20 — Análise de Caminho Crítico
  20: [
    { q: "Float zero indica:", opts: ["Atividade crítica", "Atividade com folga alta", "Sem predecessoras"], ok: 0, exp: "Atrasos aqui impactam o prazo final." },
    { q: "Replanejar caminho crítico exige:", opts: ["Atenção a lógicas e recursos", "Ignorar restrições", "Só aumentar equipe"], ok: 0, exp: "Ajustes devem manter coerência temporal." },
    { q: "Ferramenta para visualizar caminho crítico:", opts: ["Gantt/Relatórios críticos", "Planilha de custos", "E-mail"], ok: 0, exp: "Use visualização dedicada para decisões." }
  ],

  // 21 — Ritos Lean diários/semanais
  21: [
    { q: "Um rito diário eficaz é:", opts: ["Reunião curta, foco em impedimentos", "Reunião longa sem pauta", "Discussão de temas aleatórios"], ok: 0, exp: "Stand-up foca em andamento e bloqueios." },
    { q: "No semanal, revisar:", opts: ["PPC e causas, plano da semana", "Somente financeiros", "Somente segurança"], ok: 0, exp: "Integra fluxo e melhoria contínua." },
    { q: "Gestão visual ajuda a:", opts: ["Dar transparência ao status", "Ocultar problemas", "Reduzir comunicação"], ok: 0, exp: "Transparência acelera correções." }
  ],

  // 22 — Gestão de Mudanças no Cronograma
  22: [
    { q: "Mudanças devem ser:", opts: ["Controladas por processo formal", "Aplicadas sem registro", "Feitas por qualquer usuário"], ok: 0, exp: "Controle evita perda de baseline e rastreabilidade." },
    { q: "Baseline serve para:", opts: ["Comparar plano original vs atual", "Calcular custo do café", "Gerar crachá"], ok: 0, exp: "Permite medir impacto de mudanças." },
    { q: "Solicitação de mudança inclui:", opts: ["Justificativa, impacto e aprovação", "Apenas título", "Nada"], ok: 0, exp: "Documentação clara dá governança ao prazo." }
  ],

  // 23 — Integração Eng. × Planejamento
  23: [
    { q: "Integração evita:", opts: ["Retrabalho por projeto desatualizado", "Comunicação", "Planejamento"], ok: 0, exp: "Sincronia de versões e critérios reduz falhas." },
    { q: "Entregáveis-alvo:", opts: ["Modelos, listas, marcos alinhados", "Somente fotos", "Somente diagramas elétricos"], ok: 0, exp: "Pacotes consistentes suportam a obra." },
    { q: "Reuniões de interface devem:", opts: ["Resolver pendências e datas", "Ocultar problemas", "Apenas status sem decisão"], ok: 0, exp: "Interface clara libera execução." }
  ],

  // 24 — AWP — Fluxos e Interfaces
  24: [
    { q: "Sequência típica AWP:", opts: ["EWP → MWP → CWP", "CWP → MWP → EWP", "MWP → EWP → CWP"], ok: 0, exp: "Engenharia alimenta materiais, que preparam construção." },
    { q: "Gate de liberação CWP:", opts: ["EWP/MWP completos", "Apenas compra aberta", "Apenas cronograma geral"], ok: 0, exp: "CWP depende de info técnica e material disponível." },
    { q: "Benefício de interface bem definida:", opts: ["Menos bloqueios de frente", "Mais reuniões", "Mais lead time"], ok: 0, exp: "Interfaces claras liberam execução contínua." }
  ],

  // 25 — Planejamento de Comissionamento
  25: [
    { q: "Comissionamento exige:", opts: ["Checklists e pré-testes por sistema", "Somente produção", "Apenas civil"], ok: 0, exp: "Estruturar por sistemas e laudos evita retrabalho." },
    { q: "Marco comum:", opts: ["Pronto para start-up (RFSU)", "Entrega do café", "Foto da obra"], ok: 0, exp: "RFSU é marco de prontidão operacional." },
    { q: "Predecessoras-chave:", opts: ["Montagem, instrumentação e energia", "Marketing", "Jurídico"], ok: 0, exp: "Integração multidisciplinar é vital." }
  ],

  // 26 — Simulação de Cenários (What-if)
  26: [
    { q: "Cenários suportam:", opts: ["Decisão sobre prazo/recurso", "Somente visual", "Memes"], ok: 0, exp: "Avaliam impacto de alternativas no prazo." },
    { q: "Boa prática:", opts: ["Alterar cópias do cronograma (sandbox)", "Editar baseline direto", "Não documentar"], ok: 0, exp: "Sandbox evita corromper o plano vigente." },
    { q: "Exemplo de variável:", opts: ["Turnos e alocações", "Cor do EPI", "Wallpaper"], ok: 0, exp: "Parâmetros de recurso impactam caminho crítico." }
  ],

  // 27 — Curvas de Produção e Produtividade
  27: [
    { q: "Produtividade é:", opts: ["Quantidade por HH", "Custo por mês", "Equipe por área"], ok: 0, exp: "Base para comparação realizada vs planejada." },
    { q: "Melhorar produtividade requer:", opts: ["Remover perdas e balancear fluxo", "Mais burocracia", "Ignorar dados"], ok: 0, exp: "Ataque a gargalos e esperas." },
    { q: "Curva de produção exibe:", opts: ["Output por período", "Somente custo", "Lista de equipes"], ok: 0, exp: "Permite detectar quedas de ritmo." }
  ],

  // 28 — Estratégias de Sequenciamento
  28: [
    { q: "Sequenciamento busca:", opts: ["Fluxo contínuo e segurança", "Mudar todo dia", "Trabalhos sobrepostos sem análise"], ok: 0, exp: "Sequência coerente reduz conflitos e riscos." },
    { q: "Exemplo de restrição de sequência:", opts: ["Içamento com janela dedicada", "Almoço", "Foto oficial"], ok: 0, exp: "Operações críticas exigem janelas e isolamentos." },
    { q: "Ferramenta de apoio:", opts: ["Mapeamento de interfaces", "Figuras aleatórias", "Emoji"], ok: 0, exp: "Interfaces claras reduzem retrabalho." }
  ],

  // 29 — Planejamento de Restrições Críticas
  29: [
    { q: "Restrições críticas devem ser:", opts: ["Rastreáveis com dono e prazo", "Genéricas", "Sem evidência"], ok: 0, exp: "Controle ativo destrava frentes." },
    { q: "Exemplo:", opts: ["NRs e permissões de trabalho", "Wallpaper do PC", "Crachá novo"], ok: 0, exp: "Permissões podem travar início de tarefas." },
    { q: "Revisão recomendada:", opts: ["Diária/semana com status", "Semanal apenas financeiro", "Mensal"], ok: 0, exp: "Ritmo evita surpresas na execução." }
  ],

  // 30 — Planejamento 4D — Introdução
  30: [
    { q: "Planejamento 4D integra:", opts: ["Modelo 3D + tempo", "Custo + curva S", "RFI + e-mail"], ok: 0, exp: "Vincula cronograma ao modelo para simular sequência." },
    { q: "Benefício do 4D:", opts: ["Visualizar interferências e conflitos", "Somente estética", "Trocar o cronograma"], ok: 0, exp: "Antecipação de conflitos melhora decisões." },
    { q: "Pré-requisito básico:", opts: ["Modelo atualizado e WBS coerente", "Somente fotos", "Somente PPC"], ok: 0, exp: "Coerência entre EAP e objetos 3D é chave." }
  ],

  // 31 — Integração Suprimentos × Obra
  31: [
    { q: "Marcos de suprimentos impactam:", opts: ["Liberação de frentes", "Somente folha de pagamento", "Nada"], ok: 0, exp: "Entregas liberam execução e evitam esperas." },
    { q: "Ferramenta de controle:", opts: ["Lista de materiais com status", "Meme semanal", "Quadro de fotos"], ok: 0, exp: "Rastreamento evita rupturas." },
    { q: "Ação para atrasos:", opts: ["Replanejar sequência e mitigar", "Ignorar", "Cancelar atividades"], ok: 0, exp: "Ajustes protegem o caminho crítico." }
  ],

  // 32 — Gestão de Riscos no Prazo
  32: [
    { q: "Risco de prazo é:", opts: ["Evento que pode afetar cronograma", "Custo fixo", "Almoço"], ok: 0, exp: "Pode atrasar marcos e entrega." },
    { q: "Resposta a risco inclui:", opts: ["Mitigar, transferir, aceitar", "Postar no grupo", "Ignorar"], ok: 0, exp: "Estratégias adequadas reduzem impacto." },
    { q: "Registro deve conter:", opts: ["Probabilidade, impacto, dono", "Apenas título", "Somente data"], ok: 0, exp: "Governança garante acompanhamento." }
  ],

  // 33 — Liderança Lean no Campo
  33: [
    { q: "Liderança Lean foca em:", opts: ["Fluxo, pessoas e melhoria contínua", "Somente custo", "Somente comando–controle"], ok: 0, exp: "Equilíbrio entre processo e pessoas." },
    { q: "Prática essencial:", opts: ["Gemba (ir ao local de valor)", "Só relatórios", "Somente e-mail"], ok: 0, exp: "Ver o trabalho real revela perdas." },
    { q: "Comportamento esperado:", opts: ["Transparência e resolução de problemas", "Culpar", "Esconder falhas"], ok: 0, exp: "Cultura de aprendizado acelera resultados." }
  ],

  // 34 — Governança de Planejamento
  34: [
    { q: "Governança define:", opts: ["Papéis, ritos e padrões", "Somente badges", "Apenas emojis"], ok: 0, exp: "Estrutura dá consistência à execução." },
    { q: "Documento-chave:", opts: ["Critérios de medição", "Memes", "Lista telefônica"], ok: 0, exp: "Critérios sustentam Curva S e relatórios." },
    { q: "Ciclo de revisão:", opts: ["Semanal/mensal conforme nível", "Anual", "Nunca"], ok: 0, exp: "Ritmo garante atualização do plano." }
  ],

  // 35 — Portfólio e Priorização
  35: [
    { q: "Priorização alinha:", opts: ["Capacidade × Valor", "Somente custo", "Somente vontade"], ok: 0, exp: "Evita sobrecarga e dispersão." },
    { q: "Ferramenta de visão:", opts: ["Roadmap/heatmap", "Wallpapers", "Sticker"], ok: 0, exp: "Visual ajuda a comunicar prioridades." },
    { q: "Risco da falta de priorização:", opts: ["Multitarefa crônica e atrasos", "Mais foco", "Menos lead time"], ok: 0, exp: "Dispersão derruba produtividade." }
  ],

  // 36 — Tomada de decisão com KPIs
  36: [
    { q: "Decisão orientada por:", opts: ["Indicadores e evidências", "Opinião isolada", "Achismo"], ok: 0, exp: "KPIs reduzem incerteza e viés." },
    { q: "Exemplo de ação com KPI:", opts: ["Replanejar equipes após queda de produtividade", "Ignorar desvio", "Esperar"], ok: 0, exp: "Ação rápida evita efeito cascata." },
    { q: "Cuidados com KPI:", opts: ["Definição, qualidade de dados, periodicidade", "Cor do gráfico", "Quantidade de slides"], ok: 0, exp: "Sem qualidade, indicador engana." }
  ],

  // 37 — Cultura de Melhoria Contínua
  37: [
    { q: "Kaizen implica:", opts: ["Pequenas melhorias frequentes", "Grandes saltos raros", "Pausar operações"], ok: 0, exp: "Incremental e constante." },
    { q: "Ferramenta de base:", opts: ["PDCA", "GIF", "Sticker"], ok: 0, exp: "Plan-Do-Check-Act sustenta evolução." },
    { q: "Anti-padrão:", opts: ["Culpar pessoas", "Compartilhar aprendizados", "Visualizar problemas"], ok: 0, exp: "Culpa inibe aprendizado." }
  ],

  // 38 — Contratos & Planejamento (EPC, Lump Sum)
  38: [
    { q: "Em EPC, o contratista é responsável por:", opts: ["Engenharia, suprimentos e construção", "Somente engenharia", "Somente operação"], ok: 0, exp: "Entrega completa 'turn-key'." },
    { q: "Em Lump Sum, risco de:", opts: ["Custos excedentes ao contratado", "Prazo sempre menor", "Sem riscos"], ok: 0, exp: "Preço fechado transfere risco de custo." },
    { q: "Planejamento deve:", opts: ["Alinhar marcos contratuais ao cronograma", "Ignorar contrato", "Evitar baseline"], ok: 0, exp: "Coerência contratual evita disputas." }
  ],

  // 39 — BIM/IA/4D — Futuro
  39: [
    { q: "BIM 4D adiciona:", opts: ["Tempo ao 3D", "Custo", "Escopo"], ok: 0, exp: "Sincroniza execução com modelo." },
    { q: "IA pode apoiar:", opts: ["Previsão de riscos e produtividade", "Somente estética", "Nada"], ok: 0, exp: "Modelos aprendem padrões de atraso." },
    { q: "Pré-requisito:", opts: ["Dados confiáveis e padronizados", "Apenas vontade", "Nenhum"], ok: 0, exp: "Sem dados bons, IA não agrega." }
  ],

  // 40 — Digital Twin & Analytics
  40: [
    { q: "Digital Twin é:", opts: ["Gêmeo digital do ativo em operação", "Backup do cronograma", "Planilha de horas"], ok: 0, exp: "Replica comportamento do ativo em tempo real." },
    { q: "Valor prático:", opts: ["Simulações e decisões operacionais", "Mais burocracia", "Somente marketing"], ok: 0, exp: "Suporta decisões de manutenção e performance." },
    { q: "Integrações típicas:", opts: ["Sensores/SCADA + modelos", "Somente e-mails", "Memes"], ok: 0, exp: "Dados de campo alimentam o gêmeo digital." }
  ]
};

  // ===== Quizzes base (mantém fallback) =====
  function fallbackQuiz(mod){
    const t = mod.titulo;
    return [
      { q:`No módulo "${t}", qual é o objetivo principal?`, opts:["Aplicar o conceito no campo","Aumentar burocracia","Ignorar restrições"], ok:0, exp:`O foco de ${t} é gerar valor prático na execução.` },
      { q:`Em ${t}, o que NÃO é boa prática?`, opts:["Revisar premissas","Medir resultados","Tomar decisões sem dados"], ok:2, exp:`Decisões devem ser baseadas em dados e aprendizado.` },
      { q:`Como validar sucesso em ${t}?`, opts:["KPIs/indicadores claros","Impressões pessoais","Mais reuniões"], ok:0, exp:`Defina indicadores e metas para verificar resultados.` },
      { q:`Quem deve estar envolvido em ${t}?`, opts:["Time certo, donos e responsáveis","Somente diretoria","Somente estagiários"], ok:0, exp:`Envolvimento de quem executa e decide acelera resultados.` },
      { q:`Qual ação alinha ${t} ao Lean?`, opts:["Remover impedimentos","Postergar decisões","Esconder problemas"], ok:0, exp:`Lean prioriza remoção de impedimentos e transparência.` }
    ];
  }
  function getQuiz(modId){
    if (QUIZZES[modId]) return QUIZZES[modId];
    const mod = MODULES.find(m=>m.id===modId) || {titulo:`Módulo ${modId}`};
    return fallbackQuiz(mod);
  }

  // ===== Estado / Persistência =====
  const LS_KEY = 'duoplanning.v3.state';
  const SOUND_KEY = 'duoplanning.soundEnabled';
  const RANK_KEY = 'duoplanning.v3.rank';
  const todayISO = ()=> new Date().toISOString().slice(0,10);

  const defaultState = {
    user:{ name:'', avatar:'👷‍♂️' },
    xp:0,
    levelKey:'Técnico',
    viewOnlyCurrent:true,
    streak:{ last:null, days:0 },
    energy:{ max:3, cur:3, regenEveryMin:20, nextRegenAt:null, cooldownUntil:null },
    weekly:{ weekKey:null, tecnicoDone:0, rewarded:false },
    badges:{ kaizen:false, mestrePPC:false },
    perfectStreak:0,
    history:[],
    modules: Object.fromEntries(MODULES.map(m=>[m.id,{ acertos:0,total:5,concluido:false,missaoDone:false }]))
  };

  let state = loadState();
  ensureState();

  function loadState(){ try{ const raw = localStorage.getItem(LS_KEY); return raw? JSON.parse(raw): structuredClone(defaultState)}catch{ return structuredClone(defaultState) } }
  function save(){ localStorage.setItem(LS_KEY, JSON.stringify(state)) }
  function $(sel){ return document.querySelector(sel) }
  function toast(msg){ const el=$('#toast'); el.textContent=msg; el.classList.add('show'); setTimeout(()=>el.classList.remove('show'),1800) }
  function openOv(title, node){ $('#ovTitle').textContent = title; const body=$('#ovBody'); body.innerHTML=''; body.appendChild(node); $('#ov').classList.add('show') }
  function closeOv(){ $('#ov').classList.remove('show') }

  function ensureState(){
    state.badges = state.badges || { kaizen:false, mestrePPC:false };
    state.perfectStreak = state.perfectStreak ?? 0;
    state.energy = state.energy || { max:3, cur:3, regenEveryMin:20, nextRegenAt:null, cooldownUntil:null };
    state.energy.max ??= 3;
    state.energy.cur ??= state.energy.max;
    state.energy.regenEveryMin ??= 20;
    state.weekly = state.weekly || { weekKey:null, tecnicoDone:0, rewarded:false };
    state.history = state.history || [];
    state.viewOnlyCurrent = state.viewOnlyCurrent ?? true;
  }

  // ===== Som & Confetti =====
  let soundEnabled = true;
  function applySoundToggleUI(){ const btn = document.getElementById('toggleSound'); if (!btn) return; btn.textContent = soundEnabled ? '🔊 Som: Ativado' : '🔇 Som: Desativado' }
  function launchConfetti(){
    if (soundEnabled){ const win = document.getElementById('audioWin'); if (win){ try{ win.currentTime = 0; win.play().catch(()=>{}) }catch{} } }
    const duration = 900; const end = Date.now() + duration; const colors = ['#3aa0ff','#27c081','#ffb020','#ff5d5d','#ffffff'];
    (function frame(){ confetti({ particleCount: 6, angle: 60, spread: 55, origin: { x: 0 }, colors }); confetti({ particleCount: 6, angle: 120, spread: 55, origin: { x: 1 }, colors }); if (Date.now() < end) requestAnimationFrame(frame) })()
  }

  // ===== Nível & Gate por Módulos =====
  function getLevelObj(key){ return LEVELS.find(l=>l.key===key) }
  function currentLevel(){ return getLevelObj(state.levelKey) }
  function nextLevel(){ const idx=LEVELS.findIndex(l=>l.key===state.levelKey); return LEVELS[idx+1]||LEVELS[idx] }
  function modulesCompletedAt(levelKey){ const mods=MODULES.filter(m=>m.nivel===levelKey); return mods.filter(m=>state.modules[m.id]?.concluido).length }
  function canAdvance(){ const cur=currentLevel(); const haveMods=modulesCompletedAt(cur.key)>=(cur.goalMods||0); return haveMods && (LEVELS.findIndex(l=>l.key===cur.key) < LEVELS.length-1); }
  function tryAdvance(){ if(canAdvance()){ state.levelKey=nextLevel().key; save(); launchConfetti(); const box=document.createElement('div'); box.innerHTML=`<div class="card pad trophy" style="text-align:center"><div style="font-size:46px">${getLevelObj(state.levelKey).avatar}</div><h3 style="margin:.2rem 0">🏅 Nova trilha liberada!</h3><p>Você atingiu <strong>${state.levelKey}</strong> liberando novos módulos.</p><div class="flex" style="justify-content:center;margin-top:12px"><span class="badge xp">XP: ${state.xp}</span></div></div>`; openOv('Parabéns 🎉', box) } }

  // ===== Streak & Missão do Dia (corrigida: variação e avanço) =====
  function updateStreakOnLogin(){ const today=todayISO(); const last=state.streak.last; if(!last){ state.streak.last=today; state.streak.days=1 } else { const d1=new Date(last), d2=new Date(today); const diff=Math.round((d2-d1)/(1000*60*60*24)); if(diff===1){ state.streak.days+=1; state.streak.last=today } else if(diff>1){ state.streak.days=1; state.streak.last=today } } save() }
  function streakBonusXP(){ const d=state.streak.days; if(d>=7) return 25; if(d>=5) return 20; if(d>=3) return 15; return 10 }

  function runDailyMission(){
    const box=document.createElement('div');
    const info=document.createElement('div');
    info.className='muted';
    info.textContent='Responda 3 perguntas rápidas para garantir seu bônus de streak hoje.';

    const quiz=document.createElement('div');
    quiz.className='quiz';

    const pool=Object.values(QUIZZES).flat();
    const perguntas=[]; const usados=new Set();

    if(!pool.length){
      perguntas.push(
        { q:'Qual é o objetivo do LPS?', opts:['Fluxo confiável','Burocracia','Atrasar tarefas'], ok:0 },
        { q:'O que o PPC mede?', opts:['Confiabilidade do planejamento','Horas extras','Custos diretos'], ok:0 },
        { q:'Quem participa do Pull Planning?', opts:['Equipe multidisciplinar','Somente o gerente','Somente o cliente'], ok:0 }
      );
    } else {
      const n = Math.min(3, pool.length);
      while(perguntas.length < n){
        const r = Math.floor(Math.random()*pool.length);
        if(!usados.has(r)){
          usados.add(r);
          perguntas.push(pool[r]);
        }
      }
      // completa com fallback se o pool tiver <3
      while(perguntas.length < 3){
        perguntas.push({ q:'Qual é o objetivo do LPS?', opts:['Fluxo confiável','Burocracia','Atrasar tarefas'], ok:0 });
      }
    }

    let i=0, correct=0;

    function drawQuestion(){
      quiz.innerHTML='';
      const q=perguntas[i];
      const title=document.createElement('div');
      title.className='title';
      title.textContent=`(${i+1}/3) ${q.q}`;
      quiz.appendChild(title);

      let answered=false;

      // Feedback acima do botão
      const feedback=document.createElement('div');
      feedback.className='exp';
      feedback.style.display='none';
      feedback.style.marginTop='6px';
      feedback.style.marginBottom='4px';
      quiz.appendChild(feedback);

      const next=document.createElement('button');
      next.className='btn small';
      next.disabled=true;
      next.style.marginTop='8px';
      next.textContent=(i<2)?'Próxima ▶️':'Concluir ✅';
      next.onclick=()=>{ if(!answered) return; i++; (i<3) ? drawQuestion() : finishMission(); };

      (q.opts||[]).forEach((opt,idx)=>{
        const li=document.createElement('div');
        li.className='option';
        li.innerHTML=`<span class="badge">${String.fromCharCode(65+idx)}</span> <div>${opt}</div>`;
        li.onclick=()=>{
          if(answered) return;
          answered=true;
          if(idx===(q.ok||0)){
            li.classList.add('correct');
            correct++;
            launchConfetti();
            feedback.textContent='✅ Boa! Resposta correta.';
            feedback.style.display='block';
          } else {
            li.classList.add('wrong');
            feedback.textContent=`❌ ${q.exp || 'Resposta incorreta.'}`;
            feedback.style.display='block';
          }
          next.disabled=false;
        };
        quiz.appendChild(li);
      });

      quiz.appendChild(next);
    }

    function finishMission(){
      // ✅ Registrar no histórico
      try{
        state.history = Array.isArray(state.history) ? state.history : [];
        state.history.push({ type:'missao', ac: correct, total: 3, pct: Math.round((correct/3)*100), date: todayISO() });
        // Limite opcional de histórico para evitar crescimento infinito
        if (state.history.length > 500) state.history = state.history.slice(-500);
      }catch{}

      const baseXP=15+correct*5;
      const bonus=streakBonusXP();
      const total=baseXP+bonus;
      state.xp+=total;
      giveEnergy(1);
      toast('⚡ +1 energia pela Missão do Dia!');
      save();
      renderSafe();
      const done=document.createElement('div');
      done.innerHTML=`<div class="card pad" style="text-align:center"><h3>Missão do Dia concluída ✅</h3><p>Acertos: <strong>${correct}/3</strong></p><p>XP: Base ${baseXP} + Streak ${bonus} = <strong>${total}</strong></p></div>`;
      openOv('Missão do Dia', done);
      updateRank();
      renderRank();
    }

    box.append(info,quiz);
    openOv('Missão do Dia 🔥', box);
    drawQuestion();
  }

  // ===== Energia ===== =====
  function renderEnergy(){ const box = document.getElementById('energyCaps'); if (!box) return; box.innerHTML = ''; for (let i=0;i<state.energy.max;i++){ const cap = document.createElement('div'); cap.className = 'cap' + (i < state.energy.cur ? ' on':'' ); box.appendChild(cap) } }
  function spendEnergy(n=1){ state.energy.cur = Math.max(0, state.energy.cur - n); save(); renderEnergy(); scheduleRegenIfNeeded() }
  function giveEnergy(n=1){ state.energy.cur = Math.min(state.energy.max, state.energy.cur + n); save(); renderEnergy() }
  function scheduleRegenIfNeeded(){ if (state.energy.cur >= state.energy.max){ state.energy.nextRegenAt = null; save(); return; } if (!state.energy.nextRegenAt){ const t = new Date(Date.now() + state.energy.regenEveryMin*60*1000); state.energy.nextRegenAt = t.toISOString(); save(); } }
  function energyTick(){ const cdISO = state.energy.cooldownUntil; if (cdISO){ const now = Date.now(); if (new Date(cdISO).getTime() > now) return; state.energy.cooldownUntil = null; save(); } if (!state.energy.nextRegenAt) return; if (Date.now() >= new Date(state.energy.nextRegenAt).getTime()){ giveEnergy(1); if (state.energy.cur < state.energy.max){ const t = new Date(Date.now() + state.energy.regenEveryMin*60*1000); state.energy.nextRegenAt = t.toISOString(); save(); } else { state.energy.nextRegenAt = null; save(); } } }
  setInterval(energyTick, 1000*30);

  // ===== Render =====
  function renderPerformance(){ const box = document.getElementById('perf'); if (!box) return; const h = state.history || []; if (!h.length) { box.innerHTML = '<div class="muted">Sem histórico ainda — faça um quiz para ver seus números.</div>'; return; } const total = h.length; const acMedia = Math.round(h.reduce((s,r)=>s+(r.ac/r.total),0)/total*100); const xp = state.xp; box.innerHTML = `<div class="flex" style="flex-wrap:wrap;gap:10px"><span class="chip">Quizzes: <strong>${total}</strong></span><span class="chip">Acerto médio: <strong>${acMedia}%</strong></span><span class="chip">XP total: <strong>${xp}</strong></span></div>` }
  function renderHeader(){ const lvl=currentLevel(); if (!lvl) return; document.getElementById('tagNivel').textContent=`Nível: ${lvl.avatar} ${state.levelKey}`; document.getElementById('tagXP').textContent=`XP: ${state.xp}`; document.getElementById('tagStreak').textContent=`🔥 Streak: ${state.streak.days}` }
  function renderProgress(){ const cur=currentLevel(); const mods=modulesCompletedAt(cur.key); const goal=cur.goalMods||0; const pct=Math.min(100, Math.round(goal? (mods/goal)*100:0)); document.getElementById('xpGoal').textContent='—'; document.getElementById('xpNow').textContent=state.xp; document.getElementById('modsNow').textContent=mods; document.getElementById('modsGoal').textContent=goal; document.getElementById('xpBar').style.width=pct+'%'; document.getElementById('badgeTotalXP').textContent=`+${state.xp} XP`; document.getElementById('badgeStreakDia').textContent=`🔥 ${state.streak.days} dias` }
  function renderAvisos(){ const ul = document.getElementById('avisos'); if (!ul) return; ul.innerHTML = ''; const cur = currentLevel(); const mods = modulesCompletedAt(cur.key); if (mods < (cur.goalMods || 0)){ const li = document.createElement('li'); li.textContent = `Faltam ${Math.max(0, (cur.goalMods||0) - mods)} módulos para avançar.`; ul.appendChild(li); } const chipSemana = document.getElementById('chipSemana'); if (chipSemana){ chipSemana.textContent = `🗓️ Semana: ${(state.weekly?.tecnicoDone||0)}/3 módulos Técnicos (${state.weekly?.rewarded ? 'Concluída ✅ +100 XP' : 'em progresso'})`; } if (!ul.children.length){ const li = document.createElement('li'); li.textContent = 'Tudo certo! Você pode avançar assim que cumprir os requisitos de módulos.'; ul.appendChild(li); } }
  function renderBadges(){ const wrap = document.getElementById('badges'); if (!wrap) return; wrap.innerHTML = ''; const milestones = [ {xp:100,label:'Novato do LPS'},{xp:500,label:'Eng. de Campo Jr'},{xp:1000,label:'Analista Destravado'},{xp:3000,label:'Especialista em Fluxo'},{xp:6000,label:'Líder Lean'},{xp:10000,label:'Visionário 5D'} ]; milestones.forEach(ms=>{ const owned=state.xp>=ms.xp; const b=document.createElement('span'); b.className='badge xp'; b.style.opacity=owned?1:.4; b.textContent= owned?`🏅 ${ms.label}`:`🔒 ${ms.label}`; wrap.appendChild(b) }); try{ if (state.streak && state.streak.days >= 10) { state.badges.kaizen = true; } if ((state.perfectStreak || 0) >= 5) { state.badges.mestrePPC = true; } if (state.badges.kaizen){ const b=document.createElement('span'); b.className='badge xp'; b.textContent = '🛠️ Engenheiro Kaizen (10d streak)'; wrap.appendChild(b); } if (state.badges.mestrePPC){ const b=document.createElement('span'); b.className='badge xp'; b.textContent = '📈 Mestre do PPC (5× 100%)'; wrap.appendChild(b); } } catch {} }
  function renderModules(){ const box=document.getElementById('modules'); if (!box) return; box.innerHTML=''; const order=LEVELS.map(l=>l.key); const curIdx=order.indexOf(state.levelKey); MODULES.forEach(m=>{ const idx=order.indexOf(m.nivel); const locked=idx>curIdx; const st=state.modules[m.id]; const pct=Math.round((st.acertos/st.total)*100); const card=document.createElement('div'); card.className='mod card '+(locked?'locked':''); card.innerHTML=`<div class="seal">${locked?'🔒':''}</div><h4>${m.titulo}</h4><div class="meta"><span class="chip">Trilha: ${m.nivel}</span>${st.concluido?`<span class="chip">✔ 100% aprovado</span>`:`<span class="chip">${st.acertos}/${st.total} corretas (${pct}%)</span>`}${m.missao?`<span class="badge missao">🔧 Missão (≥80% auto)</span>`:''}</div><div class="footer"><button class="btn small" ${locked?'disabled':''} data-act="quiz" data-id="${m.id}">Fazer Quiz</button></div>`; box.appendChild(card) }); box.querySelectorAll('button[data-act="quiz"]').forEach(b=>b.onclick=()=>openQuiz(+b.dataset.id)) }
  function renderWeeklyBarAndCount(){ const done = state.weekly?.tecnicoDone || 0; const need = 3; const pct = Math.min(100, Math.round(done/need*100)); const bar = document.getElementById('weekBar'); const txt = document.getElementById('weekCount'); if (bar) bar.style.width = pct + '%'; if (txt) txt.textContent = `${done}/${need}` }

  function render(){ renderHeader(); renderProgress(); renderAvisos(); renderBadges(); renderModules(); tryAdvance(); renderPerformance(); renderEnergy(); renderWeeklyBarAndCount(); renderRank(); }
  function renderSafe(){ try{ render(); } catch(e){ console.error(e); toast('Houve um erro ao renderizar. Tente recarregar.'); } }

  // ===== Ranking Local =====
  function loadRank(){ try{ return JSON.parse(localStorage.getItem(RANK_KEY)||'[]') }catch{ return [] } }
  function saveRank(arr){ localStorage.setItem(RANK_KEY, JSON.stringify(arr)) }
  function updateRank(){ if (!state.user?.name) return; const rank = loadRank(); const i = rank.findIndex(r=>r.name===state.user.name); if (i>=0) rank[i].xp = Math.max(rank[i].xp, state.xp); else rank.push({name:state.user.name, xp:state.xp}); rank.sort((a,b)=>b.xp-a.xp); saveRank(rank) }
  function renderRank(){ const wrap = document.getElementById('rankList'); if (!wrap) return; const rank = loadRank().slice(0,10); if (!rank.length){ wrap.innerHTML = '<div class="muted">Sem dados ainda.</div>'; return; } wrap.innerHTML = rank.map((r,i)=> `<div>#${i+1} — <b>${r.name}</b> • ${r.xp} XP</div>`).join('') }

  // ===== Quiz mínimo funcional (evita travar o login) =====
  function openQuiz(modId){
    const mod = MODULES.find(m=>m.id===modId);
    const qz = getQuiz(modId);
    const box = document.createElement('div');
    const quiz = document.createElement('div'); quiz.className='quiz';
    let i=0, correct=0;
    
  function draw(){
      quiz.innerHTML='';
      const q = qz[i];
      const title = document.createElement('div');
      title.className = 'title';
      title.textContent = `(${i+1}/${qz.length}) ${q.q}`;
      quiz.appendChild(title);

      let answered = false;

      // Área dedicada ao feedback acima do botão
      const feedback = document.createElement('div');
      feedback.className = 'exp';
      feedback.style.display = 'none';
      feedback.style.marginTop = '6px';
      feedback.style.marginBottom = '4px';
      quiz.appendChild(feedback);

      const next = document.createElement('button');
      next.className = 'btn small';
      next.disabled = true;
      next.style.marginTop = '8px';
      next.textContent = 'Próxima ▶️';
      next.onclick = () => {
        if (!answered) return;
        i++;
        (i < qz.length) ? draw() : finish();
      };

      q.opts.forEach((opt, idx) => {
        const li = document.createElement('div');
        li.className = 'option';
        li.innerHTML = `<span class=\"badge\">${String.fromCharCode(65+idx)}</span> <div>${opt}</div>`;
        li.onclick = () => {
          if (answered) return;
          answered = true;
          if (idx === q.ok){
            li.classList.add('correct');
            correct++;
            launchConfetti();
            feedback.textContent = '✅ Boa! Resposta correta.';
            feedback.style.display = 'block';
          } else {
            li.classList.add('wrong');
            feedback.textContent = `❌ ${q.exp || 'Resposta incorreta.'}`;
            feedback.style.display = 'block';
          }
          next.disabled = false;
          next.textContent = (i < qz.length - 1) ? 'Próxima ▶️' : 'Concluir ✅';
        };
        quiz.appendChild(li);
      });

      quiz.appendChild(next);
    }

    
    function finish(){ const st = state.modules[modId]; st.acertos = correct; st.total = qz.length; st.concluido = (correct === qz.length); // 100% para concluir
      // ✅ Registrar no histórico
      try{
        state.history = Array.isArray(state.history) ? state.history : [];
        const pct = Math.round((correct/qz.length)*100);
        state.history.push({ type:'quiz', modId, ac: correct, total: qz.length, pct, date: todayISO() });
        if (state.history.length > 500) state.history = state.history.slice(-500);
      }catch{}
      // Missão automática se atingir >=80%
      const pct2 = Math.round((correct/qz.length)*100);
      if (pct2>=80 && !st.missaoDone){ st.missaoDone=true; state.xp += 50; toast('Missão cumprida! +50 XP'); }
      if(mod.nivel==='Técnico' && st.concluido){ state.weekly.tecnicoDone = (state.weekly.tecnicoDone||0) + 1; }
      save(); renderSafe(); closeOv(); }
    box.appendChild(quiz); openOv(mod.titulo, box); draw(); spendEnergy(1);
  }

  // ===== Login / Navegação =====
  const LEVEL_AVATAR = Object.fromEntries(LEVELS.map(l=>[l.key,l.avatar]));
  function showLogin() {
    const vLogin = document.getElementById('viewLogin');
    const vDash  = document.getElementById('viewDash');
    if (vLogin) vLogin.style.display = '';
    if (vDash)  vDash.style.display  = 'none';
    const nm = document.getElementById('inputName');
    if (nm) nm.value = state?.user?.name || '';
    document.getElementById('btnSair')?.style.setProperty('display','none');
  }
  function showDash() {
    const vLogin = document.getElementById('viewLogin');
    const vDash  = document.getElementById('viewDash');
    if (vLogin) vLogin.style.display = 'none';
    if (vDash)  vDash.style.display  = '';
    updateStreakOnLogin();
    renderSafe();
    document.getElementById('btnSair')?.style.setProperty('display','');
  }
  function validateName(name) {
    const ok = String(name || '').trim().length >= 2;
    const inp = document.getElementById('inputName');
    if (inp) inp.classList.toggle('error', !ok);
    if (!ok) toast('Digite seu nome (mín. 2 letras).');
    return ok;
  }
  function setupLoginHandlers() {
    const btnLogin = document.getElementById('btnLogin');
    if (btnLogin) {
      btnLogin.onclick = () => {
        const name = (document.getElementById('inputName')?.value || '').trim();
        if (!validateName(name)) return;
        state.user = { name, avatar: LEVEL_AVATAR['Técnico'] };
        state.levelKey = 'Técnico';
        save(); updateRank(); renderEnergy(); showDash();
        toast(`Bem-vindo, ${name}! Você está em Técnico. Complete os módulos para evoluir.`);
      };
    }
    const btnSair = document.getElementById('btnSair');
    if (btnSair) {
      btnSair.onclick = () => {
        localStorage.removeItem(LS_KEY);
        state = structuredClone(defaultState);
        ensureState();
        renderEnergy();
        showLogin();
        toast('Você saiu da sua sessão.');
      };
    }
    const toggle = document.getElementById('toggleSound');
    try { soundEnabled = JSON.parse(localStorage.getItem(SOUND_KEY) ?? 'true'); } catch { soundEnabled = true; }
    applySoundToggleUI();
    if (toggle) {
      toggle.onclick = () => {
        soundEnabled = !soundEnabled;
        localStorage.setItem(SOUND_KEY, JSON.stringify(soundEnabled));
        applySoundToggleUI();
        toast(soundEnabled ? 'Som ativado.' : 'Som desativado.');
      };
    }
    document.getElementById('btnMissaoDia')?.addEventListener('click', runDailyMission);
    document.getElementById('btnMissaoDia2')?.addEventListener('click', runDailyMission);
    document.getElementById('ovClose')?.addEventListener('click', closeOv);
  }
  function bootRouter() {
    const hasUser = !!(state?.user?.name);
    if (hasUser) { showDash(); } else { showLogin(); }
  }
  document.addEventListener('DOMContentLoaded', () => { try { setupLoginHandlers(); } catch(e){console.error(e)} try { bootRouter(); } catch(e){console.error(e)} });
  </script>
</body>
</html>
