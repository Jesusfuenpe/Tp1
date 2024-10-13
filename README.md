<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agregar Materias y Alumnos</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
        }
        .container {
            width: 80%;
            margin: auto;
            overflow: hidden;
        }
        header {
            background-color: #333;
            color: #fff;
            padding-top: 30px;
            min-height: 70px;
            border-bottom: #77ab59 3px solid;
        }
        header h1 {
            text-align: center;
            margin: 0;
            font-size: 24px;
            text-transform: uppercase;
        }
        #main-form {
            background: #fff;
            padding: 20px;
            margin-top: 20px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        input[type="text"], input[type="number"] {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        button {
            display: block;
            width: 100%;
            padding: 10px;
            background-color: #77ab59;
            color: #fff;
            border: none;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #669e4e;
        }
        table {
            width: 100%;
            margin-top: 20px;
            border-collapse: collapse;
        }
        table th, table td {
            padding: 12px;
            text-align: left;
            border: 1px solid #ddd;
        }
        table th {
            background-color: #77ab59;
            color: #fff;
        }
    </style>
</head>
<body>

<header>
    <h1>Registro de Materias y Alumnos</h1>
</header>

<div class="container">
    <div id="main-form">
        <h2>Agregar Materia</h2>
        <label for="materia">Nombre de la Materia:</label>
        <input type="text" id="materia" placeholder="Ingrese el nombre de la materia">
        
        <label for="alumnos">Cantidad de Alumnos:</label>
        <input type="number" id="alumnos" placeholder="Ingrese la cantidad de alumnos">
        
        <button onclick="agregarMateria()">Agregar</button>
    </div>

    <h2>Materias Registradas</h2>
    <table>
        <thead>
            <tr>
                <th>Materia</th>
                <th>Cantidad de Alumnos</th>
                <th>Acciones</th>
            </tr>
        </thead>
        <tbody id="tablaMaterias">
        </tbody>
    </table>
    <button onclick="eliminarTodasMaterias()">Eliminar Todas las Materias</button>
</div>

<script>
    function agregarMateria() {
        const nombreMateria = document.getElementById('materia').value;
        const cantidadAlumnos = document.getElementById('alumnos').value;

        if (nombreMateria === '' || cantidadAlumnos === '') {
            alert('Por favor, complete todos los campos.');
            return;
        }

        const nuevaMateria = { nombre: nombreMateria, cantidad: cantidadAlumnos };

        fetch('http://localhost:3000/materias', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(nuevaMateria)
        })
        .then(response => response.json())
        .then(data => {
            alert(data.message); // Mostrar mensaje de éxito
            document.getElementById('materia').value = '';
            document.getElementById('alumnos').value = '';
            mostrarMaterias();
        })
        .catch(error => {
            console.error('Error:', error);
        });
    }

    function mostrarMaterias() {
        fetch('http://localhost:3000/materias')
        .then(response => response.json())
        .then(materias => {
            const tabla = document.getElementById('tablaMaterias');
            tabla.innerHTML = '';

            for (let i = 0; i < materias.length; i++) {
                const fila = `
                    <tr>
                        <td>${materias[i].nombre}</td>
                        <td>${materias[i].cantidad}</td>
                        <td>
                            <button onclick="eliminarMateria(${materias[i].id})">Eliminar</button>
                            <button onclick="verMateria(${materias[i].id})">Ver</button>
                        </td>
                    </tr>
                `;
                tabla.innerHTML += fila;
            }
        })
        .catch(error => {
            console.error('Error al cargar materias:', error);
        });
    }

    function eliminarTodasMaterias() {
        fetch('http://localhost:3000/materias', {
            method: 'DELETE'
        })
        .then(response => response.json())
        .then(data => {
            alert(data.message);
            mostrarMaterias();
        })
        .catch(error => {
            console.error('Error al eliminar materias:', error);
        });
    }

    function eliminarMateria(id) {
        fetch(`http://localhost:3000/materias/${id}`, {
            method: 'DELETE'
        })
        .then(response => response.json())
        .then(data => {
            alert(data.message);
            mostrarMaterias();
        })
        .catch(error => {
            console.error('Error al eliminar materia:', error);
        });
    }

    // Inicializar la carga de materias al cargar la página
    window.onload = mostrarMaterias;
</script>
</body>
</html>

