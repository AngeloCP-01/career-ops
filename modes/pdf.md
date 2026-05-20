# Modo: pdf — Generación de PDF ATS-Optimizado

## Pipeline completo

1. Lee `cv.md` como fuentes de verdad
2. Pide al usuario el JD si no está en contexto (texto o URL)
3. Extrae 15-20 keywords del JD
4. Detecta idioma del JD → idioma del CV (EN default)
5. Detecta ubicación empresa → formato papel:
   - US/Canada → `letter`
   - Resto del mundo → `a4`
6. Detecta arquetipo del rol → adapta framing
7. Reescribe Professional Summary inyectando keywords del JD + exit narrative bridge ("Built and sold a business. Now applying systems thinking to [domain del JD].")
8. Selecciona top 3-4 proyectos más relevantes para la oferta
9. Reordena bullets de experiencia por relevancia al JD
10. Construye competency grid desde requisitos del JD (6-8 keyword phrases)
11. Inyecta keywords naturalmente en logros existentes (NUNCA inventa)
12. Genera HTML completo desde template + contenido personalizado
13. Lee `name` de `config/profile.yml` → normaliza a kebab-case lowercase (e.g. "John Doe" → "john-doe") → `{candidate}`
14. Escribe HTML a `/tmp/cv-{candidate}-{company}.html`
15. Ejecuta: `node generate-pdf.mjs /tmp/cv-{candidate}-{company}.html output/cv-{candidate}-{company}-{YYYY-MM-DD}.pdf --format={letter|a4}`
16. Reporta: ruta del PDF, nº páginas, % cobertura de keywords

## Reglas ATS (parseo limpio)

- Layout single-column (sin sidebars, sin columnas paralelas)
- Headers estándar: "Professional Summary", "Work Experience", "Education", "Skills", "Certifications", "Projects"
- Sin texto en imágenes/SVGs
- Sin info crítica en headers/footers del PDF (ATS los ignora)
- UTF-8, texto seleccionable (no rasterizado)
- Sin tablas anidadas
- Keywords del JD distribuidas: Summary (top 5), primer bullet de cada rol, Skills section

## Diseño del PDF (Jake's Resume style — single page target)

- **Fonts**: Latin Modern Roman / Computer Modern / Times New Roman serif chain (no web fonts, no CDN)
- **Header**: nombre centrado 22pt + línea de contacto centrada con `|` como separador (sin gradiente, sin color)
- **Section headers**: 11.5pt bold uppercase + horizontal rule 0.8pt debajo (full width)
- **Body**: 10-10.5pt, line-height 1.25-1.35, color negro puro
- **Entries**: dos líneas — `**Company** ... *Date*` en línea 1, `*Role* ... *Location*` en línea 2
- **Bullets**: `•` con indent 14pt
- **Márgenes**: 0.5in
- **Background**: blanco puro, todo el texto negro

### Single-page rule

Target ONE page. To enforce:
- Summary: máximo 3 líneas (mejor 2)
- Core Competencies: rendered inline (palabras separadas por `·`), no chips
- Experience: bullets condensados, máximo 3-4 por rol
- Projects: top 2-3, no top 4
- Education + Certifications: una línea por entrada si es posible
- Skills: categoría en bold + lista inline (e.g. `**Languages:** Python, Node.js, TypeScript`)

Si el PDF sale a 2 páginas, condensar bullets y reordenar proyectos antes de aceptar.

## Orden de secciones (optimizado "6-second recruiter scan")

1. Header (nombre grande, gradiente, contacto, link portfolio)
2. Professional Summary (3-4 líneas, keyword-dense)
3. Core Competencies (6-8 keyword phrases en flex-grid)
4. Work Experience (cronológico inverso)
5. Projects (top 3-4 más relevantes)
6. Education & Certifications
7. Skills (idiomas + técnicos)

## Estrategia de tailoring (REESCRIBE para AMPLIFICAR, nunca para DILUIR)

**REGLA DE ORO:** Reescribir bullets PARA MEJORARLOS está permitido y es lo deseable. Reescribir que DILUYE es inaceptable. La pregunta para cada rewrite es: "¿la nueva versión es objetivamente más fuerte que la de cv.md para este JD?" Si no es claramente sí → mantén el original.

**Lo que se tailorea por oferta (orden de inversión):**

