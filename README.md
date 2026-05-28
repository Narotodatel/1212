Глеб Жопа:
-- Создание базы данных
CREATE DATABASE BookPlatform;
GO

USE BookPlatform;
GO

-- 1. Роли
CREATE TABLE Roles (
    RoleID INT PRIMARY KEY IDENTITY(1,1),
    RoleName NVARCHAR(50) NOT NULL UNIQUE,
    Description NVARCHAR(255) NULL
);

-- 2. Пользователи
CREATE TABLE Users (
    UserID INT PRIMARY KEY IDENTITY(1,1),
    Login NVARCHAR(100) NOT NULL UNIQUE,
    PasswordHash NVARCHAR(255) NOT NULL, -- пароль в реальном проекте хешируется
    Email NVARCHAR(255) NOT NULL UNIQUE,
    DisplayName NVARCHAR(100) NOT NULL,
    RoleID INT NOT NULL,
    IsFrozen BIT NOT NULL DEFAULT 0,
    CreatedAt DATETIME NOT NULL DEFAULT GETDATE(),
    FOREIGN KEY (RoleID) REFERENCES Roles(RoleID)
);

-- 3. Жанры
CREATE TABLE Genres (
    GenreID INT PRIMARY KEY IDENTITY(1,1),
    GenreName NVARCHAR(100) NOT NULL UNIQUE,
    Description NVARCHAR(255) NULL
);

-- 4. Книги
CREATE TABLE Books (
    BookID INT PRIMARY KEY IDENTITY(1,1),
    Title NVARCHAR(255) NOT NULL,
    Description NVARCHAR(MAX) NULL,
    CoverImagePath NVARCHAR(500) NULL,
    Content NVARCHAR(MAX) NOT NULL, -- текстовое содержимое
    AuthorID INT NOT NULL,
    IsFrozen BIT NOT NULL DEFAULT 0,
    CreatedAt DATETIME NOT NULL DEFAULT GETDATE(),
    FOREIGN KEY (AuthorID) REFERENCES Users(UserID)
);

-- 5. Связь книги с жанрами (многие ко многим)
CREATE TABLE BookGenres (
    BookID INT NOT NULL,
    GenreID INT NOT NULL,
    PRIMARY KEY (BookID, GenreID),
    FOREIGN KEY (BookID) REFERENCES Books(BookID),
    FOREIGN KEY (GenreID) REFERENCES Genres(GenreID)
);

-- 6. Отзывы
CREATE TABLE Reviews (
    ReviewID INT PRIMARY KEY IDENTITY(1,1),
    UserID INT NOT NULL,
    BookID INT NOT NULL,
    ReviewText NVARCHAR(MAX) NOT NULL,
    Rating INT NOT NULL,
    CreatedAt DATETIME NOT NULL DEFAULT GETDATE(),
    FOREIGN KEY (UserID) REFERENCES Users(UserID),
    FOREIGN KEY (BookID) REFERENCES Books(BookID),
    CONSTRAINT CHK_Rating CHECK (Rating BETWEEN 1 AND 10)
);

-- 7. Списки чтения
CREATE TABLE ReadingLists (
    ReadingListID INT PRIMARY KEY IDENTITY(1,1),
    UserID INT NOT NULL,
    BookID INT NOT NULL,
    Section NVARCHAR(50) NOT NULL, -- Заброшено, В планах, Читаю, Прочитано
    AddedAt DATETIME NOT NULL DEFAULT GETDATE(),
    FOREIGN KEY (UserID) REFERENCES Users(UserID),
    FOREIGN KEY (BookID) REFERENCES Books(BookID),
    CONSTRAINT UQ_UserBook_ReadingList UNIQUE (UserID, BookID),
    CONSTRAINT CHK_Section CHECK (Section IN (N'Заброшено', N'В планах', N'Читаю', N'Прочитано'))
);

