# How to filter Xamarin.Forms TreeView (SfTreeView)?

You can filter the TreeView nodes based on the search text in Xamarin.Forms [SfTreeView](https://help.syncfusion.com/xamarin/treeview/overview?) by changing the collection.

**XAML**

Use [SearchBar](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/user-interface/searchbar) to search the [TreeViewNodes](https://help.syncfusion.com/cr/cref_files/xamarin/Syncfusion.SfTreeView.XForms~Syncfusion.TreeView.Engine.TreeViewNode.html?).

``` xml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TreeViewXamarin"
             xmlns:syncfusion="clr-namespace:Syncfusion.XForms.TreeView;assembly=Syncfusion.SfTreeView.XForms"
             x:Class="TreeViewXamarin.MainPage">
 
    <ContentPage.BindingContext>
        <local:FileManagerViewModel x:Name="viewModel"/>
    </ContentPage.BindingContext>
    <ContentPage.Behaviors>
        <local:Behavior/>
    </ContentPage.Behaviors>
    <StackLayout>
        <SearchBar Placeholder="Search TreeView" x:Name="searchBar"/>
        <syncfusion:SfTreeView x:Name="treeView" ChildPropertyName="SubFiles" ItemTemplateContextType="Node" AutoExpandMode="AllNodesExpanded" ItemsSource="{Binding ImageNodeInfo}">
            <syncfusion:SfTreeView.ItemTemplate>
                <DataTemplate>
                    <Grid x:Name="grid" RowSpacing="0" >
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="40" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding Content.ImageIcon}" VerticalOptions="Center" HorizontalOptions="Center" HeightRequest="35" WidthRequest="35"/>
                        <Grid Grid.Column="1" RowSpacing="1" Padding="1,0,0,0" VerticalOptions="Center">
                            <Label LineBreakMode="NoWrap" Text="{Binding Content.ItemName}" VerticalTextAlignment="Center"/>
                        </Grid>
                    </Grid>
                </DataTemplate>
            </syncfusion:SfTreeView.ItemTemplate>
        </syncfusion:SfTreeView>
    </StackLayout>
</ContentPage>
``` 

**C#**

[TextChanged](https://docs.microsoft.com/en-us/dotnet/api/xamarin.forms.inputview.textchanged) event of the SerachBar to filter the TreeView collection.

``` c#
namespace TreeViewXamarin
{
    public class Behavior : Behavior<ContentPage>
    {
        SearchBar SearchBar;
        protected override void OnAttachedTo(ContentPage bindable)
        {
            SearchBar = bindable.FindByName<SearchBar>("searchBar");
            SearchBar.TextChanged += SearchBar_TextChanged;
            base.OnAttachedTo(bindable);
        }
    }
}
```

Compare the search text to the model class property to filter the item. The filtered objects are set to [TreeView.ItemsSource](https://help.syncfusion.com/cr/cref_files/xamarin/Syncfusion.SfTreeView.XForms~Syncfusion.XForms.TreeView.SfTreeView~ItemsSource.html?).

``` c#
private void SearchBar_TextChanged(object sender, TextChangedEventArgs e)
{
    if (string.IsNullOrEmpty(e.NewTextValue))
    {
        TreeView.ItemsSource = ViewModel.ImageNodeInfo;
    }
    else
    {
        List<FileManager> items = ViewModel.ImageNodeInfo.Where(x => (x.ItemName.ToLower()).StartsWith(e.NewTextValue.ToLower())).ToList<FileManager>();
        TreeView.ItemsSource = items;
 
        if (items.Count == 0)
        {
            foreach (var node in ViewModel.ImageNodeInfo)
                this.GetChildNode(node, e.NewTextValue);
        }
    }
}
 
private void GetChildNode(FileManager node, string searchText)
{
    if (node.SubFiles.Count < 0) return;
 
    foreach (var child in node.SubFiles)
    {
        if (child.ItemName.ToLower().StartsWith(searchText.ToLower()))
        {
            List<FileManager> items = new List<FileManager>();
            items.Add(child);
            TreeView.ItemsSource = items;
        }
        if (child.SubFiles != null)
        {
            this.GetChildNode(child, searchText);
        }
    }
}
```
