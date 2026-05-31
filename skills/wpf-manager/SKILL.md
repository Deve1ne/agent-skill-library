---
name: wpf-manager
description: >
  Expert WPF desktop : analyse de fichiers XAML, insertion et déplacement de contrôles,
  scaffolding ViewModel MVVM pur (INotifyPropertyChanged manuel, RelayCommand).
  Utilise ce skill dès que la demande porte sur un fichier .xaml WPF (pas Xamarin/MAUI/Blazor),
  un ViewModel C# MVVM pur à modifier, ou un contrôle WPF à ajouter/déplacer/configurer.
  Déclenche sur : "analyse ce xaml", "ajoute un contrôle dans le panel", "déplace cet élément",
  "visibilité conditionnelle WPF", "DataTrigger", "Visibility Converter", "binding WPF",
  "propriété dans le ViewModel", "ajoute une commande ICommand", "RelayCommand",
  "INotifyPropertyChanged manuel", "commande async Task ViewModel", "ObservableCollection",
  "DataGrid colonnes CellTemplate", "UserControl DependencyProperty", "ResourceDictionary WPF",
  "StaticResource Style WPF". Ne déclenche PAS pour : debug d'erreurs génériques, migration
  Xamarin/MAUI, CommunityToolkit.Mvvm, déploiement, questions de setup ou de comparaison de frameworks.
---

# WPF Manager

Expert WPF : analyse de pages XAML, insertion/déplacement de contrôles, scaffolding
ViewModel MVVM pur (INotifyPropertyChanged manuel).

---

## Compatibilité agents

Ce skill est conçu pour fonctionner avec **n'importe quel agent LLM** : Claude, Copilot,
ChatGPT, Cursor, ou tout autre assistant IA. Il ne suppose aucune capacité spécifique à
une plateforme.

**Fichiers de référence optionnels** — ce skill peut s'appuyer sur deux fichiers externes.
Si l'utilisateur les fournit en contexte (copie-colle, upload, ou pièce jointe), les utiliser.
Sinon, appliquer les connaissances générales WPF intégrées.

| Fichier | Contenu | Quand le demander |
|---------|---------|-------------------|
| `visibility-patterns.md` | Patterns Converter, DataTrigger, MultiDataTrigger | Si la page contient des conditions de visibilité complexes |
| `datagrid-patterns.md` | Colonnes, CellTemplates, RowStyle, RowDetails | Si la page contient un DataGrid non trivial |

Si ces fichiers ne sont pas disponibles, signaler à l'utilisateur :
> "Pour une analyse plus précise des [DataTriggers / colonnes DataGrid], tu peux me fournir
> le fichier `references/visibility-patterns.md` (ou `datagrid-patterns.md`) depuis le dossier
> du skill. Sinon je m'appuie sur mes connaissances WPF générales."

---

## Hors scope

Ce skill **ne traite pas** :
- Le debug d'erreurs runtime (NullReferenceException, BindingExpression errors en console)
- La migration Xamarin.Forms / MAUI / Blazor / UWP → WPF
- Le pattern MVVM avec CommunityToolkit.Mvvm, Prism, ReactiveUI
- Le déploiement, la publication, les questions de configuration projet
- Les questions comparatives ("WPF vs WinForms", "WPF vs Avalonia")

Si la demande glisse vers un de ces cas en cours de conversation, le signaler clairement
et proposer une réorientation.

---

## Modes disponibles

| Mode | Déclencheurs typiques | Sortie |
|------|-----------------------|--------|
| **ANALYSE** | "analyse ce xaml", fichier .xaml fourni | `<nom>-analyse.md` |
| **INSERTION** | "ajoute X dans Y", "insère un contrôle" | diff XAML annoté |
| **DÉPLACEMENT** | "déplace X vers Y", "change la place de" | diff XAML annoté |
| **VIEWMODEL** | "propriété", "commande", "méthode", "ICommand" | snippet C# prêt à coller |

---

## MODE ANALYSE

L'analyse produit un fichier `<NomFichier>-analyse.md` structuré en 6 sections.
Lire le XAML en entier avant de commencer. Remplir les sections dans l'ordre.

### Section 1 — Carte de structure (arbre ASCII)

Représenter la hiérarchie complète des panels. Chaque nœud indique son type,
son nom (`x:Name`) si présent, et ses bindings critiques.

```
UserControl / Window / Page  [x:Class]
└── Grid (rootGrid)  [3 rows x 2 cols]
    ├── Row 0 — StackPanel (header, horizontal)
    │   ├── lblTitre -> Binding: Titre
    │   └── lblCount -> Binding: NombreItems
    ├── Row 1 — Grid (contenu) [2 cols]
    │   ├── Col 0 — panelGauche (StackPanel)
    │   │   └── lstItems -> Binding: Items / ItemSelectionne
    │   └── Col 1 — panelDroit (StackPanel)
    │       ├── txtNom -> Binding: ItemSelectionne.Nom [TwoWay]
    │       └── dgDetails -> Binding: Details [DataGrid]
    └── Row 2 — FooterPanel
        └── btnValider -> Command: ValiderCommand
```

