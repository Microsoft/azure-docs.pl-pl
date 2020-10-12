---
title: Konfigurowanie modułów IoT Edge w usłudze Azure SQL Edge
description: W drugiej części tego samouczka dotyczącego usługi Azure SQL Edge na potrzeby przewidywania zanieczyszczeń żelaza można skonfigurować IoT Edge modułów i połączeń.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 7b2432fda70e8f9a5fa8bc64ede846d977672e9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886486"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Konfigurowanie modułów IoT Edge i połączeń

W drugiej części tego samouczka z trzema częściami w celu przewidywania zanieczyszczeń żelaza w usłudze Azure SQL Edge skonfigurujesz następujące moduły IoT Edge:

- Usługa Azure SQL Edge
- Moduł IoT Edge generatora danych

## <a name="specify-container-registry-credentials"></a>Określ poświadczenia rejestru kontenera

Należy określić poświadczenia dla obrazów modułu hostingu rejestrów kontenerów. Można je znaleźć w rejestrze kontenerów, który został utworzony w grupie zasobów. Przejdź do sekcji **klucze dostępu** . Zanotuj następujące pola:

- Nazwa rejestru
- Serwer logowania
- Nazwa użytkownika
- Hasło

Teraz określ poświadczenia kontenera w module IoT Edge.

1. Przejdź do centrum IoT Hub utworzonego w grupie zasobów.

2. W sekcji **IoT Edge** w obszarze **Automatyczne zarządzanie urządzeniami**kliknij pozycję **Identyfikator urządzenia**. W tym samouczku identyfikator jest `IronOrePredictionDevice` .

3. Wybierz sekcję **Ustawianie modułów** .

4. W obszarze **Container Registry poświadczenia**wprowadź następujące wartości:

   _Pole_|_Wartość_
   -------|-------
   Nazwa|Nazwa rejestru
   Adres|Serwer logowania
   Nazwa użytkownika|Nazwa użytkownika
   Hasło|Hasło
  
## <a name="deploy-the-data-generator-module"></a>Wdrażanie modułu generatora danych

1. W sekcji **IoT Edge** w obszarze **Automatyczne zarządzanie urządzeniami**kliknij pozycję **Identyfikator urządzenia**. W tym samouczku identyfikator jest, `IronOrePredictionDevice` a następnie kliknij pozycję **Ustaw moduły**.

2.  W sekcji **IoT Edge modułów** na stronie **Ustawianie modułów na urządzeniu:** kliknij pozycję **+ Dodaj** i wybierz **moduł IoT Edge**.

3. Podaj prawidłową nazwę i identyfikator URI obrazu dla modułu IoT Edge.
   Identyfikator URI obrazu można znaleźć w rejestrze kontenerów w grupie zasobów utworzonej w części pierwszej z tego samouczka. Wybierz sekcję **repozytoria** w obszarze **usługi**. Na potrzeby tego samouczka wybierz repozytorium o nazwie `silicaprediction` . Wybierz odpowiedni tag. Identyfikator URI obrazu będzie miał format:

   *serwer logowania containerregistry* / *Nazwa repozytorium*:*nazwa tagu*

   Na przykład:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

4. Pozostaw *zasady ponownego uruchamiania* i *wymagane pola stanu* , jeśli jest to.

5. Kliknij pozycję **Dodaj**.


## <a name="deploy-the-azure-sql-edge-module"></a>Wdrażanie modułu usługi Azure SQL Edge

1. Wdróż moduł usługi Azure SQL Edge, klikając pozycję **+ Dodaj** , a następnie **moduł Marketplace**. 

2. W bloku **portalu Marketplace modułu IoT Edge** Wyszukaj ciąg *Azure SQL Edge* i wybierz pozycję *Azure SQL Edge Developer*. 

3. Kliknij nowo dodany moduł *usługi Azure SQL Edge* w obszarze **IoT Edge** modules, aby skonfigurować moduł usługi Azure SQL Edge. Aby uzyskać więcej informacji na temat opcji konfiguracji, zobacz [wdrażanie usługi Azure SQL Edge](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal).

4. Dodaj `MSSQL_PACKAGE` zmienną środowiskową do wdrożenia modułu *usługi Azure SQL Edge* i określ adres URL sygnatury dostępu współdzielonego pliku dacpac bazy danych utworzonej w kroku 8 [części](tutorial-deploy-azure-resources.md) w tym samouczku.

5. Kliknij przycisk **Aktualizuj**

6. Na stronie **Ustawianie modułów na urządzeniu** kliknij pozycję **dalej: trasy >**.

7. W okienku trasy na stronie **Ustawianie modułów na urządzeniu** Określ trasy modułu do IoT Edge komunikację z centrum, zgodnie z poniższym opisem. Pamiętaj o zaktualizowaniu nazw modułów w definicjach tras poniżej.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Na przykład:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. Na stronie **Ustawianie modułów na urządzeniu** kliknij przycisk **Dalej: recenzja + tworzenie >**

8. Na stronie **Ustawianie modułów na urządzeniu** kliknij pozycję **Utwórz** .

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Utwórz i uruchom zadanie przesyłania strumieniowego T-SQL w usłudze Azure SQL Edge.

1. Otwórz narzędzie Azure Data Studio.

2. Na karcie **Zapraszamy** Rozpocznij nowe połączenie z następującymi szczegółami:

   |_Pole_|_Wartość_|
   |-------|-------|
   |Typ połączenia| Microsoft SQL Server|
   |Serwer|Publiczny adres IP wymieniony na maszynie wirtualnej, który został utworzony dla tej wersji demonstracyjnej|
   |Nazwa użytkownika|sa|
   |Hasło|Silne hasło użyte podczas tworzenia wystąpienia usługi Azure SQL Edge|
   |baza danych|Domyślne|
   |Grupa serwerów|Domyślne|
   |Nazwa (opcjonalnie)|Podaj nazwę opcjonalną|

3. Kliknij przycisk **Połącz** .

4. Na karcie menu **plik** Otwórz nowy Notes lub użyj skrótu klawiaturowego CTRL + N.

5. W oknie Nowa kwerenda wykonaj Poniższy skrypt, aby utworzyć zadanie przesyłania strumieniowego T-SQL. Przed wykonaniem skryptu pamiętaj, aby zmienić następujące zmienne. 
   - *SQL_SA_Password:* Wartość MSSQL_SA_PASSWORD określona podczas wdrażania modułu usługi Azure SQL Edge. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. Użyj następującego zapytania, aby sprawdzić, czy dane z modułu generowania danych są przesyłane strumieniowo do bazy danych programu. 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


W tym samouczku został wdrożony moduł Generator danych i moduł SQL Edge. Następnie utworzyliśmy zadanie przesyłania strumieniowego w celu przesyłania strumieniowego danych wygenerowanych przez moduł generowania danych do SQL. 

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie modelu ML w usłudze Azure SQL Edge przy użyciu ONNX](tutorial-run-ml-model-on-sql-edge.md)
