----Moving tempdb Files.---



Alter database tempdb
Modify File(Name = 'tempdev', FileName = 'c:\DBfiles\tempdb.mdf')
Alter database tempdb
Modify File(Name = 'templog', FileName = 'c:\DBfiles\tempdb.ldf')


 
 -- Create a database by using the CREATE DATABASE statement. --


Create Database Amantle462
ON Primary 
(Name = 'Amantle462',
FileName = 'C:\DATA\Amantle462.mdf')
Log ON 
(Name = 'Amantle462_log',
FileName = 'C:\DATA\Amantle462.ldf')

---Detaching and Attaching a Database.----


Create Database Task
ON Primary 
(Name = 'Task',
FileName = 'C:\Data\Task.mdf')
Log ON 
(Name = 'Task_log',
FileName = 'C:\Data\Task_log.ldf')


exec sp_detach_db Task


Create Database Task
On
(FileName = 'C:\Data\Task.mdf'),
(FileName = 'C:\Data\Task_log.ldf')
for attach 


--- Configuring the Buffer Pool Extension (enable, verify and disable).---

----Enable

ALTER SERVER CONFIGURATION 
SET BUFFER POOL EXTENSION ON (FILENAME = 'c:\SSDCACHE\AmantleCache.bpe', SIZE = 50 GB)


----Verify

Select * from sys.dm_os_buffer_pool_extension_configuration
Select * from sys.dm_os_buffer_descriptors



----Disable

ALTER SERVER CONFIGURATION 
SET BUFFER POOL EXTENSION OFF



-------Logs and Full Recovery.-----
Use Amantle462


ALTER DATABASE Amantle462 SET RECOVERY FULL;
GO
-- Back up the AdventureWorks2012 database to new media set (backup set 1).
BACKUP DATABASE Amantle462
  TO DISK = 'c:\Backups\_Am462.bak' 
  WITH FORMAT;
GO

BACKUP LOG  Amantle462 
TO DISK = 'c:\Backups\_Am462.bak'


RESTORE Database Amantle462
  FROM DISK = 'c:\Backups\_Am462.bak'
    With NORECOVERY
RESTORE LOG Amantle462
  FROM DISK = 'c:\Backups\_Am462.bak'
  WITH FILE=1, 
    NORECOVERY;

---- Performing Backups (Full, Transactional and Differential).----

--Full 


BACKUP DATABASE Amantle462 
TO DISK = 'c:\Backups\Full_Am462.bak'
WITH INIT;

--Differential

BACKUP DATABASE Amantle462
 TO DISK = 'c:\Backups\Diff_Am462.bak' 
 WITH DIFFERENTIAL, NOINIT;


--Transactional
BACKUP LOG Amantle462
 TO  DISK = N'c:\Backups\Trans_Am462.bak' WITH NOFORMAT, NOINIT,  
 NAME = N'Amantle462-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,  
 STATS = 10
GO


---- Using Backup Compression.---

BACKUP LOG Amantle462
TO DISK = 'c:\Backups\Comp_Am462.bak' 
WITH COMPRESSION; 


 --Using Backup Encryption (Create a database master key, certificate and encrypt a database backup). ---
  
----Create a database master key

Use master
Create Master Key Encryption by Password = '<ntleAma>';



----Create a database certificate
Use master
Create Certificate BackUpCert
With Subject  = 'Certificate: Backup Encryption'

----encrypt a database backup
BACKUP DATABASE [Amantle462] 
TO  DISK = N'c:\Backups\Encrypt_Am462.bak'
WITH  DESCRIPTION = N'Amantle462-Full Database Backup',
FORMAT, INIT,  MEDIADESCRIPTION = N'New media set for Amantle462',  
MEDIANAME = N'Amantle', 
NAME = N'Amantle462-Full Database Backup',
SKIP, NOREWIND, NOUNLOAD, 
ENCRYPTION(ALGORITHM = AES_256, SERVER CERTIFICATE = [BackUpCert]),  STATS = 10
GO








----  Verifying Backups (View the Backup and Restore Events report, query backup history tables and verify backup media). ---
--View the Backup