-- 8. Жалобы
CREATE TABLE Complaints (
    ComplaintID INT PRIMARY KEY IDENTITY(1,1),
    UserID INT NOT NULL, -- кто жалуется
    BookID INT NULL,     -- жалоба на книгу (если не NULL)
    ReviewID INT NULL,   -- жалоба на отзыв (если не NULL)
    Reason NVARCHAR(MAX) NOT NULL,
    CreatedAt DATETIME NOT NULL DEFAULT GETDATE(),
    FOREIGN KEY (UserID) REFERENCES Users(UserID),
    FOREIGN KEY (BookID) REFERENCES Books(BookID),
    FOREIGN KEY (ReviewID) REFERENCES Reviews(ReviewID),
    CONSTRAINT CHK_ComplaintTarget CHECK (
        (BookID IS NOT NULL AND ReviewID IS NULL) OR
        (BookID IS NULL AND ReviewID IS NOT NULL)
    )
);

-- 9. Заявки на роль Автор
CREATE TABLE AuthorRoleRequests (
    RequestID INT PRIMARY KEY IDENTITY(1,1),
    UserID INT NOT NULL,
    RequestDate DATETIME NOT NULL DEFAULT GETDATE(),
    IsProcessed BIT NOT NULL DEFAULT 0, -- обработана ли заявка
    IsApproved BIT NULL,                -- NULL - не обработана, 1 - одобрена, 0 - отклонена
    FOREIGN KEY (UserID) REFERENCES Users(UserID)
);

-- 10. Заявки на разморозку (аккаунта или книги)
CREATE TABLE UnfreezeRequests (
    UnfreezeRequestID INT PRIMARY KEY IDENTITY(1,1),
    UserID INT NOT NULL,          -- кто подаёт заявку
    BookID INT NULL,              -- если разморозка книги (иначе NULL)
    Reason NVARCHAR(MAX) NOT NULL,

RequestDate DATETIME NOT NULL DEFAULT GETDATE(),
    IsProcessed BIT NOT NULL DEFAULT 0,
    IsApproved BIT NULL,
    FOREIGN KEY (UserID) REFERENCES Users(UserID),
    FOREIGN KEY (BookID) REFERENCES Books(BookID),
    CONSTRAINT CHK_UnfreezeTarget CHECK (
        (BookID IS NOT NULL) OR (BookID IS NULL) -- NULL = разморозка аккаунта
    )
);

-- Индексы (по требованию — только автоматические, но для производительности на внешних ключах они создаются автоматически)
-- Тем не менее, можно добавить явно, если нужно, но в задании сказано "кроме автоматических для первичных и внешних ключей" — значит, не требуется.



USE BookPlatform;
GO

UPDATE Books
SET CoverImagePath = 'Images/Covers/mansion.jpg'
WHERE Title = N'Тайна старого особняка';

UPDATE Books
SET CoverImagePath = 'Images/Covers/stars.jpg'
WHERE Title = N'Звездные скитальцы';

UPDATE Books
SET CoverImagePath = 'Images/Covers/letter.jpg'
WHERE Title = N'Забытое письмо';


