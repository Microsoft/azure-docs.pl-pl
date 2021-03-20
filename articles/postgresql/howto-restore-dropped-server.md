---
title: Przywróć usunięty serwer Azure Database for PostgreSQL
description: W tym artykule opisano sposób przywracania usuniętego serwera w Azure Database for PostgreSQL przy użyciu Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 591f01004cfba247112f702625ab05ddc0aaede3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652929"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Przywróć usunięty serwer Azure Database for PostgreSQL

Po porzucenia serwera kopie zapasowe serwera bazy danych mogą być przechowywane nawet przez pięć dni w usłudze. Kopia zapasowa bazy danych jest dostępna i przywracana tylko w ramach subskrypcji platformy Azure, w której pierwotnie znajdowały się serwer. Następujące zalecane kroki można wykonać w celu odzyskania porzuconego zasobu serwera PostgreSQL w ciągu 5 dni od momentu usunięcia serwera. Zalecane kroki będą działały tylko wtedy, gdy kopia zapasowa serwera jest nadal dostępna i nie została usunięta z systemu. 

## <a name="pre-requisites"></a>Wymagania wstępne
Aby przywrócić usunięty serwer Azure Database for PostgreSQL, potrzebne są następujące elementy:
- Nazwa subskrypcji platformy Azure obsługująca oryginalny serwer
- Lokalizacja, w której został utworzony serwer

## <a name="steps-to-restore"></a>Procedura przywracania

1. Przejdź do [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Wybierz usługę **Azure monitor** , a następnie wybierz pozycję **Dziennik aktywności**.

2. W dzienniku aktywności kliknij pozycję **Dodaj filtr** , jak pokazano, i ustaw następujące filtry dla następujących elementów:

    - **Subskrypcja** = subskrypcja obsługująca usunięty serwer
    - **Typ zasobu** = serwery Azure Database for PostgreSQL (Microsoft. DBforPostgreSQL/serwery)
    - **Operacja** = Delete PostgreSQL Server (Microsoft. DBforPostgreSQL/serwery/Delete)
 
    ![Przefiltrowana dziennika aktywności dla operacji usuwania serwera PostgreSQL](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Wybierz zdarzenie **Usuń serwer PostgreSQL** , a następnie wybierz **kartę JSON**. Skopiuj `resourceId` atrybuty i `submissionTimestamp` w danych wyjściowych JSON. Identyfikator zasobu jest w następującym formacie: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Przejdź do strony PostgreSQL [Utwórz serwer interfejsu API REST](/rest/api/PostgreSQL/servers/create) i wybierz wyróżnioną kartę **try** (zielony). Zaloguj się przy użyciu konta platformy Azure.

 5. Podaj właściwości **resourceGroupName**, **servername** (Deleted Server Name), identyfikator **subskrypcji** na podstawie wartości JSON atrybutu ResourceID przechwyconej w poprzednim kroku 3. Właściwość API-Version jest wstępnie wypełniona i można ją pozostawić jako-is, jak pokazano na poniższej ilustracji.

    ![Tworzenie serwera przy użyciu interfejsu API REST](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Przewiń w sekcji treść żądania i wklej następujący tekst: "porzucona lokalizacja serwera", "submissionTimestamp" i "resourceId". Dla elementu "restorePointInTime" Określ wartość "submissionTimestamp" minus **15 minut** , aby upewnić się, że polecenie nie zostanie wychodzące.
    
    ```json
    {
      "location": "Dropped Server Location",  
      "properties": 
      {
        "restorePointInTime": "submissionTimestamp - 15 minutes",
        "createMode": "PointInTimeRestore",
        "sourceServerId": "resourceId"
      }
    }
    ```

    Jeśli na przykład bieżąca godzina to 2020-11-02T23:59:59.0000000 Z, zalecamy co najmniej 15 minut poprzedniego punktu przywracania w czasie 2020-11-02T23:44:59.0000000 Z.

    > [!Important]
    > Po porzuconym serwerze występuje limit czasu równy pięć dni. Po pięciu dniach jest oczekiwany błąd, ponieważ nie można odnaleźć pliku kopii zapasowej.
    
7. Jeśli zobaczysz kod odpowiedzi 201 lub 202, żądanie przywrócenia zostało pomyślnie przesłane. 

    Tworzenie serwera może zająć trochę czasu w zależności od rozmiaru bazy danych i zasobów obliczeniowych, które są obsługiwane na oryginalnym serwerze. Stan przywracania można monitorować z dziennika aktywności przez filtrowanie 
   - **Subskrypcja** = subskrypcja
   - **Typ zasobu** = serwery Azure Database for PostgreSQL (Microsoft. DBforPostgreSQL/serwery) 
   - **Operacja** = aktualizowanie serwera PostgreSQL

## <a name="next-steps"></a>Następne kroki
- Jeśli próbujesz przywrócić serwer w ciągu pięciu dni i nadal pojawia się komunikat o błędzie po dokładnym wykonaniu opisanej wcześniej czynności, Otwórz zdarzenie pomocy technicznej w celu uzyskania pomocy. Jeśli próbujesz przywrócić usunięty serwer po pięciu dniach, jest oczekiwany błąd, ponieważ nie można odnaleźć pliku kopii zapasowej. W tym scenariuszu nie należy otwierać biletu pomocy technicznej. Zespół pomocy technicznej nie może zapewnić żadnej pomocy, jeśli kopia zapasowa zostanie usunięta z systemu. 
- Aby zapobiec przypadkowemu usunięciu serwerów, zdecydowanie zalecamy użycie [blokad zasobów](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222).
