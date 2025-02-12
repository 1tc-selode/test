# Grid: TicTacToe
## Feladat
Solution neve: **Wpf_1_TicTacToe**

Projekt leírása: hozz létre egy 3x3-as Grid-et, aminek minden cellájába egy-egy (összesen 9db) felirat nélküli, strech-elt Button-t helyezz el

Működés: Minden Button-ra kattintáskor egy X-et vagy O-t helyezzünk bele

1. Window méretét beállítjuk 600x600-asra
2. Grid: A Grid-et 3x3-asra osztjuk, hogy a Button-ok egyenletesen elhelyezkedjenek.
3. Button-ok: Minden Button-t elhelyeztünk a megfelelő cella (Row, Column) pozícióba.
4. A buttonok property-jében beállítjuk a strech-elést mindkét irányban (margin 0) és a "Name"-t 5. beállítjuk a cella pozíciójának megfelelően
5. A Click eseményre a "Button_Click" eseménykezelőt állítjuk be minden Buttonra

```C#
<Window x:Class="Wpf_1_TetrisDesigner1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="600" Width="600">
    <Grid x:Name="DesignerGrid">
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>

        <Button x:Name="Button00" Grid.Column="0" Grid.Row="0" Click="Button_Click"/>
        <Button x:Name="Button01" Grid.Column="1" Grid.Row="0" Click="Button_Click"/>
        <Button x:Name="Button02" Grid.Column="2" Grid.Row="0" Click="Button_Click"/>
        <Button x:Name="Button10" Grid.Column="0" Grid.Row="1" Click="Button_Click"/>
        <Button x:Name="Button11" Grid.Column="1" Grid.Row="1" Click="Button_Click"/>
        <Button x:Name="Button12" Grid.Column="2" Grid.Row="1" Click="Button_Click"/>
        <Button x:Name="Button20" Grid.Column="0" Grid.Row="2" Click="Button_Click"/>
        <Button x:Name="Button21" Grid.Column="1" Grid.Row="2" Click="Button_Click"/>
        <Button x:Name="Button22" Grid.Column="2" Grid.Row="2" Click="Button_Click"/>
    </Grid>
</Window>
```

6. Felveszünk egy amőba nevű mátrixot, és egy player1 boolean változót (ez majd a későbbiekben fog kelleni)

```C#
private int[,] amoba = new int[3, 3] { { 0, 0, 0 }, { 0, 0, 0 }, { 0, 0, 0 } };
private bool player1 = true;
```

7. A Button_Click metódus-ban meg kell állapítani, hogy melyik gombon történt a kattintás, ezt az object típusú sender paraméter tartalmazza. Az object típus lehetővé teszi, hogy bármilyen típusú objektumot átadjunk az eseménykezelőnek, mert minden típus az object típusból származik, így az object típusú referencia bármilyen típusú objektumra mutathat. Az eseménykezelőn belül típuskonverziót végzünk a sender paraméteren, hogy a konkrét típusú objektumhoz férjünk hozzá. Például, ha a sender egy gomb, akkor Button típusra konvertáljuk, hogy a gomb tulajdonságait módosíthassuk. 
is operátor: ellenőrízzük, hogy a sender egy adott típusú objektum-e.

```C#
private void Button_Click(object sender, RoutedEventArgs e)
{
    if (sender is Button button && button.Content == null)
    {
        
    }
}
```

8. Ha sikerül Button típusként azonosítani a sender-t, akkor kiolvassuk az adott button helyzetét és ezt felvesszük sor és oszlopként int változókba.

```C#
private void Button_Click(object sender, RoutedEventArgs e)
{
    if (sender is Button button && button.Content == null)
    {
        int row = Grid.GetRow(button);
        int col = Grid.GetColumn(button);
    }
}
```