<Window x:Class="ExamGrader.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Оценка за экзамен" Height="400" Width="400"
        WindowStartupLocation="CenterScreen">
    <Grid Margin="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto"/>
            <ColumnDefinition Width="*"/>
        </Grid.ColumnDefinitions>

        <!-- Выбор уровня: Используем ComboBox для выбора -->
        <Label Grid.Row="0" Grid.Column="0" Content="Уровень экзамена:" Margin="5"/>
        <ComboBox x:Name="comboBoxLevel" Grid.Row="0" Grid.Column="1" Margin="5"
                  SelectionChanged="ComboBoxLevel_SelectionChanged">
            <ComboBoxItem Content="Базовый (БУ)" Tag="1"/>
            <ComboBoxItem Content="Профильный (ПУ)" Tag="2"/>
            <ComboBoxItem Content="Профильный+ (ПУ+)" Tag="3"/>
        </ComboBox>

        <!-- Блок ввода баллов: Модули 1-3 всегда видимы -->
        <Label Grid.Row="1" Grid.Column="0" Content="Модуль 1:" Margin="5"/>
        <TextBox x:Name="textBoxModule1" Grid.Row="1" Grid.Column="1" Margin="5"/>

        <Label Grid.Row="2" Grid.Column="0" Content="Модуль 2:" Margin="5"/>
        <TextBox x:Name="textBoxModule2" Grid.Row="2" Grid.Column="1" Margin="5"/>

        <Label Grid.Row="3" Grid.Column="0" Content="Модуль 3:" Margin="5"/>
        <TextBox x:Name="textBoxModule3" Grid.Row="3" Grid.Column="1" Margin="5"/>

        <!-- Модули 4 и 5 управляются динамически -->
        <Label Grid.Row="4" Grid.Column="0" Content="Модуль 4:" Margin="5"
               x:Name="labelModule4" Visibility="Collapsed"/>
        <TextBox x:Name="textBoxModule4" Grid.Row="4" Grid.Column="1" Margin="5"
                 Visibility="Collapsed"/>

        <Label Grid.Row="5" Grid.Column="0" Content="Модуль 5:" Margin="5"
               x:Name="labelModule5" Visibility="Collapsed"/>
        <TextBox x:Name="textBoxModule5" Grid.Row="5" Grid.Column="1" Margin="5"
                 Visibility="Collapsed"/>

        <!-- Кнопка расчета -->
        <Button x:Name="buttonCalculate" Content="Рассчитать" Grid.Row="6" Grid.Column="0" 
                Grid.ColumnSpan="2" Margin="5" Height="30" Click="ButtonCalculate_Click"/>

        <!-- Вывод результата -->
        <Label Grid.Row="7" Grid.Column="0" Content="Сумма баллов:" Margin="5"/>
        <TextBox x:Name="textBoxSum" Grid.Row="7" Grid.Column="1" Margin="5" IsReadOnly="True"/>

        <Label Grid.Row="8" Grid.Column="0" Content="Оценка:" Margin="5"/>
        <TextBox x:Name="textBoxGrade" Grid.Row="8" Grid.Column="1" Margin="5" IsReadOnly="True"
                 FontWeight="Bold" FontSize="14"/>
    </Grid>
</Window>

using System;
using System.Collections.Generic;
using System.Windows;
using System.Windows.Controls;

namespace ExamGrader
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
            // Установка начального уровня для согласованности UI
            comboBoxLevel.SelectedIndex = 0;
        }

        // Обработчик изменения уровня экзамена
        private void ComboBoxLevel_SelectionChanged(object sender, SelectionChangedEventArgs e)
        {
            // Проверка, что все элементы загружены
            if (textBoxModule4 == null) return;

            var selectedItem = (ComboBoxItem)comboBoxLevel.SelectedItem;
            string level = selectedItem.Tag.ToString();

            switch (level)
            {
                case "1": // Базовый
                    labelModule4.Visibility = Visibility.Collapsed;
                    textBoxModule4.Visibility = Visibility.Collapsed;
                    labelModule5.Visibility = Visibility.Collapsed;
                    textBoxModule5.Visibility = Visibility.Collapsed;
                    textBoxModule4.Text = "";
                    textBoxModule5.Text = "";
                    break;
                case "2": // Профильный
                    labelModule4.Visibility = Visibility.Visible;
                    textBoxModule4.Visibility = Visibility.Visible;
                    labelModule5.Visibility = Visibility.Collapsed;
                    textBoxModule5.Visibility = Visibility.Collapsed;
                    textBoxModule5.Text = "";
                    break;
                case "3": // Профильный+
                    labelModule4.Visibility = Visibility.Visible;
                    textBoxModule4.Visibility = Visibility.Visible;
                    labelModule5.Visibility = Visibility.Visible;
                    textBoxModule5.Visibility = Visibility.Visible;
                    break;
            }
        }

        // Главная логика обработки нажатия кнопки
        private void ButtonCalculate_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Сбор данных
                var selectedItem = (ComboBoxItem)comboBoxLevel.SelectedItem;
                if (selectedItem == null)
                {
                    MessageBox.Show("Выберите уровень экзамена.");
                    return;
                }
                string level = selectedItem.Tag.ToString();

                List<int> scores = new List<int>();

                // Парсинг модулей 1-3 (обязательные)
                if (!TryParseScore(textBoxModule1.Text, "Модуль 1", out int m1)) return;
                if (!TryParseScore(textBoxModule2.Text, "Модуль 2", out int m2)) return;
                if (!TryParseScore(textBoxModule3.Text, "Модуль 3", out int m3)) return;
                scores.Add(m1);
                scores.Add(m2);
                scores.Add(m3);

                // Парсинг модуля 4, если выбран ПУ или ПУ+
                if (level == "2" || level == "3")
                {
                    if (!TryParseScore(textBoxModule4.Text, "Модуль 4", out int m4)) return;
                    scores.Add(m4);
                }

                // Парсинг модуля 5, если выбран ПУ+
                if (level == "3")
                {
                    if (!TryParseScore(textBoxModule5.Text, "Модуль 5", out int m5)) return;
                    scores.Add(m5);
                }

                // Вызов класса логики
                string grade = ExamCalculator.CalculateGrade(scores, level);
                int sum = ExamCalculator.CalculateSum(scores);

                // Отображение результата
                textBoxSum.Text = sum.ToString();
                textBoxGrade.Text = grade;
            }
            catch (Exception ex) // Общий обработчик на случай необработанных ошибок
            {
                MessageBox.Show($"Произошла непредвиденная ошибка: {ex.Message}", "Ошибка", MessageBoxButton.OK, MessageBoxImage.Error);
            }
        }

        // Вспомогательный метод для парсинга и валидации ввода
        private bool TryParseScore(string input, string fieldName, out int score)
        {
            score = 0;
            if (string.IsNullOrWhiteSpace(input))
            {
                MessageBox.Show($"Поле '{fieldName}' не может быть пустым.", "Ошибка ввода", MessageBoxButton.OK, MessageBoxImage.Warning);
                return false;
            }

            if (!int.TryParse(input, out score))
            {
                MessageBox.Show($"Некорректный ввод в поле '{fieldName}'. Введите целое число.", "Ошибка ввода", MessageBoxButton.OK, MessageBoxImage.Warning);
                return false;
            }

            return true;
        }
    }
}