Note : utiliser `->` au lieu de `→` pour la compatibilité universelle.

Pour les **UserControls imbriqués** : noter le type, l'assembly si disponible,
et les DependencyProperties bindées depuis le parent.
Exemple : `<local:MonEditeur Valeur="{Binding ChampX}" EstReadOnly="{Binding EnLecture}"/>`

### Section 2 — Résumé global

Tableau synthétique : type de root element, x:Class, ViewModel déduit,
converters déclarés dans les ressources, namespaces utilisés.

### Section 3 — Détail des contrôles

Pour chaque contrôle portant un `x:Name` **ou** impliqué dans un binding/trigger :

```
#### `nomControle` — ligne N
**Type** : TextBox / Button / DataGrid / <local:MonUserControl>
**Position** : Grid Row=1 Col=0 | StackPanel enfant n°2 | DockPanel.Dock=Left
**Bindings** :
  - Text -> {Binding Prop, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}
  - IsEnabled -> {Binding PeutEditer}
**Visibilite** : -> voir tableau Section 4
**Style** : {StaticResource NomStyle} — defini dans [App.xaml / MergedDictionaries / local]
**Commentaire** : role fonctionnel en une phrase
```

Pour les **DataGrid** : ajouter un sous-tableau des colonnes :

```
| #  | Type                    | Header        | Binding              | CellTemplate     |
|----|-------------------------|---------------|----------------------|------------------|
| 1  | DataGridTextColumn      | "Nom"         | {Binding Nom}        | —                |
| 2  | DataGridTemplateColumn  | "Action"      | —                    | btnSupprimer     |
```

### Section 4 — Conditions d'affichage

Tableau exhaustif de toutes les conditions (Visibility, IsEnabled, IsReadOnly, IsChecked) :

```
| Controle      | Propriete  | Mecanisme              | Condition              | Resultat              |
|---------------|------------|------------------------|------------------------|-----------------------|
| btnSuppr      | Visibility | Converter BoolToVis    | EstSupprimable = true  | Visible               |
| panelAdmin    | Visibility | DataTrigger            | Role = "Admin"         | Visible (def. Coll.)  |
| txtRef        | IsReadOnly | Binding direct         | EstEnLecture = true    | ReadOnly              |
```

Mecanismes a detecter : Converter, DataTrigger, MultiDataTrigger, Style.Triggers,
ControlTemplate.Triggers, bindings directs sur IsEnabled / IsReadOnly / IsChecked.

### Section 5 — Styles et Ressources

Pour chaque Style, ControlTemplate, DataTemplate present ou reference :

```
| Cle             | Type         | Defini dans        | Cible   | Effet principal              |
|-----------------|--------------|--------------------|---------|------------------------------|
| BoutonPrimaire  | Style        | App.xaml           | Button  | Background bleu, coins ronds |
| RowTemplate     | DataTemplate | local              | ListBox | Affiche Nom + Icone          |
```

Si le XAML utilise MergedDictionaries : lister les dictionnaires importes et noter
quelles cles proviennent de chacun.

### Section 6 — ViewModel et Points d'attention

**ViewModel detecte** : `NomViewModel`

**Proprietes bindees** :

```
| Propriete          | Type probable              | Usage                     | Writable |
|--------------------|----------------------------|---------------------------|----------|
| Titre              | string                     | TextBlock header           | Non      |
| NomUtilisateur     | string                     | TextBox TwoWay             | Oui      |
| Items              | ObservableCollection<Item> | ItemsSource ListBox        | Non      |
| ValiderCommand     | ICommand                   | Button Command             | —        |
| PeutValider        | bool                       | IsEnabled / CanExecute     | Oui      |
```

**Commandes** :

```
| Commande        | Declencheur    | CanExecute probable         |
|-----------------|----------------|-----------------------------|
| ValiderCommand  | btnValider     | PeutValider = true          |
| SupprimerCommand| btnSupprimer   | ItemSelectionne != null     |
```

**Points d'attention** : signaler tout binding suspect, propriete probablement manquante
dans le VM, IsEnabled dupliquant un CanExecute, converter applique a un mauvais type,
elements superposes sans Z-order explicite, MultiDataTrigger sans Setter.

### Boucle de confirmation apres analyse

Une fois le fichier genere, poser ces questions avant toute modification :

> "Analyse generee. Avant de continuer :
> 1. Les points d'attention detectes sont-ils corrects ?
> 2. Y a-t-il une section sur laquelle tu veux zoomer ?
> 3. Quelle est la prochaine action : insertion, deplacement, ou scaffolding VM ?"

**Anti-pattern a eviter** :
```
# Ne pas faire : generer l'analyse ET modifier le XAML dans la meme reponse
# sans confirmation de l'utilisateur.
```

---

## MODE INSERTION / DEPLACEMENT

