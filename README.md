# ESTUDIO-CREDITICIO
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Solicitud de Préstamo</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #1d0552;
            color: #ffffff;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }

        .container {
            background: #1d0552;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.2);
            width: 100%;
            max-width: 500px;
            text-align: center;
            color: #ffffff;
        }

        h1 {
            margin-bottom: 20px;
            color: #ffffff;
        }

        #form-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            color: #ffffff;
        }

        label {
            font-size: 1.2em;
            margin-bottom: 10px;
        }

        input, select {
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            border: 1px solid #ccc;
            width: 100%;
            color: #1d0552;
        }

        #button-container {
            margin-top: 20px;
        }

        button {
            background-color: #007bff;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            margin-right: 10px;
        }

        button:hover {
            background-color: #0056b3;
        }

        #result-container {
            margin-top: 20px;
            font-size: 1.2em;
            text-align: left;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Solicitud de Préstamo</h1>
        <div id="form-container">
            <form id="loan-form">
                <!-- Las preguntas se generarán aquí -->
            </form>
            <div id="button-container">
                <button id="next-button" onclick="nextQuestion()">Siguiente</button>
            </div>
            <div id="result-container"></div>
        </div>
    </div>
    <script>
        let currentQuestion = 0;
        const formContainer = document.getElementById('loan-form');
        const resultContainer = document.getElementById('result-container');
        const questions = [
            { question: "Edad:", type: "number" },
            { question: "Situación laboral:", options: ["Empleado", "Autónomo", "Desempleado", "Jubilado"] },
            { question: "Ingresos netos mensuales (€):", type: "number" },
            { question: "Ingresos adicionales (€):", type: "number" },
            { question: "Ingresos mensuales totales del hogar (€):", type: "number" },
            { question: "Gastos mensuales fijos (€):", type: "number" },
            { question: "Gastos mensuales variables (€):", type: "number" },
            { question: "Deudas pendientes (€):", type: "number" },
            { question: "Ahorros o inversiones (€):", type: "number" },
            { question: "Historial crediticio:", options: ["Sin retrasos", "Con retrasos", "En ASNEF/RAI"] },
            { question: "Importe del préstamo solicitado (€):", type: "number" },
            { question: "Plazo de devolución deseado (meses):", type: "number" },
            { question: "Finalidad del préstamo:", options: ["Consumo", "Consolidación de deudas", "Gastos médicos", "Otros"] }
        ];

        const answers = [];

        function renderQuestion() {
            formContainer.innerHTML = '';
            const question = questions[currentQuestion];
            const questionElement = document.createElement('div');
            questionElement.innerHTML = `<label>${question.question}</label>`;

            if (question.options) {
                questionElement.innerHTML += `<select name="answer" required>
                    ${question.options.map(option => `<option value="${option}">${option}</option>`).join('')}
                </select>`;
            } else {
                questionElement.innerHTML += `<input type="${question.type}" name="answer" required>`;
            }

            formContainer.appendChild(questionElement);
        }

        function nextQuestion() {
            const answerElement = document.querySelector('input[name="answer"], select[name="answer"]');
            if (answerElement) {
                const answer = answerElement.value;
                
                // Validar respuesta numérica si es necesario
                if (answerElement.type === 'number') {
                    if (!isValidNumberInput(answer)) {
                        alert('Por favor, ingresa un valor numérico válido para esta pregunta.');
                        return;
                    }
                }

                // Validar edad
                if (currentQuestion === 0) {
                    const age = parseInt(answer);
                    if (age < 18 || age > 75 || isNaN(age)) {
                        alert('Lo siento, debes tener entre 18 y 75 años para solicitar un préstamo.');
                        return;
                    }
                }

                answers.push(answer);
                currentQuestion++;
                if (currentQuestion < questions.length) {
                    renderQuestion();
                } else {
                    showResult();
                }
            } else {
                alert('Por favor, responde la pregunta.');
            }
        }

        function isValidNumberInput(value) {
            return /^\d+(\.\d+)?$/.test(value);
        }

        function showResult() {
            formContainer.innerHTML = '';
            document.getElementById('button-container').style.display = 'none';

            let score = 0;

            // Evaluación de la situación laboral
            if (answers[1] === 'Empleado' || answers[1] === 'Autónomo') {
                score += 20;
            } else if (answers[1] === 'Jubilado') {
                score += 10;
            }

            // Evaluación de los ingresos netos mensuales
            if (answers[2] >= 2000) {
                score += 20;
            } else if (answers[2] >= 1000) {
                score += 10;
            }

            // Evaluación de los ingresos adicionales
            if (answers[3] > 0) {
                score += 5;
            }

            // Evaluación de los ingresos mensuales totales del hogar
            if (answers[4] >= 3000) {
                score += 20;
            } else if (answers[4] >= 1500) {
                score += 10;
            }

            // Evaluación de los gastos mensuales fijos
            if (answers[5] < 1000) {
                score += 15;
            } else if (answers[5] < 2000) {
                score += 5;
            }

            // Evaluación de los gastos mensuales variables
            if (answers[6] < 500) {
                score += 10;
            } else if (answers[6] < 1000) {
                score += 5;
            }

            // Evaluación de las deudas pendientes
            if (answers[7] === 0) {
                score += 20;
            } else if (answers[7] < 1000) {
                score += 10;
            }

            // Evaluación de los ahorros o inversiones
            if (answers[8] >= 5000) {
                score += 20;
            } else if (answers[8] >= 1000) {
                score += 10;
            }

            // Evaluación del historial crediticio
            if (answers[9] === 'Sin retrasos') {
                score += 20;
            } else if (answers[9] === 'Con retrasos') {
                score += 5;
            }

            // Evaluación del importe del préstamo solicitado
            if (answers[10] <= 5000) {
                score += 15;
            } else if (answers[10] <= 10000) {
                score += 10;
            }

            // Evaluación del plazo de devolución deseado
            if (answers[11] <= 12) {
                score += 15;
            } else if (answers[11] <= 24) {
                score += 10;
            }

            // Evaluación de la finalidad del préstamo
            if (answers[12] === 'Consumo' || answers[12] === 'Consolidación de deudas') {
                score += 10;
            }

            let resultMessage = '';

            resultMessage += 'Basado en la información proporcionada, hemos evaluado tu solicitud de préstamo con detalle: ';

            if (score >= 150) {
                resultMessage += 'Tu solicitud muestra una alta probabilidad de ser aprobada. Tu situación financiera es sólida, con ingresos estables y un buen historial crediticio.';
            } else if (score >= 100) {
                resultMessage += 'Tu solicitud muestra una probabilidad media de ser aprobada. Tu situación financiera es aceptable, aunque podría haber áreas de mejora.';
            } else {
                resultMessage += 'Lamentablemente, en base a la información proporcionada, la probabilidad de aprobación en este momento es baja. Te recomendamos revisar tus ingresos y situación crediticia para aumentar tus posibilidades.';
            }

            resultMessage += '<br><br>';

            resultMessage += 'Es crucial considerar varios factores para la aprobación de préstamos en España en 2024, incluyendo la residencia legal, historial crediticio positivo, capacidad de endeudamiento y estabilidad financiera. Esto asegura tanto la viabilidad del préstamo como tu bienestar financiero a largo plazo.';

            resultContainer.innerHTML = `<p>${resultMessage}</p>`;
        }

        renderQuestion();
    </script>
</body>
</html>
