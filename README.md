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
