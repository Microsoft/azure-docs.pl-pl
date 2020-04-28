---
title: Jak monitorować udział danych platformy Azure
description: Dowiedz się, jak monitorować stan zaproszenia, udostępniać subskrypcje i historię migawek w udziale danych platformy Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73490512"
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

Na karcie Historia można wyświetlić migawki, które zostały skopiowane do dzierżawy użytkownika danych. Można monitorować częstotliwość i czas trwania każdego interwału migawek. 

![Historia migawek](./media/sent-shares.png "Historia migawek") 

Aby wyświetlić więcej szczegółów na temat poszczególnych migawek, kliknij datę rozpoczęcia przebiegu. 

Domyślnie zostanie wyświetlona historia migawek do 30 dni. Jeśli potrzebujesz więcej niż 30 dni historii, przejdź do obszaru monitorowanie — > ustawienia diagnostyczne i wybierz pozycję **Dodaj ustawienie diagnostyczne**. Użytkownik musi wybrać konto magazynu, w którym będą przechowywane te dzienniki. 

![Historia migawek](./media/diagnostic-settings.png "Ustawienia diagnostyczne") 

## <a name="next-steps"></a>Następne kroki 

Dowiedz się więcej o [terminologii dotyczącej udziałów danych platformy Azure](terminology.md)