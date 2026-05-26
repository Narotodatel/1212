CREATE DATABASE ReadWriteDB;
GO

USE ReadWriteDB;
GO

/* =========================
   ТАБЛИЦА РОЛЕЙ
========================= */
CREATE TABLE Roles
(
    RoleID INT PRIMARY KEY IDENTITY(1,1),
    RoleName NVARCHAR(50) NOT NULL UNIQUE
);
GO

/* =========================
   ТАБЛИЦА ПОЛЬЗОВАТЕЛЕЙ
========================= */
CREATE TABLE Users
(
    UserID INT PRIMARY KEY IDENTITY(1,1),
    Login NVARCHAR(50) NOT NULL UNIQUE,
    PasswordHash NVARCHAR(255) NOT NULL,
    Email NVARCHAR(100) NOT NULL UNIQUE,
    DisplayName NVARCHAR(100) NOT NULL,
    RoleID INT NOT NULL,
    IsFrozen BIT NOT NULL DEFAULT 0,
    CreatedAt DATETIME NOT NULL DEFAULT GETDATE(),

    CONSTRAINT FK_Users_Roles
        FOREIGN KEY (RoleID)
        REFERENCES Roles(RoleID)
);
GO

/* =========================
   ТАБЛИЦА КНИГ
========================= */
CREATE TABLE Books
(
    BookID INT PRIMARY KEY IDENTITY(1,1),
    Title NVARCHAR(200) NOT NULL,
    Description NVARCHAR(MAX) NULL,
    CoverPath NVARCHAR(255) NULL,
    Content NVARCHAR(MAX) NOT NULL,
    AuthorID INT NOT NULL,
    IsFrozen BIT NOT NULL DEFAULT 0,
    CreatedAt DATETIME NOT NULL DEFAULT GETDATE(),

    CONSTRAINT FK_Books_Users
        FOREIGN KEY (AuthorID)
        REFERENCES Users(UserID)
);
GO

/* =========================
   ТАБЛИЦА ЖАНРОВ
========================= */
CREATE TABLE Genres
(
    GenreID INT PRIMARY KEY IDENTITY(1,1),
    GenreName NVARCHAR(100) NOT NULL UNIQUE,
    Description NVARCHAR(MAX) NULL
);
GO

/* =========================
   СВЯЗЬ КНИГ И ЖАНРОВ
========================= */
CREATE TABLE BookGenres
(
    BookGenreID INT PRIMARY KEY IDENTITY(1,1),
    BookID INT NOT NULL,
    GenreID INT NOT NULL,

    CONSTRAINT FK_BookGenres_Books
        FOREIGN KEY (BookID)
        REFERENCES Books(BookID),

    CONSTRAINT FK_BookGenres_Genres
        FOREIGN KEY (GenreID)
        REFERENCES Genres(GenreID),

    CONSTRAINT UQ_BookGenres
        UNIQUE (BookID, GenreID)
);
GO

/* =========================
   ОТЗЫВЫ
========================= */
CREATE TABLE Reviews
(
    ReviewID INT PRIMARY KEY IDENTITY(1,1),
    UserID INT NOT NULL,
    BookID INT NOT NULL,
    ReviewText NVARCHAR(MAX) NOT NULL,
    Rating INT NOT NULL,
    CreatedAt DATETIME NOT NULL DEFAULT GETDATE(),

    CONSTRAINT FK_Reviews_Users
        FOREIGN KEY (UserID)
        REFERENCES Users(UserID),

    CONSTRAINT FK_Reviews_Books
        FOREIGN KEY (BookID)
        REFERENCES Books(BookID),

    CONSTRAINT CHK_Reviews_Rating
        CHECK (Rating BETWEEN 1 AND 10)
);
GO

/* =========================
   СПИСКИ ЧТЕНИЯ
========================= */
CREATE TABLE ReadingLists
(
    ReadingListID INT PRIMARY KEY IDENTITY(1,1),
    UserID INT NOT NULL,
    BookID INT NOT NULL,
    StatusName NVARCHAR(50) NOT NULL,
    AddedAt DATETIME NOT NULL DEFAULT GETDATE(),

    CONSTRAINT FK_ReadingLists_Users
        FOREIGN KEY (UserID)
        REFERENCES Users(UserID),

    CONSTRAINT FK_ReadingLists_Books
        FOREIGN KEY (BookID)
        REFERENCES Books(BookID),

    CONSTRAINT UQ_ReadingLists_UserBook
        UNIQUE (UserID, BookID),

    CONSTRAINT CHK_ReadingLists_Status
        CHECK (StatusName IN
        (
            N'Заброшено',
            N'В планах',
            N'Читаю',
            N'Прочитано'
        ))
);
GO

/* =========================
   ЖАЛОБЫ
========================= */
CREATE TABLE Complaints
(
    ComplaintID INT PRIMARY KEY IDENTITY(1,1),
    UserID INT NOT NULL,
    BookID INT NULL,
    ReviewID INT NULL,
    Reason NVARCHAR(MAX) NOT NULL,
    CreatedAt DATETIME NOT NULL DEFAULT GETDATE(),

    CONSTRAINT FK_Complaints_Users
        FOREIGN KEY (UserID)
        REFERENCES Users(UserID),

    CONSTRAINT FK_Complaints_Books
        FOREIGN KEY (BookID)
        REFERENCES Books(BookID),

    CONSTRAINT FK_Complaints_Reviews
        FOREIGN KEY (ReviewID)
        REFERENCES Reviews(ReviewID),

    CONSTRAINT CHK_Complaints_Target
        CHECK
        (
            (BookID IS NOT NULL AND ReviewID IS NULL)
            OR
            (BookID IS NULL AND ReviewID IS NOT NULL)
        )
);
GO

