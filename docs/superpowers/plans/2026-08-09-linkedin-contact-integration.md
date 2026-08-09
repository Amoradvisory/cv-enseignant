# LinkedIn Contact Integration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ajouter le profil LinkedIn officiel comme preuve secondaire visible et cliquable dans les deux CV Web et sur la première page des deux PDF, sans concurrencer l'email et le téléphone.

**Architecture:** Les deux pages statiques reçoivent le même composant de lien secondaire dans leur zone finale de contact et un libellé harmonisé dans le pied de page. Les deux PDF sont régénérés avec leurs générateurs ReportLab d'origine, dont les sorties actuelles correspondent bit à bit aux PDF publics. Un test de régression transversal valide le HTML, les annotations PDF, le nombre de pages et l'absence d'un nouveau QR code avant toute publication.

**Tech Stack:** HTML/CSS statique, Python 3.11, ReportLab, pypdf, Git, GitHub Pages.

## Global Constraints

- L'email et le téléphone restent les deux seules actions principales.
- LinkedIn est présenté comme une preuve professionnelle secondaire.
- URL canonique unique : `https://www.linkedin.com/in/amor-el-hamrouni/`.
- Texte visible unique : `linkedin.com/in/amor-el-hamrouni`.
- Aucun deuxième QR code et aucune nouvelle image.
- Aucun contenu de parcours, d'expérience, de compétence ou de formation n'est modifié.
- Chaque PDF reste exactement sur deux pages.
- Les URL publiques restent `https://amoradvisory.github.io/cv-enseignant/` et `https://amoradvisory.github.io/cv-formateur/`.

---

### Task 1: Test de régression transversal en échec

**Files:**
- Create: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/verification/test_linkedin_cv_integration.py`
- Test: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/verification/test_linkedin_cv_integration.py`

**Interfaces:**
- Consumes: les deux `index.html` publics et les deux PDF publics.
- Produces: une commande de contrôle unique qui échoue tant que la preuve LinkedIn n'est pas intégrée selon la spécification.

- [ ] **Step 1: Écrire le test statique HTML et PDF**

```python
from pathlib import Path

from pypdf import PdfReader


WORK = Path(__file__).resolve().parents[1]
CANONICAL = "https://www.linkedin.com/in/amor-el-hamrouni/"
DISPLAY = "linkedin.com/in/amor-el-hamrouni"
CASES = (
    (
        WORK / "public-cv-enseignant" / "index.html",
        WORK / "public-cv-enseignant" / "pdf" / "CV_Amor_El_Hamrouni_Enseignant.pdf",
    ),
    (
        WORK / "public-cv-formateur" / "index.html",
        WORK / "public-cv-formateur" / "pdf" / "CV_Amor_El_Hamrouni_Formateur.pdf",
    ),
)


def pdf_links(page):
    links = []
    for ref in page.get("/Annots", []):
        annotation = ref.get_object()
        action = annotation.get("/A")
        if action and action.get("/URI"):
            links.append(str(action["/URI"]))
    return links


def test_case(html_path, pdf_path):
    html = html_path.read_text(encoding="utf-8")
    contact = html.split('<section class="wrap contact-band" id="contact">', 1)[1].split("</section>", 1)[0]
    assert contact.count('class="contact-command') == 2
    assert 'class="linkedin-proof"' in contact
    assert f'href="{CANONICAL}"' in contact
    assert DISPLAY in contact
    assert 'target="_blank"' in contact
    assert 'rel="noopener noreferrer"' in contact
    assert "qr" not in contact.lower()
    assert f'>{DISPLAY}</a>' in html

    reader = PdfReader(str(pdf_path))
    assert len(reader.pages) == 2
    first_page = reader.pages[0]
    assert DISPLAY in (first_page.extract_text() or "")
    assert CANONICAL in pdf_links(first_page)


if __name__ == "__main__":
    for html_path, pdf_path in CASES:
        test_case(html_path, pdf_path)
    print("LinkedIn integration: OK")
```

- [ ] **Step 2: Exécuter le test pour constater l'échec attendu**

Run:

```powershell
& 'C:\Users\A\.cache\codex-runtimes\codex-primary-runtime\dependencies\python\python.exe' 'C:\Users\A\Documents\Codex\2026-07-29\je-je-vais-te-donner-il\work\verification\test_linkedin_cv_integration.py'
```

Expected: `AssertionError` sur l'absence de `class="linkedin-proof"` dans le premier CV testé.

---

### Task 2: Preuve LinkedIn secondaire dans les deux CV Web

