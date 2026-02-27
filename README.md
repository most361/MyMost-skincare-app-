# MyMost-skincare-app-
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>K-Beauty Clinic - Analisi Professionale</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        :root { --gold: #b08968; --soft-bg: #fdfaf6; --dark: #2f3e46; }
        body { font-family: 'Helvetica Neue', sans-serif; background-color: var(--soft-bg); color: var(--dark); padding: 15px; }
        .container { max-width: 700px; margin: auto; background: white; padding: 35px; border-radius: 30px; box-shadow: 0 15px 40px rgba(0,0,0,0.06); }
        h1 { color: var(--gold); text-align: center; letter-spacing: 2px; text-transform: uppercase; font-size: 24px; }
        h3 { border-bottom: 1px solid #eee; padding-bottom: 10px; font-size: 14px; text-transform: uppercase; color: var(--gold); margin-top: 25px; }
        
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .form-group { margin-bottom: 15px; }
        label { display: block; margin-bottom: 5px; font-weight: 600; font-size: 13px; }
        input, select, textarea { width: 100%; padding: 12px; border: 1px solid #eee; border-radius: 12px; font-size: 14px; box-sizing: border-box; }
        
        /* Checkbox Styling para múltiples opciones */
        .checkbox-group { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; background: #f9f9f9; padding: 15px; border-radius: 12px; }
        .checkbox-item { display: flex; align-items: center; font-size: 13px; }
        .checkbox-item input { width: auto; margin-right: 8px; }

        button { width: 100%; padding: 18px; background: var(--gold); color: white; border: none; border-radius: 12px; cursor: pointer; font-weight: bold; font-size: 16px; margin-top: 20px; transition: 0.3s; }
        button:hover { background: #7f5539; }

        #result-card { display: none; margin-top: 30px; padding: 25px; border-radius: 20px; border: 2px solid var(--gold); background: #fffcf9; }
        .routine-step { margin-bottom: 15px; padding-left: 15px; border-left: 3px solid var(--gold); }
        .warning { color: #d9534f; font-size: 0.85em; font-style: italic; margin-top: 10px; }
    </style>
</head>
<body>

<div class="container">
    <h1>K-BEAUTY CLINIC</h1>
    <p style="text-align:center; font-size: 12px; color: #999;">ANALISI CUTANEA AVANZATA & ANAMNESI</p>

    <h3>1. Dati Personali</h3>
    <div class="grid">
        <div class="form-group"><label>Nome</label><input type="text" id="name"></div>
        <div class="form-group"><label>Età</label><input type="number" id="age"></div>
    </div>
    <div class="grid">
        <div class="form-group">
            <label>Sesso</label>
            <select id="gender"><option value="Femmina">Femmina</option><option value="Maschio">Maschio</option></select>
        </div>
        <div class="form-group">
            <label>Clima</label>
            <select id="climate"><option value="Secco">Secco</option><option value="Umido">Umido</option><option value="Temperato">Temperato</option></select>
        </div>
    </div>

    <h3>2. Stile di Vita</h3>
    <div class="grid">
        <div class="form-group">
            <label>Fumatore?</label>
            <select id="smoker"><option value="No">No</option><option value="Si">Sì</option></select>
        </div>
        <div class="form-group">
            <label>Esposizione Solare</label>
            <select id="sun"><option value="Bassa">Bassa</option><option value="Media">Media</option><option value="Alta">Alta</option></select>
        </div>
    </div>

    <h3>3. Obiettivi di Bellezza (Scegli max 3)</h3>
    <div class="checkbox-group" id="concerns-group">
        <div class="checkbox-item"><input type="checkbox" value="Rughe/Fidatezza"> Rughe / Cedimento</div>
        <div class="checkbox-item"><input type="checkbox" value="Macchie"> Macchie / Iperpigmentazione</div>
        <div class="checkbox-item"><input type="checkbox" value="Sensibilità"> Sensibilità / Arrossamento</div>
        <div class="checkbox-item"><input type="checkbox" value="Acne"> Acne / Impurità</div>
        <div class="checkbox-item"><input type="checkbox" value="Grasso"> Eccesso di Sebo</div>
        <div class="checkbox-item"><input type="checkbox" value="Texture"> Texture Irregolare</div>
        <div class="checkbox-item"><input type="checkbox" value="Idratazione"> Disidratazione</div>
    </div>

    <h3>4. Preferenza Routine</h3>
    <select id="routine_type">
        <option value="4">Base Coreana (4 Passi)</option>
        <option value="10">Autentica Coreana (10 Passi)</option>
    </select>

    <button onclick="generareAnalisi()">GENERARE DIAGNOSI</button>

    <div id="result-card">
        <h2 id="res-title" style="color:var(--gold);"></h2>
        <div id="res-content"></div>
        <button style="background:#444;" onclick="scaricarePDF()">SCARICA PDF</button>
        <a id="wa-link" href="#" target="_blank" style="text-decoration:none;">
            <button style="background:#25D366;">CONSULENZA WHATSAPP</button>
        </a>
    </div>
</div>

<script>
    const activos = {
        "Rughe/Fidatezza": "Retinoidi, PDRN (DNA di Salmone), Resveratrolo e Peptidi.",
        "Macchie": "Acido Tranexamico, Arbutina, Acido Kojico e Vitamina C.",
        "Sensibilità": "Centella Asiatica, Niacinamide e Pantenolo.",
        "Acne": "BHA (Acido Salicilico), Zinco e Olio di Tea Tree.",
        "Grasso": "Niacinamide e PHA (Gluconolattone).",
        "Texture": "AHA (Acido Glicolico/Lattico) per esfoliazione.",
        "Idratazione": "Acido Ialuronico e Ceramidi."
    };

    function generareAnalisi() {
        const selected = Array.from(document.querySelectorAll('#concerns-group input:checked')).map(i => i.value);
        if (selected.length > 3) { alert("Per favore, seleziona massimo 3 opzioni."); return; }
        if (selected.length === 0) { alert("Seleziona almeno un obiettivo."); return; }

        const name = document.getElementById('name').value || "Cliente";
        const routineType = document.getElementById('routine_type').value;
        const smoker = document.getElementById('smoker').value;

        let advice = selected.map(s => `<li><strong>${s}:</strong> ${activos[s]}</li>`).join("");
        
        let steps = routineType === "4" 
            ? "1. Detersione, 2. Siero Attivo, 3. Crema Idratante, 4. Protezione Solare."
            : "1. Doppia Detersione, 2. Esfoliazione, 3. Tonico, 4. Essenza, 5. Siero, 6. Maschera, 7. Contorno Occhi, 8. Labbra, 9. Idratante, 10. Protezione.";

        document.getElementById('res-title').innerText = `Piano per ${name}`;
        document.getElementById('res-content').innerHTML = `
            <div class="routine-step"><strong>Obiettivi:</strong> ${selected.join(", ")}</div>
            <div class="routine-step"><strong>Attivi Consigliati:</strong><ul>${advice}</ul></div>
            <div class="routine-step"><strong>Routine (${routineType} passi):</strong><br>${steps}</div>
            ${smoker === 'Si' ? '<p class="warning">Nota: Il fumo degrada il collagene. Aumentare l\'uso di Resveratrolo.</p>' : ''}
        `;

        const waMsg = encodeURIComponent(`Ciao! Sono ${name}. Il mio test K-Beauty suggerisce: ${selected.join(", ")}. Voglio la routine da ${routineType} passi.`);
        document.getElementById('wa-link').href = `https://wa.me/393520570220?text=${waMsg}`; // Cambia il numero qui
        
        document.getElementById('result-card').style.display = 'block';
        window.scrollTo(0, document.body.scrollHeight);
    }

    function scaricarePDF() {
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF();
        const content = document.getElementById('res-content').innerText;
        doc.setFontSize(18);
        doc.text("REPORT K-BEAUTY CLINIC", 20, 20);
        doc.setFontSize(11);
        doc.text(doc.splitTextToSize(content, 170), 20, 40);
        doc.save("Diagnosi_Skincare.pdf");
    }
</script>

</body>
</html>




    
