# decisiones.md — TP01 Git Básico

## 1. Configurar entorno y preparar repositorio
- **user.name**: Belén Treachi
- **user.email**: belutreachi22@gmail.com
- Comandos usados:
```bash
git config user.name "Belén Treachi"
git config user.email "belutreachi22@gmail.com"
git config --list --show-origin | grep -E "user.name|user.email"
git remote add upstream https://github.com/ingsoft3ucc/2025_TP01_RepoBase.git
```

## 2. Desarrollo de la funcionalidad (rama + commits atómicos)

**Rama creada:** `feat/nueva-funcionalidad`  
**Comandos ejecutados:**
```bash
git checkout -b feat/nueva-funcionalidad
mkdir -p docs
echo "# Nueva funcionalidad\n\nDescripción breve de la feature y cómo probarla." > docs/feature-nueva.md
git add docs/feature-nueva.md
git commit -m "docs: agrega documento de nueva funcionalidad"

echo "\n## Nueva funcionalidad\nVer docs/feature-nueva.md para detalles." >> README.md
git add README.md
git commit -m "docs: referencia la nueva funcionalidad en README"

git push -u origin feat/nueva-funcionalidad
```

**Commits realizados (atómicos):**
1. `docs: agrega documento de nueva funcionalidad` → Alta del archivo `docs/feature-nueva.md` con la descripción y cómo probar.
2. `docs: referencia la nueva funcionalidad en README` → Enlace desde `README.md` hacia la documentación creada.

### Justificación de la estrategia

- **¿Por qué esa rama (feature branch)?**
  - Aísla el desarrollo de `main` (que representa producción), evitando introducir cambios inestables.
  - Permite abrir un **Pull Request** para revisión, discusiones y pruebas antes de integrar.
  - Facilita la **trazabilidad** del trabajo (todas las tareas de la feature viven en una rama con nombre semántico).
  - Convención: prefijo `feat/` para identificar fácilmente el tipo de trabajo (feature) en el listado de ramas.

- **¿Por qué esos commits (atómicos y con mensajes claros)?**
  - Cada commit introduce **un cambio pequeño y coherente** (primero crear la doc de la feature; luego enlazarla en el README).
  - Facilita revertir o bisectar (“¿cuándo se rompió algo?”) porque cada commit es minimalista.
  - Mensajería con **Conventional Commits** (`docs:`) mejora la lectura del historial, búsquedas y automatizaciones (changelogs, releases).
  - La granularidad de los commits aporta **auditoría y trazabilidad**: se entiende qué cambió, por qué y dónde.

**Criterios de calidad aplicados:**
- Rama separada + PR → control de integración.
- Commits atómicos + mensajes estándar → claridad histórica y mantenibilidad.
- Documentación de la feature y referencia en README → mejora la experiencia de prueba/uso y la transferencia al equipo.

## 3. Corrección de error (hotfix) e integración

**Escenario simulado:** agregué intencionalmente una línea errónea en `main` para emular un fallo en producción y practicar un hotfix.

**Ramas usadas:**
- `main` (producción)
- `hotfix/readme-errata` (corrección urgente)
- `feat/nueva-funcionalidad` (desarrollo en curso)

**Comandos ejecutados:**
```bash
# 1) Simular error en main
git checkout main
echo "\nERR: línea con error simulado" >> README.md
git add README.md
git commit -m "chore: simula error en main para práctica de hotfix"
git push origin main
```
```bash
# 2) Crear hotfix y arreglar
git checkout -b hotfix/readme-errata
# eliminar la línea de error
sed -i.bak '/ERR: línea con error simulado/d' README.md && rm README.md.bak
git add README.md
git commit -m "fix(readme): elimina línea errónea simulada en main"   # SHA del fix: 25787ec
```
```bash
# 3) Integrar el hotfix a main con merge explícito
git checkout main
git merge --no-ff hotfix/readme-errata -m "merge: integra hotfix de errata en README"
git push origin main
```
```bash
# 4) Propagar el fix a la rama de feature
git checkout feat/nueva-funcionalidad
git cherry-pick 25787ec     # puede resultar vacío si el cambio ya está
# (si hay conflicto: editar archivo, git add <archivo>, git cherry-pick --continue)
# (si queda vacío: git cherry-pick --skip)
git push
```

### Elección de integración y explicación

- **A `main`: `merge --no-ff`**  
  Elegí `--no-ff` para **dejar un commit de merge explícito** que documenta el hotfix en el historial. Esto mejora la **trazabilidad** de incidentes críticos (se ve claro cuándo y por qué se aplicó un fix de producción).

- **A `feat/nueva-funcionalidad`: `cherry-pick` del commit del fix (`25787ec`)**  
  Elegí `cherry-pick` para **aplicar exactamente el mismo cambio** a la rama de desarrollo **sin traer otros commits** de `main`. Así mantengo aislado el trabajo de la feature y evito mezclar historia ajena.

---

### Resultado real del cherry-pick

- Apareció un **conflicto en `README.md`** (porque la feature también modificaba ese archivo). Resolví dejando **mi sección nueva** y asegurando que **no quedara la línea `ERR: …`**.  
- Tras resolver y `git add`, al continuar el cherry-pick, Git indicó que **el commit quedó vacío** (el fix ya estaba efectivamente aplicado). En ese caso, seguí la práctica recomendada: **`git cherry-pick --skip`**.  
- Con esto, la feature quedó **sin el error** y **sin ruido extra** en su historia.

---

### Alternativas consideradas

- **Merge de `main` → `feat/nueva-funcionalidad`:** válido cuando necesito más cambios de `main` además del fix, pero agrega commits que no eran necesarios para esta feature.  
- **Rebase de la feature sobre `main`:** mantiene historia lineal pero **reescribe** commits, menos indicado si ya hay trabajo compartido/publicado.

---

### Conclusión

- Para producción, **merge explícito** del hotfix a `main` mejora la auditoría.  
- Para la rama de desarrollo, **cherry-pick del fix puntual** minimiza el ruido y mantiene la rama enfocada.