9. Majd megnézzük a player1 változónkant amit kivűl létre hoztunk, hogy igaz e? Ha igaz, akkor az adott button-nak a Contentjét, tehát a szövegét beállítjuk X-re, a player1 érték false lesz (ezért legközelebb O-t rak le) és átadja a button kordinátáját az amőba mátrixnak, és a helyére egy 1-est írunk be, ez fogja az X-eket jelölni a mátrixban. Viszont legközelebb ha rámegyünk a gombra, akkor a player1 változónk már hamis, ezért a button szövege O lesz, és vissza állítjuk igazra a player1-et így mindig egyszer X-et egyszer, O-t rakunk le. És persze ennek is átadjuk a kordinátáját, és az O gombokat 2-vel jelöljük. És itt létrekell hozzunk egy WinnerCheck() függvényt, ahol minden kattintáskor ellenőrizzük hogy valaki nem e nyert e

```C#
private void Button_Click(object sender, RoutedEventArgs e)
{
    if (sender is Button button && button.Content == null)
    {
        int row = Grid.GetRow(button);
        int col = Grid.GetColumn(button);
        if (player1)
        {
            button.Content = "x";
            player1 = false;
            amoba[row, col] = 1;
        }
        else
        {
            button.Content= "o";
            player1 = true;
            amoba[row,col] = 2;
        }
        WinnerCheck();
    }
}
```

10. A WinnerCheck() függvényben ahogy említettem minden gombnyomásnál megnézzük hogy nyert e vagy az X vagy az O. Először felveszünk egy üres string változót winner névvel, ide fogjuk elmenteni a nyertest (x vagy o). 

```C#
private void WinnerCheck()
{
    string winner = string.Empty;
}
```

11. 3 nagy esetben nyerhet valamelyik fél. Vagy oszloposan 3 féle képen, vagy vízszintesen 3 féle képen, vagy átlósan 2 féle képen. A C# úgy kezeli a két dimenziós tömböket, hogyha végig megyünk rajtuk egy for-al, akkor a "amoba.GetLength(0)" a "0" mindig a sorok számát veszi át (a mi esetünkben ez 3 most) és az "amoba.GetLength(1)" az "1" mindig az oszlopok számát veszi át (ez is 3 a mi esetünkben, de ha a mátrixnak különböző az értéke, akkor ez változik) 
    1. *Oszloposan megyünk végig rajta:*
    For ciklussal végig megyünk a mátrixon, 0-ról kezdünk és addig megyünk, ahány sorból áll a mátrix, ezt tudjuk a amoba.GetLength(0)-val elérni. A for cikloson belül, megnézzük hogy ha egy adott oszlop, összes értéke 1, akkor X nyer, ha nem 1 akkor O nyer. Mivel oszlopokat nézünk, ezért a mátrix első értéke mindig ugyan azok lesznek (0, 1, 2), mert az jelöli a sorokat, és csak a második értéke lesz az *i*-edik elem, így tudjuk megnézni az összes oszlopot (Az első oszlop adatai a mi esetünkben **0,0; 1,0 és 2,0**). És ezeket meg nézzük hogy az értékük egyenlőek-e. És ha van egy nyertes, akkor egy break a for végén, hogy ne ellenőrizze tovább.
    ```C#
    for (int i = 0; i < amoba.GetLength(0); i++)
    {
        if (amoba[0,i]!= 0 && amoba[0,i] == amoba[1,i] && amoba[1,i] == amoba[2,i])
        {
            winner = amoba[0, i] == 1 ? "x" : "o";
            break;
        }
    }
    ```
    2. *Vízszintesen megyünk végig rajta:*
    Ez ugyan úgy működik mint amikor az oszlopokat ellenőriztük, csak a amoba.GetLength(1) itt 1 lesz az érték mivel itt most egy sor a mi esetünkben 3 oszlop tartalma. És az amőba mátrik első értéke lesz így az *i*-edik érték, mert az fog változni mint oszlopok, és a második érték az vagy 0 vagy 1 vagy 2. És ezeket meg nézzük hogy az értékük egyenlőek-e. És ha van egy nyertes, akkor egy break a for végén, hogy ne ellenőrizze tovább.
    ```C#
    for (int i = 0;i < amoba.GetLength(1); i++)
    {
        if (amoba[i, 0] != 0 && amoba[i, 0] == amoba[i, 1] && amoba[i, 1] == amoba[i, 2])
        {
            winner = amoba[i, 0] == 1 ? "x" : "o";
            break;
        }
    }
    ```
    3. *Átlósan ellenőrizzük le:*
    Itt két féle képen lehet alaki nyertes, ilyen X alakból az egyik átló. A középső, mátrixban 1,1-elem azon mindenféle képpen át fog menni, és megnézzük hogy megvan e az egyik átló "\" vagy a másik átló "/". És itt mivel az 1,1 az mind két esetben benne van, azt leellenőrizzük, hogyha 1 az értéke akkor X, ha nem 1 akkor O nyert.
    ```C#
    if ((amoba[0,0] != 0 && amoba[0,0] == amoba[1, 1] && amoba[1, 1] == amoba[2, 2]) ||
            (amoba[0, 2] != 0 && amoba[0, 2] == amoba[1, 1] && amoba[1, 1] == amoba[2, 0]))
    {
        winner = amoba[1, 1] == 1 ? "x" : "o";
    }
    ```

