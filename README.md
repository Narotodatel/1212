# 1212
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">

    <!-- Кнопки -->
    <Style TargetType="Button">
        <Setter Property="Foreground" Value="White"/>
        <Setter Property="Background" Value="{StaticResource PrimaryBrush}"/>
        <Setter Property="BorderThickness" Value="0"/>
        <Setter Property="Padding" Value="12 8"/>
        <Setter Property="Cursor" Value="Hand"/>
        <Setter Property="FontSize" Value="14"/>
        <Setter Property="FontWeight" Value="SemiBold"/>
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="Button">
                    <Border x:Name="border"
                            Background="{TemplateBinding Background}"
                            CornerRadius="12">

                        <ContentPresenter HorizontalAlignment="Center"
                                          VerticalAlignment="Center"/>
                    </Border>

                    <ControlTemplate.Triggers>

                        <Trigger Property="IsMouseOver" Value="True">
                            <Setter TargetName="border"
                                    Property="Opacity"
                                    Value="0.9"/>
                        </Trigger>

                        <Trigger Property="IsPressed" Value="True">
                            <Setter TargetName="border"
                                    Property="Opacity"
                                    Value="0.75"/>
                        </Trigger>

                    </ControlTemplate.Triggers>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>

    <!-- TextBox -->
    <Style TargetType="TextBox">
        <Setter Property="Padding" Value="10"/>
        <Setter Property="FontSize" Value="14"/>
        <Setter Property="Background" Value="White"/>
        <Setter Property="BorderBrush" Value="{StaticResource BorderBrush}"/>
        <Setter Property="BorderThickness" Value="1"/>
    </Style>

    <!-- ComboBox -->
    <Style TargetType="ComboBox">
        <Setter Property="Padding" Value="8"/>
        <Setter Property="FontSize" Value="14"/>
    </Style>

</ResourceDictionary>


<Window x:Class="ExpenseTrackerWPF1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="BookWave"
        Height="720"
        Width="1280"
        WindowStartupLocation="CenterScreen"
        Background="{StaticResource BackgroundBrush}">

    <Grid>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="90"/>
            <ColumnDefinition Width="*"/>
        </Grid.ColumnDefinitions>

        <!-- Sidebar -->
        <Border Grid.Column="0"
                Background="{StaticResource SidebarBrush}">

            <StackPanel VerticalAlignment="Stretch">

                <!-- Logo -->
                <Border Height="90"
                        Background="#0F172A">

                    <TextBlock Text="BW"
                               Foreground="White"
                               FontSize="28"
                               FontWeight="Bold"
                               VerticalAlignment="Center"
                               HorizontalAlignment="Center"/>
                </Border>

                <!-- Buttons -->

                <Button Height="60"
                        Margin="10"
                        ToolTip="Каталог">
                    <TextBlock Text="📚"
                               FontSize="24"/>
                </Button>

                <Button Height="60"
                        Margin="10"
                        ToolTip="Списки">
                    <TextBlock Text="⭐"
                               FontSize="24"/>
                </Button>

                <Button Height="60"
                        Margin="10"
                        ToolTip="Автор">
                    <TextBlock Text="✍"
                               FontSize="24"/>
                </Button>

                <Button Height="60"
                        Margin="10"
                        ToolTip="Администрирование">
                    <TextBlock Text="🛠"
                               FontSize="24"/>
                </Button>

                <Button Height="60"
                        Margin="10"
                        ToolTip="Профиль">
                    <TextBlock Text="👤"
                               FontSize="24"/>
                </Button>

            </StackPanel>

        </Border>

        <!-- Main Content -->
        <Grid Grid.Column="1">

            <Grid.RowDefinitions>
                <RowDefinition Height="70"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <!-- TopBar -->
            <Border Background="White"
                    BorderBrush="{StaticResource BorderBrush}"
                    BorderThickness="0 0 0 1">

                <Grid Margin="20 0">

                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="*"/>
                        <ColumnDefinition Width="250"/>
                    </Grid.ColumnDefinitions>

                    <TextBlock Text="Каталог книг"
                               FontSize="26"
                               FontWeight="SemiBold"
                               Foreground="{StaticResource TextBrush}"
                               VerticalAlignment="Center"/>

                    <TextBox Grid.Column="1"
                             Height="40"
                             VerticalAlignment="Center"
                             Text="Поиск книг..."/>
                </Grid>
            </Border>

            <!-- Content -->
            <ScrollViewer Grid.Row="1">

                <WrapPanel Margin="20">

                    <!-- Card -->
                    <Border Width="220"
                            Height="320"
                            Margin="10"
                            Background="{StaticResource CardBrush}"
                            BorderBrush="{StaticResource BorderBrush}"
                            BorderThickness="1"
                            CornerRadius="18">

                        <Grid>

                            <Grid.RowDefinitions>
                                <RowDefinition Height="180"/>
                                <RowDefinition Height="*"/>
                            </Grid.RowDefinitions>

                            <!-- Cover -->
                            <Border Background="#CBD5E1"
                                    CornerRadius="18 18 0 0">

                                <TextBlock Text="Обложка"
                                           HorizontalAlignment="Center"
                                           VerticalAlignment="Center"
                                           FontSize="18"
                                           Foreground="{StaticResource MutedTextBrush}"/>
                            </Border>

                            <!-- Info -->
                            <StackPanel Grid.Row="1"
                                        Margin="14">

                                <TextBlock Text="Название книги"
                                           FontWeight="Bold"
                                           FontSize="16"
                                           TextWrapping="Wrap"
                                           Foreground="{StaticResource TextBrush}"/>

                                <TextBlock Text="Автор"
                                           Margin="0 6 0 0"
                                           Foreground="{StaticResource MutedTextBrush}"/>

                                <Border Background="#EEF2FF"
                                        CornerRadius="8"
                                        Padding="8 4"
                                        Margin="0 10 0 0"
                                        HorizontalAlignment="Left">

                                    <TextBlock Text="★ 4.8"
                                               Foreground="{StaticResource PrimaryBrush}"
                                               FontWeight="SemiBold"/>
                                </Border>

                            </StackPanel>

                        </Grid>

                    </Border>

                </WrapPanel>

            </ScrollViewer>

        </Grid>

    </Grid>

