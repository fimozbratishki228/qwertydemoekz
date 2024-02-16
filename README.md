# qwertydemoekz
1337

Вход и регистрация
<TabControl>
    <TabItem Header="Авторизация">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <StackPanel Grid.Row="0" VerticalAlignment="Center" HorizontalAlignment="Center">
                <Label Content="Логин:" FontSize="15"/>
                <TextBox x:Name="tbLogin" FontSize="15" Width="125" Height="25"/>
                <Label Content="Пароль:" FontSize="15" />
                <PasswordBox x:Name="pbPassword" FontSize="15" Width="125" Height="25"/>
                <Button Height="50" x:Name="btnAutorization" Content="Войти" FontSize="15" Margin="5" Click="btnAutorization_Click"/>
            </StackPanel>
        </Grid>
    </TabItem>
    <TabItem Header="Регистрация">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <StackPanel Grid.Row="1" VerticalAlignment="Center" HorizontalAlignment="Center">
                <Label Content="Введите Логин:" FontSize="15"/>
                <TextBox x:Name="tbLoginReg" FontSize="15" Width="125" Height="25"/>
                <Label Content="Пароль:" FontSize="15"/>
                <PasswordBox x:Name="pbPasswordReg" FontSize="15" Width="125" Height="25"/>
                <Label Content="Повторите Пароль:" FontSize="15"/>
                <PasswordBox x:Name="pbPasswordRepeat" FontSize="15" Width="125" Height="25" />
                <Button Height="50" x:Name="btnRegistr" Content="Зарегистрироваться" Margin="5" FontSize="15" Click="btnRegistr_Click"/>
            </StackPanel>
        </Grid>
    </TabItem>
</TabControl>


private void btnRegistr_Click(object sender, RoutedEventArgs e)
{
    using (var context = new caledjEntities())
    {
        if (context.Пользователь.Any(u => u.Логин == tbLoginReg.Text))
        {
            MessageBox.Show("Пользователь с таким логином существует");
        }
        if (tbLoginReg.Text == null || pbPasswordReg.Password == null || pbPasswordReg.Password != pbPasswordRepeat.Password)
        {
            MessageBox.Show("Заполните данные правильно!!");
        }
        else
        {
            Пользователь пользователь = new Пользователь();
            пользователь.Логин = tbLoginReg.Text;
            пользователь.Пароль = pbPasswordReg.Password;
            context.Пользователь.Add(пользователь);
            context.SaveChanges();
        MessageBox.Show("Пользователь зарегистрирован");
        }
    }
}
private void btnAutorization_Click(object sender, RoutedEventArgs e)
{
    string login = tbLogin.Text;
    string password = pbPassword.Password;
    using (var context = new caledjEntities())
    {
        Пользователь пользователь = context.Пользователь.FirstOrDefault(u =>u.Логин == login.Trim() && u.Пароль == password.Trim());
        PersonalCabWindow personalCabWindow = new PersonalCabWindow();
        if (пользователь != null)
        {
            MessageBox.Show("Успешная авторизация!!");
            personalCabWindow.Show();
            Close();
        }
        else
        {
            MessageBox.Show("Неверный логин или пароль!!");
        }
    }
}

Основное окно для изменения

<Grid.RowDefinitions>
    <RowDefinition Height="50"/>
    <RowDefinition/>
    <RowDefinition Height="75"/>
</Grid.RowDefinitions>
<StackPanel Orientation="Horizontal">
    <Label Content="Поиск:" HorizontalAlignment="Left" VerticalAlignment="Center"/>
    <TextBox x:Name="tbSeatch" Grid.Row="0" HorizontalAlignment="Left" VerticalAlignment="Center" FontSize="20" Width="200" TextChanged="tbSeatch_TextChanged"/>
