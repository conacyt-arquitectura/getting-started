Lineamientos generales para el versionamiento de  código

1. Un número normal de versión **DEBE** tomar la forma `X.Y.Z` donde `X`, `Y`, y `Z` son enteros no negativos. `X` es la versión **“major”**, `Y` es la versión **“minor”**, y `Z` es la versión **“patch”**. Cada elemento **DEBE** incrementarse numéricamente en incrementos de 1. Por ejemplo: `1.9.0` -> `1.10.0` -> `1.11.0`

2. Una vez que un paquete versionado ha sido liberado **(release)**, los contenidos de esa versión **NO DEBEN** ser modificadas. Cualquier modificación **DEBE** ser liberada como una nueva versión

3. La _versión major_ en cero `(0.y.z)` es para desarrollo inicial. Cualquier cosa puede cambiar en cualquier momento. El API pública no debiera ser considerada estable

4. La versión `1.0.0` define el API pública. La forma en que el número de versión es incrementado después de este release depende de esta API pública y de cómo esta cambia

5. La _versión patch_ `Z` `(x.y.Z | x > 0)` **DEBE** incrementarse cuando se introducen solo arreglos compatibles con la versión anterior. Un arreglo de bug se define como un cambio interno que corrige un comportamiento erróneo

6. La _versión minor_ `Y` `(x.Y.z | x > 0)` **DEBE** ser incrementada si se introduce nueva funcionalidad compatible con la versión anterior. Se **DEBE** incrementar si cualquier funcionalidad de la API es marcada como deprecada. **PUEDE** ser incrementada si se agrega funcionalidad o arreglos considerables al código privado. Puede incluir cambios de nivel `patch`. La _versión patch_ **DEBE** ser reseteada a 0 cuando la _versión minor_ es incrementada

7. La _versión major_ `X` `(X.y.z | X > 0)` **DEBE** ser incrementada si cualquier cambio no compatible con la versión anterior es introducida a la API pública. **PUEDE** incluir cambios de nivel `minor` y/o `patch`. Las _versiones patch_ y _minor_ **DEBEN** ser reseteadas a 0 cuando se incrementa la _versión major_

8. Una _versión pre-release_ **PUEDE** ser representada por adjuntar un guión y una serie de identificadores separados por puntos inmediatamente después de la _versión patch_. Los identificadores **DEBEN** consistir solo de caracteres **ASCII** alfanuméricos y el guión `[0-9A-Za-z-]`. Las _versiones pre-release_ satisfacen pero tienen una menor precedencia que la versión normal asociada.Ejemplos: `1.0.0-alpha`, `1.0.0-alpha.1`, `1.0.0-0.3.7`, `1.0.0-x.7.z.92`

9. La _metadata de build_ **PUEDE** ser representada adjuntando un signo más y una serie de identificadores separados por puntos inmediatamente después de la _versión patch_ o la _pre-release_. Los identificadores **DEBEN** consistir sólo de caracteres **ASCII** alfanuméricos y el guión `[0-9A-Za-z-]`. Los meta-datos de build **DEBIERAN** ser ignorados cuando se intenta determinar precedencia de versiones. Luego, dos paquetes con la misma versión pero distinta metadata de build se consideran la misma versión. Ejemplos: `1.0.0-alpha+001`, `1.0.0+20130313144700`, `1.0.0-beta+exp.sha.5114f85`

10. La precedencia se refiere a como son comparadas dos versiones una con la otra cuando son ordeandas. La precedencia **DEBE** ser calculada separando la _versión en major_, _minor_, _patch_ e identificadores `pre-release` en ese orden (La metadata de build no figuran en la precedencia). Las versiones `major`, `minor`, y `patch` son siempre comparadas numéricamente. La precedencia de `pre-release` **DEBE** ser determinada comparando cada identificador separado por puntos de la siguiente manera: los identificadores que solo consisten de números son comparados numéricamente y los identificadores con letras o guiones son comparados de acuerdo al orden establecido por **ASCII**. Los identificadores numéricos siempre tienen una precedencia menor que los no-numéricos. Ejemplo: `1.0.0-alpha` `<` `1.0.0-alpha.1` `<` `1.0.0-beta.2` `<` `1.0.0-beta.11` `<` `1.0.0-rc.1` `<` `1.0.0`