</Window>


<Page x:Class="ExpenseTrackerWPF1.Pages.CatalogPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      Background="{StaticResource BackgroundBrush}">

    <Grid Margin="24">

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <!-- Header -->

        <Grid Margin="0 0 0 24">

            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="280"/>
            </Grid.ColumnDefinitions>

            <StackPanel>

                <TextBlock Text="Каталог книг"
                           FontSize="34"
                           FontWeight="Bold"
                           Foreground="{StaticResource TextBrush}"/>

                <TextBlock Text="Найди что почитать сегодня"
                           FontSize="15"
                           Foreground="{StaticResource MutedTextBrush}"
                           Margin="0 4 0 0"/>

            </StackPanel>

            <!-- Search -->

            <Border Grid.Column="1"
                    Height="48"
                    CornerRadius="14"
                    Background="White"
                    BorderBrush="{StaticResource BorderBrush}"
                    BorderThickness="1">

                <Grid Margin="14 0">

                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto"/>
                        <ColumnDefinition Width="*"/>
                    </Grid.ColumnDefinitions>

                    <TextBlock Text="🔍"
                               FontSize="16"
                               VerticalAlignment="Center"
                               Margin="0 0 10 0"/>

                    <TextBox BorderThickness="0"
                             Background="Transparent"
                             VerticalAlignment="Center"
                             FontSize="15"
                             Text="Поиск по названию или автору"/>

                </Grid>

            </Border>

        </Grid>

        <!-- Filters -->

        <Border Grid.Row="1"
                Background="White"
                CornerRadius="18"
                Padding="20"
                Margin="0 0 0 24"
                BorderBrush="{StaticResource BorderBrush}"
                BorderThickness="1">

            <WrapPanel VerticalAlignment="Center">

                <ComboBox Width="180"
                          Height="40"
                          Margin="0 0 14 0">

                    <ComboBoxItem Content="Все жанры"/>
                    <ComboBoxItem Content="Фэнтези"/>
                    <ComboBoxItem Content="Детектив"/>
                    <ComboBoxItem Content="Роман"/>
                    <ComboBoxItem Content="Хоррор"/>

                </ComboBox>

                <ComboBox Width="180"
                          Height="40"
                          Margin="0 0 14 0">

                    <ComboBoxItem Content="Сортировка"/>
                    <ComboBoxItem Content="По имени"/>
                    <ComboBoxItem Content="По рейтингу"/>

                </ComboBox>

                <Button Width="140"
                        Height="40"
                        Content="Применить"/>

            </WrapPanel>

        </Border>

        <!-- Books -->

        <ScrollViewer Grid.Row="2"
                      VerticalScrollBarVisibility="Auto">

            <WrapPanel>

                <!-- Book Card -->

                <Border Width="240"
                        Height="420"
                        Margin="0 0 20 20"
                        Background="White"
                        CornerRadius="24"
                        BorderBrush="{StaticResource BorderBrush}"
                        BorderThickness="1">

                    <Grid>

                        <Grid.RowDefinitions>
                            <RowDefinition Height="240"/>
                            <RowDefinition Height="*"/>
                        </Grid.RowDefinitions>

                        <!-- Cover -->

                        <Grid>

                            <Border Margin="14"
                                    CornerRadius="18"
                                    Background="#CBD5E1">

                                <TextBlock Text="Обложка"
                                           FontSize="18"
                                           Foreground="#64748B"
                                           VerticalAlignment="Center"
                                           HorizontalAlignment="Center"/>

                            </Border>

                            <!-- Rating -->

                            <Border Width="70"
                                    Height="32"
                                    HorizontalAlignment="Right"
                                    VerticalAlignment="Top"
                                    Margin="0 24 24 0"
                                    CornerRadius="10"
                                    Background="#EEF2FF">

                                <StackPanel Orientation="Horizontal"
                                            HorizontalAlignment="Center"
                                            VerticalAlignment="Center">

                                    <TextBlock Text="★"
                                               Foreground="#6366F1"
                                               Margin="0 0 4 0"/>

                                    <TextBlock Text="4.8"
                                               FontWeight="SemiBold"
                                               Foreground="#6366F1"/>

                                </StackPanel>

                            </Border>

                        </Grid>

                        <!-- Info -->

                        <Grid Grid.Row="1"
                              Margin="18">

                            <Grid.RowDefinitions>
                                <RowDefinition Height="Auto"/>
                                <RowDefinition Height="Auto"/>
                                <RowDefinition Height="*"/>
                                <RowDefinition Height="Auto"/>
                            </Grid.RowDefinitions>

                            <TextBlock Text="Название книги"
                                       FontSize="18"
                                       FontWeight="Bold"
                                       TextWrapping="Wrap"
                                       Foreground="{StaticResource TextBrush}"/>

                            <TextBlock Grid.Row="1"
                                       Text="Автор книги"
                                       Margin="0 6 0 0"
                                       FontSize="14"
                                       Foreground="{StaticResource MutedTextBrush}"/>

                            <!-- Genres -->

                            <WrapPanel Grid.Row="2"
                                       Margin="0 14 0 0">

                                <Border Background="#F1F5F9"
                                        CornerRadius="8"
                                        Padding="10 4"
                                        Margin="0 0 8 8">

                                    <TextBlock Text="Фэнтези"
                                               FontSize="12"/>
                                </Border>

                                <Border Background="#F1F5F9"
                                        CornerRadius="8"
                                        Padding="10 4"
                                        Margin="0 0 8 8">

                                    <TextBlock Text="Роман"
                                               FontSize="12"/>
                                </Border>

                            </WrapPanel>

                            <!-- Buttons -->

                            <Grid Grid.Row="3"
                                  Margin="0 14 0 0">

                                <Grid.ColumnDefinitions>
                                    <ColumnDefinition Width="*"/>
                                    <ColumnDefinition Width="52"/>
                                </Grid.ColumnDefinitions>

                                <Button Height="44"
                                        Content="Открыть"/>

                                <Button Grid.Column="1"
                                        Height="44"
                                        Margin="10 0 0 0"
                                        Background="#EEF2FF"
                                        Foreground="#6366F1"
                                        Content="+"/>

                            </Grid>

                        </Grid>

                    </Grid>

                </Border>

            </WrapPanel>

        </ScrollViewer>

    </Grid>

</Page>