declare @backupSetId as int
select @backupSetId = position from msdb..backupset where database_name=N'Amantle462' and backup_set_id=(select max(backup_set_id) from msdb..backupset where database_name=N'Amantle462' )
if @backupSetId is null begin raiserror(N'Verify failed. Backup information for database ''Amantle462'' not found.', 16, 1) end
RESTORE VERIFYONLY FROM  DISK = N'c:\Backups\Encrypt_Am462.bak' WITH  FILE = @backupSetId,  NOUNLOAD,  NOREWIND
GO



---Restore Events report

--screen shot---
  


---query backup history tables

SELECT 
	bs.media_set_id,
	bs.backup_finish_date,
	bs.type,
	bs.backup_size,
	bs.compressed_backup_size,
	mf.physical_device_name
FROM msdb.dbo.backupset AS bs
INNER JOIN msdb.dbo.backupmediafamily AS mf
ON bs.media_set_id = mf.media_set_id	
WHERE database_name = 'AMANTLE462'
ORDER BY backup_finish_date DESC;
GO



----verify backup media

--  Use RESTORE HEADERONLY
RESTORE HEADERONLY 
FROM DISK = 'c:\Backups\Encrypt_Am462.bak';
GO

--  Use RESTORE FILELISTONLY
RESTORE FILELISTONLY 
FROM DISK = 'c:\Backups\Encrypt_Am462.bak';
GO

--  Use RESTORE VERIFYONLY
RESTORE VERIFYONLY 
FROM DISK = 'c:\Backups\Encrypt_Am462.bak';
GO

----Managing Server-Level Security (Create a login, manage server level roles and permissions).


Use master
---Create a login
CREATE LOGIN Amantle 
WITH PASSWORD = '1234Amy',
 DEFAULT_DATABASE = [BackUpAmy]; 

 ---manage server level roles 
 
-- Create a user-defined server role
CREATE SERVER ROLE Amantle_Employee;
GO

-- Add a login to the role
ALTER SERVER ROLE Amantle_Employee
ADD MEMBER [Amantle\Admin];
GO


---manage server level permissions 

-- Grant permission to alter login
GRANT ALTER ANY LOGIN TO  Amantle_Employee;

---Create users
CREATE USER GetPaid_App
FOR LOGIN [Amantle\Admin]
WITH DEFAULT_SCHEMA = dbo;

Use BackupAmy 
--Create roles
CREATE ROLE EmployeesHr;

-- Add members
ALTER ROLE EmployeesHr
ADD MEMBER guest;



SELECT 'Default user context' As Context, user_name() AS [DB Identity], SUSER_NAME() AS [System Identity]

--Creating an Application Role. 
CREATE APPLICATION ROLE Amee WITH PASSWORD = '1a2b3c4d'; 


-- Use application role
DECLARE @cookie varbinary(8000);
EXEC sp_setapprole 'Amee', '1a2b3c4d', @fCreateCookie = true, @cookie = @cookie OUTPUT;

SELECT 'Application role active' As Context, user_name() AS [DB Identity], SUSER_NAME() AS [System Identity]


--- Grant  schema permissions
USE [BackupAmy]
GO
CREATE SCHEMA [Amy]
GO
use [BackupAmy]
GO
GRANT ALTER ON SCHEMA::[Amy] TO [Amees] WITH GRANT OPTION 
GO
use [BackupAmy]
GO
GRANT DELETE ON SCHEMA::[Amy] TO [Amees] WITH GRANT OPTION 
GO
use [BackupAmy]
GO
GRANT INSERT ON SCHEMA::[Amy] TO [Amees] WITH GRANT OPTION 
GO
use [BackupAmy]
GO
GRANT SELECT ON SCHEMA::[Amy] TO [Amees] WITH GRANT OPTION 
GO
use [BackupAmy]
GO
GRANT TAKE OWNERSHIP ON SCHEMA::[Amy] TO [Amees] WITH GRANT OPTION 
GO
use [BackupAmy]
GO
GRANT UPDATE ON SCHEMA::[Amy] TO [Amees] WITH GRANT OPTION 
GO
use [BackupAmy]
GO
GRANT VIEW DEFINITION ON SCHEMA::[Amy] TO [Amees] WITH GRANT OPTION 
GO




Create Table Amy.Employees
(empid int,
name varchar(20))




USE BackupAmy;
GO
CREATE TABLE Amy.AuditRates
(LogEntryID INTEGER IDENTITY PRIMARY KEY,
 EventTime DATETIME DEFAULT GETDATE(),
 UserName SYSNAME DEFAULT SUSER_NAME(),
 EmployeeID INT,
 Oldname varchar(20),
 Newnaam varchar(20));
 GO

 -- Create a Trigger
