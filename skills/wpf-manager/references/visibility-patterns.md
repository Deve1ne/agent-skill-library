# Patterns de Visibilité WPF

## Référence rapide — quel mécanisme choisir ?

| Situation | Mécanisme recommandé |
|-----------|----------------------|
| 1 bool → Visible/Collapsed | `BooleanToVisibilityConverter` natif |
| 1 bool inversé | `InverseBoolToVisibilityConverter` custom |
| Valeur d'une propriété = X | `DataTrigger` dans Style |
| Plusieurs conditions ET | `MultiDataTrigger` |
| Conditions complexes / OU | `MultiBinding` + `IMultiValueConverter` |
| Activation bouton | `RelayCommand.CanExecute` (pas de binding IsEnabled) |

---

## 1. BooleanToVisibilityConverter (natif)

```xaml
<Window.Resources>
    <BooleanToVisibilityConverter x:Key="BoolToVisibility"/>
</Window.Resources>
<Button Visibility="{Binding EstActif, Converter={StaticResource BoolToVisibility}}"/>
```
`true` → `Visible` | `false` → `Collapsed`

## 2. InverseBoolToVisibilityConverter (custom)

```csharp
public class InverseBoolToVisibilityConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        => (value is bool b && b) ? Visibility.Collapsed : Visibility.Visible;
    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        => value is Visibility v && v == Visibility.Collapsed;
}
```

## 3. DataTrigger dans Style

```xaml
<TextBlock>
    <TextBlock.Style>
        <Style TargetType="TextBlock">
            <Setter Property="Visibility" Value="Collapsed"/>  <!-- défaut obligatoire ici -->
            <Style.Triggers>
                <DataTrigger Binding="{Binding HasErrors}" Value="True">
                    <Setter Property="Visibility" Value="Visible"/>
                </DataTrigger>
            </Style.Triggers>
        </Style>
    </TextBlock.Style>
</TextBlock>
```
⚠️ **Piège** : la valeur par défaut DOIT être dans `<Setter>`, pas directement sur
l'attribut du contrôle — sinon le trigger ne peut pas la réinitialiser.

## 4. MultiDataTrigger (AND)

```xaml
<Style.Triggers>
    <MultiDataTrigger>
        <MultiDataTrigger.Conditions>
            <Condition Binding="{Binding EstConnecte}" Value="True"/>
            <Condition Binding="{Binding Role}" Value="Admin"/>
        </MultiDataTrigger.Conditions>
        <Setter Property="Visibility" Value="Visible"/>
    </MultiDataTrigger>
</Style.Triggers>
```

## 5. MultiBinding + IMultiValueConverter (conditions complexes)

```xaml
<TextBlock>
    <TextBlock.Visibility>
        <MultiBinding Converter="{StaticResource TousVraiConverter}">
            <Binding Path="EstConnecte"/>
            <Binding Path="FichierOuvert"/>
        </MultiBinding>
    </TextBlock.Visibility>
</TextBlock>
```
```csharp
public class TousVraiConverter : IMultiValueConverter
{
    public object Convert(object[] values, Type targetType, object parameter, CultureInfo culture)
        => values.All(v => v is bool b && b) ? Visibility.Visible : Visibility.Collapsed;
    public object[] ConvertBack(object value, Type[] t, object p, CultureInfo c)
        => throw new NotImplementedException();
}
```

## 6. Collapsed vs Hidden

- `Collapsed` → l'élément **ne prend aucune place** dans le layout
- `Hidden` → l'élément **occupe son espace** mais n'est pas rendu

## 7. FallbackValue / TargetNullValue

```xaml
<TextBlock Text="{Binding Nom, TargetNullValue='(sans nom)'}"/>
<Button IsEnabled="{Binding PeutValider, FallbackValue=False}"/>
```

## 8. IsEnabled via CanExecute (bonne pratique)

```xaml
<!-- Préférer ceci -->
<Button Command="{Binding ValiderCommand}"/>
<!-- à ceci -->
<Button Command="{Binding ValiderCommand}" IsEnabled="{Binding PeutValider}"/>
```
Le `IsEnabled` est géré automatiquement par `CanExecute`. Binding direct = redondant
et source de désynchronisation.

## 9. Converter appliqué à un objet (piège fréquent)

Si un `BooleanToVisibilityConverter` reçoit un objet (ex : `DossierSelectionne`)
au lieu d'un bool, il plantera silencieusement. Le converter custom doit gérer :
```csharp
if (value == null) return Visibility.Collapsed;
if (value is bool b) return b ? Visibility.Visible : Visibility.Collapsed;
return Visibility.Visible; // objet non-null = visible
```