using System;
using System.Collections.Generic;
using System.Linq;

namespace ExamGrader
{
    public static class ExamCalculator
    {
        /// <summary>
        /// Рассчитывает сумму баллов.
        /// </summary>
        public static int CalculateSum(List<int> scores)
        {
            if (scores == null || scores.Count == 0)
                throw new ArgumentException("Список баллов не может быть пустым.");

            // Проверка на отрицательные баллы (доп. логика)
            if (scores.Any(s => s < 0))
                throw new ArgumentException("Баллы не могут быть отрицательными.");

            return scores.Sum();
        }

        /// <summary>
        /// Рассчитывает оценку по 5-балльной шкале в зависимости от уровня и процента выполнения.
        /// </summary>
        /// <param name="scores">Список баллов за модули.</param>
        /// <param name="level">Уровень: "1" - БУ, "2" - ПУ, "3" - ПУ+</param>
        public static string CalculateGrade(List<int> scores, string level)
        {
            if (scores == null || scores.Count == 0)
                throw new ArgumentException("Список баллов не может быть пустым.");

            int sum = scores.Sum();
            int maxScore = 0;

            // Определяем максимальный балл в зависимости от уровня
            switch (level)
            {
                case "1": maxScore = 3 * 10; break; // БУ: 3 модуля по 10 баллов
                case "2": maxScore = 4 * 10; break; // ПУ: 4 модуля по 10 баллов
                case "3": maxScore = 5 * 10; break; // ПУ+: 5 модулей по 10 баллов
                default:
                    throw new ArgumentException("Некорректный уровень экзамена. Используйте '1', '2' или '3'.");
            }

            // Проверка на соответствие количества модулей выбранному уровню
            if ((level == "1" && scores.Count != 3) ||
                (level == "2" && scores.Count != 4) ||
                (level == "3" && scores.Count != 5))
            {
                throw new ArgumentException($"Для уровня {level} ожидается другое количество модулей.");
            }

            // Вычисление процента выполнения
            double percentage = (double)sum / maxScore * 100;

            // Определение оценки по классической шкале
            if (percentage >= 90) return "5 (отлично)";
            if (percentage >= 70) return "4 (хорошо)";
            if (percentage >= 50) return "3 (удовлетворительно)";
            return "2 (неудовлетворительно)";
        }
    }
}