CREATE TRIGGER Amy.EmployeesTrig
ON Amy.Employees
FOR UPDATE
AS
BEGIN
	IF UPDATE(name)
	BEGIN
		INSERT INTO Amy.AuditRates(EmployeeID, OldName, Newnaam)
		SELECT i.empid, d.name,i.name
		FROM inserted i
		JOIN deleted d ON i.empid = d.empid;
	END;
END;
GO


--Update a name
UPDATE Amy.Employees
SET name =  'Amantle'
WHERE empid = 1
Go

-- View the audit log
SELECT * FROM Amy.AuditRates;












-- Create an audit
USE master;

CREATE SERVER AUDIT Audit_Amy
TO FILE 
(	FILEPATH = 'c:\Data\'
	,MAXSIZE = 0 MB
	,MAX_ROLLOVER_FILES = 2147483647
	,RESERVE_DISK_SPACE = OFF
)
WITH
(	QUEUE_DELAY = 1000
	,ON_FAILURE = FAIL_OPERATION
)
GO

ALTER SERVER AUDIT Audit_Amy
WITH (STATE = ON);
GO

--Create a server audit specification
CREATE SERVER AUDIT SPECIFICATION AmyServerAuditSpec
FOR SERVER AUDIT Audit_Amy
ADD (FAILED_LOGIN_GROUP),
ADD (SUCCESSFUL_LOGIN_GROUP)
WITH (STATE = ON);
GO

-- Create a database audit specification
USE BackUpAmy;
CREATE DATABASE AUDIT SPECIFICATION AmyDatabaseAuditSpec
FOR SERVER AUDIT Audit_Amy
ADD (Insert ON SCHEMA::Amy BY Amees)
WITH (STATE = ON);
GO

-- View audited events
SELECT event_time, action_id, succeeded, statement, user_defined_information, server_principal_name, database_name, schema_name, object_name
FROM sys.fn_get_audit_file ('C:\Data\*',default,default)
WHERE server_principal_name NOT IN ('BackUpAmy\guest');





-- Run DBCC CHECKDB with default options
DBCC CHECKDB('BackUpAmy');
GO
-- Run DBCC CHECKDB without informational messages
DBCC CHECKDB('BackUpAmy') WITH NO_INFOMSGS;
GO





-- Create a table with a primary key
USE BackupAmy;
CREATE TABLE Amy.TimeIn
( EmpID int IDENTITY(1,1) PRIMARY KEY,
  TimeLog datetime2 NOT NULL,
  PhoneNumber nvarchar(100) NOT NULL,
  TimeLength int NOT NULL
);
GO

-- Insert some data into the table
SET NOCOUNT ON;
DECLARE @Counter int = 0;
WHILE @Counter < 10000 BEGIN
  INSERT Amy.TimeIn(TimeLog, PhoneNumber, TimeLength)
    VALUES(SYSDATETIME(),'999-9999',CAST(RAND() * 1000 AS int));
  SET @Counter += 1;
END;
GO

-- Check fragmentation
SELECT * 
FROM sys.dm_db_index_physical_stats(DB_ID(),
                                    OBJECT_ID('Amy.TimeIn'),
                                    NULL,
                                    NULL,
                                    'DETAILED');
GO

-- Modify the data in the table 
SET NOCOUNT ON;
DECLARE @Count int = 0;
WHILE @Count < 10000 BEGIN
  UPDATE Amy.TimeIn SET PhoneNumber = REPLICATE('9',CAST(RAND() * 100 AS int))
    WHERE EmpID = @Count % 10000;
  IF @Count % 100 = 0 PRINT @Count;
  SET @Count += 1;
END;
GO

-- Re-check fragmentation
SELECT * 
FROM sys.dm_db_index_physical_stats(DB_ID(),
                                    OBJECT_ID('Amy.TimeIn'),
                                    NULL,
                                    NULL,
                                    'DETAILED');
GO

-- Rebuild the table and its indexes
ALTER INDEX ALL ON Amy.TimeIn REBUILD;
GO

-- Check the fragmentation again
SELECT * 
FROM sys.dm_db_index_physical_stats(DB_ID(),
                                    OBJECT_ID('Amy.TimeIn'),
                                    NULL,
                                    NULL,
                                    'DETAILED');
GO




