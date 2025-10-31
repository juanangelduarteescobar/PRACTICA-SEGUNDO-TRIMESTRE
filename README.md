# PRACTICA-SEGUNDO-TRIMESTRE
1) Resolver “Hola Mundo” con promesa
function hola() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve("Hola Mundo");
    }, 200);
  });
}

// Consumir con then
hola().then((mensaje) => console.log(mensaje));

// Consumir con await (necesita estar dentro de una función asíncrona)
async function consumirHola() {
  const mensaje = await hola();
  console.log(mensaje);
}

consumirHola();

2) Validar parámetro y rechazar
function validarNumero(n) {
  return new Promise((resolve, reject) => {
    if (isNaN(n)) {
      reject(new Error("No es número"));
    } else {
      setTimeout(() => resolve(n * 2), 150);
    }
  });
}

// Manejar error con try/catch
async function consumirValidarNumero() {
  try {
    const resultado = await validarNumero(5);
    console.log(resultado);  // 10
  } catch (error) {
    console.error(error.message);  // "No es número" si pasa un valor incorrecto
  }
}

consumirValidarNumero();

3) Encadenar transformaciones
function obtenerBase() {
  return new Promise((resolve) => {
    setTimeout(() => resolve(10), 100);
  });
}

obtenerBase()
  .then((valor) => valor + 5)  // Sumar 5
  .then((valor) => valor * 3)  // Multiplicar por 3
  .then((valor) => `resultado: ${valor}`)  // Convertir a string
  .then((mensaje) => console.log(mensaje));  // "resultado: 45"

4) Paralelizar con Promise.all
function tarea(id, ms) {
  return new Promise((resolve) => {
    setTimeout(() => resolve(id), ms);
  });
}

Promise.all([tarea(1, 300), tarea(2, 100), tarea(3, 200)])
  .then((resultados) => console.log(resultados));  // [2, 3, 1]

5) El primero en llegar con Promise.race
function tareaConDelay(id, ms) {
  return new Promise((resolve) => {
    setTimeout(() => resolve(id), ms);
  });
}

Promise.race([tareaConDelay(1, 150), tareaConDelay(2, 400)])
  .then((resultado) => console.log(`El primero en llegar es: ${resultado}`));  // "El primero en llegar es: 1"

6) Promise.allSettled con éxito y error
function tarea(id, ms, falla = false) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (falla) {
        reject(`Error en tarea ${id}`);
      } else {
        resolve(`Éxito en tarea ${id}`);
      }
    }, ms);
  });
}

Promise.allSettled([
  tarea(1, 100),
  tarea(2, 150, true),  // Esta fallará
  tarea(3, 200),
]).then((resultados) => {
  resultados.forEach((resultado) => {
    console.log(resultado.status);  // "fulfilled" o "rejected"
    console.log(resultado.value || resultado.reason);  // Resultado o error
  });
});

7) Timeout simple (corta operaciones lentas)
function conTimeout(promesa, ms) {
  let timeout;
  const timeoutPromesa = new Promise((_, reject) => {
    timeout = setTimeout(() => reject('Timeout'), ms);
  });

  return Promise.race([promesa, timeoutPromesa]).finally(() => clearTimeout(timeout));
}

// Ejemplo de uso
const promesaLarga = new Promise((resolve) => setTimeout(() => resolve('Éxito'), 500));
conTimeout(promesaLarga, 200)
  .then((resultado) => console.log(resultado))
  .catch((error) => console.error(error));  // "Timeout"

8) Secuencial por dependencia
function login(user, pass) {
  return new Promise((resolve) => {
    setTimeout(() => resolve({ token: '12345' }), 100);
  });
}

function perfil(token) {
  return new Promise((resolve) => {
    setTimeout(() => resolve({ nombre: 'Juan' }), 120);
  });
}

// Secuencial
async function obtenerPerfil() {
  try {
    const { token } = await login('usuario', 'contraseña');
    const { nombre } = await perfil(token);
    console.log(nombre);  // "Juan"
  } catch (error) {
    console.error(error);
  }
}

obtenerPerfil();

9) Map paralelo + manejo de errores
function getUsuario(id) {
  return new Promise((resolve, reject) => {
    if (id <= 0) {
      reject(`Error: Usuario con id ${id} no existe`);
    } else {
      setTimeout(() => resolve(`Usuario ${id}`), 100);
    }
  });
}

const ids = [1, 2, 0, 3];

async function obtenerUsuarios() {
  const resultados = await Promise.allSettled(ids.map(id => getUsuario(id)));

  resultados.forEach((resultado, index) => {
    if (resultado.status === 'rejected') {
      console.log(`Error al obtener el usuario con id ${ids[index]}: ${resultado.reason}`);
    } else {
      console.log(`Usuario obtenido: ${resultado.value}`);
    }
  });
}

obtenerUsuarios();
