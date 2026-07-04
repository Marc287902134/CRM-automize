<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<!-- FORZAR A QUE SE MUESTRE COMO PÁGINA WEB -->
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>AutomizeAI · CRM Interno</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@600;700;800&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  /* ======================================================
     1. VARIABLES DE MARCA — AutomizeAI
     ====================================================== */
  :root{
    --black: #080c12;
    --white: #f5f8fc;
    --off-white: #edf2f8;
    --accent: #1E8FE1;
    --accent-dark: #1272b8;
    --accent-light: #5ab4f0;
    --text-muted: #5a6474;
    --text-light: #8a96a6;
    --border: rgba(30,143,225,0.10);
    --border-strong: rgba(30,143,225,0.22);
    --card-bg: #ffffff;

    --sidebar-w-collapsed: 76px;
    --sidebar-w-expanded: 240px;
    --radius: 14px;
    --radius-sm: 8px;
    --transition: 0.28s cubic-bezier(.4,0,.2,1);
  }

  *{ box-sizing:border-box; margin:0; padding:0; }

  html,body{
    height:100%;
    background:var(--off-white);
    color:var(--black);
    font-family:'Inter',sans-serif;
    -webkit-font-smoothing:antialiased;
  }

  h1,h2,h3,h4{ font-family:'Syne',sans-serif; letter-spacing:-0.02em; color:var(--black); }

  ::selection{ background:var(--accent-light); color:var(--black); }

  ::-webkit-scrollbar{ width:8px; height:8px; }
  ::-webkit-scrollbar-thumb{ background:var(--border-strong); border-radius:10px; }
  ::-webkit-scrollbar-track{ background:transparent; }

  button{ font-family:'Inter',sans-serif; cursor:pointer; border:none; outline:none; background:none; }
  input,select{ font-family:'Inter',sans-serif; outline:none; }
  a{ color:inherit; text-decoration:none; }

  /* Animación fade-up reutilizable */
  @keyframes fadeUp{
    from{ opacity:0; transform:translateY(14px); }
    to{ opacity:1; transform:translateY(0); }
  }
  .fade-up{ animation:fadeUp 0.5s ease both; }
  .fade-up:nth-child(1){ animation-delay:.02s; }
  .fade-up:nth-child(2){ animation-delay:.08s; }
  .fade-up:nth-child(3){ animation-delay:.14s; }
  .fade-up:nth-child(4){ animation-delay:.2s; }
  .fade-up:nth-child(5){ animation-delay:.26s; }

  /* ======================================================
     2. PANTALLA DE ACCESO (contraseña)
     ====================================================== */
  #login-screen{
    position:fixed; inset:0; z-index:999;
    display:flex; align-items:center; justify-content:center;
    background:
      radial-gradient(circle at 20% 20%, rgba(30,143,225,0.18), transparent 45%),
      radial-gradient(circle at 80% 80%, rgba(30,143,225,0.12), transparent 45%),
      var(--black);
  }
  .login-card{
    width:100%; max-width:380px; margin:20px;
    background:rgba(245,248,252,0.04);
    border:1px solid rgba(245,248,252,0.1);
    backdrop-filter:blur(6px);
    border-radius:var(--radius);
    padding:40px 34px;
    text-align:center;
  }
  .login-logo{
    display:inline-flex; align-items:center; gap:10px; margin-bottom:26px;
  }
  .login-logo .mark{
    width:34px; height:34px; border-radius:9px;
    background:linear-gradient(135deg,var(--accent),var(--accent-dark));
    display:flex; align-items:center; justify-content:center;
    font-family:'Syne',sans-serif; font-weight:800; color:var(--white); font-size:16px;
  }
  .login-logo span{ font-family:'Syne',sans-serif; font-weight:700; font-size:18px; color:var(--white); }
  .login-card h1{ color:var(--white); font-size:22px; margin-bottom:8px; }
  .login-card p{ color:var(--text-light); font-size:13.5px; margin-bottom:26px; }
  .login-card input{
    width:100%; padding:13px 16px; border-radius:var(--radius-sm);
    border:1px solid rgba(245,248,252,0.14);
    background:rgba(245,248,252,0.06); color:var(--white); font-size:14px;
    transition:border-color var(--transition);
  }
  .login-card input::placeholder{ color:var(--text-light); }
  .login-card input:focus{ border-color:var(--accent); }
  .login-card button{
    width:100%; margin-top:14px; padding:13px 16px; border-radius:var(--radius-sm);
    background:var(--accent); color:var(--white); font-weight:600; font-size:14px;
    transition:background var(--transition), transform var(--transition);
  }
  .login-card button:hover{ background:var(--accent-dark); transform:translateY(-1px); }
  #login-error{
    color:#ff8787; font-size:12.5px; margin-top:12px; min-height:16px; opacity:0;
    transition:opacity var(--transition);
  }
  #login-error.show{ opacity:1; }

  /* ======================================================
     3. LAYOUT PRINCIPAL
     ====================================================== */
  #app{ display:none; min-height:100vh; }
  #app.active{ display:flex; }

  /* --- SIDEBAR --- */
  .sidebar{
    width:var(--sidebar-w-collapsed);
    background:var(--black);
    height:100vh;
    position:fixed; left:0; top:0; z-index:100;
    display:flex; flex-direction:column;
    transition:width var(--transition);
    overflow:hidden;
    border-right:1px solid rgba(245,248,252,0.06);
  }
  .sidebar:hover{ width:var(--sidebar-w-expanded); }

  .sidebar-logo{
    display:flex; align-items:center; gap:12px;
    padding:24px 22px; white-space:nowrap;
    border-bottom:1px solid rgba(245,248,252,0.06);
    min-height:76px;
  }
  .sidebar-logo .mark{
    flex-shrink:0; width:32px; height:32px; border-radius:9px;
    background:linear-gradient(135deg,var(--accent),var(--accent-dark));
    display:flex; align-items:center; justify-content:center;
    font-family:'Syne',sans-serif; font-weight:800; color:var(--white); font-size:15px;
  }
  .sidebar-logo span{
    font-family:'Syne',sans-serif; font-weight:700; font-size:16.5px; color:var(--white);
    opacity:0; transition:opacity var(--transition);
  }
  .sidebar:hover .sidebar-logo span{ opacity:1; }

  .sidebar-nav{ flex:1; padding:16px 12px; display:flex; flex-direction:column; gap:4px; }
  .nav-item{
    display:flex; align-items:center; gap:14px;
    padding:12px; border-radius:var(--radius-sm);
    color:var(--text-light); white-space:nowrap;
    transition:background var(--transition), color var(--transition);
  }
  .nav-item .icon{ flex-shrink:0; width:20px; height:20px; display:flex; align-items:center; justify-content:center; }
  .nav-item svg{ width:19px; height:19px; }
  .nav-item span.label{
    font-size:14px; font-weight:500; opacity:0; transition:opacity var(--transition);
  }
  .sidebar:hover .nav-item span.label{ opacity:1; }
  .nav-item:hover{ background:rgba(245,248,252,0.06); color:var(--white); }
  .nav-item.active{ background:var(--accent); color:var(--white); }

  .sidebar-footer{
    padding:16px 12px; border-top:1px solid rgba(245,248,252,0.06);
  }
  .user-chip{
    display:flex; align-items:center; gap:12px; padding:10px; border-radius:var(--radius-sm);
    white-space:nowrap;
  }
  .user-chip .avatar{
    flex-shrink:0; width:30px; height:30px; border-radius:50%;
    background:var(--accent-light); color:var(--black); font-weight:700; font-size:12px;
    display:flex; align-items:center; justify-content:center;
  }
  .user-chip .info{ opacity:0; transition:opacity var(--transition); }
  .sidebar:hover .user-chip .info{ opacity:1; }
  .user-chip .info div:first-child{ font-size:13px; color:var(--white); font-weight:600; }
  .user-chip .info div:last-child{ font-size:11px; color:var(--text-light); }
  .logout-btn{
    margin-top:10px; width:100%; text-align:left; padding:9px 10px; border-radius:var(--radius-sm);
    color:var(--text-light); font-size:12.5px; display:flex; align-items:center; gap:10px;
    transition:background var(--transition), color var(--transition);
  }
  .logout-btn:hover{ background:rgba(245,248,252,0.06); color:#ff8787; }
  .logout-btn svg{ width:15px; height:15px; flex-shrink:0; }
  .logout-btn span{ opacity:0; transition:opacity var(--transition); white-space:nowrap; }
  .sidebar:hover .logout-btn span{ opacity:1; }

  /* --- MAIN --- */
  .main{
    margin-left:var(--sidebar-w-collapsed);
    flex:1; min-height:100vh;
    padding:32px 40px 60px;
    transition:margin-left var(--transition);
  }

  .topbar{
    display:flex; align-items:center; justify-content:space-between;
    margin-bottom:32px; flex-wrap:wrap; gap:16px;
  }
  .topbar h1{ font-size:26px; }
  .topbar p{ color:var(--text-muted); font-size:13.5px; margin-top:4px; }
  .topbar-actions{ display:flex; gap:12px; align-items:center; }

  .btn{
    padding:11px 20px; border-radius:var(--radius-sm); font-size:13.5px; font-weight:600;
    display:inline-flex; align-items:center; gap:8px;
    transition:background var(--transition), transform var(--transition), border-color var(--transition);
  }
  .btn svg{ width:15px; height:15px; }
  .btn-primary{ background:var(--accent); color:var(--white); }
  .btn-primary:hover{ background:var(--accent-dark); transform:translateY(-1px); }
  .btn-secondary{ background:var(--card-bg); color:var(--black); border:1px solid var(--border-strong); }
  .btn-secondary:hover{ border-color:var(--accent); color:var(--accent); }
  .btn-ghost{ color:var(--text-muted); }
  .btn-ghost:hover{ color:var(--accent); }

  .section{ display:none; }
  .section.active{ display:block; }

  /* ======================================================
     4. DASHBOARD
     ====================================================== */
  .metrics-grid{
    display:grid; grid-template-columns:repeat(4,1fr); gap:20px; margin-bottom:28px;
  }
  .metric-card{
    background:var(--card-bg); border:1px solid var(--border); border-radius:var(--radius);
    padding:22px; position:relative; overflow:hidden;
  }
  .metric-card::before{
    content:''; position:absolute; top:0; left:0; width:100%; height:3px;
    background:linear-gradient(90deg,var(--accent),var(--accent-light));
  }
  .metric-card .label{ font-size:12.5px; color:var(--text-muted); font-weight:600; text-transform:uppercase; letter-spacing:.04em; }
  .metric-card .value{ font-family:'Syne',sans-serif; font-size:32px; font-weight:700; margin-top:10px; }
  .metric-card .delta{ font-size:12px; color:var(--accent-dark); margin-top:6px; font-weight:600; }

  .dash-grid{ display:grid; grid-template-columns:1.4fr 1fr; gap:20px; margin-bottom:20px; }

  .panel{
    background:var(--card-bg); border:1px solid var(--border); border-radius:var(--radius); padding:24px;
  }
  .panel h3{ font-size:16px; margin-bottom:20px; }

  .chart-wrap{ display:flex; align-items:flex-end; gap:14px; height:180px; padding-top:10px; }
  .chart-bar-col{ flex:1; display:flex; flex-direction:column; align-items:center; height:100%; justify-content:flex-end; gap:8px; }
  .chart-bar{
    width:100%; max-width:38px; border-radius:6px 6px 0 0;
    background:linear-gradient(180deg,var(--accent-light),var(--accent));
    transition:transform var(--transition);
  }
  .chart-bar:hover{ transform:scaleY(1.03); filter:brightness(1.05); }
  .chart-bar-col .month-label{ font-size:11.5px; color:var(--text-light); font-weight:600; }

  .activity-list{ display:flex; flex-direction:column; gap:16px; }
  .activity-item{ display:flex; gap:12px; align-items:flex-start; }
  .activity-dot{
    flex-shrink:0; width:8px; height:8px; border-radius:50%; background:var(--accent); margin-top:6px;
  }
  .activity-item .txt{ font-size:13.5px; color:var(--black); line-height:1.4; }
  .activity-item .time{ font-size:11.5px; color:var(--text-light); margin-top:2px; }

  .pipeline-mini{ display:grid; grid-template-columns:repeat(4,1fr); gap:14px; }
  .pipeline-mini-card{
    background:var(--off-white); border:1px solid var(--border); border-radius:var(--radius-sm);
    padding:16px; text-align:center;
  }
  .pipeline-mini-card .num{ font-family:'Syne',sans-serif; font-size:24px; font-weight:700; color:var(--accent-dark); }
  .pipeline-mini-card .name{ font-size:12px; color:var(--text-muted); margin-top:4px; font-weight:600; }

  /* ======================================================
     5. CLIENTES / LEADS
     ====================================================== */
  .filters-row{ display:flex; gap:12px; margin-bottom:20px; flex-wrap:wrap; }
  .search-input{
    flex:1; min-width:220px; padding:11px 16px; border-radius:var(--radius-sm);
    border:1px solid var(--border-strong); background:var(--card-bg); font-size:13.5px;
  }
  .search-input:focus{ border-color:var(--accent); }
  .filter-select{
    padding:11px 16px; border-radius:var(--radius-sm); border:1px solid var(--border-strong);
    background:var(--card-bg); font-size:13.5px; color:var(--black);
  }

  .table-wrap{
    background:var(--card-bg); border:1px solid var(--border); border-radius:var(--radius);
    overflow:hidden;
  }
  table{ width:100%; border-collapse:collapse; }
  thead th{
    text-align:left; padding:15px 20px; font-size:11.5px; text-transform:uppercase;
    letter-spacing:.05em; color:var(--text-muted); font-weight:700; background:var(--off-white);
    border-bottom:1px solid var(--border);
  }
  tbody td{ padding:15px 20px; font-size:13.5px; border-bottom:1px solid var(--border); }
  tbody tr:last-child td{ border-bottom:none; }
  tbody tr{ transition:background var(--transition); }
  tbody tr:hover{ background:var(--off-white); }
  .cell-name{ font-weight:600; }
  .cell-muted{ color:var(--text-muted); }

  .status-pill{
    display:inline-block; padding:5px 12px; border-radius:20px; font-size:11.5px; font-weight:700;
  }
  .status-Lead{ background:rgba(138,150,166,0.15); color:var(--text-muted); }
  .status-Contactado{ background:rgba(30,143,225,0.12); color:var(--accent-dark); }
  .status-Negociación{ background:rgba(255,171,64,0.15); color:#b3720a; }
  .status-Cerrado{ background:rgba(46,196,131,0.15); color:#1a8f5e; }

  .row-actions{ display:flex; gap:8px; }
  .icon-btn{
    width:30px; height:30px; border-radius:8px; display:flex; align-items:center; justify-content:center;
    color:var(--text-muted); transition:background var(--transition), color var(--transition);
  }
  .icon-btn svg{ width:15px; height:15px; }
  .icon-btn:hover{ background:var(--off-white); color:var(--accent); }
  .icon-btn.danger:hover{ color:#e05252; }

  .empty-state{ text-align:center; padding:50px 20px; color:var(--text-light); font-size:13.5px; }

  /* ======================================================
     6. PIPELINE (KANBAN)
     ====================================================== */
  .kanban-board{ display:grid; grid-template-columns:repeat(4,1fr); gap:18px; align-items:start; }
  .kanban-col{
    background:var(--off-white); border:1px solid var(--border); border-radius:var(--radius);
    padding:16px; min-height:120px;
  }
  .kanban-col-header{ display:flex; align-items:center; justify-content:space-between; margin-bottom:14px; padding:0 4px; }
  .kanban-col-header .title{ font-size:13px; font-weight:700; display:flex; align-items:center; gap:8px; }
  .kanban-col-header .count{
    background:rgba(30,143,225,0.12); color:var(--accent-dark); font-size:11px; font-weight:700;
    padding:2px 8px; border-radius:10px;
  }
  .kanban-cards{ display:flex; flex-direction:column; gap:10px; min-height:40px; }
  .kanban-card{
    background:var(--card-bg); border:1px solid var(--border); border-radius:var(--radius-sm);
    padding:14px; cursor:grab; transition:box-shadow var(--transition), transform var(--transition), opacity var(--transition);
  }
  .kanban-card:hover{ box-shadow:0 6px 18px rgba(8,12,18,0.08); transform:translateY(-2px); }
  .kanban-card.dragging{ opacity:0.4; }
  .kanban-card .name{ font-weight:700; font-size:13.5px; }
  .kanban-card .company{ font-size:12px; color:var(--text-muted); margin-top:3px; }
  .kanban-card .date{ font-size:11px; color:var(--text-light); margin-top:8px; display:flex; align-items:center; gap:5px; }
  .kanban-col.drag-over{ background:rgba(30,143,225,0.08); border-color:var(--accent); }

  .add-card-btn{
    width:100%; margin-top:10px; padding:10px; border-radius:var(--radius-sm);
    border:1.5px dashed var(--border-strong); color:var(--text-muted); font-size:12.5px; font-weight:600;
    transition:border-color var(--transition), color var(--transition), background var(--transition);
  }
  .add-card-btn:hover{ border-color:var(--accent); color:var(--accent); background:rgba(30,143,225,0.05); }

  /* ======================================================
     7. TAREAS
     ====================================================== */
  .task-list{ display:flex; flex-direction:column; gap:10px; }
  .task-item{
    display:flex; align-items:center; gap:16px;
    background:var(--card-bg); border:1px solid var(--border); border-radius:var(--radius-sm);
    padding:15px 18px;
    transition:opacity var(--transition), background var(--transition);
  }
  .task-item.completed{ opacity:0.55; }
  .task-checkbox{
    flex-shrink:0; width:20px; height:20px; border-radius:6px; border:2px solid var(--border-strong);
    display:flex; align-items:center; justify-content:center; transition:all var(--transition);
  }
  .task-checkbox.checked{ background:var(--accent); border-color:var(--accent); }
  .task-checkbox svg{ width:12px; height:12px; color:var(--white); opacity:0; transition:opacity var(--transition); }
  .task-checkbox.checked svg{ opacity:1; }
  .task-main{ flex:1; min-width:0; }
  .task-title{ font-size:14px; font-weight:600; }
  .task-item.completed .task-title{ text-decoration:line-through; }
  .task-meta{ font-size:12px; color:var(--text-muted); margin-top:3px; display:flex; gap:14px; flex-wrap:wrap; }
  .task-meta span{ display:flex; align-items:center; gap:5px; }
  .task-meta svg{ width:12px; height:12px; }

  /* ======================================================
     8. MODAL
     ====================================================== */
  .modal-overlay{
    position:fixed; inset:0; background:rgba(8,12,18,0.55); backdrop-filter:blur(3px);
    display:none; align-items:center; justify-content:center; z-index:500; padding:20px;
  }
  .modal-overlay.active{ display:flex; }
  .modal{
    background:var(--card-bg); border-radius:var(--radius); width:100%; max-width:440px;
    padding:28px; animation:fadeUp 0.3s ease both;
    max-height:90vh; overflow-y:auto;
  }
  .modal-header{ display:flex; align-items:center; justify-content:space-between; margin-bottom:22px; }
  .modal-header h3{ font-size:18px; }
  .modal-close{ width:30px; height:30px; border-radius:8px; display:flex; align-items:center; justify-content:center; color:var(--text-muted); }
  .modal-close:hover{ background:var(--off-white); color:var(--black); }
  .form-group{ margin-bottom:16px; }
  .form-group label{ display:block; font-size:12.5px; font-weight:600; margin-bottom:7px; color:var(--text-muted); }
  .form-group input, .form-group select{
    width:100%; padding:11px 14px; border-radius:var(--radius-sm); border:1px solid var(--border-strong);
    font-size:13.5px; background:var(--white);
  }
  .form-group input:focus, .form-group select:focus{ border-color:var(--accent); }
  .modal-actions{ display:flex; gap:10px; margin-top:22px; }
  .modal-actions .btn{ flex:1; justify-content:center; }

  /* Toast de confirmación */
  #toast{
    position:fixed; bottom:26px; left:50%; transform:translateX(-50%) translateY(20px);
    background:var(--black); color:var(--white); padding:13px 22px; border-radius:var(--radius-sm);
    font-size:13px; font-weight:600; z-index:1000; opacity:0; pointer-events:none;
    transition:opacity var(--transition), transform var(--transition);
    display:flex; align-items:center; gap:10px;
  }
  #toast.show{ opacity:1; transform:translateX(-50%) translateY(0); }
  #toast .dot{ width:7px; height:7px; border-radius:50%; background:var(--accent-light); }

  /* ======================================================
     9. RESPONSIVE
     ====================================================== */
  @media (max-width: 1024px){
    .metrics-grid{ grid-template-columns:repeat(2,1fr); }
    .dash-grid{ grid-template-columns:1fr; }
    .kanban-board{ grid-template-columns:repeat(2,1fr); }
  }

  @media (max-width: 720px){
    .sidebar{ width:100%; height:64px; top:auto; bottom:0; flex-direction:row; }
    .sidebar:hover{ width:100%; }
    .sidebar-logo{ display:none; }
    .sidebar-nav{ flex-direction:row; padding:8px; width:100%; justify-content:space-around; }
    .nav-item{ flex-direction:column; gap:4px; padding:8px 6px; }
    .nav-item span.label{ opacity:1; font-size:10px; }
    .sidebar-footer{ display:none; }
    .main{ margin-left:0; padding:24px 18px 90px; }
    .metrics-grid{ grid-template-columns:1fr 1fr; gap:12px; }
    .pipeline-mini{ grid-template-columns:1fr 1fr; }
    .kanban-board{ grid-template-columns:1fr; }
    .table-wrap{ overflow-x:auto; }
    table{ min-width:640px; }
    .topbar{ flex-direction:column; align-items:flex-start; }
  }
</style>
</head>
<body>

<!-- ============================================================
     PANTALLA DE LOGIN (protección con contraseña simple)
     ============================================================ -->
<div id="login-screen">
  <div class="login-card">
    <div class="login-logo">
      <div class="mark">A</div>
      <span>AutomizeAI</span>
    </div>
    <h1>Acceso al CRM</h1>
    <p>Introduce la contraseña del equipo para continuar</p>
    <form id="login-form" autocomplete="off">
      <input type="password" id="password-input" placeholder="Contraseña" autofocus>
      <button type="submit">Entrar</button>
      <div id="login-error">Contraseña incorrecta. Inténtalo de nuevo.</div>
    </form>
  </div>
</div>

<!-- ============================================================
     APLICACIÓN PRINCIPAL
     ============================================================ -->
<div id="app">

  <!-- ---------- SIDEBAR ---------- -->
  <aside class="sidebar">
    <div class="sidebar-logo">
      <div class="mark">A</div>
      <span>AutomizeAI</span>
    </div>

    <nav class="sidebar-nav">
  <a href="#" class="nav-item active" data-section="dashboard">
    <span class="icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="3" width="7" height="9"/><rect x="14" y="3" width="7" height="5"/><rect x="14" y="12" width="7" height="9"/><rect x="3" y="16" width="7" height="5"/></svg></span>
    <span class="label">Dashboard</span>
  </a>
  <a href="#" class="nav-item" data-section="clientes">
    <span class="icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M23 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/></svg></span>
    <span class="label">Clientes</span>
  </a>
  <a href="#" class="nav-item" data-section="pipeline">
    <span class="icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 3h18v4H3z"/><path d="M3 10h12v4H3z"/><path d="M3 17h6v4H3z"/></svg></span>
    <span class="label">Pipeline</span>
  </a>
  <a href="#" class="nav-item" data-section="tareas">
    <span class="icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M9 11l3 3L22 4"/><path d="M21 12v7a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11"/></svg></span>
    <span class="label">Tareas</span>
  </a>

  <!-- LÍNEA DIVISORA -->
  <div style="height:1px;background:rgba(245,248,252,0.08);margin:10px 4px;"></div>

  <!-- NUEVO: ENLACE A CONTABILIDAD -->
  <a href="contabilidad.html" class="nav-item" style="color:var(--accent-light);">
    <span class="icon"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/><line x1="8" y1="13" x2="16" y2="13"/><line x1="8" y1="17" x2="16" y2="17"/></svg></span>
    <span class="label">📊 Contabilidad</span>
  </a>
</nav>
    <div class="sidebar-footer">
      <div class="user-chip">
        <div class="avatar">M</div>
        <div class="info">
          <div>Marc &amp; Mario</div>
          <div>Equipo AutomizeAI</div>
        </div>
      </div>
      <button class="logout-btn" id="logout-btn">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" y1="12" x2="9" y2="12"/></svg>
        <span>Cerrar sesión</span>
      </button>
    </div>
  </aside>

  <!-- ---------- CONTENIDO PRINCIPAL ---------- -->
  <main class="main">

    <!-- ============ DASHBOARD ============ -->
    <section class="section active" id="section-dashboard">
      <div class="topbar">
        <div>
          <h1>Dashboard</h1>
          <p>Resumen general de la actividad comercial de AutomizeAI</p>
        </div>
      </div>

      <div class="metrics-grid" id="metrics-grid"><!-- generado por JS --></div>

      <div class="dash-grid">
        <div class="panel fade-up">
          <h3>Evolución de leads (últimos 6 meses)</h3>
          <div class="chart-wrap" id="chart-wrap"><!-- generado por JS --></div>
        </div>
        <div class="panel fade-up">
          <h3>Actividad reciente</h3>
          <div class="activity-list" id="activity-list"><!-- generado por JS --></div>
        </div>
      </div>

      <div class="panel fade-up">
        <h3>Resumen del pipeline</h3>
        <div class="pipeline-mini" id="pipeline-mini"><!-- generado por JS --></div>
      </div>
    </section>

    <!-- ============ CLIENTES ============ -->
    <section class="section" id="section-clientes">
      <div class="topbar">
        <div>
          <h1>Clientes &amp; Leads</h1>
          <p>Gestiona todos tus contactos comerciales en un solo lugar</p>
        </div>
        <div class="topbar-actions">
          <button class="btn btn-primary" id="btn-new-lead">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>
            Nuevo lead
          </button>
        </div>
      </div>

      <div class="filters-row">
        <input type="text" class="search-input" id="search-input" placeholder="Buscar por nombre, empresa o email...">
        <select class="filter-select" id="filter-status">
          <option value="todos">Todos los estados</option>
          <option value="Lead">Lead</option>
          <option value="Contactado">Contactado</option>
          <option value="Negociación">Negociación</option>
          <option value="Cerrado">Cerrado</option>
        </select>
      </div>

      <div class="table-wrap">
        <table>
          <thead>
            <tr>
              <th>Nombre</th>
              <th>Empresa</th>
              <th>Email</th>
              <th>Teléfono</th>
              <th>Estado</th>
              <th></th>
            </tr>
          </thead>
          <tbody id="clientes-tbody"><!-- generado por JS --></tbody>
        </table>
      </div>
    </section>

    <!-- ============ PIPELINE (KANBAN) ============ -->
    <section class="section" id="section-pipeline">
      <div class="topbar">
        <div>
          <h1>Pipeline de ventas</h1>
          <p>Arrastra las tarjetas para actualizar el estado de cada lead</p>
        </div>
      </div>

      <div class="kanban-board" id="kanban-board"><!-- generado por JS --></div>
    </section>

    <!-- ============ TAREAS ============ -->
    <section class="section" id="section-tareas">
      <div class="topbar">
        <div>
          <h1>Tareas</h1>
          <p>El día a día del equipo, siempre bajo control</p>
        </div>
        <div class="topbar-actions">
          <button class="btn btn-primary" id="btn-new-task">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>
            Nueva tarea
          </button>
        </div>
      </div>

      <div class="task-list" id="task-list"><!-- generado por JS --></div>
    </section>

  </main>
</div>

<!-- ============================================================
     MODAL: NUEVO LEAD
     ============================================================ -->
<div class="modal-overlay" id="modal-lead">
  <div class="modal">
    <div class="modal-header">
      <h3>Nuevo lead</h3>
      <button class="modal-close" data-close="modal-lead">✕</button>
    </div>
    <form id="form-lead">
      <div class="form-group">
        <label>Nombre completo</label>
        <input type="text" id="lead-nombre" required placeholder="Ej. Sara Gómez">
      </div>
      <div class="form-group">
        <label>Empresa</label>
        <input type="text" id="lead-empresa" required placeholder="Ej. Gómez Consulting">
      </div>
      <div class="form-group">
        <label>Email</label>
        <input type="email" id="lead-email" required placeholder="sara@empresa.com">
      </div>
      <div class="form-group">
        <label>Teléfono</label>
        <input type="tel" id="lead-telefono" required placeholder="+34 600 000 000">
      </div>
      <div class="form-group">
        <label>Estado</label>
        <select id="lead-estado">
          <option value="Lead">Lead</option>
          <option value="Contactado">Contactado</option>
          <option value="Negociación">Negociación</option>
          <option value="Cerrado">Cerrado</option>
        </select>
      </div>
      <div class="modal-actions">
        <button type="button" class="btn btn-secondary" data-close="modal-lead">Cancelar</button>
        <button type="submit" class="btn btn-primary">Guardar lead</button>
      </div>
    </form>
  </div>
</div>

<!-- ============================================================
     MODAL: NUEVA TARJETA DE PIPELINE (rápida)
     ============================================================ -->
<div class="modal-overlay" id="modal-kanban">
  <div class="modal">
    <div class="modal-header">
      <h3>Añadir tarjeta</h3>
      <button class="modal-close" data-close="modal-kanban">✕</button>
    </div>
    <form id="form-kanban">
      <div class="form-group">
        <label>Nombre del contacto</label>
        <input type="text" id="kanban-nombre" required placeholder="Ej. Sara Gómez">
      </div>
      <div class="form-group">
        <label>Empresa</label>
        <input type="text" id="kanban-empresa" required placeholder="Ej. Gómez Consulting">
      </div>
      <input type="hidden" id="kanban-estado" value="Lead">
      <div class="modal-actions">
        <button type="button" class="btn btn-secondary" data-close="modal-kanban">Cancelar</button>
        <button type="submit" class="btn btn-primary">Añadir</button>
      </div>
    </form>
  </div>
</div>

<!-- ============================================================
     MODAL: NUEVA TAREA
     ============================================================ -->
<div class="modal-overlay" id="modal-task">
  <div class="modal">
    <div class="modal-header">
      <h3>Nueva tarea</h3>
      <button class="modal-close" data-close="modal-task">✕</button>
    </div>
    <form id="form-task">
      <div class="form-group">
        <label>Título</label>
        <input type="text" id="task-titulo" required placeholder="Ej. Llamar a nuevo lead">
      </div>
      <div class="form-group">
        <label>Asignado a</label>
        <select id="task-asignado">
          <option value="Marc">Marc</option>
          <option value="Mario">Mario</option>
        </select>
      </div>
      <div class="form-group">
        <label>Fecha límite</label>
        <input type="date" id="task-fecha" required>
      </div>
      <div class="modal-actions">
        <button type="button" class="btn btn-secondary" data-close="modal-task">Cancelar</button>
        <button type="submit" class="btn btn-primary">Guardar tarea</button>
      </div>
    </form>
  </div>
</div>

<!-- Toast de confirmación -->
<div id="toast"><span class="dot"></span><span id="toast-text">Guardado correctamente</span></div>

<script>
/* ================================================================
   AutomizeAI CRM — Lógica de la aplicación (JavaScript vanilla)
   ================================================================ */

/* ---------- 1. CONFIGURACIÓN Y CONTRASEÑA ---------- */
const APP_PASSWORD = "automize2025";
const STORAGE_KEYS = { leads: "automizeai_leads", tasks: "automizeai_tasks" };

/* ---------- 2. DATOS DE EJEMPLO (semilla inicial) ---------- */
const SEED_LEADS = [
  { id: 1, nombre: "María González",  empresa: "TechSolutions",         email: "maria.gonzalez@techsolutions.es",   telefono: "+34 611 222 333", estado: "Lead",        fecha: "2025-01-10" },
  { id: 2, nombre: "Carlos Ruiz",     empresa: "Distribuciones Ruiz",   email: "carlos.ruiz@distribru.es",          telefono: "+34 622 333 444", estado: "Contactado",  fecha: "2025-01-12" },
  { id: 3, nombre: "Laura Martínez",  empresa: "InnovaDigital",         email: "laura.martinez@innovadigital.es",   telefono: "+34 633 444 555", estado: "Negociación", fecha: "2025-01-14" },
  { id: 4, nombre: "Pedro Sánchez",   empresa: "Grupo Sánchez S.A.",    email: "pedro.sanchez@gruposanchez.es",     telefono: "+34 644 555 666", estado: "Cerrado",     fecha: "2025-01-15" },
  { id: 5, nombre: "Ana López",       empresa: "Consultoría López",     email: "ana.lopez@consultorialopez.es",     telefono: "+34 655 666 777", estado: "Lead",        fecha: "2025-01-16" }
];

const SEED_TASKS = [
  { id: 1, titulo: "Llamar a María González",        asignado: "Marc",  fecha: "2025-01-20", completada: false },
  { id: 2, titulo: "Enviar propuesta a Carlos Ruiz",  asignado: "Mario", fecha: "2025-01-22", completada: false },
  { id: 3, titulo: "Reunión con Laura Martínez",      asignado: "Marc",  fecha: "2025-01-25", completada: false },
  { id: 4, titulo: "Seguimiento a Pedro Sánchez",     asignado: "Mario", fecha: "2025-01-18", completada: true  }
];

const ACTIVIDAD_RECIENTE = [
  { txt: "Nuevo lead añadido: <strong>Ana López</strong>",                     time: "Hace 2 horas" },
  { txt: "<strong>Pedro Sánchez</strong> movido a Cerrado",                    time: "Hace 5 horas" },
  { txt: "Tarea completada: Seguimiento a Pedro Sánchez",                      time: "Ayer" },
  { txt: "<strong>Laura Martínez</strong> pasó a Negociación",                 time: "Hace 2 días" },
  { txt: "Propuesta enviada a <strong>Carlos Ruiz</strong>",                   time: "Hace 3 días" }
];

const LEADS_POR_MES = [ { mes:"Ago", valor:8 }, { mes:"Sep", valor:14 }, { mes:"Oct", valor:11 }, { mes:"Nov", valor:19 }, { mes:"Dic", valor:16 }, { mes:"Ene", valor:23 } ];

/* ---------- 3. ESTADO DE LA APLICACIÓN ---------- */
let leads = [];
let tasks = [];
let kanbanDragId = null;

/* ---------- 4. PERSISTENCIA (localStorage) ---------- */
function cargarDatos(){
  const savedLeads = localStorage.getItem(STORAGE_KEYS.leads);
  const savedTasks = localStorage.getItem(STORAGE_KEYS.tasks);
  leads = savedLeads ? JSON.parse(savedLeads) : JSON.parse(JSON.stringify(SEED_LEADS));
  tasks = savedTasks ? JSON.parse(savedTasks) : JSON.parse(JSON.stringify(SEED_TASKS));
  if(!savedLeads) guardarLeads();
  if(!savedTasks) guardarTasks();
}
function guardarLeads(){ localStorage.setItem(STORAGE_KEYS.leads, JSON.stringify(leads)); }
function guardarTasks(){ localStorage.setItem(STORAGE_KEYS.tasks, JSON.stringify(tasks)); }

/* ---------- 5. LOGIN ---------- */
const loginScreen = document.getElementById('login-screen');
const loginForm = document.getElementById('login-form');
const passwordInput = document.getElementById('password-input');
const loginError = document.getElementById('login-error');
const appEl = document.getElementById('app');

loginForm.addEventListener('submit', function(e){
  e.preventDefault();
  if(passwordInput.value === APP_PASSWORD){
    sessionStorage.setItem('automizeai_auth', '1');
    entrarEnApp();
  } else {
    loginError.classList.add('show');
    passwordInput.value = '';
    passwordInput.focus();
  }
});

document.getElementById('logout-btn').addEventListener('click', function(){
  sessionStorage.removeItem('automizeai_auth');
  location.reload();
});

function entrarEnApp(){
  loginScreen.style.display = 'none';
  appEl.classList.add('active');
  cargarDatos();
  renderTodo();
}

// Si ya se autenticó en esta sesión del navegador, entra directamente
if(sessionStorage.getItem('automizeai_auth') === '1'){
  entrarEnApp();
}

/* ---------- 6. NAVEGACIÓN ENTRE SECCIONES ---------- */
document.querySelectorAll('.nav-item').forEach(item => {
  item.addEventListener('click', function(e){
    e.preventDefault();
    const target = this.dataset.section;
    document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('active'));
    this.classList.add('active');
    document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
    document.getElementById('section-' + target).classList.add('active');
  });
});

/* ---------- 7. UTILIDADES ---------- */
function formatearFecha(fechaStr){
  const d = new Date(fechaStr + 'T00:00:00');
  return d.toLocaleDateString('es-ES', { day:'2-digit', month:'short', year:'numeric' });
}
function mostrarToast(texto){
  const toast = document.getElementById('toast');
  document.getElementById('toast-text').textContent = texto;
  toast.classList.add('show');
  setTimeout(() => toast.classList.remove('show'), 2400);
}
function iniciales(nombre){
  return nombre.split(' ').map(p => p[0]).slice(0,2).join('').toUpperCase();
}
function abrirModal(id){ document.getElementById(id).classList.add('active'); }
function cerrarModal(id){ document.getElementById(id).classList.remove('active'); }
document.querySelectorAll('[data-close]').forEach(btn => {
  btn.addEventListener('click', () => cerrarModal(btn.dataset.close));
});
document.querySelectorAll('.modal-overlay').forEach(overlay => {
  overlay.addEventListener('click', (e) => { if(e.target === overlay) overlay.classList.remove('active'); });
});

/* ---------- 8. RENDER GENERAL ---------- */
function renderTodo(){
  renderDashboard();
  renderClientes();
  renderKanban();
  renderTareas();
}

/* ---------- 9. DASHBOARD ---------- */
function renderDashboard(){
  const totalLeads = leads.length;
  const clientesActivos = leads.filter(l => l.estado === 'Cerrado').length;
  const ingresosMes = clientesActivos * 4150; // valor medio simulado por cliente cerrado
  const tareasPendientes = tasks.filter(t => !t.completada).length;

  document.getElementById('metrics-grid').innerHTML = `
    <div class="metric-card fade-up">
      <div class="label">Total leads</div>
      <div class="value">${totalLeads}</div>
      <div class="delta">↑ Pipeline activo</div>
    </div>
    <div class="metric-card fade-up">
      <div class="label">Clientes activos</div>
      <div class="value">${clientesActivos}</div>
      <div class="delta">↑ Contratos cerrados</div>
    </div>
    <div class="metric-card fade-up">
      <div class="label">Ingresos del mes</div>
      <div class="value">${ingresosMes.toLocaleString('es-ES')}€</div>
      <div class="delta">↑ Estimado enero</div>
    </div>
    <div class="metric-card fade-up">
      <div class="label">Tareas pendientes</div>
      <div class="value">${tareasPendientes}</div>
      <div class="delta">${tareasPendientes > 0 ? 'Requieren atención' : 'Todo al día'}</div>
    </div>
  `;

  // Gráfico de barras: evolución de leads
  const maxValor = Math.max(...LEADS_POR_MES.map(m => m.valor));
  document.getElementById('chart-wrap').innerHTML = LEADS_POR_MES.map(m => `
    <div class="chart-bar-col">
      <div class="chart-bar" style="height:${(m.valor / maxValor * 100)}%" title="${m.valor} leads"></div>
      <div class="month-label">${m.mes}</div>
    </div>
  `).join('');

  // Actividad reciente
  document.getElementById('activity-list').innerHTML = ACTIVIDAD_RECIENTE.map(a => `
    <div class="activity-item">
      <div class="activity-dot"></div>
      <div>
        <div class="txt">${a.txt}</div>
        <div class="time">${a.time}</div>
      </div>
    </div>
  `).join('');

  // Resumen del pipeline
  const estados = ['Lead','Contactado','Negociación','Cerrado'];
  document.getElementById('pipeline-mini').innerHTML = estados.map(e => `
    <div class="pipeline-mini-card">
      <div class="num">${leads.filter(l => l.estado === e).length}</div>
      <div class="name">${e}</div>
    </div>
  `).join('');
}

/* ---------- 10. CLIENTES / LEADS (TABLA) ---------- */
const searchInput = document.getElementById('search-input');
const filterStatus = document.getElementById('filter-status');
searchInput.addEventListener('input', renderClientes);
filterStatus.addEventListener('change', renderClientes);

function renderClientes(){
  const query = searchInput.value.trim().toLowerCase();
  const estadoFiltro = filterStatus.value;

  let filtrados = leads.filter(l => {
    const coincideTexto = l.nombre.toLowerCase().includes(query) ||
                           l.empresa.toLowerCase().includes(query) ||
                           l.email.toLowerCase().includes(query);
    const coincideEstado = estadoFiltro === 'todos' || l.estado === estadoFiltro;
    return coincideTexto && coincideEstado;
  });

  const tbody = document.getElementById('clientes-tbody');

  if(filtrados.length === 0){
    tbody.innerHTML = `<tr><td colspan="6"><div class="empty-state">No se han encontrado clientes con esos criterios.</div></td></tr>`;
    return;
  }

  tbody.innerHTML = filtrados.map(l => `
    <tr data-id="${l.id}">
      <td class="cell-name">${l.nombre}</td>
      <td>${l.empresa}</td>
      <td class="cell-muted">${l.email}</td>
      <td class="cell-muted">${l.telefono}</td>
      <td><span class="status-pill status-${l.estado.replace('ó','o')}">${l.estado}</span></td>
      <td>
        <div class="row-actions">
          <button class="icon-btn" title="Editar estado" onclick="ciclarEstadoLead(${l.id})">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.5 2.5a2.12 2.12 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/></svg>
          </button>
          <button class="icon-btn danger" title="Eliminar" onclick="eliminarLead(${l.id})">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="3 6 5 6 21 6"/><path d="M19 6l-1 14a2 2 0 0 1-2 2H8a2 2 0 0 1-2-2L5 6"/><path d="M10 11v6"/><path d="M14 11v6"/><path d="M9 6V4a1 1 0 0 1 1-1h4a1 1 0 0 1 1 1v2"/></svg>
          </button>
        </div>
      </td>
    </tr>
  `).join('');
}

// Nota: la clase CSS usa "status-Negociacion" (sin tilde) por seguridad de selectores
const styleFix = document.createElement('style');
styleFix.textContent = `.status-Negociacion{ background:rgba(255,171,64,0.15); color:#b3720a; }`;
document.head.appendChild(styleFix);

const estadosCiclo = ['Lead','Contactado','Negociación','Cerrado'];
function ciclarEstadoLead(id){
  const lead = leads.find(l => l.id === id);
  if(!lead) return;
  const idx = estadosCiclo.indexOf(lead.estado);
  lead.estado = estadosCiclo[(idx + 1) % estadosCiclo.length];
  guardarLeads();
  renderTodo();
  mostrarToast(`${lead.nombre} ahora está en "${lead.estado}"`);
}
function eliminarLead(id){
  const lead = leads.find(l => l.id === id);
  if(!lead) return;
  if(!confirm(`¿Eliminar a ${lead.nombre} de la lista de clientes?`)) return;
  leads = leads.filter(l => l.id !== id);
  guardarLeads();
  renderTodo();
  mostrarToast('Lead eliminado');
}

// Formulario: nuevo lead
document.getElementById('btn-new-lead').addEventListener('click', () => abrirModal('modal-lead'));
document.getElementById('form-lead').addEventListener('submit', function(e){
  e.preventDefault();
  const nuevo = {
    id: Date.now(),
    nombre: document.getElementById('lead-nombre').value.trim(),
    empresa: document.getElementById('lead-empresa').value.trim(),
    email: document.getElementById('lead-email').value.trim(),
    telefono: document.getElementById('lead-telefono').value.trim(),
    estado: document.getElementById('lead-estado').value,
    fecha: new Date().toISOString().split('T')[0]
  };
  leads.unshift(nuevo);
  guardarLeads();
  renderTodo();
  this.reset();
  cerrarModal('modal-lead');
  mostrarToast('Lead añadido correctamente');
});

/* ---------- 11. PIPELINE (KANBAN CON DRAG & DROP) ---------- */
const COLUMNAS = ['Lead','Contactado','Negociación','Cerrado'];

function renderKanban(){
  const board = document.getElementById('kanban-board');
  board.innerHTML = COLUMNAS.map(col => {
    const leadsCol = leads.filter(l => l.estado === col);
    return `
      <div class="kanban-col" data-estado="${col}">
        <div class="kanban-col-header">
          <div class="title">${col}</div>
          <div class="count">${leadsCol.length}</div>
        </div>
        <div class="kanban-cards" data-estado="${col}">
          ${leadsCol.map(l => `
            <div class="kanban-card" draggable="true" data-id="${l.id}">
              <div class="name">${l.nombre}</div>
              <div class="company">${l.empresa}</div>
              <div class="date">
                <svg width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg>
                ${formatearFecha(l.fecha)}
              </div>
            </div>
          `).join('')}
        </div>
        <button class="add-card-btn" onclick="abrirModalKanban('${col}')">+ Añadir tarjeta</button>
      </div>
    `;
  }).join('');

  activarDragAndDrop();
}

function activarDragAndDrop(){
  document.querySelectorAll('.kanban-card').forEach(card => {
    card.addEventListener('dragstart', function(){
      kanbanDragId = Number(this.dataset.id);
      this.classList.add('dragging');
    });
    card.addEventListener('dragend', function(){
      this.classList.remove('dragging');
    });
  });

  document.querySelectorAll('.kanban-col').forEach(col => {
    col.addEventListener('dragover', function(e){
      e.preventDefault();
      this.classList.add('drag-over');
    });
    col.addEventListener('dragleave', function(){
      this.classList.remove('drag-over');
    });
    col.addEventListener('drop', function(e){
      e.preventDefault();
      this.classList.remove('drag-over');
      const nuevoEstado = this.dataset.estado;
      const lead = leads.find(l => l.id === kanbanDragId);
      if(lead && lead.estado !== nuevoEstado){
        lead.estado = nuevoEstado;
        guardarLeads();
        renderTodo();
        mostrarToast(`${lead.nombre} movido a "${nuevoEstado}"`);
      }
    });
  });
}

let kanbanColumnaActual = 'Lead';
function abrirModalKanban(col){
  kanbanColumnaActual = col;
  document.getElementById('kanban-estado').value = col;
  abrirModal('modal-kanban');
}
document.getElementById('form-kanban').addEventListener('submit', function(e){
  e.preventDefault();
  const nuevo = {
    id: Date.now(),
    nombre: document.getElementById('kanban-nombre').value.trim(),
    empresa: document.getElementById('kanban-empresa').value.trim(),
    email: '—',
    telefono: '—',
    estado: kanbanColumnaActual,
    fecha: new Date().toISOString().split('T')[0]
  };
  leads.unshift(nuevo);
  guardarLeads();
  renderTodo();
  this.reset();
  cerrarModal('modal-kanban');
  mostrarToast('Tarjeta añadida al pipeline');
});

/* ---------- 12. TAREAS ---------- */
function renderTareas(){
  const list = document.getElementById('task-list');
  if(tasks.length === 0){
    list.innerHTML = `<div class="empty-state">No hay tareas todavía. ¡Añade la primera!</div>`;
    return;
  }
  // Pendientes primero, luego completadas; dentro de cada grupo, por fecha
  const ordenadas = [...tasks].sort((a,b) => {
    if(a.completada !== b.completada) return a.completada ? 1 : -1;
    return a.fecha.localeCompare(b.fecha);
  });

  list.innerHTML = ordenadas.map(t => `
    <div class="task-item ${t.completada ? 'completed' : ''}" data-id="${t.id}">
      <div class="task-checkbox ${t.completada ? 'checked' : ''}" onclick="toggleTarea(${t.id})">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"/></svg>
      </div>
      <div class="task-main">
        <div class="task-title">${t.titulo}</div>
        <div class="task-meta">
          <span><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="8" r="4"/><path d="M4 21v-1a7 7 0 0 1 14 0v1"/></svg>${t.asignado}</span>
          <span><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg>${formatearFecha(t.fecha)}</span>
          <span>${t.completada ? '✅ Completada' : '⏳ Pendiente'}</span>
        </div>
      </div>
      <button class="icon-btn danger" title="Eliminar tarea" onclick="eliminarTarea(${t.id})">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="3 6 5 6 21 6"/><path d="M19 6l-1 14a2 2 0 0 1-2 2H8a2 2 0 0 1-2-2L5 6"/></svg>
      </button>
    </div>
  `).join('');
}

function toggleTarea(id){
  const tarea = tasks.find(t => t.id === id);
  if(!tarea) return;
  tarea.completada = !tarea.completada;
  guardarTasks();
  renderTodo();
  mostrarToast(tarea.completada ? 'Tarea marcada como completada' : 'Tarea marcada como pendiente');
}
function eliminarTarea(id){
  if(!confirm('¿Eliminar esta tarea?')) return;
  tasks = tasks.filter(t => t.id !== id);
  guardarTasks();
  renderTodo();
  mostrarToast('Tarea eliminada');
}

document.getElementById('btn-new-task').addEventListener('click', () => {
  document.getElementById('task-fecha').valueAsDate = new Date();
  abrirModal('modal-task');
});
document.getElementById('form-task').addEventListener('submit', function(e){
  e.preventDefault();
  const nueva = {
    id: Date.now(),
    titulo: document.getElementById('task-titulo').value.trim(),
    asignado: document.getElementById('task-asignado').value,
    fecha: document.getElementById('task-fecha').value,
    completada: false
  };
  tasks.unshift(nueva);
  guardarTasks();
  renderTodo();
  this.reset();
  cerrarModal('modal-task');
  mostrarToast('Tarea añadida correctamente');
});
</script>
</body>
</html>