1. **HEADLINE** (línea bajo el nombre): adaptar al rol — e.g. "Software Developer | NodeJS | Python" para generalista, "Backend Engineer | Payments | ISO 8583" para Maya, "Full Stack Engineer | Node.js | React" para PayMongo
2. **SUMMARY**: 3-4 líneas, reescritura libre con keywords del JD. Mantén verdad.
3. **SKILLS reordering**: sube las categorías y elementos relevantes al JD; NO inventes tecnologías
4. **EXPERIENCE bullets**: reordenar primero, luego reescribir solo si claramente mejora (ver reglas abajo). Bullets totalmente irrelevantes pueden omitirse.
5. **PROJECTS selection + rewrite**: elige los 2-3 más relevantes; reescribe si mejora para el JD
6. **EDUCATION / CERTIFICATIONS**: tal cual

**Reescritura permitida (AMPLIFICA):** ✅
- Verbo más fuerte y específico: "Contributed to CI/CD pipelines" → "Built CI/CD pipelines with GitHub Actions for backend, web, and mobile deploys"
- Añadir contexto/tecnología que el JD pide y el candidato genuinamente usó: "Owned end-to-end feature development across backend, React frontend, and React Native apps" → "Owned end-to-end feature development across NestJS backend, React frontend, and React Native apps, aligning system design with business needs" (si "NestJS" es real)
- Tighten wording sin perder señal: "Managed production deployments, including Google Play Store releases and Linode server provisioning" → "Shipped production deployments to Google Play Store and provisioned Linode servers for backend services"
- Convertir voz pasiva a activa: "Assigned to production support for a Java FEP" → "Maintained Java FEP payment switch processing real-time ISO 8583 authorizations to HOST (AS400) and HSM"

**Reescritura prohibida (DILUYE):** ❌
- Quitar métricas verbatim: "~35%", "~40%", "~25%", "~30%", "9,000-12,000 peak daily orders" — SIEMPRE sobreviven, palabra por palabra
- Quitar nombres de sistemas específicos cuando son relevantes: ISO 8583, AS400, HSM, FEP, VisaNet, TiDB, NestJS, FastAPI — son señal de experiencia real, NO se reemplazan por categorías genéricas ("a payment system", "a SQL database")
- Fusionar 2-3 bullets en uno solo perdiendo contenido
- Reemplazar concreto por abstracto: "designing domain-isolated services using DDD principles, reducing cross-domain coupling and deployment risk" → "decomposed services using DDD" (perdiste "deployment risk", "cross-domain coupling", el contexto de carga)
- Reemplazar decisión específica por verbo genérico: "balancing monolithic vs microservices trade-offs based on system requirements" → "designed scalable architectures" (perdiste el tradeoff explícito que demuestra criterio técnico)
- Inventar tecnologías, métricas, o experiencias

**Test rápido antes de aceptar un rewrite:** Lee el bullet original y el reescrito uno tras otro. Si el reescrito (a) inyecta keyword del JD útil sin sonar forzado, (b) mantiene TODAS las métricas y nombres de sistemas del original, y (c) suena más activo/concreto — acéptalo. Si falla alguno → vuelve al original.

## Template HTML

Usar el template en `cv-template.html`. Reemplazar los placeholders `{{...}}` con contenido personalizado:

| Placeholder | Contenido |
|-------------|-----------|
| `{{LANG}}` | `en` o `es` |
| `{{PAGE_WIDTH}}` | `8.5in` (letter) o `210mm` (A4) |
| `{{NAME}}` | (from profile.yml) |
| `{{HEADLINE}}` | Tagline bajo el nombre, tailored al rol (e.g. "Backend Engineer \| Payments \| ISO 8583"). Default: "Software Developer \| NodeJS \| Python" |
| `{{PHONE}}` | (from profile.yml — include with its separator only when `profile.yml` has a non-empty `phone` value; omit both `<span>` and `<span class="separator">` otherwise) |
| `{{EMAIL}}` | (from profile.yml) |
| `{{LINKEDIN_URL}}` | [from profile.yml] |
| `{{LINKEDIN_DISPLAY}}` | [from profile.yml] |
| `{{PORTFOLIO_URL}}` | [from profile.yml] (o /es según idioma) |
| `{{PORTFOLIO_DISPLAY}}` | [from profile.yml] (o /es según idioma) |
| `{{LOCATION}}` | [from profile.yml] |
| `{{SECTION_SUMMARY}}` | Professional Summary / Resumen Profesional |
| `{{SUMMARY_TEXT}}` | Summary personalizado con keywords |
| `{{SECTION_COMPETENCIES}}` | Core Competencies / Competencias Core |
| `{{COMPETENCIES}}` | `<span class="competency-tag">keyword</span>` × 6-8 |
| `{{SECTION_EXPERIENCE}}` | Work Experience / Experiencia Laboral |
| `{{EXPERIENCE}}` | HTML de cada trabajo con bullets reordenados |
| `{{SECTION_PROJECTS}}` | Projects / Proyectos |
| `{{PROJECTS}}` | HTML de top 3-4 proyectos |
| `{{SECTION_EDUCATION}}` | Education / Formación |
| `{{EDUCATION}}` | HTML de educación |
| `{{SECTION_CERTIFICATIONS}}` | Certifications / Certificaciones |
| `{{CERTIFICATIONS}}` | HTML de certificaciones |
| `{{SECTION_SKILLS}}` | Skills / Competencias |
| `{{SKILLS}}` | HTML de skills |

