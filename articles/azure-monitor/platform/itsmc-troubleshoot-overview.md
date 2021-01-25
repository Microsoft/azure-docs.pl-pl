---
title: Rozwiązywanie problemów w łączniku ITSM
description: Rozwiązywanie problemów z łącznik zarządzania usługami IT
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: e43c5fb36c5395e12fd0b9c2c67b787a1137f5d0
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761978"
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

## <a name="common-symptoms---how-should-it-be-resolved"></a>Często spotykane objawy — jak należy je rozwiązać?

Poniższa lista zawiera typowe objawy i sposób ich rozwiązywania:

* **Objaw**: Jeśli połączenie z systemem narzędzia ITSM nie powiedzie się i **wystąpi błąd podczas zapisywania komunikatu połączenia** .

    **Przyczyna**: Przyczyna może być jedną z opcji:
    * Nieprawidłowe poświadczenia
     * Niewystarczające uprawnienia
     * Aplikacja sieci Web powinna zostać wdrożona prawidłowo

    **Rozwiązanie**:
    * Dla połączeń usługi ServiceNow, Cherwell i Provance:
        * Upewnij się, że poprawnie wprowadzono nazwę użytkownika, hasło, identyfikator klienta i klucz tajny klienta dla każdego połączenia.  
        * W przypadku usługi ServiceNow: Upewnij się, że masz wystarczające uprawnienia w odpowiednim produkcie narzędzia ITSM, aby nawiązać połączenie w [określony](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)sposób.
  * Dla połączeń Service Manager:  
      * Upewnij się, że aplikacja sieci Web została pomyślnie wdrożona, a połączenie hybrydowe zostało utworzone. Aby sprawdzić, czy połączenie zostało pomyślnie nawiązane z lokalnym komputerem Service Manager, przejdź do adresu URL aplikacji sieci Web, zgodnie z opisem w dokumentacji dotyczącej tworzenia [połączenia hybrydowego](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  
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