**Files:**
- Modify: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/public-cv-enseignant/index.html`
- Modify: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/public-cv-formateur/index.html`
- Modify: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/sources/codex-cv-formateur-adultes/v0-officielle/index.html`
- Test: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/verification/test_linkedin_cv_integration.py`

**Interfaces:**
- Consumes: la grille `.contact-actions` existante et son comportement responsive.
- Produces: un lien `.linkedin-proof` en pleine largeur, lisible et secondaire, avec une URL canonique identique dans les deux CV.

- [ ] **Step 1: Ajouter le style du lien secondaire dans chaque HTML**

Ajouter après les règles `.contact-command small` :

```css
.linkedin-proof {
  grid-column: 1 / -1;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 14px;
  padding: 10px 14px;
  color: #e7f8f2;
  border-top: 1px solid rgba(255, 255, 255, .28);
  text-decoration: none;
  transition: color .18s ease, border-color .18s ease;
}

.linkedin-proof:hover {
  color: var(--white);
  border-color: rgba(255, 255, 255, .68);
}

.linkedin-proof strong,
.linkedin-proof small {
  display: block;
}

.linkedin-proof strong {
  font-size: .82rem;
  line-height: 1.3;
}

.linkedin-proof small {
  margin-top: 2px;
  font-size: .72rem;
  overflow-wrap: anywhere;
  opacity: .78;
}

.linkedin-proof-mark {
  flex: 0 0 auto;
  font-size: 1rem;
  line-height: 1;
}
```

- [ ] **Step 2: Ajouter le lien sous les deux cartes principales**

Insérer dans chaque `.contact-actions`, après la carte téléphone et avant `.contact-feedback` :

```html
<a
  class="linkedin-proof"
  href="https://www.linkedin.com/in/amor-el-hamrouni/"
  target="_blank"
  rel="noopener noreferrer"
  aria-label="Consulter le profil professionnel LinkedIn d'Amor El Hamrouni"
>
  <span>
    <strong>Voir mon profil professionnel sur LinkedIn</strong>
    <small>linkedin.com/in/amor-el-hamrouni</small>
  </span>
  <span class="linkedin-proof-mark" aria-hidden="true">↗</span>
</a>
```

- [ ] **Step 3: Harmoniser le pied de page**

Remplacer le lien `LinkedIn` existant par :

```html
<a href="https://www.linkedin.com/in/amor-el-hamrouni/" target="_blank" rel="noopener noreferrer">linkedin.com/in/amor-el-hamrouni</a>
```

- [ ] **Step 4: Exécuter le test et constater qu'il échoue désormais uniquement sur les PDF**

Run: la commande de Task 1, Step 2.  
Expected: les assertions HTML passent et l'échec se produit sur le texte LinkedIn absent de la première page PDF.

---

### Task 3: URL LinkedIn lisible et cliquable dans les deux PDF

**Files:**
- Modify: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/sources/cv-enseignant-c/archive-cv-enseignant-2026-07-30/pdf/generer-v9-contact.py`
- Modify: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/sources/codex-cv-formateur-adultes/v0-officielle/generer-v0-formateur.py`
- Replace: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/public-cv-enseignant/pdf/CV_Amor_El_Hamrouni_Enseignant.pdf`
- Replace: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/public-cv-formateur/pdf/CV_Amor_El_Hamrouni_Formateur.pdf`
- Test: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/verification/test_linkedin_cv_integration.py`

**Interfaces:**
- Consumes: le `Paragraph` ReportLab `hero_contact` de la première page.
- Produces: le texte imprimable `linkedin.com/in/amor-el-hamrouni` et une annotation `/URI` vers l'URL canonique sur la première page de chaque PDF.

- [ ] **Step 1: Modifier la ligne de contact des deux générateurs**

Remplacer le lien LinkedIn court par :

```python
'<link href="https://www.linkedin.com/in/amor-el-hamrouni/" '
'color="#FFFFFF"><u>linkedin.com/in/amor-el-hamrouni</u></link>'
```

Si ReportLab replie la ligne, conserver le repli naturel dans le bloc de coordonnées et compenser uniquement à l'intérieur du bandeau héro afin que le document reste sur deux pages ; ne supprimer aucun contenu.

- [ ] **Step 2: Régénérer les deux PDF sources**

Run:

```powershell
& 'C:\Users\A\.cache\codex-runtimes\codex-primary-runtime\dependencies\python\python.exe' 'C:\Users\A\Documents\Codex\2026-07-29\je-je-vais-te-donner-il\work\sources\cv-enseignant-c\archive-cv-enseignant-2026-07-30\pdf\generer-v9-contact.py'
& 'C:\Users\A\.cache\codex-runtimes\codex-primary-runtime\dependencies\python\python.exe' 'C:\Users\A\Documents\Codex\2026-07-29\je-je-vais-te-donner-il\work\sources\codex-cv-formateur-adultes\v0-officielle\generer-v0-formateur.py'
```

Expected: deux messages `PDF généré` sans erreur.

- [ ] **Step 3: Copier les PDF régénérés dans les dépôts publics**

Copier :

```text
CV_Amor_El_Hamrouni_Enseignant_V9_CONTACT_VALIDATION.pdf
→ public-cv-enseignant/pdf/CV_Amor_El_Hamrouni_Enseignant.pdf

