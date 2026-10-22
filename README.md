<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gerador de Recibos Universal - Input Criativo</title>
    <!-- Google Fonts & FontAwesome -->
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;600;700;800;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- SDKs do Firebase (Auth & Firestore) -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-firestore-compat.js"></script>

    <style>
        :root {
            --primary-blue: #0f172a;
            --accent-green: #059669;
            --accent-hover: #047857;
            --light-bg: #f8fafc;
            --sidebar-bg: #ffffff;
            --border-color: #cbd5e1;
            --text-dark: #0f172a;
            --text-muted: #64748b;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Montserrat', sans-serif;
        }

        body {
            background-color: #f1f5f9;
            color: var(--text-dark);
            min-height: 100vh;
        }

        /* MODAL DE AUTENTICAÇÃO COM O GOOGLE */
        .auth-overlay {
            position: fixed;
            top: 0; left: 0; width: 100vw; height: 100vh;
            background: rgba(15, 23, 42, 0.85);
            backdrop-filter: blur(5px);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 9999;
        }

        .auth-card {
            background: white;
            border-radius: 12px;
            padding: 30px;
            width: 90%;
            max-width: 420px;
            text-align: center;
            box-shadow: 0 20px 25px -5px rgba(0,0,0,0.3);
        }

        .auth-card h2 {
            font-size: 1.3rem;
            font-weight: 800;
            color: var(--primary-blue);
            margin-bottom: 6px;
        }

        .auth-card p {
            font-size: 0.8rem;
            color: var(--text-muted);
            margin-bottom: 20px;
        }

        .btn-google-login {
            width: 100%;
            background: white;
            border: 1px solid var(--border-color);
            color: var(--text-dark);
            padding: 12px;
            border-radius: 8px;
            font-weight: 700;
            font-size: 0.88rem;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
            transition: background 0.2s;
        }

        .btn-google-login:hover {
            background: #f8fafc;
        }

        .btn-guest-login {
            margin-top: 15px;
            background: none;
            border: none;
            color: var(--text-muted);
            font-size: 0.75rem;
            font-weight: 700;
            cursor: pointer;
            text-decoration: underline;
        }

        /* BARRA DO USUÁRIO LOGADO */
        .user-auth-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: var(--light-bg);
            padding: 10px 12px;
            border-radius: 8px;
            margin-bottom: 12px;
            border: 1px solid var(--border-color);
        }

        .user-auth-info {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 0.75rem;
            font-weight: 700;
            color: var(--primary-blue);
        }

        .user-auth-info img {
            width: 28px;
            height: 28px;
            border-radius: 50%;
        }

        .btn-logout {
            background: none;
            border: none;
            color: #ef4444;
            font-size: 0.75rem;
            font-weight: 800;
            cursor: pointer;
        }

        /* ABAS DE NAVEGAÇÃO LATERAL */
        .navigation-tabs {
            display: flex;
            gap: 6px;
            margin-bottom: 12px;
            border-bottom: 2px solid var(--border-color);
            padding-bottom: 8px;
        }

        .nav-tab-btn {
            flex: 1;
            padding: 8px;
            background: var(--light-bg);
            border: 1px solid var(--border-color);
            border-radius: 6px;
            font-size: 0.72rem;
            font-weight: 800;
            color: var(--text-muted);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 5px;
        }

        .nav-tab-btn.active {
            background: var(--primary-blue);
            color: white;
            border-color: var(--primary-blue);
        }

        .app-container {
            display: flex;
            width: 100%;
            min-height: 100vh;
        }

        /* PAINEL LATERAL ESQUERDO */
        .editor-sidebar {
            width: 440px;
            background-color: var(--sidebar-bg);
            border-right: 1px solid var(--border-color);
            padding: 20px;
            overflow-y: auto;
            height: 100vh;
            position: sticky;
            top: 0;
            flex-shrink: 0;
            box-shadow: 2px 0 10px rgba(0,0,0,0.05);
        }

        .sidebar-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid var(--border-color);
        }

        .sidebar-header h2 {
            font-size: 1.1rem;
            font-weight: 800;
            color: var(--primary-blue);
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .btn-fast-data {
            background-color: #ecfdf5;
            color: var(--accent-green);
            border: 1px solid var(--accent-green);
            padding: 6px 10px;
            border-radius: 6px;
            font-size: 0.72rem;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.2s;
        }

        .btn-fast-data:hover {
            background-color: var(--accent-green);
            color: white;
        }

        .btn-print-main {
            width: 100%;
            background-color: var(--accent-green);
            color: white;
            border: none;
            padding: 12px;
            border-radius: 8px;
            font-size: 0.95rem;
            font-weight: 800;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            margin-bottom: 15px;
            box-shadow: 0 4px 10px rgba(5, 150, 105, 0.25);
            transition: background 0.2s;
        }

        .btn-print-main:hover {
            background-color: var(--accent-hover);
        }

        .action-buttons-row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
            margin-bottom: 15px;
        }

        .btn-secondary-action {
            background: white;
            border: 1px solid var(--border-color);
            padding: 8px;
            border-radius: 6px;
            font-size: 0.72rem;
            font-weight: 700;
            color: var(--text-muted);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
        }

        .btn-secondary-action:hover {
            background-color: #f1f5f9;
            color: var(--text-dark);
        }

        .editor-section {
            background: #fafafa;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 12px;
            margin-bottom: 12px;
        }

        .section-title {
            font-size: 0.75rem;
            font-weight: 800;
            color: var(--primary-blue);
            text-transform: uppercase;
            letter-spacing: 0.5px;
            margin-bottom: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .badge-editable {
            background-color: #d1fae5;
            color: #065f46;
            font-size: 0.6rem;
            padding: 2px 6px;
            border-radius: 4px;
        }

        .form-row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
            margin-bottom: 8px;
        }

        .form-group {
            display: flex;
            flex-direction: column;
            margin-bottom: 8px;
        }

        .form-group.full {
            grid-column: span 2;
        }

        .form-group label {
            font-size: 0.68rem;
            font-weight: 700;
            color: var(--text-muted);
            margin-bottom: 3px;
        }

        .form-group input, .form-group textarea, .form-group select {
            padding: 7px 9px;
            border: 1px solid var(--border-color);
            border-radius: 6px;
            font-size: 0.78rem;
            outline: none;
            transition: border-color 0.2s;
            background-color: white;
        }

        .form-group input:focus, .form-group textarea:focus {
            border-color: var(--accent-green);
        }

        .service-item-editor {
            background-color: white;
            border: 1px dashed var(--border-color);
            border-radius: 6px;
            padding: 8px;
            margin-bottom: 8px;
        }

        .service-item-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 0.68rem;
            font-weight: 800;
            color: var(--accent-green);
            margin-bottom: 6px;
        }

        .btn-remove-item {
            color: #ef4444;
            cursor: pointer;
            border: none;
            background: none;
            font-size: 0.8rem;
        }

        .btn-add-item {
            background: none;
            border: none;
            color: var(--accent-green);
            font-size: 0.75rem;
            font-weight: 800;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 4px;
        }

        /* PAINÉIS DE HISTÓRICO E CLIENTES */
        .list-container {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .list-item-card {
            background: white;
            border: 1px solid var(--border-color);
            padding: 10px;
            border-radius: 6px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .list-item-info h4 {
            font-size: 0.78rem;
            font-weight: 800;
            color: var(--primary-blue);
        }

        .list-item-info p {
            font-size: 0.68rem;
            color: var(--text-muted);
        }

        .btn-card-action {
            background: var(--accent-green);
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 4px;
            font-size: 0.68rem;
            font-weight: 800;
            cursor: pointer;
        }

        /* ÁREA DE PREVIEW (RECIBO A4) */
        .preview-area {
            flex: 1;
            padding: 30px;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            overflow-y: auto;
        }

        .receipt-card {
            width: 800px;
            background: white;
            border-radius: 8px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.06);
            overflow: hidden;
            display: flex;
            flex-direction: column;
            border: 1px solid #e2e8f0;
        }

        .receipt-header {
            background-color: var(--primary-blue);
            color: white;
            padding: 22px 35px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .brand-container {
            display: flex;
            align-items: center;
            gap: 16px;
        }

        .brand-logo-img {
            height: 52px;
            width: auto;
            object-fit: contain;
            filter: drop-shadow(0px 2px 4px rgba(0,0,0,0.3));
        }

        .brand-text h1 {
            font-size: 1.5rem;
            font-weight: 800;
            line-height: 1.1;
            color: white;
        }

        .brand-text p {
            font-size: 0.72rem;
            color: #94a3b8;
            font-weight: 600;
            margin-top: 4px;
        }

        .header-title-box {
            text-align: right;
        }

        .status-badge-top {
            display: inline-block;
            background-color: rgba(255,255,255,0.15);
            color: #34d399;
            font-size: 0.62rem;
            font-weight: 800;
            padding: 3px 8px;
            border-radius: 4px;
            letter-spacing: 0.5px;
            margin-bottom: 4px;
        }

        .header-title-box h2 {
            font-size: 2rem;
            font-weight: 900;
            letter-spacing: 1px;
            line-height: 1;
        }

        .header-title-box p {
            font-size: 0.72rem;
            font-weight: 700;
            color: #cbd5e1;
            margin-top: 4px;
        }

        .receipt-body {
            padding: 22px 35px;
            flex: 1;
        }

        .meta-status-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 12px;
            margin-bottom: 18px;
            font-size: 0.8rem;
        }

        .status-pill {
            background-color: #d1fae5;
            color: #047857;
            padding: 3px 8px;
            border-radius: 10px;
            font-size: 0.68rem;
            font-weight: 800;
        }

        .payer-container {
            background-color: var(--light-bg);
            border-radius: 8px;
            padding: 16px 20px;
            margin-bottom: 18px;
            position: relative;
            border: 1px solid #e2e8f0;
        }

        .section-tag {
            position: absolute;
            top: -10px;
            left: 15px;
            background-color: var(--primary-blue);
            color: white;
            font-size: 0.6rem;
            font-weight: 800;
            padding: 3px 8px;
            border-radius: 4px;
            letter-spacing: 0.5px;
        }

        .payer-title {
            font-size: 0.98rem;
            font-weight: 800;
            color: var(--primary-blue);
            margin-top: 4px;
            margin-bottom: 10px;
            line-height: 1.3;
        }

        .payer-details-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 6px 15px;
            font-size: 0.75rem;
        }

        .payer-detail-item {
            color: var(--text-muted);
        }

        .payer-detail-item strong {
            color: var(--text-dark);
        }

        .declaration-text {
            font-size: 0.8rem;
            line-height: 1.4;
            color: var(--text-dark);
            padding-left: 12px;
            border-left: 3px solid var(--accent-green);
            margin-bottom: 18px;
        }

        .section-header-title {
            display: flex;
            align-items: center;
            gap: 6px;
            font-size: 0.78rem;
            font-weight: 800;
            color: var(--primary-blue);
            margin-bottom: 10px;
        }

        .services-table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 18px;
            border-radius: 6px;
            overflow: hidden;
            border: 1px solid var(--border-color);
        }

        .services-table th {
            background-color: var(--primary-blue);
            color: white;
            font-size: 0.68rem;
            font-weight: 700;
            text-transform: uppercase;
            padding: 8px 12px;
            text-align: left;
        }

        .services-table td {
            padding: 10px 12px;
            font-size: 0.78rem;
            border-bottom: 1px solid var(--border-color);
            background-color: white;
        }

        .text-center { text-align: center; }
        .text-right { text-align: right; }

        .total-box {
            display: flex;
            background-color: var(--primary-blue);
            border-radius: 6px;
            overflow: hidden;
            margin-bottom: 20px;
        }

        .total-label {
            flex: 1;
            padding: 12px 18px;
            display: flex;
            align-items: center;
            gap: 10px;
            color: white;
        }

        .total-label i {
            font-size: 1.3rem;
            color: var(--accent-green);
        }

        .total-label-text h4 {
            font-size: 0.75rem;
            font-weight: 800;
        }

        .total-label-text p {
            font-size: 0.68rem;
            color: #34d399;
        }

        .total-amount-side {
            background-color: var(--accent-green);
            color: white;
            font-size: 1.5rem;
            font-weight: 900;
            padding: 12px 24px;
            display: flex;
            align-items: center;
            justify-content: center;
            clip-path: polygon(10px 0, 100% 0, 100% 100%, 0 100%);
        }

        .bottom-grid {
            display: grid;
            grid-template-columns: 1.2fr 0.8fr;
            gap: 15px;
        }

        .payment-info-card {
            background-color: var(--light-bg);
            border-radius: 6px;
            padding: 14px;
            border: 1px solid var(--border-color);
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 12px;
        }

        .payment-details-col {
            flex: 1;
        }

        .payment-info-title {
            display: flex;
            align-items: center;
            gap: 6px;
            font-size: 0.75rem;
            font-weight: 800;
            color: var(--primary-blue);
            margin-bottom: 8px;
        }

        .payment-details p {
            font-size: 0.72rem;
            margin-bottom: 3px;
            color: var(--text-muted);
        }

        .payment-details strong {
            color: var(--text-dark);
        }

        .payment-details .pix-key {
            color: var(--accent-green);
            font-weight: 700;
            word-break: break-all;
        }

        .pix-qrcode-box {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            background: white;
            padding: 6px;
            border-radius: 6px;
            border: 1px solid var(--border-color);
            text-align: center;
            min-width: 105px;
        }

        .pix-qrcode-box img {
            width: 90px;
            height: 90px;
            object-fit: contain;
        }

        .pix-qrcode-box span {
            font-size: 0.55rem;
            font-weight: 800;
            color: var(--primary-blue);
            margin-top: 3px;
            text-transform: uppercase;
        }

        .signature-box {
            display: flex;
            flex-direction: column;
            justify-content: flex-end;
            align-items: center;
            text-align: center;
            padding: 10px;
        }

        .signature-line {
            width: 80%;
            border-top: 1px solid #94a3b8;
            margin-bottom: 6px;
        }

        .signature-label-top {
            font-size: 0.6rem;
            font-style: italic;
            color: #94a3b8;
            margin-bottom: 3px;
        }

        .signature-name {
            font-size: 0.8rem;
            font-weight: 800;
            color: var(--primary-blue);
        }

        .signature-role {
            font-size: 0.68rem;
            color: var(--text-muted);
        }

        .signature-company {
            font-size: 0.68rem;
            font-weight: 700;
            color: var(--accent-green);
        }

        .receipt-footer {
            background-color: var(--primary-blue);
            color: white;
            padding: 10px 35px;
            font-size: 0.62rem;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        @media print {
            body { background: white; }
            .editor-sidebar, .auth-overlay { display: none !important; }
            .preview-area { padding: 0; overflow: visible; }
            .receipt-card { box-shadow: none; width: 100%; border-radius: 0; border: none; }
            * { -webkit-print-color-adjust: exact !important; print-color-adjust: exact !important; }
        }
    </style>
</head>
<body>

    <!-- MODAL DE AUTENTICAÇÃO GOOGLE -->
    <div id="authModal" class="auth-overlay">
        <div class="auth-card">
            <h2>Input Criativo</h2>
            <p>Acesse com sua conta do Google para gerar, salvar e consultar o histórico de recibos e clientes.</p>
            
            <button class="btn-google-login" onclick="loginWithGoogle()">
                <i class="fa-brands fa-google" style="color: #ea4335;"></i> Entrar com a Conta Google
            </button>

            <button class="btn-guest-login" onclick="enterAsGuest()">Entrar como Convidado (Modo Local)</button>
        </div>
    </div>

    <div class="app-container">
        
        <!-- PAINEL LATERAL ESQUERDO -->
        <aside class="editor-sidebar">
            <!-- BARRA DE USUÁRIO -->
            <div class="user-auth-bar">
                <div class="user-auth-info">
                    <img id="userAvatar" src="https://ui-avatars.com/api/?name=User&background=059669&color=fff" alt="User">
                    <span id="userName">Usuário</span>
                </div>
                <button class="btn-logout" onclick="logoutUser()"><i class="fa-solid fa-right-from-bracket"></i> Sair</button>
            </div>

            <!-- NAVEGAÇÃO DE ABAS -->
            <div class="navigation-tabs">
                <button id="tabBtnEditor" class="nav-tab-btn active" onclick="switchNavTab('editor')"><i class="fa-solid fa-pen-to-square"></i> Editor</button>
                <button id="tabBtnHistory" class="nav-tab-btn" onclick="switchNavTab('history')"><i class="fa-solid fa-clock-rotate-left"></i> Histórico</button>
                <button id="tabBtnClients" class="nav-tab-btn" onclick="switchNavTab('clients')"><i class="fa-solid fa-users"></i> Clientes</button>
            </div>

            <!-- ABA: EDITOR -->
            <div id="tabContentEditor">
                <div class="sidebar-header">
                    <h2><i class="fa-solid fa-file-invoice-dollar" style="color: var(--accent-green);"></i> Gerador de Recibos</h2>
                    <button class="btn-fast-data" onclick="fillFunadifPayer()"><i class="fa-solid fa-bolt"></i> FUNADIF Pagador</button>
                </div>

                <button class="btn-print-main" onclick="saveAndPrint()">
                    <i class="fa-solid fa-print"></i> Salvar & Imprimir PDF
                </button>

                <div class="action-buttons-row">
                    <button class="btn-secondary-action" onclick="swapRoles()"><i class="fa-solid fa-arrow-rotate-left"></i> Inverter Roles</button>
                    <button class="btn-secondary-action" onclick="clearServices()"><i class="fa-solid fa-eraser"></i> Limpar Itens</button>
                </div>

                <!-- Dados do Emissor -->
                <div class="editor-section">
                    <div class="section-title">
                        <span><i class="fa-solid fa-building"></i> Dados do Emissor & Logo</span>
                        <span class="badge-editable">Editável</span>
                    </div>
                    <div class="form-group full">
                        <label>Logomarca da Empresa</label>
                        <input type="file" id="inLogoFile" accept="image/*" onchange="loadCustomLogo(event)">
                    </div>
                    <div class="form-group full">
                        <label>Nome / Empresa</label>
                        <input type="text" id="inIssuerName" value="INPUT CRIATIVO">
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label>CNPJ / CPF</label>
                            <input type="text" id="inIssuerCnpj" value="68.339.728/0001-86">
                        </div>
                        <div class="form-group">
                            <label>Titular do Pix</label>
                            <input type="text" id="inIssuerHolder" value="GEORGE BATISTA">
                        </div>
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label>Chave Pix</label>
                            <input type="text" id="inIssuerPix" value="inputcriativoficial@gmail.com">
                        </div>
                        <div class="form-group">
                            <label>Banco</label>
                            <input type="text" id="inIssuerBank" value="Nubank">
                        </div>
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label>E-mail</label>
                            <input type="text" id="inIssuerEmail" value="inputcriativoficial@gmail.com">
                        </div>
                        <div class="form-group">
                            <label>Website</label>
                            <input type="text" id="inIssuerSite" value="www.inputcriativo.com.br">
                        </div>
                    </div>
                </div>

                <!-- Identificação e Datas -->
                <div class="editor-section">
                    <div class="section-title">
                        <span><i class="fa-solid fa-hashtag"></i> Identificação e Status</span>
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label>Nº do Recibo</label>
                            <input type="text" id="inReceiptNum" value="REC-2026/001">
                        </div>
                        <div class="form-group">
                            <label>Data Emissão</label>
                            <input type="date" id="inEmitDate" value="2026-10-21">
                        </div>
                    </div>
                    <div class="form-group full">
                        <label>Status do Pagamento</label>
                        <select id="inStatusSelect" onchange="updateStatusPill(this.value)">
                            <option value="QUITADO">QUITADO</option>
                            <option value="PENDENTE">PENDENTE</option>
                            <option value="PROCESSANDO">EM PROCESSAMENTO</option>
                        </select>
                    </div>
                </div>

                <!-- Dados do Pagador -->
                <div class="editor-section">
                    <div class="section-title">
                        <span><i class="fa-solid fa-address-card"></i> Dados do Pagador (Cliente)</span>
                        <button class="btn-fast-data" onclick="saveCurrentClient()"><i class="fa-solid fa-user-plus"></i> Salvar Cliente</button>
                    </div>
                    <div class="form-group full">
                        <label>Razão Social / Nome</label>
                        <input type="text" id="inPayerName" value="FUNDACAO DE APOIO AO DESENVOLVIMENTO DO INSTITUTO FEDERAL DE EDUCACAO, CIENCIA E TECNOLOGIA DE MATO GROSSO - FUNDIF">
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label>Nome Fantasia</label>
                            <input type="text" id="inPayerTrade" value="FUNADIF">
                        </div>
                        <div class="form-group">
                            <label>CNPJ / CPF</label>
                            <input type="text" id="inPayerCnpj" value="52.306.613/0001-55">
                        </div>
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label>Inscrição Estadual</label>
                            <input type="text" id="inPayerIe" value="Isento">
                        </div>
                        <div class="form-group">
                            <label>Telefone</label>
                            <input type="text" id="inPayerPhone" value="(65) 9681-5093">
                        </div>
                    </div>
                    <div class="form-group full">
                        <label>Endereço Completo</label>
                        <input type="text" id="inPayerAddress" value="Av. Marechal Deodoro, nº 1419, Centro-Norte, Cuiabá/MT - CEP: 78.005-100">
                    </div>
                    <div class="form-group full">
                        <label>E-mail</label>
                        <input type="email" id="inPayerEmail" value="projetos@funadif.org.br">
                    </div>
                </div>

                <!-- Tabela de Serviços -->
                <div class="editor-section">
                    <div class="section-title">
                        <span><i class="fa-solid fa-list-check"></i> Serviços / Produtos</span>
                        <button class="btn-add-item" onclick="addServiceItem()"><i class="fa-solid fa-plus-circle"></i> Adicionar</button>
                    </div>
                    <div id="servicesEditorContainer"></div>
                </div>

                <!-- Imagem QR Code & Assinatura -->
                <div class="editor-section">
                    <div class="section-title">
                        <span><i class="fa-solid fa-qrcode"></i> QR Code & Assinatura</span>
                    </div>
                    <div class="form-group full">
                        <label>Arquivo Imagem QR Code</label>
                        <input type="file" id="inQrCodeFile" accept="image/*" onchange="loadCustomQrCode(event)">
                    </div>
                    <div class="form-group full">
                        <label>Nome do Signatário</label>
                        <input type="text" id="inSignName" value="George Batista">
                    </div>
                    <div class="form-group full">
                        <label>Cargo / Função</label>
                        <input type="text" id="inSignRole" value="Diretor de Projetos">
                    </div>
                </div>
            </div>

            <!-- ABA: HISTÓRICO DE RECIBOS -->
            <div id="tabContentHistory" style="display: none;">
                <div class="editor-section">
                    <div class="section-title">
                        <span><i class="fa-solid fa-clock-rotate-left"></i> Histórico de Recibos Salvos</span>
                    </div>
                    <div id="historyContainer" class="list-container">
                        <p style="font-size: 0.7rem; color: var(--text-muted);">Nenhum recibo emitido ainda.</p>
                    </div>
                </div>
            </div>

            <!-- ABA: CLIENTES CADASTRADOS -->
            <div id="tabContentClients" style="display: none;">
                <div class="editor-section">
                    <div class="section-title">
                        <span><i class="fa-solid fa-users"></i> Clientes Cadastrados</span>
                    </div>
                    <div id="clientsContainer" class="list-container">
                        <p style="font-size: 0.7rem; color: var(--text-muted);">Nenhum cliente cadastrado.</p>
                    </div>
                </div>
            </div>
        </aside>

        <!-- PREVIEW DO RECIBO -->
        <main class="preview-area">
            <div class="receipt-card">
                
                <header class="receipt-header">
                    <div class="brand-container">
                        <img id="outBrandLogoImg" class="brand-logo-img" src="banner principal.png" alt="Input Criativo" onerror="this.onerror=null; this.src='image/banner principal.png';">
                        <div class="brand-text">
                            <h1 id="outHeaderBrandName">INPUT CRIATIVO</h1>
                            <p id="outHeaderSubtext">Comprovante de Pagamento e Quitação de Serviços</p>
                        </div>
                    </div>
                    <div class="header-title-box">
                        <div class="status-badge-top">COMPROVANTE OFICIAL</div>
                        <h2>RECIBO</h2>
                        <p>Nº: <span id="outReceiptNum">REC-2026/001</span></p>
                    </div>
                </header>

                <div class="receipt-body">
                    
                    <div class="meta-status-bar">
                        <div class="date-info">
                            <i class="fa-regular fa-calendar-check"></i> Data de Emissão: <strong id="outEmitDate">21/10/2026</strong>
                        </div>
                        <div style="display: flex; align-items: center; gap: 6px; font-weight: 700;">
                            <i class="fa-solid fa-shield-halved" style="color: var(--accent-green);"></i>
                            Status: <span class="status-pill" id="outStatusPill">QUITADO</span>
                        </div>
                    </div>

                    <div class="payer-container">
                        <div class="section-tag">DADOS DO PAGADOR (CLIENTE)</div>
                        <h3 class="payer-title" id="outPayerName">FUNDACAO DE APOIO AO DESENVOLVIMENTO DO INSTITUTO FEDERAL DE EDUCACAO, CIENCIA E TECNOLOGIA DE MATO GROSSO - FUNDIF</h3>
                        <div class="payer-details-grid">
                            <div class="payer-detail-item"><strong>Nome Fantasia:</strong> <span id="outPayerTrade">FUNADIF</span></div>
                            <div class="payer-detail-item"><strong>CNPJ/CPF:</strong> <span id="outPayerCnpj">52.306.613/0001-55</span></div>
                            <div class="payer-detail-item"><strong>Inscrição Estadual:</strong> <span id="outPayerIe">Isento</span></div>
                            <div class="payer-detail-item"><strong>Telefone:</strong> <span id="outPayerPhone">(65) 9681-5093</span></div>
                            <div class="payer-detail-item" style="grid-column: span 2;"><strong>Endereço:</strong> <span id="outPayerAddress">Av. Marechal Deodoro, nº 1419, Centro-Norte, Cuiabá/MT - CEP: 78.005-100</span></div>
                            <div class="payer-detail-item" style="grid-column: span 2;"><strong>E-mail:</strong> <span id="outPayerEmail">projetos@funadif.org.br</span></div>
                        </div>
                    </div>

                    <div class="declaration-text">
                        Recebemos da empresa/pessoa acima identificada a quantia discriminada neste documento, referente à prestação dos serviços técnicos ou fornecimento de produtos abaixo relacionados.
                    </div>

                    <div class="section-header-title">
                        <i class="fa-solid fa-list-check" style="color: var(--accent-green);"></i>
                        DESCRIÇÃO DOS SERVIÇOS / PRODUTOS
                    </div>

                    <table class="services-table">
                        <thead>
                            <tr>
                                <th width="5%" class="text-center">#</th>
                                <th width="60%">Descrição</th>
                                <th width="10%" class="text-center">Qtd</th>
                                <th width="12%" class="text-right">Valor Unit.</th>
                                <th width="13%" class="text-right">Total</th>
                            </tr>
                        </thead>
                        <tbody id="outServicesTable"></tbody>
                    </table>

                    <div class="total-box">
                        <div class="total-label">
                            <i class="fa-solid fa-wallet"></i>
                            <div class="total-label-text">
                                <h4>VALOR TOTAL COMPENSADO</h4>
                                <p><span id="outTotalText">R$ 1.400,00</span> (Valor Declarado)</p>
                            </div>
                        </div>
                        <div class="total-amount-side" id="outTotalBanner">
                            R$ 1.400,00
                        </div>
                    </div>

                    <div class="bottom-grid">
                        <div class="payment-info-card">
                            <div class="payment-details-col">
                                <div class="payment-info-title">
                                    <i class="fa-solid fa-square-check" style="color: var(--accent-green);"></i>
                                    DADOS DE PAGAMENTO PIX
                                </div>
                                <div class="payment-details">
                                    <p><strong>Titular:</strong> <span id="outIssuerHolder">GEORGE BATISTA</span></p>
                                    <p><strong>Chave Pix:</strong> <span class="pix-key" id="outIssuerPix">inputcriativoficial@gmail.com</span></p>
                                    <p><strong>Banco:</strong> <span id="outIssuerBank">Nubank</span></p>
                                </div>
                            </div>
                            
                            <div class="pix-qrcode-box">
                                <img id="outQrCodeImg" src="qrcod.jpeg" alt="QR Code Pix" onerror="this.onerror=null; this.src='image/qrcod.jpeg';">
                                <span>PAGUE VIA QR CODE</span>
                            </div>
                        </div>

                        <div class="signature-box">
                            <span class="signature-label-top">Assinado eletronicamente</span>
                            <div class="signature-line"></div>
                            <div class="signature-name" id="outSignName">George Batista</div>
                            <div class="signature-role" id="outSignRole">Diretor de Projetos</div>
                            <div class="signature-company" id="outSignCompany">INPUT CRIATIVO</div>
                        </div>
                    </div>

                </div>

                <footer class="receipt-footer">
                    <div><strong id="outFooterBrand">INPUT CRIATIVO</strong> • CNPJ/CPF: <span id="outFooterCnpj">68.339.728/0001-86</span></div>
                    <div>
                        <span id="outFooterEmail">inputcriativoficial@gmail.com</span> • 
                        <span id="outFooterSite">www.inputcriativo.com.br</span>
                    </div>
                </footer>

            </div>
        </main>
    </div>

    <script>
        // CONFIGURAÇÃO DO FIREBASE (Substitua pelas suas chaves do Firebase se desejar salvar no banco Firestore)
        const firebaseConfig = {
            apiKey: "AIzaSyDemoConfigKeyForInputCriativoProd00",
            authDomain: "input-criativo-recibos.firebaseapp.com",
            projectId: "input-criativo-recibos",
            storageBucket: "input-criativo-recibos.appspot.com",
            appId: "1:000000000000:web:inputcriativo000"
        };

        let auth = null;
        let googleProvider = null;
        let currentUser = null;
        let receiptHistory = [];
        let clientsList = [];

        try {
            firebase.initializeApp(firebaseConfig);
            auth = firebase.auth();
            googleProvider = new firebase.auth.GoogleAuthProvider();

            auth.onAuthStateChanged((user) => {
                if (user) {
                    currentUser = {
                        name: user.displayName || user.email.split('@')[0],
                        email: user.email,
                        photo: user.photoURL || `https://ui-avatars.com/api/?name=${encodeURIComponent(user.displayName || 'User')}&background=059669&color=fff`
                    };
                    updateUserUI();
                    document.getElementById('authModal').style.display = 'none';
                    loadData();
                }
            });
        } catch (e) {
            console.log("SDK Firebase não configurado; rodando em modo local.");
        }

        function loginWithGoogle() {
            if (auth && googleProvider) {
                auth.signInWithPopup(googleProvider).catch(() => {
                    auth.signInWithRedirect(googleProvider);
                });
            } else {
                enterAsGuest();
            }
        }

        function enterAsGuest() {
            currentUser = {
                name: "Convidado",
                email: "convidado@local",
                photo: "https://ui-avatars.com/api/?name=Convidado&background=059669&color=fff"
            };
            updateUserUI();
            document.getElementById('authModal').style.display = 'none';
            loadData();
        }

        function logoutUser() {
            if (auth) auth.signOut();
            currentUser = null;
            document.getElementById('authModal').style.display = 'flex';
        }

        function updateUserUI() {
            if (!currentUser) return;
            document.getElementById('userName').textContent = currentUser.name;
            document.getElementById('userAvatar').src = currentUser.photo;
        }

        function switchNavTab(tab) {
            document.getElementById('tabContentEditor').style.display = tab === 'editor' ? 'block' : 'none';
            document.getElementById('tabContentHistory').style.display = tab === 'history' ? 'block' : 'none';
            document.getElementById('tabContentClients').style.display = tab === 'clients' ? 'block' : 'none';

            document.getElementById('tabBtnEditor').classList.toggle('active', tab === 'editor');
            document.getElementById('tabBtnHistory').classList.toggle('active', tab === 'history');
            document.getElementById('tabBtnClients').classList.toggle('active', tab === 'clients');
        }

        /* LÓGICA DO RECIBO ORIGINAL DA INPUT CRIATIVO */
        let servicesData = [
            { desc: "Locação de Projetor Multimídia (Diárias)\nPrestação de serviços de locação de 01 (um) projetor multimídia por 5 (cinco) diárias, no valor unitário de R$ 280,00 por diária.", qty: 5, unit: 280.00 }
        ];

        function formatBRL(val) {
            return val.toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' });
        }

        function formatDate(dateStr) {
            if(!dateStr) return "";
            const [y, m, d] = dateStr.split('-');
            return `${d}/${m}/${y}`;
        }

        function renderServicesEditor() {
            const container = document.getElementById('servicesEditorContainer');
            container.innerHTML = '';

            servicesData.forEach((item, index) => {
                const itemEl = document.createElement('div');
                itemEl.className = 'service-item-editor';
                itemEl.innerHTML = `
                    <div class="service-item-header">
                        <span>ITEM #${index + 1}</span>
                        ${servicesData.length > 1 ? `<button class="btn-remove-item" onclick="removeServiceItem(${index})"><i class="fa-solid fa-trash-can"></i></button>` : ''}
                    </div>
                    <div class="form-group full">
                        <textarea rows="2" oninput="updateItemDesc(${index}, this.value)" placeholder="Descrição do Serviço">${item.desc}</textarea>
                    </div>
                    <div class="form-row">
                        <div class="form-group">
                            <label>Qtd</label>
                            <input type="number" value="${item.qty}" min="1" oninput="updateItemQty(${index}, this.value)">
                        </div>
                        <div class="form-group">
                            <label>Valor Unit. (R$)</label>
                            <input type="number" step="0.01" value="${item.unit}" oninput="updateItemUnit(${index}, this.value)">
                        </div>
                    </div>
                `;
                container.appendChild(itemEl);
            });
            renderPreviewTable();
        }

        function renderPreviewTable() {
            const tbody = document.getElementById('outServicesTable');
            tbody.innerHTML = '';
            let totalGeneral = 0;

            servicesData.forEach((item, index) => {
                const totalItem = (item.qty || 0) * (item.unit || 0);
                totalGeneral += totalItem;

                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="text-center" style="color: var(--text-muted);">${index + 1}</td>
                    <td style="white-space: pre-line; font-weight: 600;">${item.desc || '—'}</td>
                    <td class="text-center">${item.qty || 0}</td>
                    <td class="text-right">${formatBRL(item.unit || 0)}</td>
                    <td class="text-right"><strong>${formatBRL(totalItem)}</strong></td>
                `;
                tbody.appendChild(tr);
            });

            const formattedTotal = formatBRL(totalGeneral);
            document.getElementById('outTotalText').textContent = formattedTotal;
            document.getElementById('outTotalBanner').textContent = formattedTotal;
        }

        function addServiceItem() {
            servicesData.push({ desc: "", qty: 1, unit: 0.00 });
            renderServicesEditor();
        }

        function removeServiceItem(index) {
            servicesData.splice(index, 1);
            renderServicesEditor();
        }

        function updateItemDesc(index, val) {
            servicesData[index].desc = val;
            renderPreviewTable();
        }

        function updateItemQty(index, val) {
            servicesData[index].qty = parseFloat(val) || 0;
            renderPreviewTable();
        }

        function updateItemUnit(index, val) {
            servicesData[index].unit = parseFloat(val) || 0;
            renderPreviewTable();
        }

        function bindInput(inputId, outputIds, transformFn) {
            const input = document.getElementById(inputId);
            const targets = Array.isArray(outputIds) ? outputIds : [outputIds];
            
            input.addEventListener('input', () => {
                targets.forEach(id => {
                    const output = document.getElementById(id);
                    if(output) output.textContent = transformFn ? transformFn(input.value) : input.value;
                });
            });
        }

        bindInput('inIssuerName', ['outHeaderBrandName', 'outSignCompany', 'outFooterBrand']);
        bindInput('inIssuerCnpj', 'outFooterCnpj');
        bindInput('inIssuerHolder', 'outIssuerHolder');
        bindInput('inIssuerPix', 'outIssuerPix');
        bindInput('inIssuerBank', 'outIssuerBank');
        bindInput('inIssuerEmail', 'outFooterEmail');
        bindInput('inIssuerSite', 'outFooterSite');

        bindInput('inReceiptNum', 'outReceiptNum');
        bindInput('inEmitDate', 'outEmitDate', formatDate);
        bindInput('inPayerName', 'outPayerName');
        bindInput('inPayerTrade', 'outPayerTrade');
        bindInput('inPayerCnpj', 'outPayerCnpj');
        bindInput('inPayerIe', 'outPayerIe');
        bindInput('inPayerPhone', 'outPayerPhone');
        bindInput('inPayerAddress', 'outPayerAddress');
        bindInput('inPayerEmail', 'outPayerEmail');
        bindInput('inSignName', 'outSignName');
        bindInput('inSignRole', 'outSignRole');

        function updateStatusPill(val) {
            const pill = document.getElementById('outStatusPill');
            pill.textContent = val;
            if(val === 'QUITADO') {
                pill.style.backgroundColor = '#d1fae5';
                pill.style.color = '#047857';
            } else if(val === 'PENDENTE') {
                pill.style.backgroundColor = '#fee2e2';
                pill.style.color = '#b91c1c';
            } else {
                pill.style.backgroundColor = '#fef3c7';
                pill.style.color = '#b45309';
            }
        }

        function fillFunadifPayer() {
            document.getElementById('inPayerName').value = "FUNDACAO DE APOIO AO DESENVOLVIMENTO DO INSTITUTO FEDERAL DE EDUCACAO, CIENCIA E TECNOLOGIA DE MATO GROSSO - FUNDIF";
            document.getElementById('inPayerTrade').value = "FUNADIF";
            document.getElementById('inPayerCnpj').value = "52.306.613/0001-55";
            document.getElementById('inPayerIe').value = "Isento";
            document.getElementById('inPayerPhone').value = "(65) 9681-5093";
            document.getElementById('inPayerAddress').value = "Av. Marechal Deodoro, nº 1419, Centro-Norte, Cuiabá/MT - CEP: 78.005-100";
            document.getElementById('inPayerEmail').value = "projetos@funadif.org.br";
            
            ['inPayerName', 'inPayerTrade', 'inPayerCnpj', 'inPayerIe', 'inPayerPhone', 'inPayerAddress', 'inPayerEmail'].forEach(id => {
                document.getElementById(id).dispatchEvent(new Event('input'));
            });
        }

        function swapRoles() {
            const tempName = document.getElementById('inIssuerName').value;
            const tempCnpj = document.getElementById('inIssuerCnpj').value;

            document.getElementById('inIssuerName').value = document.getElementById('inPayerName').value;
            document.getElementById('inIssuerCnpj').value = document.getElementById('inPayerCnpj').value;

            document.getElementById('inPayerName').value = tempName;
            document.getElementById('inPayerCnpj').value = tempCnpj;

            ['inIssuerName', 'inIssuerCnpj', 'inPayerName', 'inPayerCnpj'].forEach(id => {
                document.getElementById(id).dispatchEvent(new Event('input'));
            });
        }

        function loadCustomLogo(event) {
            const file = event.target.files[0];
            if(file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    document.getElementById('outBrandLogoImg').src = e.target.result;
                };
                reader.readAsDataURL(file);
            }
        }

        function loadCustomQrCode(event) {
            const file = event.target.files[0];
            if(file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    document.getElementById('outQrCodeImg').src = e.target.result;
                };
                reader.readAsDataURL(file);
            }
        }

        function clearServices() {
            servicesData = [{ desc: "", qty: 1, unit: 0.00 }];
            renderServicesEditor();
        }

        /* GESTÃO DE HISTÓRICO E CLIENTES */
        function saveAndPrint() {
            const receipt = {
                id: Date.now(),
                num: document.getElementById('inReceiptNum').value,
                date: document.getElementById('inEmitDate').value,
                status: document.getElementById('inStatusSelect').value,
                issuer: {
                    name: document.getElementById('inIssuerName').value,
                    cnpj: document.getElementById('inIssuerCnpj').value,
                    pix: document.getElementById('inIssuerPix').value,
                    bank: document.getElementById('inIssuerBank').value
                },
                payer: {
                    name: document.getElementById('inPayerName').value,
                    trade: document.getElementById('inPayerTrade').value,
                    cnpj: document.getElementById('inPayerCnpj').value,
                    ie: document.getElementById('inPayerIe').value,
                    phone: document.getElementById('inPayerPhone').value,
                    address: document.getElementById('inPayerAddress').value,
                    email: document.getElementById('inPayerEmail').value
                },
                services: JSON.parse(JSON.stringify(servicesData)),
                total: document.getElementById('outTotalBanner').textContent
            };

            receiptHistory.unshift(receipt);
            saveDataLocally('recibos', receiptHistory);
            renderHistoryUI();
            window.print();
        }

        function loadReceiptToEditor(id) {
            const rec = receiptHistory.find(r => r.id === id);
            if (!rec) return;

            document.getElementById('inReceiptNum').value = rec.num;
            document.getElementById('inEmitDate').value = rec.date;
            document.getElementById('inStatusSelect').value = rec.status || 'QUITADO';

            document.getElementById('inPayerName').value = rec.payer.name;
            document.getElementById('inPayerTrade').value = rec.payer.trade;
            document.getElementById('inPayerCnpj').value = rec.payer.cnpj;
            document.getElementById('inPayerIe').value = rec.payer.ie;
            document.getElementById('inPayerPhone').value = rec.payer.phone;
            document.getElementById('inPayerAddress').value = rec.payer.address;
            document.getElementById('inPayerEmail').value = rec.payer.email;

            servicesData = JSON.parse(JSON.stringify(rec.services));
            renderServicesEditor();

            ['inReceiptNum', 'inEmitDate', 'inPayerName', 'inPayerTrade', 'inPayerCnpj', 'inPayerIe', 'inPayerPhone', 'inPayerAddress', 'inPayerEmail'].forEach(inputId => {
                document.getElementById(inputId).dispatchEvent(new Event('input'));
            });

            updateStatusPill(document.getElementById('inStatusSelect').value);
            switchNavTab('editor');
        }

        function saveCurrentClient() {
            const client = {
                id: Date.now(),
                name: document.getElementById('inPayerName').value,
                trade: document.getElementById('inPayerTrade').value,
                cnpj: document.getElementById('inPayerCnpj').value,
                ie: document.getElementById('inPayerIe').value,
                phone: document.getElementById('inPayerPhone').value,
                address: document.getElementById('inPayerAddress').value,
                email: document.getElementById('inPayerEmail').value
            };

            clientsList.unshift(client);
            saveDataLocally('clientes', clientsList);
            renderClientsUI();
            alert('Cliente salvo com sucesso!');
        }

        function loadClientToEditor(id) {
            const cli = clientsList.find(c => c.id === id);
            if (!cli) return;

            document.getElementById('inPayerName').value = cli.name;
            document.getElementById('inPayerTrade').value = cli.trade;
            document.getElementById('inPayerCnpj').value = cli.cnpj;
            document.getElementById('inPayerIe').value = cli.ie;
            document.getElementById('inPayerPhone').value = cli.phone;
            document.getElementById('inPayerAddress').value = cli.address;
            document.getElementById('inPayerEmail').value = cli.email;

            ['inPayerName', 'inPayerTrade', 'inPayerCnpj', 'inPayerIe', 'inPayerPhone', 'inPayerAddress', 'inPayerEmail'].forEach(inputId => {
                document.getElementById(inputId).dispatchEvent(new Event('input'));
            });

            switchNavTab('editor');
        }

        function saveDataLocally(type, data) {
            const userKey = currentUser ? currentUser.email : 'guest';
            localStorage.setItem(`ic_${type}_${userKey}`, JSON.stringify(data));
        }

        function loadData() {
            const userKey = currentUser ? currentUser.email : 'guest';
            
            const recData = localStorage.getItem(`ic_recibos_${userKey}`);
            receiptHistory = recData ? JSON.parse(recData) : [];
            
            const cliData = localStorage.getItem(`ic_clientes_${userKey}`);
            clientsList = cliData ? JSON.parse(cliData) : [];

            renderHistoryUI();
            renderClientsUI();
        }

        function renderHistoryUI() {
            const container = document.getElementById('historyContainer');
            container.innerHTML = '';

            if (receiptHistory.length === 0) {
                container.innerHTML = '<p style="font-size: 0.7rem; color: var(--text-muted);">Nenhum recibo emitido ainda.</p>';
                return;
            }

            receiptHistory.forEach((item) => {
                const card = document.createElement('div');
                card.className = 'list-item-card';
                card.innerHTML = `
                    <div class="list-item-info">
                        <h4>${item.num} - ${item.payer.name.substring(0, 16)}...</h4>
                        <p>${formatDate(item.date)} • <strong>${item.total}</strong></p>
                    </div>
                    <button class="btn-card-action" onclick="loadReceiptToEditor(${item.id})"><i class="fa-solid fa-print"></i> 2ª Via</button>
                `;
                container.appendChild(card);
            });
        }

        function renderClientsUI() {
            const container = document.getElementById('clientsContainer');
            container.innerHTML = '';

            if (clientsList.length === 0) {
                container.innerHTML = '<p style="font-size: 0.7rem; color: var(--text-muted);">Nenhum cliente cadastrado.</p>';
                return;
            }

            clientsList.forEach((cli) => {
                const card = document.createElement('div');
                card.className = 'list-item-card';
                card.innerHTML = `
                    <div class="list-item-info">
                        <h4>${cli.trade || cli.name.substring(0, 20)}</h4>
                        <p>${cli.cnpj || cli.phone}</p>
                    </div>
                    <button class="btn-card-action" onclick="loadClientToEditor(${cli.id})"><i class="fa-solid fa-user-check"></i> Usar</button>
                `;
                container.appendChild(card);
            });
        }

        renderServicesEditor();
        document.getElementById('outEmitDate').textContent = formatDate(document.getElementById('inEmitDate').value);
    </script>
</body>
</html>
