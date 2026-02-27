# MyMost-skincare-app-
¡Bienvenida/o a la herramienta de diagnóstico digital de **[MOST K-BEAUTY]**!   Esta aplicación web fue diseñada para transformar la experiencia de compra de nuestros clientes, ofreciendo una consultoría personalizada basada en los principios de la cosmética coreana y el análisis de activos específicos.
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>K-Beauty Expert - Análisis Integral</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        :root { --accent: #a68a64; --bg: #fdfaf6; --text: #3d3d3d; --warning: #d9534f; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg); color: var(--text); margin: 0; padding: 20px; }
        .container { max-width: 650px; margin: auto; background: white; padding: 30px; border-radius: 25px; box-shadow: 0 10px 30px rgba(0,0,0,0.05); }
        h1 { color: var(--accent); text-align: center; font-weight: 300; letter-spacing: 2px; margin-top: 0; }
        .form-row { display: flex; gap: 10px; margin-bottom: 15px; flex-wrap: wrap; }
        .form-group { flex: 1; min-width: 200px; margin-bottom: 15px; }
        label { display: block; margin-bottom: 8px; font-weight: 600; font-size: 0.8em; text-transform: uppercase; color: #777; }
        input, select, textarea { width: 100%; padding: 12px; border: 1px solid #eee; border-radius: 12px; font-size: 15px; box-sizing: border-box; background: #fff; font-family: inherit; }
        button { width: 100%; padding: 16px; border: none; border-radius: 12px; cursor: pointer; font-size: 16px; font-weight: bold; transition: 0.3s; margin-top: 10px; }
        .btn-submit { background-color: var(--accent); color: white; }
        #result-card { display: none; margin-top: 30px; padding: 25px; border-radius: 20px; background: #fffcf9; border: 1.5px solid #e6ccb2; }
        .step { border-left: 4px solid var(--accent); padding-left: 15px; margin-bottom: 15px; }
        .warning-box { background: #fdf2f2; border: 1px solid var(--warning); padding: 10px; border-radius: 10px; font-size: 0.9em; color: var(--warning); margin-bottom: 15px; }
        .btn-whatsapp { background-color: #25D366; color: white; text-decoration: none; display: block; text-align: center; padding: 15px; border-radius: 12px; margin-top: 15px; }
    </style>
</head>
<body>

<div class="container">
    <h1>K-BEAUTY LAB</h1>
    <p style="text-align:center; font-size: 0.8em; color: #aaa; margin-bottom: 25px;">ANÁLISIS PROFESIONAL DE ESTILO DE VIDA</p>

    <div class="form-row">
        <div class="form-group">
            <label>Nombre</label>
            <input type="text" id="name" placeholder="Tu nombre...">
        </div>
        <div class="form-group">
            <label>Edad</label>
            <input type="number" id="age" placeholder="Años">
        </div>
    </div>

    <div class="form-row">
        <div class="form-group">
            <label>Clima Predominante</label>
            <select id="climate">
                <option value="Seco">Seco (Desértico o Frío seco)</option>
                <option value="Húmedo">Húmedo (Tropical o Costa)</option>
                <option value="Templado">Templado / Variable</option>
            </select>
        </div>
        <div class="form-group">
            <label>¿Hábito de Fumar?</label>
            <select id="smoker">
                <option value="No">No fumo</option>
                <option value="Si">Sí, fumo habitualmente</option>
            </select>
        </div>
    </div>

    <div class="form-group">
        <label>Tipo de Piel y Objetivo</label>
        <select id="skin_type">
            <option value="Grasa">Grasa / Brillo</option>
            <option value="Seca">Seca / Tirantez</option>
            <option value="Mixta">Mixta</option>
            <option value="Sensible">Sensible</option>
        </select>
    </div>

    <div class="form-group">
        <label>Preocupación Principal</label>
        <select id="concern">
            <option value="Acné / Grasitud">Acné / Grasitud</option>
            <option value="Manchas / Opacidad">Manchas / Opacidad</option>
            <option value="Arrugas / Flacidez">Arrugas / Flacidez</option>
            <option value="Rojeces / Sensibilidad">Rojeces / Sensibilidad</option>
        </select>
    </div>

    <button class="btn-submit" onclick="calcular()">OBTENER ESTUDIO COMPLETO</button>

    <div id="result-card">
        <h2 id="res-header" style="color:var(--accent); margin-top:0;"></h2>
        <div id="warning-area"></div>
        <div id="output"></div>
        <button style="background: #444; color: white;" onclick="descargarPDF()">DESCARGAR REPORTE</button>
        <a id="wp-link" href="#" class="btn-whatsapp" target="_blank">ENVIAR A MI ASESORA</a>
    </div>
</div>

<script>
    const dbK = {
        "Acné / Grasitud": { activo: "BHA + Centella", ojos: "Cafeína", labios: "Ceramidas" },
        "Manchas / Opacidad": { activo: "Vitamina C + Arroz", ojos: "Niacinamida", labios: "Miel" },
        "Arrugas / Flacidez": { activo: "Ginseng + Péptidos", ojos: "Adenosina", labios: "Colágeno" },
        "Rojeces / Sensibilidad": { activo: "Cica + Artemisa", ojos: "Pantenol", labios: "Karité" }
    };

    function calcular() {
        const name = document.getElementById('name').value || "Cliente";
        const smoker = document.getElementById('smoker').value;
        const climate = document.getElementById('climate').value;
        const concern = document.getElementById('concern').value;
        const info = dbK[concern];

        document.getElementById('res-header').innerText = `Estudio de ${name}`;
        
        let warningHTML = "";
        if(smoker === "Si") {
            warningHTML = `<div class="warning-box"><strong>Nota de Experta:</strong> El tabaco acelera la oxidación. Te recomendamos duplicar el uso de antioxidantes (Vitamina C/E) para recuperar la luminosidad.</div>`;
        }

        const climaConsejo = climate === "Seco" ? "Debes usar cremas oclusivas." : "Prefiere texturas tipo gel.";

        document.getElementById('warning-area').innerHTML = warningHTML;
        document.getElementById('output').innerHTML = `
            <div class="step"><strong>Clima:</strong> ${climate}. (${climaConsejo})</div>
            <div class="step"><strong>Tratamiento Rostro:</strong> ${info.activo}</div>
            <div class="step"><strong>Contorno de Ojos:</strong> ${info.ojos}</div>
            <div class="step"><strong>Cuidado Labios:</strong> ${info.labios}</div>
        `;

        const msg = `Hola! Soy ${name}. Mi piel es de clima ${climate} y ${smoker === "Si" ? "fumo" : "no fumo"}. Quiero tratar ${concern}. ¿Qué me recomiendas?`;
        document.getElementById('wp-link').href = `https://wa.me/521234567890?text=${encodeURIComponent(msg)}`;

        document.getElementById('result-card').style.display = 'block';
        window.scrollTo(0, document.body.scrollHeight);
    }

    function descargarPDF() {
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF();
        const name = document.getElementById('name').value || "Cliente";
        const smoker = document.getElementById('smoker').value;
        const climate = document.getElementById('climate').value;
        const concern = document.getElementById('concern').value;
        const info = dbK[concern];

        doc.setFont("helvetica", "bold");
        doc.text("FICHA TÉCNICA SKINCARE PROFESIONAL", 20, 20);
        doc.setFontSize(10);
        doc.setFont("helvetica", "normal");
        doc.text(`Nombre: ${name} | Clima: ${climate} | Fumador: ${smoker}`, 20, 35);
        doc.line(20, 40, 190, 40);

        doc.text("RECOMENDACIÓN DE ACTIVOS:", 20, 55);
        doc.text(`- Activo Principal: ${info.activo}`, 20, 65);
        doc.text(`- Ojos: ${info.ojos}`, 20, 75);
        doc.text(`- Labios: ${info.labios}`, 20, 85);
        
        if(smoker === "Si") {
            doc.setTextColor(200, 0, 0);
            doc.text("ALERTA: Se sugiere reforzar con antioxidantes por hábito de fumar.", 20, 105);
        }

        doc.save(`Ficha_KBeauty_${name}.pdf`);
    }
</script>
</body>
</html>


    
