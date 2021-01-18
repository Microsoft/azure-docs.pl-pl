---
title: Rozwiązywanie problemów w łączniku ITSM
description: Rozwiązywanie problemów z łącznik zarządzania usługami IT
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 14f1056bf761eb7b591d04db34610468058bc255
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562867"
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
   -  ITSMC: rozwiązanie pokazuje podsumowanie połączeń, elementów roboczych, komputerów i nie tylko. Wybierz kafelek z etykietą **stanu łącznika** . Wykonanie tej czynności spowoduje przeprowadzenie **rejestrowania wyszukiwania** przy użyciu odpowiedniego zapytania. `LogType_S`Aby uzyskać więcej informacji, zobacz rekordy dziennika z a `ERROR` .
   - Strona **przeszukiwania dzienników** : Wyświetl błędy i powiązane informacje bezpośrednio przy użyciu zapytania `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Rozwiązywanie problemów z wdrażaniem aplikacji sieci Web Service Manager

-   Jeśli masz problemy z wdrażaniem aplikacji sieci Web, upewnij się, że masz uprawnienia do tworzenia/wdrażania zasobów w ramach subskrypcji.
-   Jeśli podczas uruchamiania [skryptu](itsmc-service-manager-script.md)otrzymasz **odwołanie do obiektu, które nie jest ustawione na wystąpienie błędu obiektu** , upewnij się, że wprowadzono prawidłowe wartości w sekcji **Konfiguracja użytkownika** .
-   Jeśli nie można utworzyć przestrzeni nazw usługi Service Bus Relay, upewnij się, że wymagany dostawca zasobów jest zarejestrowany w subskrypcji. Jeśli nie jest ona zarejestrowana, należy ręcznie utworzyć przestrzeń nazw usługi Service Bus Relay z Azure Portal. Można go również utworzyć podczas [tworzenia połączenia hybrydowego](./itsmc-connections-scsm.md#configure-the-hybrid-connection) w Azure Portal.