using Microsoft.VisualStudio.TestTools.UnitTesting;
using ExamGrader;
using System;
using System.Collections.Generic;

namespace ExamGrader.Tests
{
    [TestClass]
    public class ExamCalculatorTests
    {
        // Тесты на CalculateSum
        [TestMethod]
        public void CalculateSum_CorrectData_ReturnsSum()
        {
            // Arrange
            var scores = new List<int> { 5, 5, 5 };
            // Act
            int result = ExamCalculator.CalculateSum(scores);
            // Assert
            Assert.AreEqual(15, result);
        }

        [TestMethod]
        public void CalculateSum_WithBoundaryValues_ReturnsMaxSum()
        {
            var scores = new List<int> { 10, 10, 10 };
            int result = ExamCalculator.CalculateSum(scores);
            Assert.AreEqual(30, result);
        }

        [TestMethod]
        public void CalculateSum_EmptyList_ThrowsException()
        {
            var scores = new List<int>();
            Assert.ThrowsException<ArgumentException>(() => ExamCalculator.CalculateSum(scores));
        }

        [TestMethod]
        public void CalculateSum_NegativeScores_ThrowsException()
        {
            var scores = new List<int> { 5, -1, 5 };
            Assert.ThrowsException<ArgumentException>(() => ExamCalculator.CalculateSum(scores));
        }

        // Тесты на CalculateGrade для уровня БУ ("1")
        [TestMethod]
        public void CalculateGrade_BU_Grade5()
        {
            var scores = new List<int> { 9, 10, 9 }; // 28/30 = 93%
            string grade = ExamCalculator.CalculateGrade(scores, "1");
            Assert.AreEqual("5 (отлично)", grade);
        }

        [TestMethod]
        public void CalculateGrade_BU_Grade4()
        {
            var scores = new List<int> { 7, 8, 7 }; // 22/30 = 73%
            string grade = ExamCalculator.CalculateGrade(scores, "1");
            Assert.AreEqual("4 (хорошо)", grade);
        }

        [TestMethod]
        public void CalculateGrade_BU_Grade3()
        {
            var scores = new List<int> { 5, 5, 5 }; // 15/30 = 50%
            string grade = ExamCalculator.CalculateGrade(scores, "1");
            Assert.AreEqual("3 (удовлетворительно)", grade);
        }

        [TestMethod]
        public void CalculateGrade_BU_Grade2()
        {
            var scores = new List<int> { 1, 2, 3 }; // 6/30 = 20%
            string grade = ExamCalculator.CalculateGrade(scores, "1");
            Assert.AreEqual("2 (неудовлетворительно)", grade);
        }

        // Тесты на CalculateGrade для ПУ+ ("3")
        [TestMethod]
        public void CalculateGrade_PUPlus_Grade5()
        {
            var scores = new List<int> { 10, 10, 10, 10, 10 }; // 50/50 = 100%
            string grade = ExamCalculator.CalculateGrade(scores, "3");
            Assert.AreEqual("5 (отлично)", grade);
        }

        // Тесты на исключения
        [TestMethod]
        public void CalculateGrade_InvalidLevel_ThrowsException()
        {
            var scores = new List<int> { 1, 2, 3 };
            Assert.ThrowsException<ArgumentException>(() => ExamCalculator.CalculateGrade(scores, "99"));
        }

        [TestMethod]
        public void CalculateGrade_WrongModuleCount_ThrowsException()
        {
            // Для уровня БУ ("1") ожидается 3 модуля, но передаем 4
            var scores = new List<int> { 1, 2, 3, 4 };
            Assert.ThrowsException<ArgumentException>(() => ExamCalculator.CalculateGrade(scores, "1"));
        }

        // Проверка с Assert.IsTrue и Assert.AreNotEqual
        [TestMethod]
        public void CalculateSum_IsTrue_GreaterThanZero()
        {
            var scores = new List<int> { 1, 1, 1 };
            int sum = ExamCalculator.CalculateSum(scores);
            Assert.IsTrue(sum > 0);
        }

