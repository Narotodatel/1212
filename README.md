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
