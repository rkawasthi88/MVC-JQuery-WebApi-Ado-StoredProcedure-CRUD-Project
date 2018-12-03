# MVC-JQuery-WebApi-Ado-StoredProcedure-CRUD-Project
This project demonstrates CRUD operation using HTML, JQuery, MVC-Web API, AdoNet, and Stored Procedure ( SQL Server).  
To Demo CRUD Operation, let's create a database table called Cat. 
/****** Object:  Table [dbo].[Cat]    Script Date: 12/2/2018 7:05:26 PM ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[Cat](
	[CatId] [int] IDENTITY(1,1) NOT NULL,
	[CatName] [varchar](30) NOT NULL,
	[NumberOfTails] [int] NULL,
 CONSTRAINT [PK_CatId] PRIMARY KEY CLUSTERED 
(
	[CatId] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
To wrap Create, Retrieve, Update, and Delete operation, create the following stored procedure in the SQL Server.
/****** Object:  StoredProcedure [dbo].[pro_PerformCRUDforCat]    Script Date: 12/2/2018 6:55:25 PM ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
/*-- =============================================
-- Author:	Ram Awasthi
-- Create date: 12/01/2018
-- Description:	This procedure performs CRUD operations for Cat detail.
-- Parameters : @TransactionType - C : Create, insert record into Cat table.
								   R : Retrieve, select records from Cat table.
								   U : Update, update Cat Name and Number of Tails for selected CatID into Cat table.
								   D : Delete, delete Cat record for selected catID from Cat table.    
-- TEST Script:
   
   -- Create: insert record.
   DECLARE @ReturnMessage VARCHAR(500)
   EXEC pro_PerformCRUDforCat 'C', 0,'Jacky',1, @ReturnMessage OUT
   SELECT @ReturnMessage 

   -- Retrieve: select all cat records.
   DECLARE @ReturnMessage VARCHAR(500)
   EXEC pro_PerformCRUDforCat 'R', 0,'',0, @ReturnMessage OUT
   SELECT @ReturnMessage 

   -- Retrieve: fetch selected cat details.
   DECLARE @ReturnMessage VARCHAR(500)
   EXEC pro_PerformCRUDforCat 'R', 6,'',0, @ReturnMessage OUT
   SELECT @ReturnMessage 
   
   -- Update: update selected Cat details.
   DECLARE @ReturnMessage VARCHAR(500)
   EXEC pro_PerformCRUDforCat 'U', 2, 'Maggie I', 3, @ReturnMessage OUT
   SELECT @ReturnMessage 

   -- Delete: delete selected Cat record.
   DECLARE @ReturnMessage VARCHAR(500)
   EXEC pro_PerformCRUDforCat 'D', 6,'',0, @ReturnMessage OUT
   SELECT @ReturnMessage 
   
   Select * FROM Cat 
-- =============================================*/
CREATE OR ALTER PROCEDURE [dbo].[pro_PerformCRUDforCat]
	-- Add the parameters for the stored procedure here
	@TransactionType CHAR(1)
	,@CatID INT = 0
	,@CatName VARCHAR(30) = ''
	,@NumberofTails INT = 0
	,@ReturnMessage VARCHAR(500) OUTPUT

AS
BEGIN
	-- SET NOCOUNT ON added to prevent extra result sets from
	-- interfering with SELECT statements.
	SET NOCOUNT ON;

	BEGIN TRY

		SET @ReturnMessage = ''

		IF @TransactionType = 'C' --Create : insert record into Cat table.
		BEGIN
			INSERT INTO Cat (CatName, NumberofTails)
			VALUES (@CatName, @NumberOfTails)
			
			Set @ReturnMessage = CONVERT(INT, @@IDENTITY)  
		END
		ELSE 
		BEGIN
			IF @TransactionType = 'R' --Retrive : select records from Cat table.
			BEGIN
				IF ISNULL(@CatID,0) > 0 -- fetch cat details for selected id.   
				BEGIN 
					SELECT CatId, CatName, NumberofTails 
					FROM Cat
					WHERE CatID = @CatID 
				END
				ELSE -- If no Cat ID passed, select all cats  
				BEGIN 
					SELECT CatId, CatName, NumberofTails 
					FROM Cat 
					ORDER BY CatName    
				END
			END
			ELSE IF @TransactionType = 'U' --Update : update record into Cat table.
			BEGIN
				UPDATE Cat SET CatName = @CatName, NumberofTails = @NumberOfTails 
				WHERE CatId = @CatId 
			END
			ELSE IF @TransactionType = 'D' --Delete : delete record from Cat table.
			BEGIN
				DELETE FROM Cat
				WHERE CatId = @CatId 
			END
			
			SET @ReturnMessage = 'OK'
		END
	END TRY

	BEGIN CATCH
		SET @ReturnMessage = 'FAIL. Error Message : ' + ERROR_MESSAGE()
	END CATCH
END

Download and open MvcJQueryWebApiAdoSpCRUDProject from here.
Change database server and database name in the Web.Config connection string.
<add name="connStringName" connectionString="Data Source=DBServer;Initial Catalog=Practice;Integrated Security=True" providerName="System.Data.SqlClient" />
Run the project in the visal studio. 
