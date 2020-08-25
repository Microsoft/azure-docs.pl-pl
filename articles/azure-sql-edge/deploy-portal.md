---
title: Wdrażanie usługi Azure SQL Edge (wersja zapoznawcza) przy użyciu Azure Portal
description: Dowiedz się, jak wdrożyć usługę Azure SQL Edge (wersja zapoznawcza) przy użyciu Azure Portal
keywords: Wdróż program SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7af4264860f8d9950515cd5302f03822e7cbac39
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816868"
---
# <a name="deploy-azure-sql-edge-preview"></a>Wdrażanie usługi Azure SQL Edge (wersja zapoznawcza) 

Azure SQL Edge (wersja zapoznawcza) to aparat relacyjnej bazy danych zoptymalizowany pod kątem wdrożeń IoT i Azure IoT Edge. Zapewnia możliwości tworzenia wysokiej wydajności magazynu danych i warstwy przetwarzania dla aplikacji i rozwiązań IoT. W tym przewodniku szybki start pokazano, jak rozpocząć tworzenie modułu usługi Azure SQL Edge za pośrednictwem Azure IoT Edge przy użyciu Azure Portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
* Utwórz [IoT Hub platformy Azure](../iot-hub/iot-hub-create-through-portal.md).
* Zarejestruj [urządzenie IoT Edge z Azure Portal](../iot-edge/how-to-register-device-portal.md).
* Przygotuj IoT Edge urządzenie do [wdrożenia modułu IoT Edge z Azure Portal](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Aby wdrożyć maszynę wirtualną z systemem Linux na platformie Azure jako urządzenie IoT Edge, zapoznaj się z tym [przewodnikiem Szybki Start](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Wdrażanie modułu SQL Edge z poziomu portalu Azure Marketplace

Portal Azure Marketplace to rynek aplikacji i usług online, w którym można przeglądać szeroką gamę aplikacji i rozwiązań dla przedsiębiorstw, które są certyfikowane i zoptymalizowane pod kątem działania na platformie Azure, w tym [modułów IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Usługę Azure SQL Edge można wdrożyć na urządzeniu brzegowym za pomocą portalu Marketplace.

1. Znajdź moduł usługi Azure SQL Edge w witrynie Azure Marketplace.<br><br>

   ![SQL Edge w witrynie MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Wybierz plan oprogramowania, który najlepiej odpowiada Twoim wymaganiom, a następnie kliknij przycisk **Utwórz**. <br><br>

   ![Wybierz odpowiedni plan oprogramowania](media/deploy-portal/pick-correct-plan.png)

3. Na stronie urządzenia docelowe dla IoT Edge modułu określ następujące szczegóły, a następnie kliknij przycisk **Utwórz** .

   |**Pole**  |**Opis**  |
   |---------|---------|
   |Subskrypcja  |  Subskrypcja platformy Azure, w ramach której utworzono IoT Hub |
   |IoT Hub   |  Nazwa IoT Hub, w którym zarejestrowano urządzenie IoT Edge, a następnie wybierz opcję "wdróż w urządzeniu"|
   |Nazwa urządzenia IoT Edge  |  Nazwa urządzenia IoT Edgeego, na którym zostanie wdrożona Przeglądarka SQL Edge |

4. Na stronie **Ustawianie modułów** przejdź do sekcji dotyczącej modułów wdrażania, a następnie kliknij pozycję **Konfiguruj** względem modułu SQL Edge. 

5. W okienku **IoT Edge modułów niestandardowych** określ odpowiednie wartości zmiennych środowiskowych i/lub Dostosuj opcje tworzenia i żądane właściwości modułu. Aby uzyskać pełną listę obsługiwanych zmiennych środowiskowych, odwołują się [SQL Server zmiennych środowiskowych kontenera](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parametr**  |**Opis**|
   |---------|---------|
   | Nazwa | Nazwa modułu. |
   |SA_PASSWORD  | Określ silne hasło dla konta administratora programu SQL Edge. |
   |MSSQL_LCID   | Ustawia identyfikator języka, który ma być używany dla SQL Server. Na przykład 1036 jest francuski. |
   |MSSQL_COLLATION | Ustawia domyślne sortowanie dla SQL Server. To ustawienie przesłania domyślne mapowanie identyfikatora języka (LCID) do sortowania. |

   > [!NOTE]
   > Nie zmieniaj ani nie Aktualizuj **identyfikatora URI obrazu** ani ustawień **ACCEPT_EULA** w module.

6. W okienku **IoT Edge modułów niestandardowych** zaktualizuj odpowiednie wartości opcji Utwórz kontener dla **portu hosta**. Jeśli zachodzi potrzeba wdrożenia więcej niż jednego modułu usługi SQL DB Edge, należy zaktualizować opcję instalacji, aby utworzyć nową parę źródłową & docelowej dla woluminu trwałego. Aby uzyskać więcej informacji na temat instalacji i woluminów, zobacz [Używanie woluminów](https://docs.docker.com/storage/volumes/) w dokumentacji platformy Docker. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. W okienku **IoT Edge modułów niestandardowych** zaktualizuj *odpowiednie właściwości zestawu splotów modułu* , aby uwzględnić lokalizację pakietu SQL i informacje o zadaniu usługi Stream Analytics. Te dwa pola są opcjonalne i powinny być używane, jeśli chcesz wdrożyć moduł programu SQL Edge przy użyciu bazy danych i zadania przesyłania strumieniowego.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. W okienku **IoT Edge modułów niestandardowych** Ustaw *zasady ponownego uruchamiania* na zawsze i *żądany stan* do uruchomienia.
9. W okienku **IoT Edge modułów niestandardowych** kliknij pozycję **Zapisz**.
10. Na stronie **Ustawianie modułów** kliknij przycisk **dalej**.
11. Na stronie **Określ trasę (opcjonalnie)** dla **zestawu modułów** Określ trasy modułu do modułu lub modułu, aby IoT Edge komunikację z centrum, zobacz [wdrażanie modułów i ustanawianie tras w IoT Edge](../iot-edge/module-composition.md).
12. Kliknij przycisk **Dalej**.
13. Kliknij przycisk **Prześlij**.

## <a name="connect-to-azure-sql-edge"></a>Łączenie z usługą Azure SQL Edge

Poniższe kroki służą do nawiązywania połączenia z usługą Azure SQL Edge przy użyciu narzędzia wiersza polecenia usługi Azure SQL **Edge w**kontenerze.

> [!NOTE]
> Narzędzie sqlcmd nie jest dostępne w wersji ARM64 kontenerów programu SQL Edge.

1. Użyj `docker exec -it` polecenia, aby uruchomić interaktywną powłokę bash wewnątrz działającego kontenera. W poniższym przykładzie `azuresqledge` Nazwa określona przez `Name` parametr modułu IoT Edge.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Po utworzeniu wewnątrz kontenera Połącz się lokalnie z narzędziem sqlcmd. Narzędzie sqlcmd nie jest domyślnie ścieżką, więc należy określić pełną ścieżkę.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > Możesz pominąć hasło w wierszu polecenia, aby uzyskać monit o jego wprowadzenie.

3. Jeśli to się powiedzie, należy przejść do wiersza polecenia **sqlcmd** : `1>` .

## <a name="create-and-query-data"></a>Tworzenie i wykonywanie zapytań dotyczących danych

W poniższych sekcjach opisano, jak za pomocą narzędzia **sqlcmd** i Transact-SQL utworzyć nową bazę danych, dodać dane i uruchomić proste zapytanie.

### <a name="create-a-new-database"></a>Tworzenie nowej bazy danych

Poniższe kroki tworzą nową bazę danych o nazwie `TestDB` .

1. W wierszu polecenia **sqlcmd** Wklej następujące polecenie języka Transact-SQL, aby utworzyć testową bazę danych:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. W następnym wierszu Napisz zapytanie, aby zwrócić nazwę wszystkich baz danych na serwerze:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Wstawianie danych

Następnie utwórz nową tabelę `Inventory` i Wstaw dwa nowe wiersze.

1. W wierszu polecenia **sqlcmd** Przełącz kontekst do nowej `TestDB` bazy danych:

   ```sql
   USE TestDB
   ```

2. Utwórz nową tabelę o nazwie `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Wstaw dane do nowej tabeli:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Wpisz polecenie `GO` , aby wykonać poprzednie polecenia:

   ```sql
   GO
   ```

### <a name="select-data"></a>Wybieranie danych

Teraz uruchom zapytanie, aby zwrócić dane z `Inventory` tabeli.

1. W wierszu polecenia **sqlcmd** wprowadź zapytanie, które zwraca wiersze z `Inventory` tabeli, w której ilość jest większa niż 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Wykonaj polecenie:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Wyjdź z wiersza polecenia sqlcmd

1. Aby zakończyć sesję narzędzia **sqlcmd** , wpisz `QUIT` :

   ```sql
   QUIT
   ```

2. Aby zamknąć interaktywny wiersz polecenia w kontenerze, wpisz `exit` . Kontener nadal będzie działać po zamknięciu interaktywnej powłoki bash.

## <a name="connect-from-outside-the-container"></a>Nawiązywanie połączenia spoza kontenera

Można łączyć i uruchamiać zapytania SQL względem wystąpienia usługi Azure SQL Edge z dowolnego zewnętrznego narzędzia Linux, Windows lub macOS, które obsługuje połączenia SQL. Aby uzyskać więcej informacji na temat łączenia się z kontenerem programu SQL Edge z zewnątrz, zapoznaj się z tematem [Connect and Query Edge Azure SQL](https://docs.microsoft.com/azure/azure-sql-edge/connect).

W tym przewodniku szybki start wdrożono moduł programu SQL Edge na urządzeniu IoT Edge. 

## <a name="next-steps"></a>Następne kroki

- [Machine Learning i sztuczna inteligencja przy użyciu ONNX w programie SQL Edge](onnx-overview.md).
- [Tworzenie kompleksowego rozwiązania IoT za pomocą programu SQL Edge przy użyciu IoT Edge](tutorial-deploy-azure-resources.md).
- [Przesyłanie strumieniowe danych w usłudze Azure SQL Edge](stream-data.md)
