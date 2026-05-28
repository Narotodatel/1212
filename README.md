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
