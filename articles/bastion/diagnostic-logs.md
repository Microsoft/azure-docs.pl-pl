---
title: Włącz i pracuj z dziennikami zasobów usługi Azure bastionu
description: W tym artykule dowiesz się, jak włączyć i korzystać z dzienników diagnostycznych usługi Azure bastionu.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 1e76fffd17ee565d4103ca8a7bf1523bbd16209d
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445377"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>Włączanie dzienników zasobów usługi Bastion i praca z nimi

Gdy użytkownicy łączą się z obciążeniami przy użyciu usługi Azure bastionu, usługa bastionu może rejestrować diagnostykę sesji zdalnych. Następnie można użyć diagnostyki, aby zobaczyć, którzy użytkownicy połączeni z tymi obciążeniami, w jakim czasie, od miejsca i inne informacje dotyczące rejestrowania. Aby móc korzystać z diagnostyki, należy włączyć dzienniki diagnostyczne na platformie Azure bastionu. Ten artykuł ułatwia włączenie dzienników diagnostycznych, a następnie wyświetlenie dzienników.

## <a name="enable-the-resource-log"></a><a name="enable"></a>Włącz dziennik zasobów

1. W [Azure Portal](https://portal.azure.com)przejdź do zasobu Azure bastionu, a następnie wybierz pozycję **Ustawienia diagnostyki** na stronie bastionu platformy Azure.

   ![Zrzut ekranu przedstawiający stronę "Ustawienia diagnostyki".](./media/diagnostic-logs/1diagnostics-settings.png)
2. Wybierz pozycję **Ustawienia diagnostyczne**, a następnie wybierz pozycję **+ Dodaj ustawienie diagnostyczne** , aby dodać miejsce docelowe dla dzienników.

   ![Zrzut ekranu przedstawiający stronę "Ustawienia diagnostyki" z wybranym przyciskiem "Dodaj ustawienie diagnostyczne".](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Na stronie **Ustawienia diagnostyki** wybierz typ konta magazynu, które ma być używane do przechowywania dzienników diagnostycznych.

   ![Zrzut ekranu przedstawiający stronę "Ustawienia diagnostyki" z sekcją wybierz wyróżnioną lokalizację magazynu.](./media/diagnostic-logs/3add-storage-account.png)
4. Po zakończeniu ustawień będzie wyglądać podobnie do tego przykładu:

   ![Przykładowe ustawienia](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Wyświetl dziennik diagnostyki

Aby uzyskać dostęp do dzienników diagnostycznych, możesz bezpośrednio użyć konta magazynu określonego podczas włączania ustawień diagnostycznych.

1. Przejdź do zasobu konta magazynu, a następnie do **kontenerów**. Zobaczysz obiekt BLOB **Insights-Logs-bastionauditlogs** utworzony w kontenerze obiektów BLOB konta magazynu.

   ![Ustawienia diagnostyki](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Gdy przejdziesz do wnętrza kontenera, zobaczysz różne foldery w blogu. Te foldery wskazują hierarchię zasobów dla zasobu usługi Azure bastionu.

   ![Dodaj ustawienie diagnostyczne](./media/diagnostic-logs/2-resource-h.png)
3. Przejdź do pełnej hierarchii zasobu usługi Azure bastionu, której dzienniki diagnostyki chcesz uzyskać dostęp/wyświetlić. Wartości "y =", "-", "a =", "h =" i "=" wskazują odpowiednio rok, miesiąc, dzień, godzinę i minutę dla dzienników zasobów.

   ![Wybierz lokalizację magazynu](./media/diagnostic-logs/3-resource-location.png)
4. Znajdź plik JSON utworzony przez usługę Azure bastionu, który zawiera dane dziennika diagnostyki dla przedziału czasu, do którego przeszedł okres.

5. Pobierz plik JSON z kontenera magazynu obiektów BLOB. Poniżej przedstawiono przykładowy wpis z pliku JSON:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [bastionu często zadawanych pytań](bastion-faq.md).
