# validar-cif

[![tests](https://github.com/abasmartinc/validar-cif/actions/workflows/tests.yml/badge.svg)](https://github.com/abasmartinc/validar-cif/actions)

[![npm](https://img.shields.io/npm/v/validar-cif)](https://www.npmjs.com/package/validar-cif)
[![PyPI](https://img.shields.io/pypi/v/validar-cif)](https://pypi.org/project/validar-cif/)
[![Packagist](https://img.shields.io/packagist/v/apiempresas/validar-cif)](https://packagist.org/packages/apiempresas/validar-cif)
[![Licencia MIT](https://img.shields.io/badge/licencia-MIT-blue)](LICENSE)

Valida **CIF, DNI y NIE** españoles (formato y carácter de control) en **JavaScript, Python y PHP**. Sin dependencias, con tests y con el mismo resultado en los tres lenguajes.

Mantenido por [APIEmpresas.es](https://apiempresas.es). Es el mismo algoritmo que usa el [validador de CIF online](https://apiempresas.es/validar-cif).

> **Un CIF bien formado no garantiza que la empresa exista.** La fórmula es pública y un número inventado puede cuadrar. Para saber si hay una sociedad con ese CIF, su razón social y si sigue activa, usa la [API de empresas](https://apiempresas.es) (100 consultas gratis, sin tarjeta) o el [buscador de empresas por CIF](https://apiempresas.es/buscar-empresa-por-cif).

## JavaScript / Node.js

```bash
npm install validar-cif
```

```js
const { validarCif, validarNif, tipoNif, tipoEntidad } = require('validar-cif');

validarCif('A46103834');      // true
validarCif('a-46.103.834');   // true (limpia guiones, puntos, espacios y minúsculas)
validarCif('A46103835');      // false: el control debería ser 4
validarNif('12345678Z');      // true (CIF, DNI o NIE)
tipoNif('X1234567L');         // 'nie'
tipoEntidad('B00000000');     // 'Sociedad de responsabilidad limitada'
```

Incluye tipos de TypeScript.

## Python

```bash
pip install validar-cif
```

```python
from validar_cif import validar_cif, validar_nif, tipo_nif, tipo_entidad

validar_cif("A46103834")   # True
validar_nif("12345678Z")   # True
tipo_nif("X1234567L")      # 'nie'
tipo_entidad("A46103834")  # 'Sociedad anónima'
```

## PHP

```bash
composer require apiempresas/validar-cif
```

```php
use ApiEmpresas\ValidarCif\Validador;

Validador::cif('A46103834');          // true
Validador::nif('12345678Z');          // true
Validador::tipo('X1234567L');         // 'nie'
Validador::tipoEntidad('A46103834');  // 'Sociedad anónima'
```

## Funciones

| JavaScript | Python | PHP | Devuelve |
|---|---|---|---|
| `validarCif` | `validar_cif` | `Validador::cif` | `true` si el CIF de empresa está bien formado |
| `validarDni` | `validar_dni` | `Validador::dni` | `true` si la letra del DNI es correcta |
| `validarNie` | `validar_nie` | `Validador::nie` | `true` si la letra del NIE es correcta |
| `validarNif` | `validar_nif` | `Validador::nif` | `true` si es CIF, DNI o NIE válido |
| `tipoNif` | `tipo_nif` | `Validador::tipo` | `'cif'`, `'dni'`, `'nie'` o nulo |
| `tipoEntidad` | `tipo_entidad` | `Validador::tipoEntidad` | Forma jurídica según la letra del CIF |
| `limpiar` | `limpiar` | `Validador::limpiar` | El valor sin espacios, guiones, puntos ni barras, en mayúsculas |

## Cómo se calcula el control del CIF

Un CIF tiene 9 caracteres: una letra con la forma jurídica, 7 números y un carácter de control.

1. Suma los números en posición par (2.º, 4.º y 6.º).
2. Multiplica por 2 los de posición impar (1.º, 3.º, 5.º y 7.º); si sale de dos cifras, súmalas (7 × 2 = 14 → 5).
3. Suma los dos totales. Si la última cifra es 0, el control es 0; si no, 10 menos esa cifra.
4. Si el control va en letra, es la de esa posición en `JABCDEFGHI`.

Las letras A, B, E y H llevan control numérico; N, P, Q, R, S y W, letra; el resto, cualquiera de los dos. Ejemplo con A46103834: pares 6 + 0 + 8 = 14; impares 8 + 2 + 6 + 6 = 22; total 36 → control 10 − 6 = **4**.

En el DNI, la letra es la del resto de dividir el número entre 23 en `TRWAGMYFPDXBNJZSQVHLCKE`. En el NIE se cambia la X por 0, la Y por 1 y la Z por 2.

Más detalle y ejemplos (formularios, Laravel, Excel): [Validar CIF en PHP, Python y JavaScript](https://apiempresas.es/validar-cif-php-python) y [fórmula para Excel y Google Sheets](https://apiempresas.es/validar-cif-excel).

## Comprobar si la empresa existe

El formato solo dice que el número está bien escrito. Para saber si la empresa existe, su razón social oficial y si sigue activa, una llamada a la API:

```bash
curl "https://apiempresas.es/api/v1/companies?cif=A46103834" -H "X-API-KEY: TU_API_KEY"
```

Devuelve razón social, estado (activa, en concurso, extinguida…), CNAE, provincia y, en el plan Pro, administradores y domicilio. Para comprobar toda la lista de clientes antes de facturar (Verifactu), `POST /api/v1/companies/verify/batch` revisa hasta 1.000 NIF por petición.

- SDK oficiales: `npm install apiempresas`, `pip install apiempresas`, `composer require apiempresas/apiempresas-php`.
- [Crear cuenta gratis](https://apiempresas.es/register?intent=api&plan=free&source=github_validar_cif) · [Precios](https://apiempresas.es/precios) · [Documentación](https://apiempresas.es/documentation)

Herramientas gratis sin programar: [validar CIF](https://apiempresas.es/validar-cif), [comprobar NIF, DNI o NIE](https://apiempresas.es/comprobar-nif), [buscar empresa por CIF](https://apiempresas.es/buscar-empresa-por-cif) y [comprobar el NIF de tus clientes antes de facturar](https://apiempresas.es/validar-nif-clientes-facturacion).

## Tests

```bash
node js/test.js
cd python && python -m unittest discover -s tests
composer install && composer test
```

## English

Validates Spanish tax IDs (CIF for companies, DNI and NIE for individuals) in JavaScript, Python and PHP, with no dependencies. To check whether a company actually exists and is active, use the [Spanish company data API](https://spaincompanyapi.com).

## Licencia

MIT