/* =========================
   ЗАЯВКИ НА РОЛЬ АВТОРА
========================= */
CREATE TABLE RoleRequests
(
    RequestID INT PRIMARY KEY IDENTITY(1,1),
    UserID INT NOT NULL,
    RequestText NVARCHAR(MAX) NULL,
    StatusName NVARCHAR(50) NOT NULL DEFAULT N'На рассмотрении',
    CreatedAt DATETIME NOT NULL DEFAULT GETDATE(),

    CONSTRAINT FK_RoleRequests_Users
        FOREIGN KEY (UserID)
        REFERENCES Users(UserID)
);
GO

/* =========================
   ЗАЯВКИ НА РАЗМОРОЗКУ
========================= */
CREATE TABLE UnfreezeRequests
(
    UnfreezeRequestID INT PRIMARY KEY IDENTITY(1,1),
    UserID INT NULL,
    BookID INT NULL,
    RequestReason NVARCHAR(MAX) NOT NULL,
    StatusName NVARCHAR(50) NOT NULL DEFAULT N'На рассмотрении',
    CreatedAt DATETIME NOT NULL DEFAULT GETDATE(),

    CONSTRAINT FK_UnfreezeRequests_Users
        FOREIGN KEY (UserID)
        REFERENCES Users(UserID),

    CONSTRAINT FK_UnfreezeRequests_Books
        FOREIGN KEY (BookID)
        REFERENCES Books(BookID),

    CONSTRAINT CHK_UnfreezeRequests_Target
        CHECK
        (
            (UserID IS NOT NULL AND BookID IS NULL)
            OR
            (UserID IS NULL AND BookID IS NOT NULL)
        )
);
GO

/* =========================
   ТЕСТОВЫЕ ДАННЫЕ
========================= */

/* Роли */
INSERT INTO Roles (RoleName)
VALUES
(N'Читатель'),
(N'Автор'),
(N'Администратор');
GO

/* Пользователи */
INSERT INTO Users
(
    Login,
    PasswordHash,
    Email,
    DisplayName,
    RoleID,
    IsFrozen
)
VALUES
(N'ivanov', N'12345', N'ivan@mail.com', N'Иван Иванов', 2, 0),
(N'petrov', N'12345', N'petrov@mail.com', N'Петр Петров', 1, 0),
(N'admin', N'admin123', N'admin@mail.com', N'Администратор', 3, 0),
(N'sidorov', N'12345', N'sid@mail.com', N'Сидоров', 2, 0),
(N'anna', N'12345', N'anna@mail.com', N'Анна', 1, 0);
GO

/* Жанры */
INSERT INTO Genres (GenreName, Description)
VALUES
(N'Фэнтези', N'Магия и приключения'),
(N'Детектив', N'Расследования'),
(N'Фантастика', N'Будущее и технологии'),
(N'Роман', N'Любовные истории'),
(N'Ужасы', N'Страшные истории');
GO

/* Книги */
INSERT INTO Books
(
    Title,
    Description,
    CoverPath,
    Content,
    AuthorID,
    IsFrozen
)
VALUES
(
    N'Тень дракона',
    N'Фэнтезийная история',
    N'/covers/book1.jpg',
    N'Текст книги 1',
    1,
    0
),
(
    N'Космос будущего',
    N'Научная фантастика',
    N'/covers/book2.jpg',
    N'Текст книги 2',
    4,
    0
),
(
    N'Тайна старого дома',
    N'Детективный роман',
    N'/covers/book3.jpg',
    N'Текст книги 3',
    1,
    0
);
GO

/* Связь книг и жанров */
INSERT INTO BookGenres (BookID, GenreID)
VALUES
(1,1),
(2,3),
(3,2);
GO

/* Отзывы */
INSERT INTO Reviews
(
    UserID,
    BookID,
    ReviewText,
    Rating
)
VALUES
(2,1,N'Очень интересная книга!',9),
(5,1,N'Понравилось!',10),
(2,2,N'Неплохая фантастика',8),
(5,3,N'Хороший детектив',7);
GO

/* Списки чтения */
INSERT INTO ReadingLists
(
    UserID,
    BookID,
    StatusName
)
VALUES
(2,1,N'Читаю'),
(2,2,N'В планах'),
(5,1,N'Прочитано');
GO

/* Жалобы */
INSERT INTO Complaints
(
    UserID,
    BookID,
    ReviewID,
    Reason
)
VALUES
(2,1,NULL,N'Нарушение правил'),
(5,NULL,1,N'Оскорбительный отзыв');
GO

/* Заявки на роль */
INSERT INTO RoleRequests
(
    UserID,
    RequestText
)
VALUES
(2,N'Хочу стать автором'),
(5,N'Пишу книги');
GO

/* Заявки на разморозку */
INSERT INTO UnfreezeRequests
(
    UserID,
    BookID,
    RequestReason
)
VALUES
(1,NULL,N'Прошу разморозить аккаунт'),
(NULL,2,N'Прошу разморозить книгу');
GO
