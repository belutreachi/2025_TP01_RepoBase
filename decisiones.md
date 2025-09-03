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