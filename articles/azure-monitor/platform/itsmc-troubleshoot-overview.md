---
title: Rozwiązywanie problemów z ITSMC
description: Dowiedz się, jak rozwiązywać typowe problemy w łącznik zarządzania usługami IT.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: e8ae306a4900bc6e5815f6fc251dfa1b8b22964d
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492420"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>Rozwiązywanie problemów z łącznik zarządzania usługami IT

W tym artykule omówiono typowe problemy w programie łącznik zarządzania usługami IT (ITSMC) oraz sposoby rozwiązywania problemów z nimi.

Azure Monitor proaktywnie powiadamia w alertach o znalezieniu ważnych warunków w danych monitorowania. Te alerty ułatwiają identyfikowanie i rozwiązywanie problemów przed zapisaniem ich przez użytkowników systemu.

Możesz wybrać sposób otrzymywania alertów. Możesz wybrać opcję Poczta, wiadomość SMS lub element webhook, a nawet zautomatyzować rozwiązanie. 

Alternatywą jest powiadomienie za poorednictwem ITSMC. ITSMC umożliwia wysyłanie alertów do zewnętrznego systemu obsługi biletów, takiego jak usługi ServiceNow.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Korzystanie z pulpitu nawigacyjnego do analizowania danych zdarzenia i żądania zmiany

W zależności od konfiguracji podczas konfigurowania połączenia ITSMC może synchronizować do 120 dni zdarzenia i dane żądania zmiany. Aby uzyskać schemat rekordów dziennika dla tych danych, zapoznaj się [z artykułem synchronizowanie danych z produktu narzędzia ITSM](./itsmc-synced-data.md) .

Możesz wizualizować dane zdarzenia i żądania zmiany za pomocą pulpitu nawigacyjnego ITSMC:

![Zrzut ekranu przedstawiający pulpit nawigacyjny ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Pulpit nawigacyjny zawiera również informacje o stanie łącznika. Tych informacji można użyć jako punktu wyjścia do analizowania problemów z połączeniami. Aby uzyskać więcej informacji, zobacz [Badanie błędów przy użyciu pulpitu nawigacyjnego](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Użyj Service Map do wizualizacji zdarzeń

Możesz również wizualizować zdarzenia zsynchronizowane z komputerami, których dotyczy problem, w Service Map.

Service Map automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Umożliwia wyświetlanie Twoich serwerów w miarę ich działania: jako połączone systemy, które dostarczają usługi o kluczowym znaczeniu. 

Service Map przedstawia połączenia między serwerami, procesami i portami w ramach dowolnej architektury połączonej z protokołem TCP. Poza instalacją agenta nie jest wymagana żadna konfiguracja. Aby uzyskać więcej informacji, zobacz [używanie Service map](../insights/service-map.md).

Jeśli używasz Service Map, możesz wyświetlić elementy pomocy technicznej utworzone w rozwiązaniach do zarządzania usługami IT (narzędzia ITSM), jak pokazano w tym przykładzie:

![Zrzut ekranu przedstawiający ekran Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Rozwiązywanie problemów

W poniższych sekcjach zidentyfikowano typowe objawy, możliwe przyczyny i rozwiązania. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>Połączenie z systemem narzędzia ITSM kończy się niepowodzeniem i jest wyświetlany komunikat "Wystąpił błąd podczas zapisywania połączenia"

**Przyczyna**: Przyczyna może być jedną z następujących opcji:

* Poświadczenia są nieprawidłowe.
* Niewystarczające uprawnienia.
* Aplikacja sieci Web została niepoprawnie wdrożona.

**Rozwiązanie**:

* Dla połączeń usługi ServiceNow, Cherwell i Provance:
  * Upewnij się, że poprawnie wprowadzono nazwę użytkownika, hasło, identyfikator klienta i klucz tajny klienta dla każdego połączenia.  
  * W przypadku usługi ServiceNow upewnij się, że masz [wystarczające uprawnienia](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) w odpowiednim produkcie narzędzia ITSM.

* Dla połączeń Service Manager:  
  * Upewnij się, że aplikacja sieci Web została pomyślnie wdrożona, a połączenie hybrydowe zostało utworzone. Aby sprawdzić, czy połączenie zostało pomyślnie nawiązane z lokalnym komputerem Service Manager, przejdź do adresu URL aplikacji sieci Web, zgodnie z opisem w [dokumentacji dotyczącej tworzenia połączenia hybrydowego](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

### <a name="duplicate-work-items-are-created"></a>Utworzono zduplikowane elementy robocze

**Przyczyna**: Przyczyna może być jedną z następujących dwóch opcji:

* Dla alertu zdefiniowano więcej niż jedną akcję narzędzia ITSM.
* Alert został rozwiązany.

**Rozwiązanie**: dostępne są dwa rozwiązania:

* Upewnij się, że masz pojedynczą narzędzia ITSM grupę akcji dla każdego alertu.
* ITSMC nie obsługuje aktualizacji stanu pasujących elementów roboczych podczas rozwiązywania alertu. Utwórz nowy rozpoznany element roboczy.

### <a name="work-items-are-not-created"></a>Elementy robocze nie są tworzone

**Przyczyna**: może istnieć kilka przyczyn tego symptomu:

* Kod został zmodyfikowany po stronie usługi ServiceNow.
* Uprawnienia są nieprawidłowo skonfigurowane.
* Limity szybkości usługi ServiceNow są zbyt duże lub zbyt niskie.
* Token odświeżania wygasł.
* ITSMC został usunięty.

**Rozwiązanie**: Sprawdź [pulpit nawigacyjny](itsmc-dashboard.md) i Przejrzyj błędy w sekcji stan łącznika. Następnie przejrzyj [typowe błędy i ich rozwiązania](itsmc-dashboard-errors.md).

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>Nie można utworzyć akcji narzędzia ITSM dla grupy akcji

**Przyczyna**: nowo utworzone wystąpienie ITSMC jeszcze raz zakończyło synchronizację początkową.

**Rozwiązanie**: Przejrzyj [typowe błędy i ich rozwiązania](itsmc-dashboard-errors.md).