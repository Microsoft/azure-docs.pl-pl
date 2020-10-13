---
title: Wdrażanie usługi Azure SQL Edge przy użyciu Azure Portal
description: Dowiedz się, jak wdrożyć usługę Azure SQL Edge przy użyciu Azure Portal
keywords: Wdróż program SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 683a3fc148d3521366beef4427e87200ea81a9f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445716"
---
# <a name="deploy-azure-sql-edge"></a>Wdrażanie usługi Azure SQL Edge 

Usługa Azure SQL Edge jest aparatem relacyjnej bazy danych zoptymalizowanym pod kątem wdrożeń IoT i Azure IoT Edge. Zapewnia możliwości tworzenia wysokiej wydajności magazynu danych i warstwy przetwarzania dla aplikacji i rozwiązań IoT. W tym przewodniku szybki start pokazano, jak rozpocząć tworzenie modułu usługi Azure SQL Edge za pośrednictwem Azure IoT Edge przy użyciu Azure Portal.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

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
   |Usługa IoT Hub   |  Nazwa IoT Hub, w którym zarejestrowano urządzenie IoT Edge, a następnie wybierz opcję "wdróż w urządzeniu"|
   |Nazwa urządzenia IoT Edge  |  Nazwa urządzenia IoT Edgeego, na którym zostanie wdrożona Przeglądarka SQL Edge |

4. Na stronie **Ustawianie modułów na urządzeniu:** kliknij moduł Azure SQL Edge w obszarze **IoT Edge**modules. Domyślna nazwa modułu jest ustawiona na *AzureSQLEdge*. 

5. W sekcji *Ustawienia modułu* w bloku **Update IoT Edge module** określ odpowiednie wartości dla *IoT Edge Nazwa modułu*, *zasady ponownego uruchamiania* i *żądany stan*. 

   > [!IMPORTANT]    
   > Nie zmieniaj ani nie Aktualizuj ustawień **identyfikatora URI obrazu** w module.

6. W sekcji *zmienne środowiskowe* w bloku **Update IoT Edge module** określ odpowiednie wartości zmiennych środowiskowych. Aby uzyskać pełną listę zmiennych środowiskowych usługi Azure SQL Edge, zobacz [Konfigurowanie przy użyciu zmiennych środowiskowych](configure.md#configure-by-using-environment-variables). Następujące domyślne zmienne środowiskowe są zdefiniowane dla modułu. 

   |**Parametr**  |**Opis**|
   |---------|---------|
   | MSSQL_SA_PASSWORD  | Zmień wartość domyślną, aby określić silne hasło dla konta administratora programu SQL Edge. |
   | MSSQL_LCID   | Zmień wartość domyślną, aby ustawić żądany identyfikator języka dla programu SQL Edge. Na przykład 1036 jest francuski. |
   | MSSQL_COLLATION | Zmień wartość domyślną, aby ustawić sortowanie domyślne dla programu SQL Edge. To ustawienie przesłania domyślne mapowanie identyfikatora języka (LCID) do sortowania. |

   > [!IMPORTANT]    
   > Nie zmieniaj ani nie Aktualizuj zmiennej środowiskowej **ACCEPT_EULA** dla modułu.

7. W sekcji *Opcje tworzenia kontenera* w bloku **Update IoT Edge module** zaktualizuj poniższe opcje zgodnie z wymaganiami. 
   - **Port hosta:** Mapuj określony port hosta na port 1433 (domyślny port SQL) w kontenerze.
   - **Powiązania** i **instalacje:** Jeśli konieczne jest wdrożenie więcej niż jednego modułu programu SQL Edge, należy zaktualizować opcję instalacji, aby utworzyć nową parę źródłową & docelowej dla woluminu trwałego. Aby uzyskać więcej informacji na temat instalacji i woluminów, zobacz [Używanie woluminów](https://docs.docker.com/storage/volumes/) w dokumentacji platformy Docker. 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
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
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > Nie zmieniaj `PlanId` zmiennej środowiska w środowisku zdefiniowanym w ustawieniu Utwórz konfigurację. Jeśli ta wartość zostanie zmieniona, kontener usługi Azure SQL Edge nie zostanie uruchomiony. 
   
8. W okienku **Aktualizowanie modułu IoT Edge** kliknij pozycję **Aktualizuj**.
9. Na stronie **Ustawianie modułów na urządzeniu** kliknij pozycję **dalej: trasy >** , jeśli chcesz zdefiniować trasy dla wdrożenia. W przeciwnym razie kliknij pozycję **Przegląd + Utwórz**. Aby uzyskać więcej informacji na temat konfigurowania tras, zobacz [wdrażanie modułów i ustanawianie tras w IoT Edge](../iot-edge/module-composition.md).
11. Na stronie **Ustawianie modułów na urządzeniu** kliknij pozycję **Utwórz**.

## <a name="connect-to-azure-sql-edge"></a>Łączenie z usługą Azure SQL Edge

Poniższe kroki służą do nawiązywania połączenia z usługą Azure SQL Edge przy użyciu narzędzia wiersza polecenia usługi Azure SQL **Edge w**kontenerze.

> [!NOTE]      
> Narzędzia wiersza polecenia SQL (sqlcmd) nie są dostępne w wersji ARM64 kontenerów usługi Azure SQL Edge.

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

W poniższych sekcjach opisano, jak za pomocą narzędzia **sqlcmd** i Transact-SQL utworzyć nową bazę danych, dodać dane i uruchomić zapytanie.

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

- [Machine Learning i sztuczna inteligencja przy użyciu ONNX w programie SQL Edge](onnx-overview.md)
- [Tworzenie kompleksowego rozwiązania IoT za pomocą programu SQL Edge przy użyciu IoT Edge](tutorial-deploy-azure-resources.md)
- [Przesyłanie strumieniowe danych w usłudze Azure SQL Edge](stream-data.md)
- [Rozwiązywanie błędów związanych z wdrażaniem](troubleshoot.md)
