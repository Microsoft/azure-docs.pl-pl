---
title: Przywróć usunięty serwer Azure Database for MySQL
description: W tym artykule opisano sposób przywracania usuniętego serwera w Azure Database for MySQL przy użyciu Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 34dddd8e5f3fb418fc7155630bf82a922e418402
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657094"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>Przywróć usunięty serwer Azure Database for MySQL

Po porzucenia serwera kopie zapasowe serwera bazy danych mogą być przechowywane nawet przez pięć dni w usłudze. Kopia zapasowa bazy danych jest dostępna i przywracana tylko w ramach subskrypcji platformy Azure, w której pierwotnie znajdowały się serwer. Następujące zalecane kroki można wykonać w celu odzyskania usuniętego zasobu serwera MySQL w ciągu 5 dni od momentu usunięcia serwera. Zalecane kroki będą działały tylko wtedy, gdy kopia zapasowa serwera jest nadal dostępna i nie została usunięta z systemu. 

## <a name="pre-requisites"></a>Wymagania wstępne
Aby przywrócić usunięty serwer Azure Database for MySQL, potrzebne są następujące elementy:
- Nazwa subskrypcji platformy Azure obsługująca oryginalny serwer
- Lokalizacja, w której został utworzony serwer

## <a name="steps-to-restore"></a>Procedura przywracania

1. Przejdź do [dziennika aktywności](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) z bloku monitorowanie w Azure Portal. 

2. W dzienniku aktywności kliknij pozycję **Dodaj filtr** , jak pokazano, i ustaw następujące filtry dla 

    - **Subskrypcja** = subskrypcja obsługująca usunięty serwer
    - **Typ zasobu** = serwery Azure Database for MySQL (Microsoft. DBforMySQL/serwery) 
    - **Operacja** = Delete serwer MySQL (Microsoft. DBforMySQL/serwery/Delete) 
 
     [![Przefiltrowany dziennik aktywności dla operacji usuwania serwera MySQL](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Kliknij dwukrotnie zdarzenie Usuń serwer MySQL i kliknij kartę JSON i zanotuj atrybuty "resourceId" i "submissionTimestamp" w danych wyjściowych JSON. Identyfikator zasobu jest w następującym formacie:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. Przejdź do [strony Tworzenie interfejsu API REST serwera](/rest/api/mysql/servers/create) i kliknij pozycję "Wypróbuj ją" w kolorze zielonym, a następnie zaloguj się przy użyciu konta platformy Azure.
 
 5. Podaj wartość resourceGroupName, ServerName (usunięto nazwę serwera), Identyfikator subskrypcji, pochodną od atrybutu resourceId przechwyconego w kroku 3, podczas gdy wersja interfejsu API jest wstępnie wypełniona, jak pokazano na ilustracji.
 
     [![Tworzenie serwera przy użyciu interfejsu API REST](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
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

7. Jeśli zobaczysz kod odpowiedzi 201 lub 202, żądanie przywrócenia zostało pomyślnie przesłane. 

8. Tworzenie serwera może zająć trochę czasu w zależności od rozmiaru bazy danych i zasobów obliczeniowych, które są obsługiwane na oryginalnym serwerze. Stan przywracania można monitorować z dziennika aktywności przez filtrowanie 
   - **Subskrypcja** = subskrypcja
   - **Typ zasobu** = serwery Azure Database for MySQL (Microsoft. DBforMySQL/serwery) 
   - **Operacja** = aktualizowanie serwera MySQL

## <a name="next-steps"></a>Następne kroki
- Jeśli próbujesz przywrócić serwer w ciągu pięciu dni i nadal pojawia się komunikat o błędzie po dokładnym wykonaniu opisanej wcześniej czynności, Otwórz zdarzenie pomocy technicznej w celu uzyskania pomocy. Jeśli próbujesz przywrócić usunięty serwer po pięciu dniach, jest oczekiwany błąd, ponieważ nie można odnaleźć pliku kopii zapasowej. W tym scenariuszu nie należy otwierać biletu pomocy technicznej. Zespół pomocy technicznej nie może zapewnić żadnej pomocy, jeśli kopia zapasowa zostanie usunięta z systemu. 
- Aby zapobiec przypadkowemu usunięciu serwerów, zdecydowanie zalecamy użycie [blokad zasobów](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).