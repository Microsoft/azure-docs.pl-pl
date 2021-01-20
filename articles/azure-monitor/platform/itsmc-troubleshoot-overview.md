---
title: Rozwiązywanie problemów w łączniku ITSM
description: Rozwiązywanie problemów z łącznik zarządzania usługami IT
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a4a7b7a4008d5cc4636e2d533c225a618f35af05
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611189"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Rozwiązywanie problemów w łączniku ITSM

W tym artykule omówiono typowe problemy w łącznik ITSM i sposoby rozwiązywania problemów z nimi.

Alerty Azure Monitor z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Umożliwiają identyfikowanie i rozwiązywanie problemów przed zapisaniem ich przez użytkowników systemu. Aby uzyskać więcej informacji na temat alertów, zobacz Omówienie alertów w Microsoft Azure.
Klient może wybrać, jak chcą otrzymywać powiadomienia dotyczące alertu, niezależnie od tego, czy jest on wysyłany pocztą, wiadomości SMS, webhook, czy też do automatyzowania rozwiązania. Kolejną opcją powiadomienia jest użycie narzędzia ITSM.
NARZĘDZIA ITSM umożliwia wysyłanie alertów do zewnętrznego systemu biletów, takiego jak usługi ServiceNow.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Wizualizuj i analizuj dane zdarzenia i żądania zmiany

W zależności od konfiguracji podczas konfigurowania połączenia ITSMC może synchronizować do 120 dni zdarzenia i dane żądania zmiany. Schemat rekordów dziennika dla tych danych znajduje się w [sekcji dodatkowe informacje](./itsmc-synced-data.md) w tym artykule.

Możesz wizualizować dane zdarzenia i żądania zmiany za pomocą pulpitu nawigacyjnego ITSMC:

![Zrzut ekranu przedstawiający pulpit nawigacyjny ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Pulpit nawigacyjny zawiera również informacje o stanie łącznika, którego można użyć jako punktu wyjścia do analizowania problemów z połączeniami.

Aby uzyskać więcej informacji na temat badania pulpitu nawigacyjnego, zobacz [Badanie błędów przy użyciu pulpitu nawigacyjnego](./itsmc-dashboard.md).

### <a name="service-map"></a>Mapa usługi

Możesz również wizualizować zdarzenia zsynchronizowane z komputerami, których dotyczy problem, w Service Map.

Service Map automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Umożliwia wyświetlanie Twoich serwerów w miarę ich działania: jako połączone systemy, które dostarczają usługi o kluczowym znaczeniu. Service Map przedstawia połączenia między serwerami, procesami i portami w ramach dowolnej architektury połączonej z protokołem TCP. Poza instalacją agenta nie jest wymagana żadna konfiguracja. Aby uzyskać więcej informacji, zobacz [używanie Service map](../insights/service-map.md).

Jeśli używasz Service Map, możesz wyświetlić elementy pomocy technicznej utworzone w rozwiązaniach narzędzia ITSM, jak pokazano poniżej:

![Zrzut ekranu przedstawiający ekran Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="troubleshoot-itsm-connections"></a>Rozwiązywanie problemów z połączeniami narzędzia ITSM

- Jeśli połączenie z systemem narzędzia ITSM nie powiedzie się i **wystąpi błąd podczas zapisywania komunikatu połączenia** , wykonaj następujące czynności:
   - Dla połączeń usługi ServiceNow, Cherwell i Provance:  
     - Upewnij się, że poprawnie wprowadzono nazwę użytkownika, hasło, identyfikator klienta i klucz tajny klienta dla każdego połączenia.  
     - Upewnij się, że masz wystarczające uprawnienia do odpowiedniego produktu narzędzia ITSM, aby nawiązać połączenie.  
   - Dla połączeń Service Manager:  
     - Upewnij się, że aplikacja sieci Web została pomyślnie wdrożona, a połączenie hybrydowe zostało utworzone. Aby sprawdzić, czy połączenie zostało pomyślnie nawiązane z lokalnym komputerem Service Manager, przejdź do adresu URL aplikacji sieci Web, zgodnie z opisem w dokumentacji dotyczącej tworzenia [połączenia hybrydowego](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

- Jeśli Log Analytics alerty są wyzwalane, ale elementy robocze nie są tworzone w produkcie narzędzia ITSM, jeśli elementy konfiguracji nie są tworzone/połączone z elementami roboczymi lub innych informacji, zobacz następujące zasoby:
   -  ITSMC: rozwiązanie pokazuje [Podsumowanie połączeń](itsmc-dashboard.md), elementów roboczych, komputerów i nie tylko. Wybierz kafelek z etykietą **stanu łącznika** . Wykonanie tej czynności spowoduje przeprowadzenie **rejestrowania wyszukiwania** przy użyciu odpowiedniego zapytania. `LogType_S`Aby uzyskać więcej informacji, zobacz rekordy dziennika z a `ERROR` .
   Szczegółowe informacje o komunikatach znajdują się w [tabeli.](itsmc-dashboard-errors.md)
   - Strona **przeszukiwania dzienników** : Wyświetl błędy i powiązane informacje bezpośrednio przy użyciu zapytania `*ServiceDeskLog_CL*` .

## <a name="common-symptoms---how-should-it-be-resolved"></a>Często spotykane objawy — jak należy je rozwiązać?

Poniższa lista zawiera typowe objawy i sposób ich rozwiązywania:

* **Objaw**: utworzono zduplikowane elementy robocze

    **Przyczyna**: Przyczyna może być jedną z dwóch opcji:
    * Dla tego alertu zdefiniowano więcej niż jedną akcję narzędzia ITSM.
    * Alert został rozwiązany.

    **Rozwiązanie**: dostępne są dwa rozwiązania:
    * Upewnij się, że masz pojedynczą narzędzia ITSM grupę akcji dla każdego alertu.
    * Łącznik ITSM nie obsługuje aktualizacji stanu pasujących elementów roboczych po rozwiązaniu alertu. Zostanie utworzony nowy rozpoznany element roboczy.
* **Objaw**: elementy robocze nie są tworzone

    **Przyczyna**: może istnieć kilka przyczyn dla tego objawu:
    * Modyfikowanie kodu po stronie usługi ServiceNow
    * Niepozwolenia konfiguracja uprawnień
    * Limity szybkości usługi ServiceNow są zbyt wysokie/niskie
    * Token odświeżania wygasł
    * łącznik ITSM został usunięty

    **Rozwiązanie**: można sprawdzić [pulpit nawigacyjny](itsmc-dashboard.md) i przejrzeć błędy w sekcji stan łącznika. Przejrzyj [typowe błędy](itsmc-dashboard-errors.md) i Dowiedz się, jak rozwiązać ten problem.

* **Objaw**: nie można utworzyć akcji narzędzia ITSM dla grupy akcji

    **Przyczyna**: nowo utworzona łącznik ITSM nie mogła ukończyć synchronizacji początkowej.

    **Rozwiązanie**: można przejrzeć [typowe błędy interfejsu użytkownika](itsmc-dashboard-errors.md#ui-common-errors) i dowiedzieć się, jak rozwiązać ten problem.