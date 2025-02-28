IF OBJECT_ID('dbo.Anagrafica', 'U') IS NULL
BEGIN
    CREATE TABLE Anagrafica (
        idanagrafica INT PRIMARY KEY IDENTITY(1,1),
        Cognome VARCHAR(50) NOT NULL,
        Nome VARCHAR(50) NOT NULL,
        Indirizzo VARCHAR(100),
        Citta VARCHAR(50),
        CAP VARCHAR(10),
        Cod_Fisc CHAR(16) UNIQUE NOT NULL
    );
END;
GO

IF OBJECT_ID('dbo.TipoViolazione', 'U') IS NULL
BEGIN
    CREATE TABLE TipoViolazione (
        idviolazione INT PRIMARY KEY IDENTITY(1,1),
        descrizione VARCHAR(255) NOT NULL
    );
END;
GO

IF OBJECT_ID('dbo.Verbale', 'U') IS NULL
BEGIN
    CREATE TABLE Verbale (
        idverbale INT PRIMARY KEY IDENTITY(1,1),
        idanagrafica INT NOT NULL,
        idviolazione INT NOT NULL,
        DataViolazione DATE NOT NULL,
        IndirizzoViolazione VARCHAR(100),
        Nominativo_Agente VARCHAR(100),
        DataTrascrizioneVerbale DATE NOT NULL,
        Importo DECIMAL(10,2) NOT NULL,
        DecurtamentoPunti INT NOT NULL,
        CONSTRAINT FK_Verbale_Anagrafica FOREIGN KEY (idanagrafica) REFERENCES Anagrafica(idanagrafica),
        CONSTRAINT FK_Verbale_TipoViolazione FOREIGN KEY (idviolazione) REFERENCES TipoViolazione(idviolazione)
    );
END;
GO



INSERT INTO Anagrafica (Cognome, Nome, Indirizzo, Citta, CAP, Cod_Fisc) VALUES
('Lorenzo', 'Papa', 'Via Roma 1', 'Palermo', '90100', 'RSSMRA80A01H501Z'),
('Bianchi', 'Luca', 'Corso Italia 45', 'Milano', '20100', 'BNCLCU85B12F205Y'),
('Verdi', 'Anna', 'Via Garibaldi 12', 'Palermo', '90100', 'VRDANN75C45F205X');

INSERT INTO TipoViolazione (descrizione) VALUES
('Eccesso di velocità'),
('Passaggio con semaforo rosso'),
('Guida sotto stato di ebbrezza');

INSERT INTO Verbale (idanagrafica, idviolazione, DataViolazione, IndirizzoViolazione, Nominativo_Agente, DataTrascrizioneVerbale, Importo, DecurtamentoPunti) VALUES
(1, 1, '2024-02-10', 'Via Libertà 10', 'Agente Verdi', '2024-02-11', 150.00, 3),
(2, 2, '2024-02-12', 'Piazza Duomo', 'Agente Neri', '2024-02-13', 250.00, 5),
(3, 1, '2009-03-15', 'Via Mazzini 5', 'Agente Rossi', '2009-03-16', 120.00, 2),
(1, 3, '2009-06-20', 'Via Roma 1', 'Agente Bianchi', '2009-06-21', 450.00, 6);

-- 1
SELECT COUNT(*) AS NumeroVerbali FROM Verbale;

--  2
SELECT idanagrafica, COUNT(*) AS NumeroVerbali FROM Verbale GROUP BY idanagrafica;

-- 3
SELECT idviolazione, COUNT(*) AS NumeroVerbali FROM Verbale GROUP BY idviolazione;

-- 4
SELECT idanagrafica, SUM(DecurtamentoPunti) AS TotalePuntiDecurtati FROM Verbale GROUP BY idanagrafica;

-- 5
SELECT A.Cognome, A.Nome, V.DataViolazione, V.IndirizzoViolazione, V.Importo, V.DecurtamentoPunti 
FROM Anagrafica A 
JOIN Verbale V ON A.idanagrafica = V.idanagrafica 
WHERE A.Citta = 'Palermo';

-- 6
SELECT A.Cognome, A.Nome, A.Indirizzo, V.DataViolazione, V.Importo, V.DecurtamentoPunti 
FROM Anagrafica A 
JOIN Verbale V ON A.idanagrafica = V.idanagrafica 
WHERE V.DataViolazione BETWEEN '2009-02-01' AND '2009-07-31';

--  7
SELECT idanagrafica, SUM(Importo) AS TotaleImporti FROM Verbale GROUP BY idanagrafica;

--8
SELECT * FROM Anagrafica WHERE Citta = 'Palermo';

-- 9
SELECT DataViolazione, Importo, DecurtamentoPunti FROM Verbale WHERE DataViolazione = '2024-02-10';

-- 10
SELECT Nominativo_Agente, COUNT(*) AS NumeroVerbali FROM Verbale GROUP BY Nominativo_Agente;

--11
SELECT A.Cognome, A.Nome, A.Indirizzo, V.DataViolazione, V.Importo, V.DecurtamentoPunti 
FROM Anagrafica A 
JOIN Verbale V ON A.idanagrafica = V.idanagrafica 
WHERE V.DecurtamentoPunti > 5;

-- 12
SELECT A.Cognome, A.Nome, A.Indirizzo, V.DataViolazione, V.Importo, V.DecurtamentoPunti 
FROM Anagrafica A 
JOIN Verbale V ON A.idanagrafica = V.idanagrafica 
WHERE V.Importo > 400;