        [TestMethod]
        public void CalculateGrade_AreNotEqual_Grade2And3()
        {
            var scoresLow = new List<int> { 0, 0, 0 };
            var scoresMid = new List<int> { 5, 5, 5 };
            string gradeLow = ExamCalculator.CalculateGrade(scoresLow, "1");
            string gradeMid = ExamCalculator.CalculateGrade(scoresMid, "1");
            Assert.AreNotEqual(gradeLow, gradeMid);
        }
    }
}










# Вариант 1 — WPF (.NET Framework) + MSTest

## Структура проекта

Создай Solution из 2 проектов:

1. `ExamCalculatorWPF` — WPF App (.NET Framework)
2. `ExamCalculatorTests` — MSTest Test Project (.NET Framework)

---

# 1. Логика приложения

Создай папку `Logic`.

Файл `ExamCalculator.cs`

```csharp
using System;

namespace ExamCalculatorWPF.Logic
{
    public static class ExamCalculator
    {
        public static int CalculateTotal(
            string level,
            double m1,
            double m2,
            double m3,
            double m4,
            double m5)
        {
            ValidateModule(m1, 10, "Модуль 1");
            ValidateModule(m2, 15, "Модуль 2");
            ValidateModule(m3, 25, "Модуль 3");
            ValidateModule(m4, 25, "Модуль 4");
            ValidateModule(m5, 25, "Модуль 5");

            switch (level)
            {
                case "БУ":
                    return (int)(m1 + m2 + m3);

                case "ПУ":
                    return (int)(m1 + m2 + m3 + m4);

                case "ПУ+":
                    return (int)(m1 + m2 + m3 + m4 + m5);

                default:
                    throw new ArgumentException("Неизвестный уровень экзамена");
            }
        }

        public static int GetMaxScore(string level)
        {
            switch (level)
            {
                case "БУ":
                    return 50;

                case "ПУ":
                    return 75;

                case "ПУ+":
                    return 100;

                default:
                    throw new ArgumentException("Неизвестный уровень экзамена");
            }
        }

        public static double CalculatePercent(int total, int max)
        {
            if (max <= 0)
            {
                throw new ArgumentException("Максимальный балл должен быть больше 0");
            }

            return (double)total / max * 100;
        }

        public static int GetGrade(double percent)
        {
            if (percent < 0 || percent > 100)
            {
                throw new ArgumentException("Процент должен быть от 0 до 100");
            }

            if (percent >= 80)
                return 5;

            if (percent >= 60)
                return 4;

            if (percent >= 40)
                return 3;

            return 2;
        }

        private static void ValidateModule(double value, int max, string moduleName)
        {
            if (value < 0 || value > max)
            {
                throw new ArgumentException(
                    $"{moduleName}: значение должно быть от 0 до {max}");
            }
        }
    }
}
```

---

# 2. Интерфейс WPF

## MainWindow.xaml

