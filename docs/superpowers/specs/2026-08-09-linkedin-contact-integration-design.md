# Intégration LinkedIn dans les CV — Spécification de design

Date : 9 août 2026  
Périmètre : CV enseignant et CV formateur pour adultes, versions Web et PDF officielles.

## Objectif

Rendre le profil LinkedIn d'Amor El Hamrouni suffisamment visible pour prolonger la crédibilité du CV, sans concurrencer les deux actions de conversion prioritaires : envoyer un email et téléphoner.

URL canonique cliquable : `https://www.linkedin.com/in/amor-el-hamrouni/`  
Texte visible : `linkedin.com/in/amor-el-hamrouni`

## Contraintes approuvées

- L'email et le téléphone restent les deux seules actions principales.
- LinkedIn est présenté comme une preuve professionnelle secondaire.
- Le lien devient plus visible dans la zone finale de contact des deux CV Web.
- L'URL lisible et cliquable figure sur la première page des deux PDF.
- La même URL canonique est utilisée partout.
- Aucun deuxième QR code n'est ajouté.
- Aucun contenu de parcours, d'expérience, de compétence ou de formation n'est modifié.
- Les PDF conservent leur nombre de pages, leur ordre et leur hiérarchie visuelle.

## Approches examinées

### A. Troisième grande action au même niveau que l'email et le téléphone

Avantage : visibilité maximale.  
Inconvénient : affaiblit la hiérarchie de conversion et alourdit la zone finale.  
Décision : rejetée.

### B. Lien secondaire clairement lisible dans la zone de contact et sur la première page du PDF

Avantage : équilibre entre visibilité, crédibilité et sobriété. L'email et le téléphone restent dominants.  
Inconvénient : LinkedIn attire moins l'œil qu'un grand bouton, ce qui est volontaire.  
Décision : retenue.

### C. Conservation du seul lien discret dans le pied de page Web

Avantage : aucune modification visuelle.  
Inconvénient : lien trop facile à manquer et absent des PDF.  
Décision : rejetée.

## Design Web

Dans chacun des deux sites :

1. Conserver sans modification les deux cartes principales « Envoyer un mail » et téléphone.
2. Ajouter sous ces cartes une ligne secondaire occupant toute la largeur de la zone d'actions.
3. Libellé recommandé : `Voir mon profil professionnel sur LinkedIn`.
4. Afficher également la forme courte `linkedin.com/in/amor-el-hamrouni` dans la même ligne ou son texte d'accompagnement.
5. Le lien ouvre `https://www.linkedin.com/in/amor-el-hamrouni/` dans un nouvel onglet avec `rel="noopener noreferrer"`.
6. La présentation reste discrète : taille inférieure aux cartes principales, contraste accessible, absence de fond bleu LinkedIn dominant.
7. Sur mobile, la ligne LinkedIn reste sous les deux actions et ne rejoint pas la barre fixe email/téléphone.
8. Le pied de page conserve un lien LinkedIn, avec un texte visible harmonisé vers `linkedin.com/in/amor-el-hamrouni`.

## Design PDF

Dans chacun des deux PDF :

1. Ajouter `linkedin.com/in/amor-el-hamrouni` sur la première page, à proximité immédiate de l'email et du téléphone.
2. Conserver une hiérarchie secondaire : corps légèrement inférieur ou égal aux coordonnées, sans encadré additionnel.
3. Ajouter une annotation cliquable pointant vers `https://www.linkedin.com/in/amor-el-hamrouni/`.
4. Vérifier que le texte demeure lisible à 100 % et après impression en niveaux de gris.
5. Ne déplacer aucun bloc majeur sur une autre page et ne créer aucune troisième page.

## Cohérence et accessibilité

- L'URL cible, les majuscules, les accents et le nom affiché restent identiques dans les deux CV.
- Le lien Web possède un libellé compréhensible sans dépendre d'une icône.
- Le texte imprimé demeure exploitable même si le PDF est imprimé et que le clic disparaît.
- Aucun lien raccourci, paramètre de suivi ou URL régionale LinkedIn n'est utilisé.

## Vérification attendue

### Tests statiques Web

- Les deux HTML contiennent l'URL canonique dans la zone finale de contact.
- Les deux HTML affichent `linkedin.com/in/amor-el-hamrouni`.
- Les cartes principales restent limitées à l'email et au téléphone.
- Aucun nouveau QR code ni nouvelle image n'est ajouté.
- Les anciens liens PDF, email et téléphone restent inchangés.

### Tests PDF

- Chaque PDF contient deux pages, comme avant la modification.
- Le texte de la première page contient `linkedin.com/in/amor-el-hamrouni`.
- L'annotation de lien de la première page cible l'URL canonique.
- Le rendu visuel ne présente ni chevauchement, ni coupure, ni débordement.

### Vérification publique

- Les deux sites GitHub Pages chargent la nouvelle ligne LinkedIn.
- Les deux boutons de téléchargement servent les PDF mis à jour.
- Les liens LinkedIn des sites et des PDF ouvrent le même profil officiel.

## Critère de réussite

Un recruteur peut joindre Amor immédiatement par email ou téléphone, puis consulter LinkedIn comme preuve professionnelle complémentaire. Le lien est visible sans devenir un appel à l'action concurrent, et il survit au téléchargement comme à l'impression du PDF.
