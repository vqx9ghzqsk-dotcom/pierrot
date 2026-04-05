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
        .modal-content { background: #fff; margin: 2vh auto; padding: 40px; border-radius: 28px; width: 100%; max-width: 900px; max-height: 90vh; overflow-y: auto; shadow: var(--shadow-lg); position: relative; }
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
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
const firebaseConfig = {
  apiKey: "AIzaSyBVatrQtiMnIvxz3sAbAhm1u8LxGQimlp4",
  authDomain: "pierrot-dcddb.firebaseapp.com",
  projectId: "pierrot-dcddb",
  storageBucket: "pierrot-dcddb.firebasestorage.app",
  messagingSenderId: "276195689486",
  appId: "1:276195689486:web:53f3983eb9e65725ce285f"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
</html>