```xml
<Window x:Class="ExamCalculatorWPF.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Калькулятор экзамена"
        Height="550"
        Width="400">

    <Grid Margin="15">

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <!-- Заголовок -->
        <TextBlock Text="Расчет оценки экзамена"
                   FontSize="22"
                   FontWeight="Bold"
                   Margin="0 0 0 20"/>

        <!-- Выбор уровня -->
        <StackPanel Grid.Row="1" Margin="0 5">
            <TextBlock Text="Уровень экзамена:"/>

            <ComboBox x:Name="LevelComboBox"
                      SelectedIndex="0"
                      Height="30">
                <ComboBoxItem Content="БУ"/>
                <ComboBoxItem Content="ПУ"/>
                <ComboBoxItem Content="ПУ+"/>
            </ComboBox>
        </StackPanel>

        <!-- Модуль 1 -->
        <StackPanel Grid.Row="2" Margin="0 5">
            <TextBlock Text="Модуль 1 (0-10):"/>
            <TextBox x:Name="Module1TextBox" Height="30"/>
        </StackPanel>

        <!-- Модуль 2 -->
        <StackPanel Grid.Row="3" Margin="0 5">
            <TextBlock Text="Модуль 2 (0-15):"/>
            <TextBox x:Name="Module2TextBox" Height="30"/>
        </StackPanel>

        <!-- Модуль 3 -->
        <StackPanel Grid.Row="4" Margin="0 5">
            <TextBlock Text="Модуль 3 (0-25):"/>
            <TextBox x:Name="Module3TextBox" Height="30"/>
        </StackPanel>

        <!-- Модуль 4 -->
        <StackPanel Grid.Row="5" Margin="0 5">
            <TextBlock Text="Модуль 4 (0-25):"/>
            <TextBox x:Name="Module4TextBox" Height="30"/>
        </StackPanel>

        <!-- Модуль 5 -->
        <StackPanel Grid.Row="6" Margin="0 5">
            <TextBlock Text="Модуль 5 (0-25):"/>
            <TextBox x:Name="Module5TextBox" Height="30"/>
        </StackPanel>

        <!-- Кнопка -->
        <Button Grid.Row="7"
                Content="Рассчитать"
                Height="40"
                Margin="0 15 0 15"
                Click="CalculateButton_Click"/>

        <!-- Результат -->
        <TextBlock x:Name="ResultTextBlock"
                   Grid.Row="8"
                   FontSize="18"
                   FontWeight="Bold"
                   TextWrapping="Wrap"/>

    </Grid>
</Window>
```

---

# 3. Code Behind

## MainWindow.xaml.cs

```csharp
using System;
using System.Windows;
using System.Windows.Controls;
using ExamCalculatorWPF.Logic;

namespace ExamCalculatorWPF
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        // Обработчик кнопки расчета
        private void CalculateButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                if (!double.TryParse(Module1TextBox.Text, out double m1) ||
                    !double.TryParse(Module2TextBox.Text, out double m2) ||
                    !double.TryParse(Module3TextBox.Text, out double m3) ||
                    !double.TryParse(Module4TextBox.Text, out double m4) ||
                    !double.TryParse(Module5TextBox.Text, out double m5))
                {
                    MessageBox.Show(
                        "Введите корректные числа",
                        "Ошибка",
                        MessageBoxButton.OK,
                        MessageBoxImage.Error);

                    return;
                }

                string level =
                    ((ComboBoxItem)LevelComboBox.SelectedItem)
                    .Content
                    .ToString();

                int total = ExamCalculator.CalculateTotal(
                    level,
                    m1,
                    m2,
                    m3,
                    m4,
                    m5);

                int max = ExamCalculator.GetMaxScore(level);

                double percent = ExamCalculator.CalculatePercent(total, max);

                int grade = ExamCalculator.GetGrade(percent);

                ResultTextBlock.Text =
                    $"Сумма баллов: {total}\n" +
                    $"Процент: {percent:F2}%\n" +
                    $"Оценка: {grade}";
            }
            catch (ArgumentException ex)
            {
                MessageBox.Show(
                    ex.Message,
                    "Ошибка",
                    MessageBoxButton.OK,
                    MessageBoxImage.Warning);
            }
            catch (Exception ex)
            {
                MessageBox.Show(
                    ex.Message,
                    "Неизвестная ошибка",
                    MessageBoxButton.OK,
                    MessageBoxImage.Error);
            }
        }
    }
}
```

---

# 4. MSTest — потоковые тесты

Добавь ссылку на проект `ExamCalculatorWPF`.

## ExamCalculatorTests.cs