1. Si un fichier `*-analyse.md` existe -> le consulter. Sinon, proposer de le generer d'abord.
2. **Annoncer le plan** avant tout code :
   ```
   -> Insertion de <TextBox x:Name="txtEmail"> dans panelGauche (StackPanel)
   -> Position : apres txtPrenom (ligne 52)
   -> Binding : DossierSelectionne.Email, Mode=TwoWay
   -> IsReadOnly : {Binding EstEnLecture} — coherent avec les autres champs
   -> Style : reprend Margin="0,2,0,8" des champs voisins
   ```
3. Montrer un diff **AVANT / APRES** avec 3 a 5 lignes de contexte de chaque cote.
4. Signaler si un ajout dans le ViewModel ou le modele est necessaire.

**Regles de placement** :
- `Grid` -> toujours expliciter Grid.Row et Grid.Column, jamais implicite
- `StackPanel` -> l'ordre des enfants = ordre visuel ; inserer a la bonne position
- `DockPanel` -> respecter les DockPanel.Dock existants
- Reprendre le meme Style, Margin, convention x:Name que les elements voisins
- Si deux elements se superposent dans le meme Grid (sans Row/Col differents),
  signaler le Z-order et verifier que la visibilite exclusive est assuree

**Anti-pattern a eviter** :
```xml
<!-- Ne pas faire : laisser Grid.Row et Grid.Column implicites -->
<TextBox Text="{Binding Nom}"/>

<!-- Faire : toujours explicite -->
<TextBox Grid.Row="1" Grid.Column="0" Text="{Binding Nom}"/>
```

---

## MODE VIEWMODEL — Scaffolding MVVM pur

### Propriete simple

```csharp
private string _nomChamp;
public string NomChamp
{
    get => _nomChamp;
    set
    {
        if (_nomChamp == value) return;
        _nomChamp = value;
        OnPropertyChanged(nameof(NomChamp));
    }
}
```

Toujours : backing field `_camelCase`, guard `if value == return`, `nameof()`.

**Anti-pattern a eviter** :
```csharp
// Ne pas faire : string litteral et pas de guard
set { _nomChamp = value; OnPropertyChanged("NomChamp"); }

// Faire :
set { if (_nomChamp == value) return; _nomChamp = value; OnPropertyChanged(nameof(NomChamp)); }
```

### Commande (RelayCommand)

```csharp
public ICommand NomCommand { get; private set; }

// Constructeur
NomCommand = new RelayCommand(ExecuteNom, CanExecuteNom);

private bool CanExecuteNom(object parameter) => /* condition */;
private void ExecuteNom(object parameter) { /* logique */ }
```

### Commande async

```csharp
public ICommand NomCommand { get; private set; }

// Constructeur
NomCommand = new RelayCommand(async _ => await ExecuteNomAsync(), CanExecuteNom);

private bool CanExecuteNom(object parameter) => !EstEnChargement;

private async Task ExecuteNomAsync()
{
    EstEnChargement = true;
    try
    {
        await /* appel service */;
    }
    finally
    {
        EstEnChargement = false;
    }
}
```

Pour toute commande async : proposer systematiquement la propriete `EstEnChargement` (bool)
et son binding XAML associe (`Visibility + BoolToVisibilityConverter`).

### Collection observable

```csharp
private ObservableCollection<MonType> _items = new();
public ObservableCollection<MonType> Items
{
    get => _items;
    set { if (_items == value) return; _items = value; OnPropertyChanged(nameof(Items)); }
}
```

### Quand invalider CanExecute manuellement

Si CanExecute depend d'une propriete dont le changement ne passe pas automatiquement
par `CommandManager.RequerySuggested`, ajouter dans son setter :

```csharp
CommandManager.InvalidateRequerySuggested();
```

### Duo propriete booleenne + XAML Visibility

```csharp
// ViewModel
private bool _estVisible;
public bool EstVisible
{
    get => _estVisible;
    set { if (_estVisible == value) return; _estVisible = value; OnPropertyChanged(nameof(EstVisible)); }
}
```
```xml
<Control Visibility="{Binding EstVisible, Converter={StaticResource BoolToVisibility}}"/>
```

---

## Conventions de nommage

| Element           | Convention                    | Exemple                  |
|-------------------|-------------------------------|--------------------------|
| x:Name controle   | type prefixe + PascalCase     | btnValider, txtNom       |
| Propriete VM      | PascalCase                    | NomUtilisateur           |
| Command           | PascalCase + "Command"        | ValiderCommand           |
| Backing field     | _ + camelCase                 | _nomUtilisateur          |
| Converter         | PascalCase + "Converter"      | BoolToVisibilityConverter|
| UserControl custom| PascalCase, prefixe projet    | MonProjet:EditeurAdresse |

---

## Fichiers de reference

Ces fichiers sont optionnels. Si disponibles (fournis par l'utilisateur en contexte),
les lire avant d'analyser les sections concernees.

- `visibility-patterns.md` — Patterns Converter, DataTrigger, MultiDataTrigger,
  MultiBinding. Lire si la page contient des conditions de visibilite complexes.
- `datagrid-patterns.md` — Colonnes, CellTemplates, RowDetailsTemplate, RowStyle.
  Lire si la page contient un DataGrid non trivial.

Si non disponibles, appliquer les connaissances WPF generales et le signaler.
