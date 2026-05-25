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