```csharp
using System;
using ExamCalculatorWPF.Logic;
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace ExamCalculatorTests
{
    [TestClass]
    public class ExamCalculatorTests
    {
        // Потоковые тесты суммы баллов
        [DataTestMethod]
        [DataRow("БУ", 10, 15, 25, 0, 0, 50)]
        [DataRow("ПУ", 10, 15, 25, 25, 0, 75)]
        [DataRow("ПУ+", 10, 15, 25, 25, 25, 100)]
        [DataRow("БУ", 5, 10, 15, 0, 0, 30)]
        public void CalculateTotal_ValidData_ReturnsCorrectResult(
            string level,
            double m1,
            double m2,
            double m3,
            double m4,
            double m5,
            int expected)
        {
            int result = ExamCalculator.CalculateTotal(
                level,
                m1,
                m2,
                m3,
                m4,
                m5);

            Assert.AreEqual(expected, result);
        }

        // Проверка процентов
        [DataTestMethod]
        [DataRow(100, 100, 100)]
        [DataRow(50, 100, 50)]
        [DataRow(75, 100, 75)]
        [DataRow(40, 50, 80)]
        public void CalculatePercent_ValidData_ReturnsCorrectPercent(
            int total,
            int max,
            double expected)
        {
            double result = ExamCalculator.CalculatePercent(total, max);

            Assert.AreEqual(expected, result);
        }

        // Проверка оценок
        [DataTestMethod]
        [DataRow(100, 5)]
        [DataRow(80, 5)]
        [DataRow(79, 4)]
        [DataRow(60, 4)]
        [DataRow(59, 3)]
        [DataRow(40, 3)]
        [DataRow(39, 2)]
        [DataRow(0, 2)]
        public void GetGrade_ValidPercent_ReturnsCorrectGrade(
            double percent,
            int expected)
        {
            int result = ExamCalculator.GetGrade(percent);

            Assert.AreEqual(expected, result);
        }

        // Проверка исключений
        [TestMethod]
        public void CalculateTotal_InvalidModule_ThrowsException()
        {
            Assert.ThrowsException<ArgumentException>(() =>
            {
                ExamCalculator.CalculateTotal(
                    "БУ",
                    100,
                    15,
                    25,
                    0,
                    0);
            });
        }

        [TestMethod]
        public void GetGrade_InvalidPercent_ThrowsException()
        {
            Assert.ThrowsException<ArgumentException>(() =>
            {
                ExamCalculator.GetGrade(150);
            });
        }

        [TestMethod]
        public void CalculatePercent_InvalidMax_ThrowsException()
        {
            Assert.ThrowsException<ArgumentException>(() =>
            {
                ExamCalculator.CalculatePercent(50, 0);
            });
        }

        // Граничные значения
        [DataTestMethod]
        [DataRow(79.99, 4)]
        [DataRow(80, 5)]
        [DataRow(59.99, 3)]
        [DataRow(60, 4)]
        [DataRow(39.99, 2)]
        [DataRow(40, 3)]
        public void GetGrade_BorderValues_ReturnsCorrectGrade(
            double percent,
            int expected)
        {
            int result = ExamCalculator.GetGrade(percent);

            Assert.AreEqual(expected, result);
        }
    }
}
```

---

# 5. Что показать преподавателю

## README.md

```md
# Текущий контроль

ФИО: Иванов Иван Иванович
Вариант: 1

## Описание
WPF-приложение для расчета результата демонстрационного экзамена.

## Возможности
- выбор уровня экзамена;
- ввод баллов за модули;
- расчет процентов;
- определение оценки;
- обработка ошибок;
- автоматизированные тесты MSTest.

## Скриншоты
Добавить скриншот Обозревателя тестов.
```

---

# 6. Что обязательно должно быть

## Для соответствия заданию:

### WPF

✅ XAML интерфейс

✅ Code Behind

✅ MessageBox

✅ try-catch

✅ double.TryParse

---

### Логика

✅ Отдельный класс

✅ Методы принимают простые типы

✅ ArgumentException

---

### MSTest

✅ Отдельный тестовый проект

✅ Assert.AreEqual

✅ Assert.ThrowsException

✅ Граничные значения

✅ Некорректные данные

✅ Потоковые тесты через:

```csharp
[DataTestMethod]
[DataRow(...)]
```

---

# 7. Какие тесты запустить

В Test Explorer должны пройти:

* CalculateTotal_ValidData_ReturnsCorrectResult
* CalculatePercent_ValidData_ReturnsCorrectPercent
* GetGrade_ValidPercent_ReturnsCorrectGrade
* CalculateTotal_InvalidModule_ThrowsException
* GetGrade_InvalidPercent_ThrowsException
* CalculatePercent_InvalidMax_ThrowsException
* GetGrade_BorderValues_ReturnsCorrectGrade

Сделай скриншот успешных тестов.
