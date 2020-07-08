---
title: Jak monitorować udział danych platformy Azure
description: Dowiedz się, jak monitorować stan zaproszenia, udostępniać subskrypcje i historię migawek w udziale danych platformy Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a3d13919a54e170d410abba5da66172c2029d9b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636278"
---
# <a name="monitor-azure-data-share"></a>Monitorowanie usługi Azure Data Share  

W tym artykule wyjaśniono, jak można monitorować udziały danych za pomocą udziału danych platformy Azure. Jako dostawca danych możesz monitorować różne aspekty relacji udostępniania danych. Szczegóły, takie jak to, czy konsumenci danych zaakceptowali zaproszenie do udziału danych, a także czy utworzyły subskrypcję udziału i rozpoczęto korzystanie z danych, są dostępne do monitorowania. 

Jako odbiorca danych możesz monitorować migawki, które zostały wyzwolone w ramach subskrypcji platformy Azure. 

## <a name="monitor-invitation-status"></a>Monitoruj stan zaproszenia

Wyświetl stan zaproszeń udziału danych, przechodząc do obszaru wysłane udziały — > zaproszenia. 

![Stan zaproszenia](./media/invitation-status.png "Stan zaproszenia") 

Istnieją trzy stany, w których może znajdować się zaproszenie:

* Oczekiwanie — odbiorca udziału danych nie zaakceptował jeszcze zaproszenia.
* Zaakceptowano — odbiorca udziału danych zaakceptował zaproszenie.
* Odrzucone — odbiorca udziału danych odrzucił zaproszenie.

> [!IMPORTANT]
> Po usunięciu zaproszenia, które już zostało zaakceptowane, nie jest równoznaczne z cofnięciem dostępu. Jeśli chcesz zatrzymać kopiowanie przyszłych migawek do konta magazynu odbiorców danych, musisz odwołać dostęp za pośrednictwem karty *udostępnianie subskrypcji* . 

## <a name="monitor-share-subscriptions"></a>Monitoruj subskrypcje udziałów

Wyświetl stan subskrypcji udziałów, przechodząc do obszaru wysłane udziały — > udostępnić subskrypcje. Umożliwi to podanie szczegółowych informacji o aktywnych subskrypcjach utworzonych przez odbiorców danych po zaakceptowaniu zaproszenia. Możesz zatrzymać przyszłe aktualizacje dla konsumenta danych, wybierając pozycję Udostępnij subskrypcję i wybierając pozycję *odwołaj*. 

## <a name="snapshot-history"></a>Historia migawek 

Na karcie **historia** udziału można zobaczyć, kiedy dane są kopiowane z dostawcy danych do magazynu danych odbiorcy danych. Możesz monitorować częstotliwość, czas trwania i stan każdej migawki. 

![Historia migawek](./media/sent-shares.png "Historia migawek") 

Aby wyświetlić więcej szczegółów na temat poszczególnych migawek, kliknij datę rozpoczęcia przebiegu. Następnie kliknij stan każdego zestawu danych, aby wyświetlić ilość transferowanych danych, liczbę skopiowanych plików/rekordów, czas trwania migawki, liczbę użytych rdzeni wirtualnych i komunikat o błędzie, jeśli istnieje. 

Zostanie wyświetlona historia migawek do 30 dni. Jeśli musisz zapisać i zobaczyć więcej niż 30 dni historii, możesz użyć ustawienia diagnostycznego.

## <a name="diagnostic-setting"></a>Ustawienie diagnostyczne

Można skonfigurować ustawienie diagnostyczne, aby zapisywać dane lub zdarzenia dziennika. Przejdź do opcji monitorowanie — > ustawienia diagnostyczne i wybierz pozycję **Dodaj ustawienie diagnostyczne**. Wybierz dane lub zdarzenia dziennika, które Cię interesują, i miejsce, w którym mają być przechowywane lub wysyłane. 

![Historia migawek](./media/diagnostic-settings.png "Ustawienia diagnostyczne") 

## <a name="next-steps"></a>Następne kroki 

Dowiedz się więcej o [terminologii dotyczącej udziałów danych platformy Azure](terminology.md)