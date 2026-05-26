<Page x:Class="ExpenseTrackerWPF1.Pages.AuthPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      Background="{StaticResource BackgroundBrush}">

    <Grid>

        <!-- Левая часть -->

        <Grid Width="520"
              HorizontalAlignment="Left"
              Background="{StaticResource SidebarBrush}">

            <StackPanel VerticalAlignment="Center"
                        Margin="60">

                <TextBlock Text="BookWave"
                           FontSize="42"
                           FontWeight="Bold"
                           Foreground="White"/>

                <TextBlock Text="Платформа для чтения и публикации книг"
                           Margin="0 20 0 0"
                           FontSize="18"
                           Foreground="#CBD5E1"
                           TextWrapping="Wrap"/>

                <Border Margin="0 40 0 0"
                        Height="2"
                        Background="#334155"/>

                <StackPanel Margin="0 40 0 0">

                    <TextBlock Text="📚 Огромный каталог"
                               FontSize="18"
                               Foreground="White"
                               Margin="0 0 0 20"/>

                    <TextBlock Text="⭐ Система отзывов"
                               FontSize="18"
                               Foreground="White"
                               Margin="0 0 0 20"/>

                    <TextBlock Text="✍ Публикация книг"
                               FontSize="18"
                               Foreground="White"
                               Margin="0 0 0 20"/>

                    <TextBlock Text="🛡 Модерация контента"
                               FontSize="18"
                               Foreground="White"/>

                </StackPanel>

            </StackPanel>

        </Grid>

        <!-- Правая часть -->

        <Grid Margin="520 0 0 0">

            <Border Width="460"
                    Padding="42"
                    CornerRadius="28"
                    Background="White"
                    BorderBrush="{StaticResource BorderBrush}"
                    BorderThickness="1"
                    HorizontalAlignment="Center"
                    VerticalAlignment="Center">

                <StackPanel>

                    <!-- Заголовок -->

                    <TextBlock Text="Добро пожаловать"
                               FontSize="34"
                               FontWeight="Bold"
                               Foreground="{StaticResource TextBrush}"/>

                    <TextBlock Text="Войдите в свой аккаунт"
                               FontSize="15"
                               Margin="0 8 0 0"
                               Foreground="{StaticResource MutedTextBrush}"/>

                    <!-- Логин -->

                    <TextBlock Text="Логин"
                               Margin="0 34 0 10"
                               FontWeight="SemiBold"
                               Foreground="{StaticResource TextBrush}"/>

                    <Border CornerRadius="14"
                            BorderBrush="{StaticResource BorderBrush}"
                            BorderThickness="1"
                            Background="#F8FAFC">

                        <TextBox Height="46"
                                 BorderThickness="0"
                                 Background="Transparent"
                                 Padding="14 0"/>

                    </Border>

                    <!-- Пароль -->

                    <TextBlock Text="Пароль"
                               Margin="0 22 0 10"
                               FontWeight="SemiBold"
                               Foreground="{StaticResource TextBrush}"/>

                    <Border CornerRadius="14"
                            BorderBrush="{StaticResource BorderBrush}"
                            BorderThickness="1"
                            Background="#F8FAFC">

                        <PasswordBox Height="46"
                                     BorderThickness="0"
                                     Background="Transparent"
                                     Padding="14 0"/>

                    </Border>

                    <!-- Кнопка -->

                    <Button Height="52"
                            Margin="0 34 0 0"
                            FontSize="16"
                            Content="Войти"/>

                    <!-- Разделитель -->

                    <Grid Margin="0 30 0 0">

                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="*"/>
                            <ColumnDefinition Width="Auto"/>
                            <ColumnDefinition Width="*"/>
                        </Grid.ColumnDefinitions>

                        <Border Height="1"
                                Background="{StaticResource BorderBrush}"
                                VerticalAlignment="Center"/>

                        <TextBlock Grid.Column="1"
                                   Text="или"
                                   Margin="16 0"
                                   Foreground="{StaticResource MutedTextBrush}"/>

                        <Border Grid.Column="2"
                                Height="1"
                                Background="{StaticResource BorderBrush}"
                                VerticalAlignment="Center"/>

                    </Grid>

                    <!-- Регистрация -->

                    <Button Height="52"
                            Margin="0 30 0 0"
                            Background="#EEF2FF"
                            Foreground="#6366F1"
                            FontSize="16"
                            Content="Создать аккаунт"/>

                </StackPanel>

            </Border>

        </Grid>

    </Grid>

</Page>
