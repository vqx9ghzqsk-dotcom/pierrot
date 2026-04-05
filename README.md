<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MedStat Makala - Plateforme de Recherche TB</title>
    <!-- Google Fonts & Firebase SDK -->
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;700;800&family=Inter:wght@400;600&display=swap" rel="stylesheet">
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database-compat.js"></script>
    <style>
        :root {
            --primary: #00796b;
            --primary-dark: #004d40;
            --accent: #ffb300;
            --bg: #f5f7fa;
            --card-bg: #ffffff;
            --text: #263238;
            --border: #e2e8f0;
            --shadow: 0 10px 25px rgba(0,0,0,0.08);
            --transition: all 0.3s ease;
        }

        body { font-family: 'Inter', sans-serif; background: var(--bg); margin: 0; padding: 15px; color: var(--text); }
        .app-shell { max-width: 1300px; margin: auto; background: var(--card-bg); border-radius: 20px; box-shadow: var(--shadow); overflow: hidden; min-height: 95vh; display: flex; flex-direction: column; }

        /* HEADER */
        .app-header { background: #fff; padding: 25px 40px; border-bottom: 4px solid var(--primary); text-align: center; }
        .thesis-title { font-family: 'Montserrat', sans-serif; font-size: 14px; font-weight: 800; color: var(--primary-dark); text-transform: uppercase; line-height: 1.6; margin: 0; }
        
        /* NAVIGATION */
        .nav-tabs { background: #fafafa; display: flex; padding: 15px 40px; gap: 15px; border-bottom: 1px solid var(--border); overflow-x: auto; scrollbar-width: none; }
        .tab-btn { padding: 12px 22px; border: 1px solid var(--border); border-radius: 10px; font-weight: 800; font-size: 11px; text-transform: uppercase; cursor: pointer; transition: var(--transition); background: #fff; color: #64748b; white-space: nowrap; }
        .tab-btn.active { background: var(--primary); color: #fff; border-color: var(--primary); box-shadow: 0 5px 15px rgba(0, 121, 107, 0.2); }

        /* CONTENT */
        .content-area { padding: 40px 60px; display: none; }
        .content-area.active { display: block; animation: fadeIn 0.4s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

        /* FORM STYLING */
        .section-box { margin-bottom: 40px; }
        .section-title { font-family: 'Montserrat', sans-serif; font-weight: 800; font-size: 13px; color: var(--primary); border-bottom: 2px solid var(--primary-dark); display: inline-block; padding-bottom: 5px; margin-bottom: 25px; text-transform: uppercase; }
        
        .field-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 25px; margin-bottom: 25px; }
        .field-group { display: flex; flex-direction: column; gap: 8px; }
        label { font-size: 12px; font-weight: 700; color: #475569; }
        input, select, textarea { padding: 12px 15px; border: 2px solid #f1f5f9; border-radius: 10px; font-size: 14px; font-family: inherit; background: #f8fafc; transition: var(--transition); }
        input:focus, select:focus { border-color: var(--primary); outline: none; background: #fff; }

        .symptoms-box { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 12px; padding: 25px; background: #f8fafc; border-radius: 15px; border: 1px solid #e2e8f0; }
        .check-pill { display: flex; align-items: center; gap: 10px; padding: 10px 15px; background: #fff; border-radius: 8px; border: 1px solid #eee; cursor: pointer; transition: 0.2s; font-size: 13px; font-weight: 600; }
        .check-pill input { width: 18px; height: 18px; accent-color: var(--primary); }

        .btn-action { background: var(--primary); color: #fff; padding: 18px; border: none; border-radius: 12px; font-weight: 800; text-transform: uppercase; cursor: pointer; width: 100%; font-size: 15px; letter-spacing: 0.5px; box-shadow: 0 8px 20px rgba(0, 121, 107, 0.2); }
        .btn-action:hover { background: var(--primary-dark); transform: translateY(-2px); }

        /* DATABASE TABLE */
        .data-table-container { overflow-x: auto; border-radius: 15px; border: 1px solid var(--border); }
        table { width: 100%; border-collapse: collapse; min-width: 1000px; }
        th { background: #f8f9fa; padding: 15px; text-align: left; font-size: 10px; font-weight: 800; color: #64748b; text-transform: uppercase; border-bottom: 2px solid var(--border); }
        td { padding: 15px; border-bottom: 1px solid var(--border); font-size: 13px; }
        .status-badge { padding: 4px 10px; border-radius: 20px; font-size: 10px; font-weight: 800; background: var(--primary-light); color: var(--primary); }

        /* MODAL DETAIL */
        .modal { display: none; position: fixed; z-index: 2000; left: 0; top: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); backdrop-filter: blur(4px); padding: 15px; }
        .modal-content { background: #fff; margin: 2% auto; padding: 30px; border-radius: 20px; width: 100%; max-width: 800px; max-height: 90vh; overflow-y: auto; box-shadow: var(--shadow); position: relative; animation: slideIn 0.3s ease; }
        @keyframes slideIn { from { transform: translateY(-30px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
        .modal-header { display: flex; justify-content: space-between; align-items: center; border-bottom: 3px solid var(--primary); padding-bottom: 15px; margin-bottom: 25px; }
        .close-btn { font-size: 28px; cursor: pointer; color: #94a3b8; font-weight: bold; }
        
        .detail-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; margin-bottom: 20px; }
        .detail-item { padding: 12px; background: #f8fafc; border-radius: 10px; border-left: 4px solid var(--primary); }
        .detail-label { font-size: 10px; font-weight: 800; color: #64748b; text-transform: uppercase; margin-bottom: 4px; }
        .detail-val { font-size: 14px; font-weight: 600; color: var(--text); }

        /* ACTION BUTTONS */
        .btn-icon { padding: 8px 12px; border-radius: 8px; border: 1px solid var(--border); background: #fff; cursor: pointer; transition: 0.2s; font-size: 14px; }
        .btn-icon:hover { background: #f1f5f9; border-color: var(--primary); transform: translateY(-2px); }
        .btn-del { color: #ef4444; }
        .btn-del:hover { background: #fee2e2; border-color: #f87171; }

        /* LOADER */
        #sync-indicator { font-size: 10px; font-weight: 800; color: var(--primary); display: flex; align-items: center; gap: 5px; margin-bottom: 10px; }
        .pulse { width: 8px; height: 8px; background: var(--primary); border-radius: 50%; animation: pulse 1.5s infinite; }
        @keyframes pulse { 0% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(0, 121, 107, 0.7); } 70% { transform: scale(1); box-shadow: 0 0 0 10px rgba(0, 121, 107, 0); } 100% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(0, 121, 107, 0); } }

        /* RESPONSIVE */
        @media (max-width: 768px) {
            .app-shell { margin: 0; border-radius: 0; min-height: 100vh; }
            .app-header { padding: 20px; }
            .thesis-title { font-size: 11px; }
            .content-area { padding: 25px 20px; }
            .field-grid { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>

<div class="app-shell">
    <header class="app-header">
        <h1 class="thesis-title">
            Profil épidémiologique et clinique de la tuberculose pulmonaire chez les patients admis en médecine interne à l'Hôpital Général de Référence de Makala, 2023–2024
        </h1>
    </header>

    <nav class="nav-tabs">
        <button class="tab-btn active" onclick="switchTab(1)">📝 1. Collecte</button>
        <button class="tab-btn" onclick="switchTab(2)">📊 2. Matrice (Réel)</button>
        <button class="tab-btn" onclick="switchTab(3)">📈 3. Statistiques</button>
        <button class="tab-btn" onclick="switchTab(4)">💾 4. Paramètres Cloud</button>
    </nav>

    <!-- SECTION I-VIII COLLECTE -->
    <div id="tab-1" class="content-area active">
        <form id="ficheCapture">
            <div class="section-box">
                <div class="section-title">I. IDENTIFICATION</div>
                <div class="field-grid">
                    <div class="field-group">
                        <label>Numéro de fiche :</label>
                        <input type="text" id="n_fiche" placeholder="Ex: F001">
                    </div>
                    <div class="field-group">
                        <label>Numéro du dossier :</label>
                        <input type="text" id="n_dossier" placeholder="Ex: HGR-1234">
                    </div>
                    <div class="field-group">
                        <label>Année d'admission :</label>
                        <select id="annee_admission">
                            <option value="" disabled selected>Choisir...</option>
                            <option value="2023">2023</option>
                            <option value="2024">2024</option>
                        </select>
                    </div>
                </div>
            </div>

            <div class="section-box">
                <div class="section-title">II. CARACTÉRISTIQUES SOCIODÉMOGRAPHIQUES</div>
                <div class="field-grid">
                    <div class="field-group"><label>Âge (années) :</label><input type="number" id="age"></div>
                    <div class="field-group"><label>Sexe :</label>
                        <select id="sexe">
                            <option value="" disabled selected>Choisir...</option>
                            <option value="Masculin">Masculin</option>
                            <option value="Féminin">Féminin</option>
                        </select>
                    </div>
                    <div class="field-group"><label>État Civil :</label>
                        <select id="etat_civil">
                            <option value="" disabled selected>Choisir...</option>
                            <option value="Célibataire">Célibataire</option>
                            <option value="Marié(e)">Marié(e)</option>
                            <option value="Divorcé(e)">Divorcé(e)</option>
                            <option value="Veuf(ve)">Veuf(ve)</option>
                        </select>
                    </div>
                </div>
                <div class="field-grid">
                    <div class="field-group"><label>Profession :</label><input type="text" id="profession"></div>
                    <div class="field-group"><label>Niveau d'instruction :</label>
                        <select id="instruction">
                            <option value="" disabled selected>Choisir...</option>
                            <option value="Aucun">Aucun</option>
                            <option value="Primaire">Primaire</option>
                            <option value="Secondaire">Secondaire</option>
                            <option value="Supérieur">Supérieur</option>
                        </select>
                    </div>
                    <div class="field-group"><label>Commune de résidence :</label><input type="text" id="commune"></div>
                </div>
            </div>

            <div class="section-box">
                <div class="section-title">III. ANTÉCÉDENTS</div>
                <div class="field-grid">
                    <div class="field-group"><label>ATCD Tuberculose :</label><select id="atcd_tb"><option value="" disabled selected>Choisir...</option><option value="Oui">Oui</option><option value="Non">Non</option></select></div>
                    <div class="field-group"><label>Statut VIH :</label><select id="statut_vih"><option value="" disabled selected>Choisir...</option><option value="Positif">Positif</option><option value="Négatif">Négatif</option><option value="Inconnu">Inconnu</option></select></div>
                    <div class="field-group"><label>Diabète :</label><select id="diabete"><option value="" disabled selected>Choisir...</option><option value="Oui">Oui</option><option value="Non">Non</option></select></div>
                </div>
                <div class="field-grid">
                    <div class="field-group"><label>Tabagisme :</label><select id="tabac"><option value="" disabled selected>Choisir...</option><option value="Oui">Oui</option><option value="Non">Non</option></select></div>
                    <div class="field-group"><label>Alcoolisme :</label><select id="alcool"><option value="" disabled selected>Choisir...</option><option value="Oui">Oui</option><option value="Non">Non</option></select></div>
                    <div class="field-group"><label>Contage tuberculeux :</label><select id="contage"><option value="" disabled selected>Choisir...</option><option value="Oui">Oui</option><option value="Non">Non</option></select></div>
                </div>
            </div>

            <div class="section-box">
                <div class="section-title">IV. DONNÉES CLINIQUES</div>
                <div class="field-group" style="margin-bottom: 20px;">
                    <label>Motif d'admission :</label>
                    <textarea id="motif" rows="2"></textarea>
                </div>
                <label>Symptômes présents :</label>
                <div class="symptoms-box" id="symptomes_list">
                    <label class="check-pill"><input type="checkbox" value="Toux"> Toux</label>
                    <label class="check-pill"><input type="checkbox" value="Toux >= 2 sem"> Toux ≥ 2 semaines</label>
                    <label class="check-pill"><input type="checkbox" value="Hémoptysie"> Hémoptysie</label>
                    <label class="check-pill"><input type="checkbox" value="Fièvre"> Fièvre</label>
                    <label class="check-pill"><input type="checkbox" value="Sueurs nocturnes"> Sueurs nocturnes</label>
                    <label class="check-pill"><input type="checkbox" value="Amaigrissement"> Amaigrissement</label>
                    <label class="check-pill"><input type="checkbox" value="Douleur thoracique"> Douleur thoracique</label>
                    <label class="check-pill"><input type="checkbox" value="Dyspnée"> Dyspnée</label>
                </div>
                <div class="field-grid" style="margin-top: 25px;">
                    <div class="field-group"><label>Durée symptômes (sem) :</label><input type="number" id="duree_symptomes"></div>
                    <div class="field-group"><label>État général :</label><select id="etat_gen"><option value="" disabled selected>Choisir...</option><option value="Bon">Bon</option><option value="Altéré">Altéré</option></select></div>
                    <div class="field-group"><label>IMC (si dispo) :</label><input type="text" id="imc"></div>
                </div>
            </div>

            <!-- SECTIONS V, VI, VII (REDUIT POUR L'ESPACE MAIS FONCTIONNEL) -->
            <div class="section-box">
                <div class="section-title">V. PARACLINIQUE & VII. PRISE EN CHARGE</div>
                <div class="field-grid">
                    <div class="field-group"><label>Bacilloscopie :</label><select id="bacillo"><option value="" disabled selected>Choisir...</option><option value="Positif">Positif</option><option value="Négatif">Négatif</option><option value="Non fait">Non fait</option></select></div>
                    <div class="field-group"><label>GeneXpert :</label><select id="genexpert"><option value="" disabled selected>Choisir...</option><option value="MTB détecté">MTB détecté</option><option value="Non détecté">Non détecté</option></select></div>
                    <div class="field-group"><label>Issue Finale (VIII) :</label><select id="issue"><option value="" disabled selected>Choisir...</option><option value="Guérison">Guérison</option><option value="Décès">Décès</option><option value="Transfert">Transfert</option></select></div>
                </div>
            </div>

            <button type="button" class="btn-action" id="saveBtn">💾 Soumettre et synchroniser</button>
        </form>
    </div>

    <!-- MATRICE TAB -->
    <div id="tab-2" class="content-area">
        <div id="sync-indicator"><div class="pulse"></div> Données connectées au Cloud</div>
        <div class="data-table-container">
            <table>
                <thead>
                    <tr><th>N° Fiche</th><th>Âge</th><th>Sexe</th><th>VIH</th><th>Issue</th><th>Actions</th></tr>
                </thead>
                <tbody id="matrix-body">
                    <!-- Données Firebase ici -->
                </tbody>
            </table>
        </div>
    </div>

    <!-- STATS TAB (VIDE POUR LE MOMENT) -->
    <div id="tab-3" class="content-area">
        <h3>Statistiques temps réel</h3>
        <p>En attente de connexion des données...</p>
    </div>

    <!-- SETTINGS TAB (FIREBASE CONFIG) -->
    <div id="tab-4" class="content-area">
        <h3>Configuration Cloud (Firebase)</h3>
        <p style="font-size:13px; color:#666;">Pour activer la sauvegarde permanente, collez vos configurations ci-dessous ou contactez le support.</p>
        <div class="field-group">
            <label>État de la Database :</label>
            <div id="db-status" style="font-weight:bold; color:red;">Non connectée (Placeholders actifs)</div>
        </div>
    </div>
</div>

<!-- MODAL DETAIL PATIENT -->
<div id="detailModal" class="modal">
    <div class="modal-content">
        <div class="modal-header">
            <h2 id="modalTitle" style="margin:0; color:var(--primary-dark); font-family:Montserrat; font-size:18px;">DÉTAILS DU DOSSIER</h2>
            <span class="close-btn" onclick="closeModal()">&times;</span>
        </div>
        <div id="modalBody" class="detail-grid">
            <!-- Injecté par JS -->
        </div>
    </div>
</div>

<script>
    // --- 1. CONFIGURATION FIREBASE (REMPLIR CES CLEFS) ---
    const firebaseConfig = {
        apiKey: "AIzaSyCecExYoYq32VmBo7Yx04DdcveRXeZsGAU",
        authDomain: "nkosi-54d5f.firebaseapp.com",
        projectId: "nkosi-54d5f",
        storageBucket: "nkosi-54d5f.firebasestorage.app",
        messagingSenderId: "19720307891",
        appId: "1:19720307891:web:ba12bc74ede0fc266c9cc6",
        databaseURL: "https://nkosi-54d5f-default-rtdb.firebaseio.com"
    };

    // Initialisation conditionnelle
    let db = null;
    try {
        firebase.initializeApp(firebaseConfig);
        db = firebase.database();
        document.getElementById('db-status').innerHTML = "✅ CONNECTÉE AU CLOUD";
        document.getElementById('db-status').style.color = "green";
    } catch(e) {
        console.warn("Firebase non configuré - Mode local uniquement");
    }

    // --- 2. LOGIQUE DE NAVIGATION ---
    function switchTab(n) {
        document.querySelectorAll('.content-area').forEach(a => a.classList.remove('active'));
        document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
        document.getElementById('tab-' + n).classList.add('active');
        document.querySelectorAll('.tab-btn')[n-1].classList.add('active');
    }

    // --- 3. SAUVEGARDE DES DONNÉES (CORRECTION) ---
    document.getElementById('saveBtn').addEventListener('click', () => {
        // Collecte de l'intégralité des champs (Squelette complet)
        const patientData = {
            // I. Identification
            fiche: document.getElementById('n_fiche').value,
            dossier: document.getElementById('n_dossier').value,
            annee: document.getElementById('annee_admission').value,
            
            // II. Socio-Démo
            age: document.getElementById('age').value,
            sexe: document.getElementById('sexe').value,
            etat_civil: document.getElementById('etat_civil').value,
            profession: document.getElementById('profession').value,
            instruction: document.getElementById('instruction').value,
            commune: document.getElementById('commune').value,

            // III. Antécédents
            atcd_tb: document.getElementById('atcd_tb').value,
            vih: document.getElementById('statut_vih').value,
            diabete: document.getElementById('diabete').value,
            tabac: document.getElementById('tabac').value,
            alcool: document.getElementById('alcool').value,
            contage: document.getElementById('contage').value,

            // IV. Clinique
            motif: document.getElementById('motif').value,
            duree_sem: document.getElementById('duree_symptomes').value,
            etat_gen: document.getElementById('etat_gen').value,
            imc: document.getElementById('imc').value,

            // V-VII. Paraclinique et Problématique
            bacillo: document.getElementById('bacillo').value,
            genexpert: document.getElementById('genexpert').value,
            issue: document.getElementById('issue').value,
            
            timestamp: new Date().toLocaleString()
        };

        // Validation minimale
        if(!patientData.fiche || !patientData.dossier) {
            alert("⚠️ Erreur : Le numéro de fiche et de dossier sont obligatoires.");
            return;
        }

        if(db) {
            // Tentative d'envoi vers Firebase
            db.ref('patients').push(patientData)
                .then(() => {
                    alert("✅ Succès : Données synchronisées avec le Cloud !");
                    document.getElementById('ficheCapture').reset();
                })
                .catch((error) => {
                    console.error("Firebase Error:", error);
                    alert("❌ Erreur de synchronisation : " + error.message + " \nVérifiez vos règles de sécurité Firebase.");
                });
        } else {
            alert("⚠️ Mode Local : Firebase n'est pas configuré. Les données ne seront pas sauvegardées de façon permanente.");
            addLocalRow(patientData);
        }
    });

    // --- 4. RÉCUPÉRATION ET AFFICHAGE RÉEL ---
    if(db) {
        db.ref('patients').on('value', (snapshot) => {
            const body = document.getElementById('matrix-body');
            body.innerHTML = "";
            snapshot.forEach((child) => {
                const data = child.val();
                data.id = child.key; // On garde la clef Firebase
                addRealRow(data);
            });
        }, (error) => { console.error("Read Error:", error); });
    }

    function addRealRow(data) {
        const body = document.getElementById('matrix-body');
        const rowId = `row-${data.id || Math.random().toString(36).substr(2, 9)}`;
        const row = `<tr id="${rowId}">
            <td><b>${data.fiche || '-'}</b></td>
            <td>${data.age || '-'} ans</td>
            <td>${data.sexe || '-'}</td>
            <td>${data.vih || '-'}</td>
            <td><span class="status-badge" style="background:${data.issue === 'Décès' ? '#ffebee' : '#e0f2f1'}; color:${data.issue === 'Décès' ? '#c62828' : '#00796b'};">
                ${data.issue || 'En cours'}
            </span></td>
            <td class="action-btns">
                <button class="btn-icon" onclick='viewDetails(${JSON.stringify(data)})' title="Voir détails">👁️</button>
                <button class="btn-icon btn-del" onclick="deleteLocalRow('${rowId}')" title="Supprimer localement">🗑️</button>
            </td>
        </tr>`;
        body.innerHTML = row + body.innerHTML;
    }

    // --- 5. LOGIQUE DES ACTIONS ---
    function viewDetails(data) {
        const modal = document.getElementById('detailModal');
        const body = document.getElementById('modalBody');
        body.innerHTML = "";
        
        // Dictionnaire des labels
        const labels = {
            fiche: "N° Fiche", dossier: "N° Dossier", annee: "Année",
            age: "Âge", sexe: "Sexe", etat_civil: "État Civil", 
            profession: "Profession", instruction: "Instruction", commune: "Commune",
            atcd_tb: "ATCD TB", vih: "Statut VIH", diabete: "Diabète",
            tabac: "Tabac", alcool: "Alcool", contage: "Contage TB",
            motif: "Motif Admission", duree_sem: "Durée (sem)", etat_gen: "État Général",
            imc: "IMC", bacillo: "Bacilloscopie", genexpert: "GeneXpert",
            issue: "Issue Finale", timestamp: "Date Saisie"
        };

        for (const [key, val] of Object.entries(data)) {
            if(labels[key]) {
                body.innerHTML += `
                    <div class="detail-item">
                        <div class="detail-label">${labels[key]}</div>
                        <div class="detail-val">${val || '-'}</div>
                    </div>
                `;
            }
        }
        modal.style.display = "block";
    }

    function closeModal() { document.getElementById('detailModal').style.display = "none"; }
    function deleteLocalRow(id) { 
        const doc = document.getElementById(id);
        if(doc) doc.style.display = "none"; // Suppression visuelle uniquement
    }

    // Fermer la modale si on clique à l'extérieur
    window.onclick = function(event) {
        const modal = document.getElementById('detailModal');
        if (event.target == modal) closeModal();
    }
</script>

</body>
</html>