CV_Amor_El_Hamrouni_Formateur.pdf
→ public-cv-formateur/pdf/CV_Amor_El_Hamrouni_Formateur.pdf
```

- [ ] **Step 4: Exécuter le test complet et vérifier le passage au vert**

Run: la commande de Task 1, Step 2.  
Expected: `LinkedIn integration: OK`.

---

### Task 4: Contrôle visuel et fonctionnel

**Files:**
- Inspect: les deux `index.html` publics.
- Inspect: les deux PDF publics.

**Interfaces:**
- Consumes: les quatre livrables modifiés.
- Produces: une preuve visuelle que le lien reste secondaire et que les PDF ne débordent pas.

- [ ] **Step 1: Valider les PDF avec les outils PDF**

Vérifier pour chaque fichier : métadonnées, deux pages, texte extractible, annotation de lien en première page et absence d'erreurs structurelles.

- [ ] **Step 2: Rendre les quatre pages PDF en images**

Rendre les pages à une résolution suffisante pour inspecter les coordonnées de première page et les fins de page.

- [ ] **Step 3: Inspecter visuellement les quatre images**

Confirmer : URL lisible, aucun chevauchement, aucun bloc coupé, aucune page supplémentaire, aucune régression typographique.

- [ ] **Step 4: Contrôler les deux pages Web en vue bureau et mobile**

Confirmer : les deux cartes principales restent dominantes, la ligne LinkedIn tient sur une largeur mobile, le pied de page est harmonisé et les anciens liens email/téléphone/PDF fonctionnent encore.

---

### Task 5: Publication GitHub des deux dépôts

**Files:**
- Commit in: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/public-cv-enseignant`
- Commit in: `C:/Users/A/Documents/Codex/2026-07-29/je-je-vais-te-donner-il/work/public-cv-formateur`

**Interfaces:**
- Consumes: les dépôts propres, les tests verts et les contrôles visuels approuvés.
- Produces: les deux versions officielles publiques mises à jour sur GitHub Pages.

- [ ] **Step 1: Vérifier précisément les différences de chaque dépôt**

Run dans chaque dépôt :

```powershell
git status --short
git diff --check
git diff --stat
```

Expected: uniquement `index.html`, le PDF officiel et, pour le dépôt enseignant, les documents de conception/plan déjà validés.

- [ ] **Step 2: Committer le CV enseignant**

```powershell
git add index.html pdf/CV_Amor_El_Hamrouni_Enseignant.pdf docs/superpowers
git commit -m "Ajouter LinkedIn aux versions Web et PDF"
```

- [ ] **Step 3: Committer le CV formateur**

```powershell
git add index.html pdf/CV_Amor_El_Hamrouni_Formateur.pdf
git commit -m "Ajouter LinkedIn aux versions Web et PDF"
```

- [ ] **Step 4: Pousser les deux branches `main`**

```powershell
git push origin main
```

Expected: les deux dépôts atteignent `origin/main` sans rejet.

- [ ] **Step 5: Vérifier les sites publics après déploiement**

Ouvrir les deux URL officielles, contrôler la présence du composant LinkedIn, télécharger les PDF publics et vérifier que les quatre liens LinkedIn pointent vers le profil officiel.

---

## Plan Self-Review

- Couverture : les cinq contraintes utilisateur sont reliées aux Tasks 2, 3 et 4.
- Isolation : le test transversal est indépendant des générateurs et valide uniquement les livrables publics.
- Reproductibilité : les deux générateurs vérifiés sont les sources exactes des PDF publics actuels.
- Publication : chaque dépôt conserve son historique et reçoit un commit intentionnel séparé.
- Aucun élément de parcours ou contenu éditorial n'entre dans le périmètre.
