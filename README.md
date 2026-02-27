# MyMost-skincare-app-
¡Bienvenida/o a la herramienta de diagnóstico digital de **[MOST K-BEAUTY]**!   Esta aplicación web fue diseñada para transformar la experiencia de compra de nuestros clientes, ofreciendo una consultoría personalizada basada en los principios de la cosmética coreana y el análisis de activos específicos.
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>K-Beauty Clinic - Test de Piel</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        :root { --accent: #a68a64; --bg: #fdfaf6; --text: #3d3d3d; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg); color: var(--text); margin: 0; padding: 20px; }
        .container { max-width: 600px; margin: auto; background: white; padding: 30px; border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.05); }
        h1 { color: var(--accent); text-align: center; font-weight: 300; letter-spacing: 2px; }
        .description { text-align: center; font-size: 0.9em; color: #888; margin-bottom: 30px; }
        
        /* Estilos del Formulario */
        .form-group { margin-bottom: 20px; }
        label { display: block; margin-bottom: 8px; font-weight: 600; color: #555; }
        input, select, textarea { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 10px; box-sizing: border-box; font-family: inherit; }
        textarea { height: 80px; }

        button { width: 100%; padding: 15px; border: none; border-radius: 10px; cursor: pointer; font-size: 16px; font-weight: bold; transition: 0.3s; margin-top: 10px; }
        .btn-submit { background-color: var(--accent); color: white; }
        .btn-submit:hover { background-color: #7f5539; }

        /* Tarjeta de Resultados */
        #result-card { display: none; margin-top: 30px; padding: 20px; border: 2px solid #f4e4e1; border-radius: 15px; background: #fffcf9; }
        .step { border-left: 3px solid var(--accent); padding-left: 15px; margin-bottom: 15px; }
        .btn-whatsapp { background-color: #25D366; color: white; text-decoration: none; display: block; text-align: center; padding: 15px; border-radius: 10px; margin-top: 10px; }
    </style>
</head>
<body>

<div class="container">
    <h1>K-BEAUTY CLINIC</h1>
    <p class="description">Descubre tu rutina coreana ideal en 1 minuto</p>

    <div id="quiz-container">
        <div class="form-group">
            <label>Nombre Completo:</label>
            <input type="text" id="name" placeholder="Tu nombre...">
        </div>

        <div class="form-group">
            <label>¿Cómo sientes tu piel?</label>
            <select id="skin_type">
                <option value="Grasa">Grasa (Brillo y poros abiertos)</option>
                <option value="Seca">Seca (Tirantez y opacidad)</option>
                <option value="Mixta">Mixta (Grasa solo en frente/nariz)</option>
                <option value="Sensible">Sensible (Rojeces e irritación)</option>
            </select>
        </div>

        <div class="form-group">
            <label>Tu rutina actual (opcional):</label>
            <textarea id="routine" placeholder="Ej: Jabón normal y crema hidratante..."></textarea>
        </div>

        <button class="btn-submit" onclick="calcular()">OBTENER MI DIAGNÓSTICO</button>
    </div>

    <div id="result-card">
        <h2 style="color: var(--accent);">Tu Plan Personalizado</h2>
        <div id="output"></div>
        <button style="background: #444; color: white;" onclick="descargarPDF()">Descargar Guía PDF</button>
        <a id="wp-link" href="#" class="btn-whatsapp" target="_blank">Contactar Experta por WhatsApp</a>
    </div>
</div>

<script>
    const configuracion = {
        telefono: "393520570220", // <--- CAMBIA ESTO CON TU NÚMERO
        activos: {
            "Grasa": { rostro: "BHA y Árbol de Té", ojos: "Cafeína", labios: "Ceramidas" },
            "Seca": { rostro: "Mucina de Caracol", ojos: "Péptidos", labios: "Miel" },
            "Mixta": { rostro: "Niacinamida", ojos: "Ácido Hialurónico", labios: "Arroz" },
            "Sensible": { rostro: "Centella Asiática", ojos: "Pantenol", labios: "Karité" }
        }
    };

    function calcular() {
        const name = document.getElementById('name').value || "Cliente";
        const type = document.getElementById('skin_type').value;
        const info = configuracion.activos[type];

        const htmlResult = `
            <div class="step"><strong>Rostro:</strong> Usar ${info.rostro}</div>
            <div class="step"><strong>Ojos:</strong> Usar ${info.ojos}</div>
            <div class="step"><strong>Labios:</strong> Usar ${info.labios}</div>
        `;

        document.getElementById('output').innerHTML = htmlResult;
        
        // Crear link WhatsApp
        const msg = `Hola! Soy ${name}. Mi test K-Beauty: Piel ${type}. Activos: ${info.rostro}. ¿Qué productos tienes?`;
        document.getElementById('wp-link').href = `https://wa.me/${configuracion.telefono}?text=${encodeURIComponent(msg)}`;

        document.getElementById('result-card').style.display = 'block';
        window.scrollTo(0, document.body.scrollHeight);
    }

    function descargarPDF() {
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF();
        const name = document.getElementById('name').value || "Cliente";
        const type = document.getElementById('skin_type').value;
        const info = configuracion.activos[type];

        doc.setFontSize(20);
        doc.text("K-BEAUTY CLINIC: REPORTE", 20, 20);
        doc.setFontSize(12);
        doc.text(`Cliente: ${name} | Tipo de Piel: ${type}`, 20, 40);
        doc.text(`- Rostro: ${info.rostro}`, 20, 60);
        doc.text(`- Ojos: ${info.ojos}`, 20, 70);
        doc.text(`- Labios: ${info.labios}`, 20, 80);
        doc.save(`Rutina_${name}.pdf`);
    }
</script>

</body>
</html>