12. A WinnerCheck() végén, meg kell nézni hogy a winner, ha már nem üres, akkor kiírja a nyertest egy felugró üzenetbe, és lefuttatjuk a Restart() függvényt, hogy újra induljon a játék.
```C#
private void WinnerCheck()
{
    string winner = string.Empty;
    //columncheck
    for (int i = 0; i < amoba.GetLength(0); i++)
    {
        if (amoba[0,i]!= 0 && amoba[0,i] == amoba[1,i] && amoba[1,i] == amoba[2,i])
        {
            winner = amoba[0, i] == 1 ? "x" : "o";
            break;
        }
    }
    //rowcheck
    for (int i = 0;i < amoba.GetLength(1); i++)
    {
        if (amoba[i, 0] != 0 && amoba[i, 0] == amoba[i, 1] && amoba[i, 1] == amoba[i, 2])
        {
            winner = amoba[i, 0] == 1 ? "x" : "o";
            break;
        }
        }
        //crosscheck
        if ((amoba[0,0] != 0 && amoba[0,0] == amoba[1, 1] && amoba[1, 1] == amoba[2, 2]) ||
            (amoba[0, 2] != 0 && amoba[0, 2] == amoba[1, 1] && amoba[1, 1] == amoba[2, 0]))
        {
            winner = amoba[1, 1] == 1 ? "x" : "o";
        }
        if (winner != "")
        {
            MessageBox.Show(winner);
            Restart();
        }
}
```

13. Restart() függvényben az összes button szövegét null-ra állítjuk, így a kijelzőn törlödik az összes X és O. Majd egy dupla for ciklussal végig megyünk a mátrixon, és minden elemét vissza álltijuk 0-ra. És ekkor ha a felugró üzenetet le OK-oljál, kezdődhet újra a játék.

```C#
private void Restart()
{
    Button00.Content = null;
    Button01.Content = null;
    Button02.Content = null;
    Button10.Content = null;
    Button11.Content = null;
    Button12.Content = null;
    Button00.Content = null;
    Button21.Content = null;
    Button22.Content = null;
    for (int i = 0; i < 3; i++)
    {
        for (int j = 0; j < 3; j++)
        {
            amoba[i, j] = 0;
        }
    }
}
```
<details>
<summary>Nyiss le az xaml forrásért!</summary> 