</StackPanel>
<TabControl Grid.Row="1" x:Name="tcImtex">
    <TabItem Header="Студенты" x:Name="tiDgstud">
        <DataGrid Grid.Row="1" AutoGenerateColumns="False" x:Name="dgStud" IsReadOnly="True">
            <DataGrid.Columns>
                <DataGridTextColumn Header="Имя" Binding="{Binding Имя}"/>
                <DataGridTextColumn Header="Фамилия" Binding="{Binding Фамиия}"/>
                <DataGridTextColumn Header="Отчество" Binding="{Binding Отчетcво}"/>
                <DataGridTextColumn Header="Дата Рождения" Binding="{Binding ДатаРождения}"/>
                <DataGridTextColumn Header="Тип Пользователя" Binding="{Binding Пользователь1.ТипПользователь.Наименование}"/>
            </DataGrid.Columns>
            <DataGridTemplateColumn>
                <DataGridTemplateColumn.CellTemplate>
                    <DataTemplate>
                        <Button x:Name="btnred" Content="Редактирование" Click="btnred_Click" FontSize="10"/>
                    </DataTemplate>
                </DataGridTemplateColumn.CellTemplate>
            </DataGridTemplateColumn>
        </DataGrid>
    </TabItem>
    <TabItem Header="Пользователи" x:Name="tidgUsers">
        <DataGrid Grid.Row="1" AutoGenerateColumns="False" x:Name="dgUsers" IsReadOnly="True">
            <DataGrid.Columns>
                <DataGridTextColumn Header="Имя" Binding="{Binding Логин}"/>
                <DataGridTextColumn Header="Фамилия" Binding="{Binding Пароль}"/>
                <DataGridTextColumn Header="Тип Пользователя" Binding="{Binding ТипПользователь.Наименование}"/>
            </DataGrid.Columns>
        </DataGrid>
    </TabItem>
</TabControl>
<StackPanel Orientation="Horizontal" Grid.Row="2" HorizontalAlignment="Right" VerticalAlignment="Center">
    <Button x:Name="btnAdd" Content="Добавить"  Click="btnAdd_Click" Margin="5"/>
    <Button x:Name="btnDell" Content="Удалить!!"  Click="btnDell_Click"  Margin="5"/>
</StackPanel>


private void btnDell_Click(object sender, RoutedEventArgs e)
{
    if (tcImtex.SelectedItem == tiDgstud)
    {
        using (var context = new caledjEntities())
        {
            var removeRows = dgStud.SelectedItems.Cast<Студент>().ToList();
            if (removeRows.Count() == 0)
            {
                MessageBox.Show("Нужно выбрать строки!");
                return;
            }
            if (MessageBox.Show($"Точно удалить:{removeRows.Count()} элементов?", "Внимание!", MessageBoxButton.YesNo, MessageBoxImage.Question) == MessageBoxResult.Yes)
            {
                try
                {
                    var UsersID = removeRows.Select(p => p.КодСтудента).ToList();
                    var RemoveUsers = context.Студент.Where(u => UsersID.Contains(u.КодСтудента)).ToList();
                    context.Студент.RemoveRange(RemoveUsers);
                    context.SaveChanges();
                    MessageBox.Show("Пользоователи Удалены!!!");
                    dgStud.ItemsSource = context.Студент.Include(u => u.Пользователь1.ТипПользователь).ToList();
                }
                catch (Exception ex)
                {
                    MessageBox.Show(ex.ToString());
                }
            }
        }
    }
    if (tcImtex.SelectedItem == tidgUsers)
    {
        using (var context = new caledjEntities())
        {
            var removeRows = dgUsers.SelectedItems.Cast<Пользователь>().ToList();
            if (removeRows.Count() == 0)
            {
                MessageBox.Show("Нужно выбрать строки!");
                return;
            }
            if (MessageBox.Show($"Точно удалить:{removeRows.Count()} элементов?", "Внимание!", MessageBoxButton.YesNo, MessageBoxImage.Question) == MessageBoxResult.Yes)
            {
                try
                {
                    var UsersID = removeRows.Select(p => p.КодПользователь).ToList();
                    var RemoveUsers = context.Пользователь.Where(u => UsersID.Contains(u.КодПользователь)).ToList();
                    context.Пользователь.RemoveRange(RemoveUsers);
                    context.SaveChanges();
                    MessageBox.Show("Пользоователи Удалены!!!");
                    dgUsers.ItemsSource = context.Пользователь.Include(u => u.ТипПользователь).ToList();
                }
                catch (Exception ex) { MessageBox.Show(ex.ToString()); }
            }
        }
    }
}
private void tbSeatch_TextChanged(object sender, TextChangedEventArgs e)
{
    if (tcImtex.SelectedItem == tiDgstud)
    {
        using (var context = new caledjEntities())
        {
            dgStud.ItemsSource = context.Студент.Where(a => a.Имя.Contains(tbSeatch.Text) || a.Фамиия.Contains(tbSeatch.Text) || a.Отчетcво.Contains(tbSeatch.Text)).ToList();
        }
    }
    if (tcImtex.SelectedItem == tidgUsers)
    {
        using (var context = new caledjEntities())
        {
            dgUsers.ItemsSource = context.Пользователь.Where(a => a.Логин.StartsWith(tbSeatch.Text) || a.Логин.StartsWith(tbSeatch.Text) || a.ТипПользователь.Наименование.StartsWith(tbSeatch.Text)).ToList();
        }
    }
}
private void btnAdd_Click(object sender, RoutedEventArgs e)
{
    redWindow redWindow = new redWindow(null);
    redWindow.Show();
    Close();
}
private void btnred_Click(object sender, RoutedEventArgs e)
{
    redWindow redWindow = new redWindow((sender as Button).DataContext as Студент);
}


