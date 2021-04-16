---
title: Konserwacja zaplanowana — Azure Database for PostgreSQL — elastyczny serwer
description: W tym artykule opisano funkcję konserwacji zaplanowanej w Azure Database for PostgreSQL — elastyczny serwer.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 50ef040f1cb7d8c533ec5ee31e9bffa2e6dca2f5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478016"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Konserwacja zaplanowana w usłudze Azure Database for PostgreSQL — serwer elastyczny
 
Azure Database for PostgreSQL — elastyczny serwer przeprowadza okresową konserwację, aby zapewnić bezpieczeństwo, stabilność i aktualny dostęp do zarządzanej bazy danych. Podczas konserwacji serwer otrzymuje nowe funkcje, aktualizacje i poprawki.
 
> [!IMPORTANT]
> Azure Database for PostgreSQL — elastyczny serwer jest w wersji zapoznawczej
 
## <a name="selecting-a-maintenance-window"></a>Wybieranie okna obsługi
 
Konserwację można zaplanować w określonym dniu tygodnia i w danym okresie. Możesz też pozwolić systemowi na automatyczne wybieranie dnia i czasu w oknie czasowym. W obu sposób system powiadomi Cię o pięciu dniach przed uruchomieniem jakiejkolwiek konserwacji. System będzie również wiedzieć, kiedy konserwacja została uruchomiona i kiedy została pomyślnie ukończona.
 
Powiadomienia o nadchodzącej zaplanowanej konserwacji mogą być:
 
* Wiadomość e-mail na określony adres
* Wiadomość e-mail do Azure Resource Manager roli
* Wysyłane w wiadomości SMS do urządzeń przenośnych
* wypychane jako powiadomienie do aplikacji platformy Azure
* dostarczone jako wiadomość głosowa
 
Określając preferencje dla harmonogramu konserwacji, można wybrać dzień tygodnia i przedział czasu. Jeśli nie zostanie to określone, system wybierze godzinę między 23:00 a 7:00 czasu regionu serwera. Dla każdego serwera elastycznego w subskrypcji platformy Azure można zdefiniować różne harmonogramy. 
 
> [!IMPORTANT]
> Zwykle między pomyślnymi zdarzeniami konserwacji zaplanowanej dla serwera istnieje odstęp co najmniej 30 dni.
>
> Jednak w przypadku krytycznej aktualizacji awaryjnej, takiej jak poważna luka w zabezpieczeniach, okno powiadomień może być krótsze, niż pięć dni. Aktualizacja krytyczna może zostać zastosowana do serwera, nawet jeśli konserwacja zaplanowana została wykonana w ciągu ostatnich 30 dni.

Ustawienia planowania można zaktualizować w dowolnym momencie. Jeśli zaplanowano konserwację serwera elastycznego i zaktualizujemy preferencje planowania, bieżące uaktualnienie będzie przebiegać zgodnie z harmonogramem, a zmiany ustawień planowania zaobejdą się po pomyślnym zakończeniu następnej zaplanowanej konserwacji.

Dla każdego serwera elastycznego w subskrypcji platformy Azure można zdefiniować harmonogram zarządzany przez system lub harmonogram niestandardowy.  
* Za pomocą harmonogramu niestandardowego można określić okno obsługi dla serwera, wybierając dzień tygodnia i godzinny okres.  
* W przypadku harmonogramu zarządzanego przez system system wybierze dowolne jednogodzinne okno między godziną 23:00 a 7:00 w czasie regionu serwera.  

W ramach wycofywania zmian stosujemy aktualizacje do serwerów skonfigurowanych przy użyciu harmonogramu zarządzanego przez system, a następnie serwerów z harmonogramem niestandardowym po minimalnej przerwie 7 dni w danym regionie. Jeśli zamierzasz otrzymywać wczesne aktualizacje floty serwerów środowisk deweloperskiej i testowej, zalecamy skonfigurowanie harmonogramu zarządzanego przez system dla serwerów używanych w środowisku deweloperskiej i testowym. Pozwoli to najpierw uzyskać najnowszą aktualizację w środowisku tworzenia i testowania w celu testowania i oceny na temat walidacji. Jeśli napotkasz jakiekolwiek zachowanie lub zmiany, będziesz mieć czas na ich rozwiązania, zanim ta sama aktualizacja zostanie wdowa na serwerach produkcyjnych z niestandardowym harmonogramem zarządzanym. Aktualizacja zaczyna być stosowana na serwerach elastycznych z niestandardowym harmonogramem po 7 dniach i jest stosowana do serwera w zdefiniowanym oknie obsługi. Obecnie nie ma możliwości odroczyć aktualizacji po wysłaniu powiadomienia. Harmonogram niestandardowy jest zalecany tylko w środowiskach produkcyjnych. 

W rzadkich przypadkach system może anulować zdarzenie konserwacji lub jego pomyślne zakończenie może zakończyć się niepowodzeniem. Jeśli aktualizacja nie powiedzie się, aktualizacja zostanie przywrócona, a poprzednia wersja plików binarnych zostanie przywrócona. W takich scenariuszach aktualizacji, które zakończyły się niepowodzeniem, w oknie obsługi może nadal wystąpić ponowne uruchomienie serwera. Jeśli aktualizacja zostanie anulowana lub nie powiedzie się, system utworzy odpowiednio powiadomienie o anulowanym lub nieudanym zdarzeniu konserwacji. Następna próba przeprowadzenia konserwacji zostanie zaplanowana zgodnie z bieżącymi ustawieniami planowania i otrzymasz powiadomienie o tym z pięciodniowego wyprzedzenia. 

 
## <a name="next-steps"></a>Następne kroki
 
* Dowiedz się, jak [zmienić harmonogram konserwacji](how-to-maintenance-portal.md)
* Dowiedz się, jak [otrzymywać powiadomienia o nadchodzącej konserwacji przy](../../service-health/service-notifications.md) użyciu Azure Service Health
* Dowiedz się, jak [skonfigurować alerty dotyczące nadchodzących zaplanowanych zdarzeń konserwacji](../../service-health/resource-health-alert-monitor-guide.md)
