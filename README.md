<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DentStat Makala - Plateforme de Recherche Extractions Dentaires</title>
    <meta name="description" content="Plateforme de collecte de données cliniques pour la recherche sur les extractions dentaires à l'HGR Makala.">
    
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;700;800&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database-compat.js"></script>
    
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <style>
        :root {
            --primary: #00897b;
            --primary-dark: #00695c;
            --primary-light: #e0f2f1;
            --accent: #ffb300;
            --bg: #f5f7fa;
            --card-bg: #ffffff;
            --text-main: #2d3748;
            --text-sec: #718096;
            --border: #edf2f7;
            --radius-lg: 24px;
            --radius-md: 16px;
            --radius-sm: 12px;
            --shadow-sm: 0 4px 6px -1px rgba(0, 0, 0, 0.05);
            --shadow-lg: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
            --transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        * { box-sizing: border-box; }

        body { 
            font-family: 'Inter', system-ui, -apple-system, sans-serif; 
            background: var(--bg); 
            margin: 0; 
            padding: 20px; 
            color: var(--text-main);
            line-height: 1.6;
            -webkit-font-smoothing: antialiased;
        }

        .app-shell { 
            max-width: 1200px; 
            margin: 0 auto; 
            background: var(--card-bg); 
            border-radius: var(--radius-lg); 
            box-shadow: var(--shadow-lg); 
            overflow: hidden; 
            min-height: 90vh; 
            display: flex; 
            flex-direction: column;
            border: 1px solid rgba(0,0,0,0.02);
        }

        /* HEADER PREMIUM */
        .app-header { 
            background: linear-gradient(135deg, var(--primary-dark), var(--primary)); 
            padding: 40px 60px; 
            text-align: center;
            color: white;
            position: relative;
            overflow: hidden;
        }
        .app-header::before {
            content: "";
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle, rgba(255,255,255,0.1) 0%, transparent 70%);
            animation: pulse-bg 15s linear infinite;
        }
        @keyframes pulse-bg {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .header-sub {
            font-family: 'Montserrat', sans-serif;
            font-weight: 800;
            font-size: 11px;
            opacity: 0.9;
            letter-spacing: 3px;
            margin-bottom: 12px;
            text-transform: uppercase;
            position: relative;
        }
        .thesis-title { 
            font-family: 'Montserrat', sans-serif; 
            font-size: 1.4rem; 
            font-weight: 800; 
            text-transform: uppercase; 
            line-height: 1.3; 
            margin: 0;
            letter-spacing: 0.5px;
            position: relative;
            text-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        
        /* NAVIGATION */
        .nav-tabs { 
            background: #ffffff; 
            display: flex; 
            padding: 10px 60px; 
            gap: 15px; 
            border-bottom: 1px solid var(--border); 
            overflow-x: auto; 
            scrollbar-width: none; 
            align-items: center;
            position: sticky;
            top: 0;
            z-index: 100;
        }
        .nav-tabs::-webkit-scrollbar { display: none; }

        .tab-btn { 
            padding: 14px 28px; 
            border: none; 
            border-radius: var(--radius-sm); 
            font-weight: 700; 
            font-size: 12px; 
            text-transform: uppercase; 
            cursor: pointer; 
            transition: var(--transition); 
            background: transparent; 
            color: var(--text-sec); 
            white-space: nowrap;
            display: flex;
            align-items: center;
            gap: 10px;
            letter-spacing: 1px;
        }
        .tab-btn:hover { background: var(--primary-light); color: var(--primary); transform: translateY(-1px); }
        .tab-btn.active { 
            background: var(--primary); 
            color: #fff; 
            box-shadow: 0 8px 16px rgba(0, 137, 123, 0.2); 
        }

        /* CONTENT AREA */
        .content-area { 
            padding: 50px 100px; 
            display: none; 
            flex-grow: 1;
            background: #fff;
        }
        .content-area.active { display: block; animation: slideUp 0.6s cubic-bezier(0.16, 1, 0.3, 1); }
        @keyframes slideUp { 
            from { opacity: 0; transform: translateY(30px); } 
            to { opacity: 1; transform: translateY(0); } 
        }

        /* SECTIONS */
        .section-box { 
            margin-bottom: 60px; 
            padding-bottom: 20px;
            border-bottom: 1px solid #f7fafc;
        }
        .section-title { 
            font-family: 'Montserrat', sans-serif; 
            font-weight: 800; 
            font-size: 14px; 
            color: var(--primary); 
            display: flex;
            align-items: center;
            gap: 15px;
            margin-bottom: 35px; 
            text-transform: uppercase; 
            letter-spacing: 2px;
        }
        .section-title::after {
            content: "";
            flex-grow: 1;
            height: 2px;
            background: linear-gradient(to right, var(--primary-light), transparent);
        }
        
        /* FORM GRID */
        .field-grid { 
            display: grid; 
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); 
            gap: 30px; 
            margin-bottom: 30px; 
        }
        .field-group { display: flex; flex-direction: column; gap: 10px; }
        label { font-size: 11px; font-weight: 700; color: var(--text-sec); text-transform: uppercase; letter-spacing: 1px; }
        
        input, select, textarea { 
            padding: 16px 20px; 
            border: 2px solid #edf2f7; 
            border-radius: var(--radius-sm); 
            font-size: 16px; 
            font-family: inherit; 
            background: #f8fafc; 
            transition: var(--transition); 
            color: var(--text-main);
            width: 100%;
        }
        input:focus, select:focus, textarea:focus { 
            border-color: var(--primary); 
            outline: none; 
            background: #fff; 
            box-shadow: 0 0 0 4px rgba(0, 137, 123, 0.08);
        }

        /* PILLS & RADIOS */
        .pills-grid { 
            display: grid; 
            grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); 
            gap: 15px; 
            padding: 30px; 
            background: #f8fafc; 
            border-radius: var(--radius-md); 
            border: 1px solid var(--border); 
            margin-bottom: 15px;
        }
        .check-pill { 
            display: flex; 
            align-items: center; 
            gap: 15px; 
            padding: 14px 20px; 
            background: #fff; 
            border-radius: var(--radius-sm); 
            border: 1px solid var(--border); 
            cursor: pointer; 
            transition: var(--transition); 
            font-size: 15px; 
            font-weight: 600; 
            user-select: none;
            box-shadow: var(--shadow-sm);
        }
        .check-pill:hover { border-color: var(--primary); transform: translateY(-2px); box-shadow: 0 6px 12px rgba(0,0,0,0.05); }
        .check-pill input { width: 22px; height: 22px; accent-color: var(--primary); margin: 0; }
        
        .radio-group { display: flex; flex-wrap: wrap; gap: 40px; margin-top: 10px; }
        .radio-choice { display: flex; align-items: center; gap: 12px; cursor: pointer; font-size: 15px; font-weight: 600; }
        .radio-choice input { width: 22px; height: 22px; accent-color: var(--primary); cursor: pointer; }

        /* BUTTONS */
        .btn-action { 
            background: linear-gradient(to right, var(--primary), var(--primary-dark)); 
            color: #fff; 
            padding: 24px; 
            border: none; 
            border-radius: var(--radius-md); 
            font-weight: 800; 
            text-transform: uppercase; 
            cursor: pointer; 
            width: 100%; 
            font-size: 18px; 
            letter-spacing: 2px; 
            box-shadow: 0 10px 25px -5px rgba(0, 137, 123, 0.4);
            transition: var(--transition);
            margin-top: 40px;
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 15px;
        }
        .btn-action:hover { transform: translateY(-3px); box-shadow: 0 20px 35px -5px rgba(0, 121, 107, 0.5); }

        /* TABLES */
        .data-table-container { 
            overflow-x: auto; 
            border-radius: var(--radius-md); 
            border: 1px solid var(--border); 
            background: #fff;
            margin-top: 20px;
            box-shadow: var(--shadow-sm);
        }
        table { width: 100%; border-collapse: collapse; min-width: 900px; }
        th { background: #f8fafc; padding: 24px; text-align: left; font-size: 11px; font-weight: 800; color: var(--text-sec); text-transform: uppercase; border-bottom: 2px solid var(--border); letter-spacing: 1px; }
        td { padding: 20px 24px; border-bottom: 1px solid var(--border); font-size: 15px; }
        tr:hover td { background: #fcfdfe; }

        /* MODALS */
        .modal { display: none; position: fixed; z-index: 2000; left: 0; top: 0; width: 100%; height: 100%; background: rgba(15, 23, 42, 0.7); backdrop-filter: blur(10px); padding: 20px; }
        .modal-content { background: #fff; margin: 5vh auto; padding: 50px; border-radius: var(--radius-lg); width: 100%; max-width: 1000px; max-height: 85vh; overflow-y: auto; box-shadow: var(--shadow-lg); position: relative; }
        .close-btn { font-size: 40px; cursor: pointer; color: var(--text-sec); transition: var(--transition); }
        .close-btn:hover { color: var(--primary); transform: rotate(90deg); }
        
        .detail-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 24px; }
        .detail-item { padding: 20px; background: #f8fafc; border-radius: var(--radius-sm); border-left: 6px solid var(--primary); transition: var(--transition); }
        .detail-item:hover { transform: translateX(5px); background: #fff; box-shadow: var(--shadow-sm); }

        /* INDICATORS */
        #sync-indicator { font-size: 12px; font-weight: 800; color: var(--primary); display: flex; align-items: center; gap: 12px; margin-bottom: 25px; background: var(--primary-light); padding: 12px 24px; border-radius: 40px; width: fit-content; border: 1px solid var(--primary); }
        .pulse { width: 12px; height: 12px; background: var(--primary); border-radius: 50%; animation: pulse 2s infinite; }
        @keyframes pulse { 0% { transform: scale(0.8); opacity: 1; box-shadow: 0 0 0 0 rgba(0, 137, 123, 0.7); } 70% { transform: scale(1.2); opacity: 0; box-shadow: 0 0 0 10px rgba(0, 137, 123, 0); } 100% { transform: scale(0.8); opacity: 1; box-shadow: 0 0 0 0 rgba(0, 137, 123, 0); } }

        /* TOAST */
        .toast { position: fixed; bottom: 40px; left: 50%; transform: translateX(-50%) translateY(100px); background: #fff; color: var(--text-main); padding: 16px 32px; border-radius: 40px; box-shadow: var(--shadow-lg); z-index: 3000; display: flex; align-items: center; gap: 12px; font-weight: 700; transition: var(--transition); border: 2px solid var(--primary); pointer-events: none; opacity: 0; }
        .toast.show { transform: translateX(-50%) translateY(0); opacity: 1; }

        /* BACK TO TOP */
        #backToTop { display:none; position:fixed; bottom:30px; right:30px; width:50px; height:50px; border-radius:50%; background:var(--primary); color:white; border:none; box-shadow:var(--shadow-lg); cursor:pointer; z-index:1000; font-size:20px; transition:var(--transition); }
        #backToTop:hover { transform: scale(1.1); background: var(--primary-dark); }

        /* MOBILE OPTIMIZATION */
        @media (max-width: 1024px) {
            .content-area { padding: 40px 60px; }
        }

        @media (max-width: 768px) {
            body { padding: 10px; }
            .app-header { padding: 30px 20px; }
            .thesis-title { font-size: 1.1rem; }
            .nav-tabs { padding: 10px 20px; gap: 8px; }
            .tab-btn { padding: 12px 18px; font-size: 10px; }
            .content-area { padding: 30px 20px; }
            .field-grid { grid-template-columns: 1fr; gap: 20px; }
            .pills-grid { padding: 20px; grid-template-columns: 1fr; }
            .radio-group { gap: 20px; }
            .modal-content { padding: 30px 20px; margin: 0; max-height: 100vh; border-radius: 0; }
            .detail-grid { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>

<div id="toast" class="toast"><span>✨</span> Données synchronisées avec succès !</div>

<main class="app-shell">
    <header class="app-header">
        <div class="header-sub">FICHE D'ENQUÊTE CLINIQUE</div>
        <h1 class="thesis-title">
            Indications de l'extraction dentaire à l'Hôpital Général de Référence de Makala
        </h1>
    </header>

    <nav class="nav-tabs" aria-label="Navigation principale">
        <button class="tab-btn active" onclick="switchTab(1)">📝 Collecte</button>
        <button class="tab-btn" onclick="switchTab(2)">📊 Matrice</button>
        <button class="tab-btn" onclick="switchTab(3)">📈 Statistiques</button>
        <button class="tab-btn" onclick="switchTab(4)">⚙️ Paramètres</button>
    </nav>

    <section id="tab-1" class="content-area active">
        <form id="dentalForm">
            <div class="section-box">
                <div class="section-title"><span>I. IDENTIFICATION</span></div>
                <div class="field-grid">
                    <div class="field-group"><label>Numéro de fiche :</label><input type="text" id="n_fiche" placeholder="Ex: 001" required></div>
                    <div class="field-group"><label>Service :</label><input type="text" id="service" placeholder="..."></div>
                    <div class="field-group"><label>Nom de l'enquêteur :</label><input type="text" id="enqueteur" placeholder="..."></div>
                </div>
            </div>

            <div class="section-box">
                <div class="section-title"><span>II. DATE DE CONSULTATION</span></div>
                <div class="field-grid">
                    <div class="field-group"><label>Date :</label><input type="date" id="date_consult"></div>
                </div>
            </div>

            <div class="section-box">
                <div class="section-title"><span>III. DONNÉES SOCIODÉMOGRAPHIQUES</span></div>
                <div class="field-grid">
                    <div class="field-group"><label>Âge :</label><input type="number" id="age" min="0"></div>
                    <div class="field-group">
                        <label>Sexe :</label>
                        <div class="radio-group">
                            <label class="radio-choice"><input type="radio" name="sexe" value="Masculin"> Masculin</label>
                            <label class="radio-choice"><input type="radio" name="sexe" value="Féminin"> Féminin</label>
                        </div>
                    </div>
                </div>
            </div>

            <div class="section-box">
                <div class="section-title"><span>IV. DONNÉES CLINIQUES</span></div>
                
                <div class="field-group" style="margin-bottom: 30px;">
                    <label>1. Motif de consultation :</label>
                    <div class="pills-grid" id="motif_list">
                        <label class="check-pill"><input type="checkbox" name="motif" value="Douleur dentaire"><span>Douleur dentaire</span></label>
                        <label class="check-pill"><input type="checkbox" name="motif" value="Carie"><span>Carie</span></label>
                        <label class="check-pill"><input type="checkbox" name="motif" value="Mobilité dentaire"><span>Mobilité dentaire</span></label>
                        <label class="check-pill"><input type="checkbox" name="motif" value="Infection"><span>Infection</span></label>
                        <label class="check-pill"><input type="checkbox" name="motif" value="Traumatisme"><span>Traumatisme</span></label>
                        <label class="check-pill"><input type="checkbox" name="motif" value="Orthodontique"><span>Indication orthodontique</span></label>
                    </div>
                    <input type="text" id="motif_autres" placeholder="Autres motif...">
                </div>

                <div class="field-grid">
                    <div class="field-group"><label>2. Dent concernée :</label><input type="text" id="dent_concernee"></div>
                    <div class="field-group">
                        <label>3. Arcade :</label>
                        <div class="radio-group">
                            <label class="radio-choice"><input type="radio" name="arcade" value="Maxillaire"> Maxillaire</label>
                            <label class="radio-choice"><input type="radio" name="arcade" value="Mandibulaire"> Mandibulaire</label>
                        </div>
                    </div>
                </div>

                <div class="field-group" style="margin: 30px 0;">
                    <label>4. Indication de l'extraction :</label>
                    <div class="pills-grid" id="indication_list">
                        <label class="check-pill"><input type="checkbox" name="indication" value="Carie compliquée"><span>Carie compliquée</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Nécrose pulpaire"><span>Nécrose pulpaire</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Parodontite avancée"><span>Parodontite avancée</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Dent incluse"><span>Dent incluse</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Dent fracturée"><span>Dent fracturée</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Orthodontique"><span>Orthodontique</span></label>
                    </div>
                    <input type="text" id="indication_autres" placeholder="Autres indication...">
                </div>

                <div class="field-group" style="margin-bottom: 30px;">
                    <label>5. Examen(s) complémentaire(s) :</label>
                    <div class="pills-grid" id="exam_list">
                        <label class="check-pill"><input type="checkbox" name="examen" value="Radiographie"><span>Radiographie</span></label>
                        <label class="check-pill"><input type="checkbox" name="examen" value="Panoramique"><span>Panoramique</span></label>
                        <label class="check-pill"><input type="checkbox" name="examen" value="Aucun"><span>Aucun</span></label>
                    </div>
                    <input type="text" id="examen_autres" placeholder="Autres examens...">
                </div>

                <div class="field-group" style="margin-bottom: 30px;">
                    <label>6. Type d'extraction :</label>
                    <div class="radio-group">
                        <label class="radio-choice"><input type="radio" name="type_extraction" value="Simple"> Simple</label>
                        <label class="radio-choice"><input type="radio" name="type_extraction" value="Chirurgicale"> Chirurgicale</label>
                    </div>
                </div>

                <div class="field-group" style="margin-bottom: 30px;">
                    <label>7. Traitement médical associé :</label>
                    <div class="field-grid">
                        <div class="field-group"><label>Antalgique :</label><input type="text" id="trait_antalgique" placeholder="..."></div>
                        <div class="field-group"><label>Antibiotique :</label><input type="text" id="trait_antibiotique" placeholder="..."></div>
                        <div class="field-group"><label>AINS :</label><input type="text" id="trait_ains" placeholder="..."></div>
                        <div class="field-group"><label>Bain de bouche :</label><input type="text" id="trait_bain_bouche" placeholder="..."></div>
                    </div>
                </div>

                <div class="field-group" style="margin-bottom: 30px;">
                    <label>8. Complications :</label>
                    <div class="pills-grid" id="complications_list">
                        <label class="check-pill"><input type="checkbox" name="complication" value="Aucune"><span>Aucune</span></label>
                        <label class="check-pill"><input type="checkbox" name="complication" value="Hémorragie"><span>Hémorragie</span></label>
                        <label class="check-pill"><input type="checkbox" name="complication" value="Infection"><span>Infection</span></label>
                        <label class="check-pill"><input type="checkbox" name="complication" value="Alvéolite"><span>Alvéolite</span></label>
                        <label class="check-pill"><input type="checkbox" name="complication" value="Douleur persistante"><span>Douleur persistante</span></label>
                    </div>
                    <input type="text" id="complication_autres" placeholder="Autres complications...">
                </div>

                <div class="field-group">
                    <label>Observations complémentaires :</label>
                    <textarea id="observations" rows="5" placeholder="Notes additionnelles..."></textarea>
                </div>
            </div>

            <button type="button" class="btn-action" id="saveBtn">🚀 Envoyer les Données</button>
        </form>
    </section>

    <section id="tab-2" class="content-area">
        <div id="sync-indicator"><div class="pulse"></div> Données en temps réel</div>
        <div class="data-table-container">
            <table>
                <thead>
                    <tr>
                        <th>Fiche</th>
                        <th>Âge</th>
                        <th>Sexe</th>
                        <th>Extraction</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody id="matrix-body"></tbody>
            </table>
        </div>
    </section>

    <section id="tab-3" class="content-area">
        <div class="section-title"><span>📈 Aperçu Statistique</span></div>
        
        <div class="field-grid" style="grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); margin-bottom: 40px;">
            <div class="detail-item" style="text-align:center;">
                <small>Total Fiches</small>
                <div id="stat-total" style="font-size:32px; font-weight:800; color:var(--primary); margin-top:10px;">0</div>
            </div>
            <div class="detail-item" style="text-align:center;">
                <small>Âge Moyen</small>
                <div id="stat-age" style="font-size:32px; font-weight:800; color:var(--accent); margin-top:10px;">0</div>
            </div>
            <div class="detail-item" style="text-align:center;">
                <small>Ratio Sexe (M/F)</small>
                <div id="stat-ratio" style="font-size:32px; font-weight:800; color:var(--primary-dark); margin-top:10px;">0:0</div>
            </div>
        </div>

        <div class="field-grid" style="grid-template-columns: repeat(auto-fit, minmax(400px, 1fr)); margin-bottom: 40px;">
            <div style="background:#fff; padding:20px; border-radius:var(--radius-md); border:1px solid var(--border); box-shadow:var(--shadow-sm);">
                <canvas id="chartTypeExtraction"></canvas>
            </div>
            <div style="background:#fff; padding:20px; border-radius:var(--radius-md); border:1px solid var(--border); box-shadow:var(--shadow-sm);">
                <canvas id="chartSexe"></canvas>
            </div>
        </div>

        <div class="section-title"><span>📊 1. Tableaux de Fréquence</span></div>
        <div class="field-grid" style="grid-template-columns: 1fr 1fr;">
            <div id="freq-age" class="data-table-container"></div>
            <div id="freq-sexe" class="data-table-container"></div>
            <div id="freq-motif" class="data-table-container" style="grid-column: 1 / -1;"></div>
            <div id="freq-indication" class="data-table-container" style="grid-column: 1 / -1;"></div>
            <div id="freq-type" class="data-table-container"></div>
            <div id="freq-complications" class="data-table-container"></div>
        </div>

        <div class="section-title" style="margin-top: 60px;"><span>🔗 2. Croisements (Analyse Bivariée)</span></div>
        <div class="field-grid" style="grid-template-columns: 1fr;">
            <div id="cross-age-sexe" class="data-table-container"></div>
            <div id="cross-age-indication" class="data-table-container"></div>
            <div id="cross-indication-type" class="data-table-container"></div>
            <div id="cross-dent-indication" class="data-table-container"></div>
            <div id="cross-type-complication" class="data-table-container"></div>
            <div id="cross-examen-type" class="data-table-container"></div>
            <div id="cross-traitement-complication" class="data-table-container"></div>
        </div>
    </section>

    <section id="tab-4" class="content-area">
        <div class="section-title"><span>⚙️ Système</span></div>
        <div class="detail-item" style="display:flex; justify-content:space-between; align-items:center; border-left:none; border:1px solid var(--border);">
            <div>
                <h4 style="margin:0;">Status Connectivité</h4>
                <p id="db-status" style="margin:5px 0 0 0; font-size:14px; color:var(--text-sec);">Initialisation...</p>
            </div>
            <div class="pulse"></div>
        </div>
        <div style="margin-top:40px; display:grid; gap:20px;">
            <button class="check-pill" style="justify-content:center;" onclick="window.print()">🖨️ Imprimer la page</button>
            <button class="check-pill" style="justify-content:center;" onclick="alert('Exportation CSV demandée...')">📥 Exporter en CSV</button>
        </div>
    </section>
</main>

<button id="backToTop">↑</button>

<div id="detailModal" class="modal">
    <div class="modal-content">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:30px;">
            <h2 style="margin:0; color:var(--primary); font-size:1.2rem;">DÉTAILS DE LA FICHE</h2>
            <span class="close-btn" onclick="closeModal()">&times;</span>
        </div>
        <div id="modalBody" class="detail-grid"></div>
    </div>
</div>

<script>
    // --- FIREBASE CONFIG ---
    const firebaseConfig = {
      apiKey: "AIzaSyCmtQJNjuvkAST5bBXbUzWp7ykvcMgtYZg",
      authDomain: "pierrot-8737b.firebaseapp.com",
      projectId: "pierrot-8737b",
      storageBucket: "pierrot-8737b.firebasestorage.app",
      messagingSenderId: "884392310610",
      appId: "1:884392310610:web:7b34c50854f4715c362c8b",
      measurementId: "G-RVKZ3VG6MJ",
      databaseURL: "https://pierrot-8737b-default-rtdb.firebaseio.com"
    };
    
    let db = null;
    try {
        firebase.initializeApp(firebaseConfig);
        db = firebase.database();
        const stat = document.getElementById('db-status');
        stat.innerText = "✅ Connecté au serveur cloud";
        stat.style.color = "var(--primary)";
    } catch(e) {
        document.getElementById('db-status').innerText = "⚠️ Mode Hors-Ligne";
    }

    // --- TAB LOGIC ---
    function switchTab(n) {
        document.querySelectorAll('.content-area').forEach(a => a.classList.remove('active'));
        document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
        document.getElementById('tab-'+n).classList.add('active');
        document.querySelectorAll('.tab-btn')[n-1].classList.add('active');
        window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    // --- DATA HANDLING ---
    function getChecked(name) {
        return Array.from(document.querySelectorAll(`input[name="${name}"]:checked`)).map(c => c.value).join(', ');
    }

    function showToast() {
        const t = document.getElementById('toast');
        t.classList.add('show');
        setTimeout(() => t.classList.remove('show'), 3000);
    }

    document.getElementById('saveBtn').addEventListener('click', async () => {
        const btn = document.getElementById('saveBtn');
        const data = {
            fiche: document.getElementById('n_fiche').value,
            enqueteur: document.getElementById('enqueteur').value,
            service: document.getElementById('service').value,
            date: document.getElementById('date_consult').value,
            age: document.getElementById('age').value,
            sexe: document.querySelector('input[name="sexe"]:checked')?.value || '-',
            motif: getChecked('motif') + (document.getElementById('motif_autres').value ? ' | ' + document.getElementById('motif_autres').value : ''),
            dent: document.getElementById('dent_concernee').value,
            arcade: document.querySelector('input[name="arcade"]:checked')?.value || '-',
            indication: getChecked('indication') + (document.getElementById('indication_autres').value ? ' | ' + document.getElementById('indication_autres').value : ''),
            examens: getChecked('examen') + (document.getElementById('examen_autres').value ? ' | ' + document.getElementById('examen_autres').value : ''),
            type: document.querySelector('input[name="type_extraction"]:checked')?.value || '-',
            traitement: {
                antalgique: document.getElementById('trait_antalgique').value,
                antibiotique: document.getElementById('trait_antibiotique').value,
                ains: document.getElementById('trait_ains').value,
                bain_bouche: document.getElementById('trait_bain_bouche').value
            },
            complications: getChecked('complication') + (document.getElementById('complication_autres').value ? ' | ' + document.getElementById('complication_autres').value : ''),
            observations: document.getElementById('observations').value,
            timestamp: new Date().toISOString()
        };

        if(!data.fiche) return alert("Fiche obligatoire");

        btn.disabled = true;
        btn.innerText = "Patientez...";

        if(db) {
            await db.ref('extractions').push(data);
            showToast();
            document.getElementById('dentalForm').reset();
            switchTab(2);
        } else {
            addRow(data);
            alert("Enregistré localement");
        }
        
        btn.disabled = false;
        btn.innerText = "🚀 Envoyer les Données";
    });

    if(db) {
        db.ref('extractions').on('value', snap => {
            const body = document.getElementById('matrix-body');
            body.innerHTML = "";
            const items = [];
            snap.forEach(c => {
                const item = c.val();
                item.key = c.key; // Store key for deletion
                items.push(item);
            });
            updateStats(items);
            items.reverse().forEach(d => addRow(d));
        });
    }

    window.deleteEntry = function(key) {
        if(confirm("Confirmer la suppression de cette fiche ?")) {
            db.ref('extractions').child(key).remove()
              .then(() => {
                  alert("Supprimé.");
              })
              .catch(e => alert(e.message));
        }
    }

    // --- FONCTIONS STATISTIQUES UTILITAIRES ---
    
    // Fonction pour regrouper les âges en tranches
    function getAgeGroup(age) {
        if(!age) return "Non précisé";
        const a = parseInt(age);
        if(a < 18) return "< 18 ans";
        if(a <= 35) return "18-35 ans";
        if(a <= 50) return "36-50 ans";
        return "> 50 ans";
    }

    // Fonction pour extraire les réponses multiples (cases à cocher séparées par des virgules)
    function parseMultiple(str) {
        if (!str || str === '-') return ['Aucun/Non spécifié'];
        let mainPart = str.split(' | ')[0]; // On ignore la partie "Autres" pour les statistiques standard
        if(!mainPart) return ['Aucun'];
        return mainPart.split(', ').filter(v => v.trim() !== '');
    }

    // Fonction pour extraire les traitements pris
    function getTraitements(item) {
        let tr = [];
        if(item.traitement?.antalgique) tr.push("Antalgique");
        if(item.traitement?.antibiotique) tr.push("Antibiotique");
        if(item.traitement?.ains) tr.push("AINS");
        if(item.traitement?.bain_bouche) tr.push("Bain de bouche");
        return tr.length ? tr : ["Aucun traitement"];
    }

    // Générateur de Tableau de Fréquence Simple
    function buildFreqTable(data, title, fieldFunc) {
        const counts = {};
        let totalResponses = 0;
        
        data.forEach(item => {
            const vals = fieldFunc(item);
            (Array.isArray(vals) ? vals : [vals]).forEach(v => {
                const val = v || "Non spécifié";
                counts[val] = (counts[val] || 0) + 1;
                totalResponses++;
            });
        });
        
        let html = `<h4 style="padding:15px 24px; margin:0; background:#f8fafc; border-bottom:1px solid var(--border); color:var(--primary);">${title}</h4>
                    <table style="min-width:100%;"><thead><tr><th>Catégorie</th><th>Effectif (n)</th><th>Fréquence (%)</th></tr></thead><tbody>`;
        
        const sorted = Object.entries(counts).sort((a,b) => b[1] - a[1]); // Tri décroissant
        
        sorted.forEach(([k, v]) => {
            const pct = totalResponses > 0 ? ((v / totalResponses) * 100).toFixed(1) : 0;
            html += `<tr><td>${k}</td><td>${v}</td><td>${pct}%</td></tr>`;
        });
        html += `</tbody><tfoot><tr><th>Total</th><th>${totalResponses}</th><th>100%</th></tr></tfoot></table>`;
        return html;
    }

    // Générateur de Tableau Croisé (Contingence)
    function buildCrossTable(data, title, rowFunc, colFunc) {
        const matrix = {};
        const colTotals = {};
        const colsSet = new Set();
        let grandTotal = 0;

        data.forEach(item => {
            const rows = rowFunc(item);
            const cols = colFunc(item);
            
            const rowArr = Array.isArray(rows) ? rows : [rows];
            const colArr = Array.isArray(cols) ? cols : [cols];

            rowArr.forEach(r => {
                const rowVal = r || "Non spécifié";
                if (!matrix[rowVal]) matrix[rowVal] = {};
                
                colArr.forEach(c => {
                    const colVal = c || "Non spécifié";
                    colsSet.add(colVal);
                    matrix[rowVal][colVal] = (matrix[rowVal][colVal] || 0) + 1;
                    colTotals[colVal] = (colTotals[colVal] || 0) + 1;
                    grandTotal++;
                });
            });
        });

        const colsArray = Array.from(colsSet).sort();
        
        let html = `<h4 style="padding:15px 24px; margin:0; background:#f8fafc; border-bottom:1px solid var(--border); color:var(--primary);">${title}</h4>
                    <div style="overflow-x:auto;"><table style="min-width:100%;"><thead><tr><th>Variables</th>`;
        
        colsArray.forEach(c => { html += `<th>${c}</th>`; });
        html += `<th>Total Ligne</th></tr></thead><tbody>`;

        Object.keys(matrix).sort().forEach(r => {
            html += `<tr><td><b>${r}</b></td>`;
            let rTotal = 0;
            colsArray.forEach(c => {
                const val = matrix[r][c] || 0;
                rTotal += val;
                html += `<td>${val}</td>`;
            });
            html += `<td><b>${rTotal}</b></td></tr>`;
        });
        
        html += `</tbody><tfoot><tr><th>Total Colonne</th>`;
        let cGrand = 0;
        colsArray.forEach(c => {
            const val = colTotals[c] || 0;
            cGrand += val;
            html += `<th>${val}</th>`;
        });
        html += `<th>${cGrand}</th></tr></tfoot></table></div>`;
        return html;
    }

    // --- MISE À JOUR PRINCIPALE (STATS & GRAPHIQUES) ---
    let chartTypeExt = null;
    let chartSexeStat = null;

    function updateStats(items) {
        if(!items.length) return;
        
        // 1. Chiffres Clés
        document.getElementById('stat-total').innerText = items.length;
        const avg = items.reduce((acc, c) => acc + (parseInt(c.age) || 0), 0) / items.length;
        document.getElementById('stat-age').innerText = Math.round(avg) + " ans";
        const m = items.filter(i => i.sexe === 'Masculin').length;
        const f = items.filter(i => i.sexe === 'Féminin').length;
        document.getElementById('stat-ratio').innerText = `${m}:${f}`;

        // 2. Graphiques
        const types = {'Simple': 0, 'Chirurgicale': 0};
        items.forEach(i => { if(types[i.type] !== undefined) types[i.type]++; });

        if(chartTypeExt) chartTypeExt.destroy();
        chartTypeExt = new Chart(document.getElementById('chartTypeExtraction'), {
            type: 'bar',
            data: {
                labels: Object.keys(types),
                datasets: [{ label: 'Nombre de cas', data: Object.values(types), backgroundColor: ['#00897b', '#ffb300'] }]
            },
            options: { plugins: { title: { display: true, text: "Types d'Extraction" } } }
        });

        if(chartSexeStat) chartSexeStat.destroy();
        chartSexeStat = new Chart(document.getElementById('chartSexe'), {
            type: 'pie',
            data: {
                labels: ['Masculin', 'Féminin'],
                datasets: [{ data: [m, f], backgroundColor: ['#3182ce', '#e53e3e'] }]
            },
            options: { plugins: { title: { display: true, text: "Répartition par Sexe" } } }
        });

        // 3. GÉNÉRATION DES TABLEAUX DE FRÉQUENCE
        document.getElementById('freq-age').innerHTML = buildFreqTable(items, "Répartition selon l'Âge", i => getAgeGroup(i.age));
        document.getElementById('freq-sexe').innerHTML = buildFreqTable(items, "Répartition selon le Sexe", i => i.sexe);
        document.getElementById('freq-motif').innerHTML = buildFreqTable(items, "Répartition selon le Motif", i => parseMultiple(i.motif));
        document.getElementById('freq-indication').innerHTML = buildFreqTable(items, "Répartition selon l'Indication", i => parseMultiple(i.indication));
        document.getElementById('freq-type').innerHTML = buildFreqTable(items, "Répartition selon le Type", i => i.type);
        document.getElementById('freq-complications').innerHTML = buildFreqTable(items, "Répartition selon les Complications", i => parseMultiple(i.complications));

        // 4. GÉNÉRATION DES TABLEAUX CROISÉS
        document.getElementById('cross-age-sexe').innerHTML = buildCrossTable(items, "Âge + Sexe", i => getAgeGroup(i.age), i => i.sexe);
        document.getElementById('cross-age-indication').innerHTML = buildCrossTable(items, "Âge + Indications", i => getAgeGroup(i.age), i => parseMultiple(i.indication));
        document.getElementById('cross-indication-type').innerHTML = buildCrossTable(items, "Indications + Type d'extraction", i => parseMultiple(i.indication), i => i.type);
        document.getElementById('cross-dent-indication').innerHTML = buildCrossTable(items, "Dent concernée + Indications", i => i.dent || "Non précisé", i => parseMultiple(i.indication));
        document.getElementById('cross-type-complication').innerHTML = buildCrossTable(items, "Type d'extraction + Complications", i => i.type, i => parseMultiple(i.complications));
        document.getElementById('cross-examen-type').innerHTML = buildCrossTable(items, "Examen complémentaire + Type d'extraction", i => parseMultiple(i.examens), i => i.type);
        document.getElementById('cross-traitement-complication').innerHTML = buildCrossTable(items, "Traitement médical + Complications", i => getTraitements(i), i => parseMultiple(i.complications));
    }

    function addRow(d) {
        const body = document.getElementById('matrix-body');
        const row = document.createElement('tr');
        const safeData = btoa(unescape(encodeURIComponent(JSON.stringify(d))));
        row.innerHTML = `
            <td><b style="color:var(--primary)">#${d.fiche}</b></td>
            <td>${d.age} ans</td>
            <td>${d.sexe}</td>
            <td>${d.type}</td>
            <td>
                <div style="display:flex; gap:10px;">
                    <button onclick="viewDetails('${safeData}')" style="cursor:pointer; border:1px solid var(--border); padding:5px 10px; border-radius:8px;">👁️</button>
                    <button onclick="deleteEntry('${d.key}')" style="cursor:pointer; border:1px solid #ff4444; color:#ff4444; padding:5px 10px; border-radius:8px; background:transparent;">🗑️</button>
                </div>
            </td>
        `;
        body.appendChild(row);
    }

    window.viewDetails = function(encoded) {
        const d = JSON.parse(decodeURIComponent(escape(atob(encoded))));
        const body = document.getElementById('modalBody');
        body.innerHTML = "";
        
        const labels = {
            fiche: "N° Fiche",
            enqueteur: "Enquêteur",
            service: "Service",
            date: "Date Consultation",
            age: "Âge",
            sexe: "Sexe",
            motif: "Motif de Consultation",
            dent: "Dent concernée",
            arcade: "Arcade",
            indication: "Indication d'extraction",
            examens: "Examens complémentaires",
            type: "Type d'extraction",
            traitement: "Traitement associé",
            complications: "Complications post-op",
            observations: "Observations"
        };

        Object.keys(labels).forEach(k => {
            if(!d[k]) return;
            let val = d[k];
            if(k === 'traitement') {
                val = Object.entries(val)
                    .filter(([_, v]) => v)
                    .map(([subK, subV]) => `<span style="color:var(--primary)">${subK}:</span> ${subV}`)
                    .join('<br>');
            }
            if(!val) return;
            body.innerHTML += `<div class="detail-item"><small style="text-transform:uppercase; color:var(--text-sec); font-size:10px; font-weight:800;">${labels[k]}</small><div style="margin-top:5px; font-weight:600;">${val}</div></div>`;
        });
        document.getElementById('detailModal').style.display = "block";
    }

    window.closeModal = function() { document.getElementById('detailModal').style.display = "none"; }
    window.onclick = e => { if(e.target == document.getElementById('detailModal')) closeModal(); }

    window.onscroll = () => {
        const b = document.getElementById('backToTop');
        b.style.display = (window.scrollY > 500) ? "block" : "none";
    };
    document.getElementById('backToTop').onclick = () => window.scrollTo({top:0, behavior:'smooth'});
</script>
</body>
</html>