## Canva CV Generation (optional)

If `config/profile.yml` has `cv.canva_resume_design_id` set, offer the user a choice before generating:
- **"HTML/PDF (fast, ATS-optimized)"** — existing flow above
- **"Canva CV (visual, design-preserving)"** — new flow below

If the user has no `cv.canva_resume_design_id`, skip this prompt and use the HTML/PDF flow.

### Canva workflow

#### Step 1 — Duplicate the base design

a. `export-design` the base design (using `cv.canva_resume_design_id`) as PDF → get download URL
b. `import-design-from-url` using that download URL → creates a new editable design (the duplicate)
c. Note the new `design_id` for the duplicate

#### Step 2 — Read the design structure

a. `get-design-content` on the new design → returns all text elements (richtexts) with their content
b. Map text elements to CV sections by content matching:
   - Look for the candidate's name → header section
   - Look for "Summary" or "Professional Summary" → summary section
   - Look for company names from cv.md → experience sections
   - Look for degree/school names → education section
   - Look for skill keywords → skills section
c. If mapping fails, show the user what was found and ask for guidance

#### Step 3 — Generate tailored content

Same content generation as the HTML flow (Steps 1-11 above):
- Rewrite Professional Summary with JD keywords + exit narrative
- Reorder experience bullets by JD relevance
- Select top competencies from JD requirements
- Inject keywords naturally (NEVER invent)

**IMPORTANT — Character budget rule:** Each replacement text MUST be approximately the same length as the original text it replaces (within ±15% character count). If tailored content is longer, condense it. The Canva design has fixed-size text boxes — longer text causes overlapping with adjacent elements. Count the characters in each original element from Step 2 and enforce this budget when generating replacements.

#### Step 4 — Apply edits

a. `start-editing-transaction` on the duplicate design
b. `perform-editing-operations` with `find_and_replace_text` for each section:
   - Replace summary text with tailored summary
   - Replace each experience bullet with reordered/rewritten bullets
   - Replace competency/skills text with JD-matched terms
   - Replace project descriptions with top relevant projects
c. **Reflow layout after text replacement:**
   After applying all text replacements, the text boxes auto-resize but neighboring elements stay in place. This causes uneven spacing between work experience sections. Fix this:
   1. Read the updated element positions and dimensions from the `perform-editing-operations` response
   2. For each work experience section (top to bottom), calculate where the bullets text box ends: `end_y = top + height`
   3. The next section's header should start at `end_y + consistent_gap` (use the original gap from the template, typically ~30px)
   4. Use `position_element` to move the next section's date, company name, role title, and bullets elements to maintain even spacing
   5. Repeat for all work experience sections
d. **Verify layout before commit:**
   - `get-design-thumbnail` with the transaction_id and page_index=1
   - Visually inspect the thumbnail for: text overlapping, uneven spacing, text cut off, text too small
   - If issues remain, adjust with `position_element`, `resize_element`, or `format_text`
   - Repeat until layout is clean
d. Show the user the final preview and ask for approval
e. `commit-editing-transaction` to save (ONLY after user approval)

#### Step 5 — Export and download PDF

a. `export-design` the duplicate as PDF (format: a4 or letter based on JD location)
b. **IMMEDIATELY** download the PDF using Bash:
   ```bash
   curl -sL -o "output/cv-{candidate}-{company}-canva-{YYYY-MM-DD}.pdf" "{download_url}"
   ```
   The export URL is a pre-signed S3 link that expires in ~2 hours. Download it right away.
c. Verify the download:
   ```bash
   file output/cv-{candidate}-{company}-canva-{YYYY-MM-DD}.pdf
   ```
   Must show "PDF document". If it shows XML or HTML, the URL expired — re-export and retry.
d. Report: PDF path, file size, Canva design URL (for manual tweaking)

#### Error handling

- If `import-design-from-url` fails → fall back to HTML/PDF pipeline with message
- If text elements can't be mapped → warn user, show what was found, ask for manual mapping
- If `find_and_replace_text` finds no matches → try broader substring matching
- Always provide the Canva design URL so the user can edit manually if auto-edit fails

## Post-generación

Actualizar tracker si la oferta ya está registrada: cambiar PDF de ❌ a ✅.
