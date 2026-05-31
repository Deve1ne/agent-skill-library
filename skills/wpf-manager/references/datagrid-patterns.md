# DataGrid WPF — Patterns & Analyse

## Colonnes standard

```xaml
<DataGrid ItemsSource="{Binding Lignes}" AutoGenerateColumns="False"
          SelectedItem="{Binding LigneSelectionnee}">
    <DataGrid.Columns>

        <!-- Texte simple -->
        <DataGridTextColumn Header="Nom" Binding="{Binding Nom}" Width="*"/>

        <!-- Lecture seule avec format -->
        <DataGridTextColumn Header="Montant"
                            Binding="{Binding Montant, StringFormat='{}{0:C}'}"
                            IsReadOnly="True" Width="100"/>

        <!-- CheckBox -->
        <DataGridCheckBoxColumn Header="Actif" Binding="{Binding EstActif}" Width="60"/>

        <!-- ComboBox -->
        <DataGridComboBoxColumn Header="Statut"
                                SelectedItemBinding="{Binding Statut}"
                                ItemsSource="{Binding Source={StaticResource Statuts}}"
                                Width="120"/>
    </DataGrid.Columns>
</DataGrid>
```

## DataGridTemplateColumn (CellTemplate / CellEditingTemplate)

```xaml
<DataGridTemplateColumn Header="Action" Width="80">
    <DataGridTemplateColumn.CellTemplate>
        <DataTemplate>
            <Button Content="Suppr"
                    Command="{Binding DataContext.SupprimerCommand,
                              RelativeSource={RelativeSource AncestorType=DataGrid}}"
                    CommandParameter="{Binding}"/>
        </DataTemplate>
    </DataGridTemplateColumn.CellTemplate>
</DataGridTemplateColumn>
```
⚠️ **Piège** : dans un `CellTemplate`, le `DataContext` est la ligne (item), pas le
ViewModel. Pour accéder au ViewModel, utilise `RelativeSource AncestorType=DataGrid`
(ou `UserControl`/`Window`).

## CellEditingTemplate (édition inline)

```xaml
<DataGridTemplateColumn Header="Catégorie">
    <DataGridTemplateColumn.CellTemplate>
        <DataTemplate>
            <TextBlock Text="{Binding Categorie.Libelle}"/>
        </DataTemplate>
    </DataGridTemplateColumn.CellTemplate>
    <DataGridTemplateColumn.CellEditingTemplate>
        <DataTemplate>
            <ComboBox ItemsSource="{Binding DataContext.Categories,
                                   RelativeSource={RelativeSource AncestorType=DataGrid}}"
                      SelectedItem="{Binding Categorie}"/>
        </DataTemplate>
    </DataGridTemplateColumn.CellEditingTemplate>
</DataGridTemplateColumn>
```

## RowDetailsTemplate

```xaml
<DataGrid.RowDetailsTemplate>
    <DataTemplate>
        <StackPanel Margin="20,4">
            <TextBlock Text="{Binding Description}" TextWrapping="Wrap"/>
        </StackPanel>
    </DataTemplate>
</DataGrid.RowDetailsTemplate>
```
Affiché quand la ligne est sélectionnée (ou `RowDetailsVisibilityMode="VisibleWhenSelected"`).

## Style conditionnel sur les lignes

```xaml
<DataGrid.RowStyle>
    <Style TargetType="DataGridRow">
        <Style.Triggers>
            <DataTrigger Binding="{Binding EstErreur}" Value="True">
                <Setter Property="Background" Value="#FFEEEE"/>
                <Setter Property="FontWeight" Value="Bold"/>
            </DataTrigger>
        </Style.Triggers>
    </Style>
</DataGrid.RowStyle>
```

## Tableau d'analyse des colonnes (format pour *-analyse.md)

Quand tu analyses un DataGrid dans le fichier d'analyse, utilise ce format :

| # | Type colonne | Header | Binding | CellTemplate | Remarques |
|---|-------------|--------|---------|--------------|-----------|
| 1 | TextColumn | "Nom" | `Nom` | — | Width=* |
| 2 | TemplateColumn | "Action" | — | `btnSupprimer` | RelativeSource VM |
| 3 | CheckBoxColumn | "Actif" | `EstActif` | — | |

## Scaffolding ViewModel pour DataGrid

```csharp
// Collection source
private ObservableCollection<MonItem> _lignes = new();
public ObservableCollection<MonItem> Lignes
{
    get => _lignes;
    set { if (_lignes == value) return; _lignes = value; OnPropertyChanged(nameof(Lignes)); }
}

// Item sélectionné
private MonItem _ligneSelectionnee;
public MonItem LigneSelectionnee
{
    get => _ligneSelectionnee;
    set
    {
        if (_ligneSelectionnee == value) return;
        _ligneSelectionnee = value;
        OnPropertyChanged(nameof(LigneSelectionnee));
        CommandManager.InvalidateRequerySuggested(); // réactive CanExecute des commandes liées
    }
}

// Commande sur item (reçoit la ligne en paramètre)
public ICommand SupprimerCommand { get; private set; }
// Constructeur :
SupprimerCommand = new RelayCommand(ExecuteSupprimer, CanExecuteSupprimer);

private bool CanExecuteSupprimer(object parameter) => parameter is MonItem;
private void ExecuteSupprimer(object parameter)
{
    if (parameter is MonItem item)
        Lignes.Remove(item);
}
```