Окно Редактирования

<Grid.RowDefinitions>
    <RowDefinition/>
    <RowDefinition Height="60"/>
</Grid.RowDefinitions>
<StackPanel VerticalAlignment="Center" HorizontalAlignment="Center" >
    <Label Content="Имя:" FontSize="25"/>
    <TextBox Text="{Binding Имя}" x:Name="tbName" Width="150" FontSize="25" Margin="5"/>
    <Label Content="Фамилия:" FontSize="25"/>
    <TextBox Text="{Binding Фалимия}" x:Name="tbFam" Width="150" FontSize="25" Margin="5"/>
    <Label Content="Отчество:" FontSize="25"/>
    <TextBox Text="{Binding Отчество}" x:Name="tbOtch" Width="150" FontSize="25" Margin="5"/>
    <Label Content="Пользователь:" FontSize="25"/>
    <ComboBox SelectedItem="{Binding КодТипаПолользователя}" DisplayMemberPath="Наименование" x:Name="cbType" Width="150" FontSize="25" Margin="5"/>
</StackPanel>
<StackPanel HorizontalAlignment="Center" VerticalAlignment="Center" Orientation="Horizontal" Grid.Row="1">
    <Button x:Name="SaveBack" Content="Сохранить"  Click="SaveBack_Click" Margin="5" />
    <Button x:Name="btnBack" Content="Назад"  Margin="5" Click="btnBack_Click"/>
</StackPanel>


public RedaktWindow()
{
    InitializeComponent(); using (var context = new caledjEntities())
    {
        cbType.ItemsSource = context.ТипПользователь.ToList();
        var qwered = context.ТипПользователь.ToList();
        var erewre = context.Студент.ToList();
    }
}
private void btnBack_Click(object sender, RoutedEventArgs e)
{
    PersonalWindow personalCabWindow = new PersonalWindow();
    personalCabWindow.Show();
    Close();
}
private void SaveBack_Click(object sender, RoutedEventArgs e)
 {
     using (var context = new testEntities())
     {
         if (_students.StudentID == 0)
         {
             context.Students.Add(_students);
             context.SaveChanges();
             MessageBox.Show("fds14234123rfffrfrqqwdfdas");
             PersonalCabWindow personalCabWindow = new PersonalCabWindow();
             personalCabWindow.Show();
             Close();
         }
     }
 }

