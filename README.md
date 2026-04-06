<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DentStat Makala - Plateforme de Recherche Extractions Dentaires</title>
    <!-- Google Fonts & Firebase SDK -->
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;700;800&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database-compat.js"></script>
    <style>
        :root {
            --primary: #00897b;
            --primary-dark: #00695c;
            --primary-light: #e0f2f1;
            --accent: #ffb300;
            --bg: #f0f4f8;
            --card-bg: #ffffff;
            --text-main: #1a202c;
            --text-sec: #4a5568;
            --border: #e2e8f0;
            --shadow-sm: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
            --shadow-lg: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
            --transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        body { 
            font-family: 'Inter', sans-serif; 
            background: var(--bg); 
            margin: 0; 
            padding: 20px; 
            color: var(--text-main);
            line-height: 1.5;
        }

        .app-shell { 
            max-width: 1400px; 
            margin: 0 auto; 
            background: var(--card-bg); 
            border-radius: 24px; 
            box-shadow: var(--shadow-lg); 
            overflow: hidden; 
            min-height: 92vh; 
            display: flex; 
            flex-direction: column;
        }

        .app-header { 
            background: linear-gradient(135deg, var(--primary-dark), var(--primary)); 
            padding: 30px 50px; 
            text-align: center;
            color: white;
            position: relative;
        }
        .header-sub {
            font-family: 'Montserrat', sans-serif;
            font-weight: 800;
            font-size: 11px;
            opacity: 0.8;
            letter-spacing: 2px;
            margin-bottom: 8px;
            text-transform: uppercase;
        }
        .thesis-title { 
            font-family: 'Montserrat', sans-serif; 
            font-size: 16px; 
            font-weight: 800; 
            text-transform: uppercase; 
            line-height: 1.4; 
            margin: 0;
            letter-spacing: 0.5px;
        }
        
        .nav-tabs { 
            background: #ffffff; 
            display: flex; 
            padding: 10px 50px; 
            gap: 10px; 
            border-bottom: 1px solid var(--border); 
            overflow-x: auto; 
            scrollbar-width: none; 
            align-items: center;
        }
        .nav-tabs::-webkit-scrollbar { display: none; }

        .tab-btn { 
            padding: 14px 24px; 
            border: none; 
            border-radius: 12px; 
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
            gap: 8px;
        }
        .tab-btn:hover { background: var(--primary-light); color: var(--primary); }
        .tab-btn.active { 
            background: var(--primary); 
            color: #fff; 
            box-shadow: 0 4px 14px rgba(0, 137, 123, 0.3); 
        }

        .content-area { 
            padding: 40px 80px; 
            display: none; 
            flex-grow: 1;
            background: #fff;
        }
        .content-area.active { display: block; animation: slideUp 0.5s ease-out; }
        @keyframes slideUp { 
            from { opacity: 0; transform: translateY(20px); } 
            to { opacity: 1; transform: translateY(0); } 
        }

        .section-box { 
            margin-bottom: 45px; 
            position: relative;
        }
        .section-title { 
            font-family: 'Montserrat', sans-serif; 
            font-weight: 800; 
            font-size: 14px; 
            color: var(--primary); 
            display: flex;
            align-items: center;
            gap: 12px;
            margin-bottom: 30px; 
            text-transform: uppercase; 
            letter-spacing: 1px;
        }
        .section-title::after {
            content: "";
            flex-grow: 1;
            height: 1px;
            background: var(--border);
        }
        
        .field-grid { 
            display: grid; 
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); 
            gap: 25px; 
            margin-bottom: 25px; 
        }
        .field-group { display: flex; flex-direction: column; gap: 8px; }
        label { font-size: 11px; font-weight: 700; color: var(--text-sec); text-transform: uppercase; letter-spacing: 0.5px; }
        
        input, select, textarea { 
            padding: 14px 18px; 
            border: 2px solid #f1f5f9; 
            border-radius: 12px; 
            font-size: 15px; 
            font-family: inherit; 
            background: #f8fafc; 
            transition: var(--transition); 
            color: var(--text-main);
        }
        input:focus, select:focus, textarea:focus { 
            border-color: var(--primary); 
            outline: none; 
            background: #fff; 
            box-shadow: 0 0 0 4px rgba(0, 137, 123, 0.1);
        }

        .pills-grid { 
            display: grid; 
            grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); 
            gap: 12px; 
            padding: 25px; 
            background: #f8fafc; 
            border-radius: 16px; 
            border: 1px solid var(--border); 
            margin-bottom: 10px;
        }
        .check-pill { 
            display: flex; 
            align-items: center; 
            gap: 12px; 
            padding: 12px 16px; 
            background: #fff; 
            border-radius: 12px; 
            border: 1px solid var(--border); 
            cursor: pointer; 
            transition: var(--transition); 
            font-size: 14px; 
            font-weight: 600; 
            user-select: none;
        }
        .check-pill:hover { border-color: var(--primary); transform: translateY(-1px); }
        .check-pill input { width: 20px; height: 20px; accent-color: var(--primary); margin: 0; }
        
        .radio-group { display: flex; gap: 30px; margin-top: 5px; }
        .radio-choice { display: flex; align-items: center; gap: 10px; cursor: pointer; font-size: 14px; font-weight: 600; }
        .radio-choice input { width: 18px; height: 18px; accent-color: var(--primary); }

        .btn-action { 
            background: linear-gradient(to right, var(--primary), var(--primary-dark)); 
            color: #fff; 
            padding: 20px; 
            border: none; 
            border-radius: 16px; 
            font-weight: 800; 
            text-transform: uppercase; 
            cursor: pointer; 
            width: 100%; 
            font-size: 16px; 
            letter-spacing: 1px; 
            box-shadow: 0 10px 15px -3px rgba(0, 137, 123, 0.3);
            transition: var(--transition);
            margin-top: 20px;
        }
        .btn-action:hover { transform: translateY(-2px); box-shadow: 0 20px 25px -5px rgba(0, 121, 107, 0.4); }

        .data-table-container { 
            overflow-x: auto; 
            border-radius: 20px; 
            border: 1px solid var(--border); 
            background: #fff;
        }
        table { width: 100%; border-collapse: collapse; min-width: 1000px; }
        th { background: #f8fafc; padding: 20px; text-align: left; font-size: 11px; font-weight: 800; color: var(--text-sec); text-transform: uppercase; border-bottom: 2px solid var(--border); }
        td { padding: 18px 20px; border-bottom: 1px solid var(--border); font-size: 14px; }

        .modal { display: none; position: fixed; z-index: 2000; left: 0; top: 0; width: 100%; height: 100%; background: rgba(15, 23, 42, 0.6); backdrop-filter: blur(8px); padding: 20px; }
        .modal-content { background: #fff; margin: 2vh auto; padding: 40px; border-radius: 28px; width: 100%; max-width: 900px; max-height: 90vh; overflow-y: auto; box-shadow: var(--shadow-lg); position: relative; }
        .close-btn { font-size: 32px; cursor: pointer; color: var(--text-sec); }
        .detail-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 20px; }
        .detail-item { padding: 16px; background: #f8fafc; border-radius: 14px; border-left: 5px solid var(--primary); }

        #sync-indicator { font-size: 11px; font-weight: 800; color: var(--primary); display: flex; align-items: center; gap: 10px; margin-bottom: 15px; background: var(--primary-light); padding: 10px 20px; border-radius: 30px; width: fit-content; }
        .pulse { width: 10px; height: 10px; background: var(--primary); border-radius: 50%; animation: pulse 1.5s infinite; }
        @keyframes pulse { 0% { transform: scale(0.9); opacity: 1; } 70% { transform: scale(1.1); opacity: 0.3; } 100% { transform: scale(0.9); opacity: 1; } }

        @media (max-width: 768px) {
            .app-shell { border-radius: 0; min-height: 100vh; }
            .content-area { padding: 30px 20px; }
            .nav-tabs { padding: 10px 20px; }
        }
    </style>
</head>
<body>

<div class="app-shell">
    <header class="app-header">
        <div class="header-sub">FICHE D'ENQUÊTE</div>
        <h1 class="thesis-title">
            Indications de l'extraction dentaire à l'Hôpital Général de Référence de Makala
        </h1>
    </header>

    <nav class="nav-tabs">
        <button class="tab-btn active" onclick="switchTab(1)">📝 Collecte</button>
        <button class="tab-btn" onclick="switchTab(2)">📊 Matrice Données</button>
        <button class="tab-btn" onclick="switchTab(3)">📈 Statistiques</button>
        <button class="tab-btn" onclick="switchTab(4)">⚙️ Paramètres</button>
    </nav>

    <!-- TAB 1: COLLECTE (CONFORME À LA PHOTO) -->
    <div id="tab-1" class="content-area active">
        <form id="dentalForm">
            <!-- I. IDENTIFICATION -->
            <div class="section-box">
                <div class="section-title"><span>I. IDENTIFICATION</span></div>
                <div class="field-grid">
                    <div class="field-group"><label>Numéro de fiche :</label><input type="text" id="n_fiche" placeholder="..." required></div>
                    <div class="field-group"><label>Service :</label><input type="text" id="service" placeholder="..."></div>
                    <div class="field-group"><label>Nom de l'enquêteur :</label><input type="text" id="enqueteur" placeholder="..."></div>
                </div>
            </div>

            <!-- II. DATE DE CONSULTATION -->
            <div class="section-box">
                <div class="section-title"><span>II. DATE DE CONSULTATION</span></div>
                <div class="field-grid">
                    <div class="field-group"><label>Date :</label><input type="date" id="date_consult"></div>
                </div>
            </div>

            <!-- III. DONNÉES SOCIODÉMOGRAPHIQUES -->
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

            <!-- IV. DONNÉES CLINIQUES -->
            <div class="section-box">
                <div class="section-title"><span>IV. DONNÉES CLINIQUES</span></div>
                
                <!-- 1. Motif -->
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
                    <input type="text" id="motif_autres" placeholder="Autres : ...">
                </div>

                <!-- 2 & 3 -->
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

                <!-- 4. Indication -->
                <div class="field-group" style="margin: 30px 0;">
                    <label>4. Indication de l'extraction :</label>
                    <div class="pills-grid" id="indication_list">
                        <label class="check-pill"><input type="checkbox" name="indication" value="Carie compliquée"><span>Carie compliquée</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Nécrose pulpaire"><span>Nécrose pulpaire</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Parodontite avancée"><span>Parodontite avancée</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Dent incluse"><span>Dent incluse</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Dent fracturée"><span>Dent fracturée</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Orthodontique"><span>Orthodontique</span></label>
                        <label class="check-pill"><input type="checkbox" name="indication" value="Infection sévère"><span>Infection sévère</span></label>
                    </div>
                    <input type="text" id="indication_autres" placeholder="Autres : ...">
                </div>

                <!-- 5. Examens -->
                <div class="field-group" style="margin-bottom: 30px;">
                    <label>5. Examen(s) complémentaire(s) :</label>
                    <div class="pills-grid" id="examen_list">
                        <label class="check-pill"><input type="checkbox" name="examen" value="Radiographie"><span>Radiographie</span></label>
                        <label class="check-pill"><input type="checkbox" name="examen" value="Panoramique"><span>Panoramique</span></label>
                        <label class="check-pill"><input type="checkbox" name="examen" value="Aucun"><span>Aucun</span></label>
                    </div>
                    <input type="text" id="examen_autres" placeholder="Autres : ...">
                </div>

                <!-- 6. Type -->
                <div class="field-group" style="margin-bottom: 30px;">
                    <label>6. Type d'extraction :</label>
                    <div class="radio-group">
                        <label class="radio-choice"><input type="radio" name="type_extraction" value="Simple"> Simple</label>
                        <label class="radio-choice"><input type="radio" name="type_extraction" value="Chirurgicale"> Chirurgicale</label>
                    </div>
                </div>

                <!-- 7. Traitement associé -->
                <div class="field-group" style="margin-bottom: 30px;">
                    <label>7. Traitement médical associé :</label>
                    <div class="field-grid" style="margin-top: 10px;">
                        <div class="field-group"><label style="font-size: 9px;">Antalgique :</label><input type="text" id="trait_antalgique"></div>
                        <div class="field-group"><label style="font-size: 9px;">Antibiotique :</label><input type="text" id="trait_antibio"></div>
                        <div class="field-group"><label style="font-size: 9px;">AINS :</label><input type="text" id="trait_ains"></div>
                        <div class="field-group"><label style="font-size: 9px;">Bain de bouche :</label><input type="text" id="trait_bain"></div>
                    </div>
                </div>

                <!-- 8. Complications -->
                <div class="field-group" style="margin-bottom: 30px;">
                    <label>8. Complications :</label>
                    <div class="pills-grid" id="complication_list">
                        <label class="check-pill"><input type="checkbox" name="complication" value="Aucune"><span>Aucune</span></label>
                        <label class="check-pill"><input type="checkbox" name="complication" value="Hémorragie"><span>Hémorragie</span></label>
                        <label class="check-pill"><input type="checkbox" name="complication" value="Infection"><span>Infection</span></label>
                        <label class="check-pill"><input type="checkbox" name="complication" value="Alvéolite"><span>Alvéolite</span></label>
                        <label class="check-pill"><input type="checkbox" name="complication" value="Douleur persistante"><span>Douleur persistante</span></label>
                    </div>
                    <input type="text" id="complication_autres" placeholder="Autres : ...">
                </div>

                <!-- Observations -->
                <div class="field-group">
                    <label>Observations complémentaires :</label>
                    <textarea id="observations" rows="5" placeholder="Saisir ici les observations finales..."></textarea>
                </div>
            </div>

            <button type="button" class="btn-action" id="saveBtn">🚀 Soumettre & Synchroniser</button>
        </form>
    </div>

    <!-- TABS 2, 3, 4 (OMITTED FOR BREVITY BUT KEPT LOGIC) -->
    <div id="tab-2" class="content-area">
        <div id="sync-indicator"><div class="pulse"></div> Données en direct</div>
        <div class="data-table-container"><table><thead><tr><th>N° Fiche</th><th>Âge</th><th>Sexe</th><th>Traitement</th><th>Actions</th></tr></thead><tbody id="matrix-body"></tbody></table></div>
    </div>
    <div id="tab-3" class="content-area"><h3>Analyses Statistiques</h3><p>Graphiques en cours de génération...</p></div>
    <div id="tab-4" class="content-area"><h3>Configuration Cloud</h3><p id="db-status" style="color:red;">Non connecté</p></div>
</div>

<div id="detailModal" class="modal"><div class="modal-content"><div style="display:flex;justify-content:space-between;margin-bottom:20px;"><h2>Détails complets</h2><span class="close-btn" onclick="closeModal()">&times;</span></div><div id="modalBody" class="detail-grid"></div></div></div>

<script>
    // --- 1. CONFIGURATION FIREBASE ---
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
    try { firebase.initializeApp(firebaseConfig); db = firebase.database(); document.getElementById('db-status').innerText = "✅ Connecté"; document.getElementById('db-status').style.color = "green"; } catch(e) {}

    function switchTab(n) { document.querySelectorAll('.content-area').forEach(a => a.classList.remove('active')); document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active')); document.getElementById('tab-'+n).classList.add('active'); document.querySelectorAll('.tab-btn')[n-1].classList.add('active'); }
    function getChecked(name) { let items = []; document.querySelectorAll(`input[name="${name}"]:checked`).forEach(c => items.push(c.value)); return items.join(', '); }

    document.getElementById('saveBtn').addEventListener('click', () => {
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
            examen: getChecked('examen') + (document.getElementById('examen_autres').value ? ' | ' + document.getElementById('examen_autres').value : ''),
            type: document.querySelector('input[name="type_extraction"]:checked')?.value || '-',
            traitement: `Ant: ${document.getElementById('trait_antalgique').value}, Antibio: ${document.getElementById('trait_antibio').value}, AINS: ${document.getElementById('trait_ains').value}, Bain: ${document.getElementById('trait_bain').value}`,
            complication: getChecked('complication') + (document.getElementById('complication_autres').value ? ' | ' + document.getElementById('complication_autres').value : ''),
            observations: document.getElementById('observations').value,
            timestamp: new Date().toLocaleString()
        };
        if(!data.fiche) return alert("Fiche obligatoire");
        if(db) db.ref('extractions').push(data).then(() => { alert("Synchronisé!"); document.getElementById('dentalForm').reset(); }).catch(e => alert(e.message));
        else addRow(data);
    });

    if(db) db.ref('extractions').on('value', snap => { const body = document.getElementById('matrix-body'); body.innerHTML = ""; snap.forEach(c => addRow(c.val())); });
    function addRow(d) { const row = `<tr><td><b>#${d.fiche}</b></td><td>${d.age} ans</td><td>${d.sexe}</td><td>${d.type || '-'}</td><td><button onclick='viewDetails(${JSON.stringify(d)})'>👁️</button></td></tr>`; document.getElementById('matrix-body').innerHTML = row + document.getElementById('matrix-body').innerHTML; }
    function viewDetails(d) { const body = document.getElementById('modalBody'); body.innerHTML = ""; Object.keys(d).forEach(k => body.innerHTML += `<div class="detail-item"><small>${k.toUpperCase()}</small><div>${d[k]}</div></div>`); document.getElementById('detailModal').style.display = "block"; }
    function closeModal() { document.getElementById('detailModal').style.display = "none"; }
</script>
</body>
</html>
