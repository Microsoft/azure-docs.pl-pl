---
title: Przywracanie usuniętego Azure Database for PostgreSQL serwera
description: W tym artykule opisano sposób przywracania usuniętego serwera w Azure Database for PostgreSQL użyciu Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 5b5bb9fd6e3d34fc4a6b0ae90a2cd76fc84e9ce1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366525"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Przywracanie usuniętego Azure Database for PostgreSQL serwera

Po porzuceniu serwera kopia zapasowa serwera bazy danych może być zachowywana w usłudze do pięciu dni. Dostęp do kopii zapasowej bazy danych można uzyskać i przywrócić tylko z subskrypcji platformy Azure, w której pierwotnie znajdował się serwer. Aby odzyskać usunięty zasób serwera PostgreSQL w ciągu 5 dni od czasu usunięcia serwera, można wykonać następujące zalecane czynności. Zalecane kroki będą działały tylko wtedy, gdy kopia zapasowa serwera jest nadal dostępna i nie została usunięta z systemu. 

## <a name="pre-requisites"></a>Wymagania wstępne
Aby przywrócić usunięty serwer Azure Database for PostgreSQL, potrzebne są następujące elementy:
- Nazwa subskrypcji platformy Azure hostowania oryginalnego serwera
- Lokalizacja, w której utworzono serwer

## <a name="steps-to-restore"></a>Kroki przywracania

1. Przejdź do strony [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Wybierz **usługę Azure Monitor,** a następnie wybierz **pozycję Dziennik aktywności.**

2. W dzienniku aktywności kliknij pozycję **Dodaj filtr, jak** pokazano, i ustaw następujące filtry dla następujących filtrów

    - **Subskrypcja** = Subskrypcja hostowania usuniętego serwera
    - **Typ zasobu** = Azure Database for PostgreSQL serwerach (Microsoft.DBforPostgreSQL/servers)
    - **Operation** = Delete PostgreSQL Server (Microsoft.DBforPostgreSQL/servers/delete)
 
    ![Dziennik aktywności przefiltrowany pod celu usunięcia operacji serwera PostgreSQL](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Wybierz zdarzenie **Usuń serwer PostgreSQL,** a następnie wybierz **kartę JSON.** Skopiuj atrybuty `resourceId` i `submissionTimestamp` w danych wyjściowych JSON. ResourceId ma następujący format: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Przejdź do strony interfejsu [API REST](/rest/api/PostgreSQL/servers/create) Tworzenia serwera PostgreSQL i wybierz kartę **Wypróbuj** wyróżnione na zielono. Zaloguj się przy użyciu konta platformy Azure.

 5. Podaj właściwości **resourceGroupName,** **serverName** (nazwa usuniętego serwera), **subscriptionId** na podstawie wartości JSON atrybutu resourceId przechwyconej w poprzednim kroku 3. Właściwość api-version jest wstępnie wypełniona i można ją pozostawić bez wartości, jak pokazano na poniższej ilustracji.

    ![Tworzenie serwera przy użyciu interfejsu API REST](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Przewiń poniżej sekcję Treść żądania i wklej następujące wartości, zastępując wartości "Dropped server Location" (lokalizacja usuniętego serwera), np. CentralUS, EastUS itp., "submissionTimestamp" i "resourceId". W przypadku wartości "restorePointInTime" określ wartość "submissionTimestamp" minus **15** minut, aby upewnić się, że polecenie nie powoduje błędu.
    
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

    Jeśli na przykład bieżąca godzina to 2020-11-02T23:59:59.0000000Z, zalecamy co najmniej 15 minut poprzedzających punkt przywracania w czasie 2020-11-02T23:44:59.0000000Z.

    > [!Important]
    > Istnieje limit czasu pięciu dni po porzuceniu serwera. Po upływie pięciu dni jest oczekiwany błąd, ponieważ nie można odnaleźć pliku kopii zapasowej.
    
7. Jeśli zostanie wyświetlony kod odpowiedzi 201 lub 202, żądanie przywrócenia zostanie pomyślnie przesłane. 

    Tworzenie serwera może zająć trochę czasu w zależności od rozmiaru bazy danych i zasobów obliczeniowych aprowowanych na oryginalnym serwerze. Stan przywracania można monitorować z dziennika aktywności, filtrując pod kątem 
   - **Subskrypcja** = Twoja subskrypcja
   - **Typ zasobu** = Azure Database for PostgreSQL serwerach (Microsoft.DBforPostgreSQL/servers) 
   - **Operation** = Update PostgreSQL Server Create

## <a name="next-steps"></a>Następne kroki
- Jeśli próbujesz przywrócić serwer w ciągu pięciu dni i nadal występuje błąd po dokładnym zakończeniu kroków omówiony wcześniej, otwórz zdarzenie pomocy technicznej, aby uzyskać pomoc. Jeśli próbujesz przywrócić usunięty serwer po pięciu dniach, jest oczekiwany błąd, ponieważ nie można odnaleźć pliku kopii zapasowej. W tym scenariuszu nie otwieraj biletu pomocy technicznej. Zespół pomocy technicznej nie może udzielić żadnej pomocy, jeśli kopia zapasowa zostanie usunięta z systemu. 
- Aby zapobiec przypadkowemu usunięciu serwerów, zdecydowanie zalecamy używanie [blokad zasobów](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222).