```C#
<Window x:Class="Wpf_1_TicTacToe.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:Wpf_1_TicTacToe"
        mc:Ignorable="d"
        Title="TicTacToe" Height="600" Width="600">
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Button x:Name="Button00" Grid.Row="0" Grid.Column="0" Click="Button_Click" FontSize="100"/>
            <Button x:Name="Button01" Grid.Row="0" Grid.Column="1" Click="Button_Click" FontSize="100"/>
            <Button x:Name="Button02" Grid.Row="0" Grid.Column="2" Click="Button_Click" FontSize="100"/>
            <Button x:Name="Button10" Grid.Row="1" Grid.Column="0" Click="Button_Click" FontSize="100"/>
            <Button x:Name="Button11" Grid.Row="1" Grid.Column="1" Click="Button_Click" FontSize="100"/>
            <Button x:Name="Button12" Grid.Row="1" Grid.Column="2" Click="Button_Click" FontSize="100"/>
            <Button x:Name="Button20" Grid.Row="2" Grid.Column="0" Click="Button_Click" FontSize="100"/>
            <Button x:Name="Button21" Grid.Row="2" Grid.Column="1" Click="Button_Click" FontSize="100"/>
            <Button x:Name="Button22" Grid.Row="2" Grid.Column="2" Click="Button_Click" FontSize="100"/>
        </Grid>
</Window>

```
</details>

<details>
<summary>Nyiss le az xaml.cs forrásért!</summary>

```C#
using System.Text;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;

namespace Wpf_1_TicTacToe
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        private int[,] amoba = new int[3, 3] { { 0, 0, 0 }, { 0, 0, 0 }, { 0, 0, 0 } };
        private bool player1 = true;
        public MainWindow()
        {
            InitializeComponent();
        }
        private void Button_Click(object sender, RoutedEventArgs e)
        {
            if (sender is Button button && button.Content == null)
            {
                //button.Content = player1 ? "x" : "o";
                //player1 = !player1;
                int row = Grid.GetRow(button);
                int col = Grid.GetColumn(button);
                if (player1)
                {
                    button.Content = "x";
                    player1 = false;
                    amoba[row, col] = 1;
                }
                else
                {
                    button.Content= "o";
                    player1 = true;
                    amoba[row,col] = 2;
                }
                WinnerCheck();
            }
        }
        private void WinnerCheck()
        {
            string winner = string.Empty;
            //columncheck
            for (int i = 0; i < amoba.GetLength(0); i++)
            {
                if (amoba[0,i]!= 0 && amoba[0,i] == amoba[1,i] && amoba[1,i] == amoba[2,i])
                {
                    winner = amoba[0, i] == 1 ? "x" : "o";
                    break;
                }
            }
            //rowcheck
            for (int i = 0;i < amoba.GetLength(1); i++)
            {
                if (amoba[i, 0] != 0 && amoba[i, 0] == amoba[i, 1] && amoba[i, 1] == amoba[i, 2])
                {
                    winner = amoba[i, 0] == 1 ? "x" : "o";
                    break;
                }
            }
            //crosscheck
            if ((amoba[0,0] != 0 && amoba[0,0] == amoba[1, 1] && amoba[1, 1] == amoba[2, 2]) ||
                (amoba[0, 2] != 0 && amoba[0, 2] == amoba[1, 1] && amoba[1, 1] == amoba[2, 0]))
            {
                winner = amoba[1, 1] == 1 ? "x" : "o";
            }
            if (winner != "")
            {
                MessageBox.Show(winner);
                Restart();
            }
        }
        private void Restart()
        {
            Button00.Content = null;
            Button01.Content = null;
            Button02.Content = null;
            Button10.Content = null;
            Button11.Content = null;
            Button12.Content = null;
            Button00.Content = null;
            Button21.Content = null;
            Button22.Content = null;
            for (int i = 0; i < 3; i++)
            {
                for (int j = 0; j < 3; j++)
                {
                    amoba[i, j] = 0;
                }
            }
        }
    }
}
```
</details>
