---
title: Azure AD Connect Health — dane usługi kondycji są nieaktualne Microsoft Docs
description: W tym dokumencie opisano przyczynę alertu "dane usługi kondycji są nieaktualne" i jak rozwiązać ten problem.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d1d87d9b576a8e181b5b339052a6b6512f18a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85359232"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Dane usługi kondycji są nieaktualne

## <a name="overview"></a>Omówienie

Agenci na maszynach lokalnych, którzy Azure AD Connect Health monitoruje okresowo przekazywać dane do usługi Azure AD Connect Health. Jeśli usługa nie odbiera danych od agenta, informacje prezentowane przez portal będą przestarzałe. Aby wyróżnić problem, usługa zgłosi **dane usługi kondycji, które nie są aktualne** . Ten alert jest generowany, gdy usługa nie otrzymała pełnych danych w ciągu ostatnich dwóch godzin.  

- Alert stanu **ostrzegawczego** jest uruchamiany, jeśli usługa kondycji odebrał tylko **częściowe** typy danych wysłane z serwera w ciągu ostatnich dwóch godzin. Alert stanu ostrzegawczego nie powoduje wyzwolenia powiadomień e-mail dotyczących skonfigurowanych adresatów. 
- Alert stanu **błędu** jest uruchamiany, jeśli usługa kondycji nie odebrał żadnych typów danych z serwera w ciągu ostatnich dwóch godzin. Alert stanu błędu wyzwala powiadomienia e-mail dla skonfigurowanych adresatów.

Usługa pobiera dane z agentów, które są uruchomione na maszynach lokalnych, w zależności od typu usługi. W poniższej tabeli wymieniono agentów uruchomionych na komputerze, co robią, oraz typów danych generowanych przez usługę. W niektórych przypadkach w procesie występuje wiele usług, więc każdy z nich może być przyczyna. 

## <a name="understanding-the-alert"></a>Informacje o alercie

Blok **szczegóły alertu** pokazuje, kiedy wystąpiło i ostatnio wykryto alert. Proces w tle, który jest uruchamiany co dwie godziny, generuje i ponownie oblicza alert. W poniższym przykładzie początkowy alert wystąpił w dniu 03/10 o 9:59 AM. Alert nadal istniał w dniu 03/12 o godzinie 10:00, gdy alert został ponownie oceniony. Blok zawiera również informacje o czasie, który Usługa kondycji ostatnio odebrał określonego typu danych. 
 
 ![Szczegóły alertu Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
W poniższej tabeli przedstawiono typy usług odpowiadające wymaganym typom danych:

| Typ usługi | Agent (nazwa usługi systemu Windows) | Przeznaczenie | Wygenerowano typ danych  |
| --- | --- | --- | --- |  
| Azure AD Connect (synchronizacja) | Usługa szczegółowych informacji synchronizacji programu Azure AD Connect Health | Zbierz informacje specyficzne dla połączenia usługi AAD (łączniki, reguły synchronizacji itp.) | -AadSyncService-SynchronizationRules <br />  -AadSyncService-Connectors <br /> -AadSyncService-GlobalConfigurations  <br />  -AadSyncService-RunProfileResults <br /> -AadSyncService-ServiceConfigurations <br /> -AadSyncService-ServiceStatus   |
|  | Usługa monitorowania synchronizacji programu Azure AD Connect Health | Zbieranie liczników wydajności specyficznych dla połączenia usługi AAD, śledzenia ETW, plików | Licznik wydajności |
| AD DS | Usługa szczegółowych informacji usług AD DS programu Azure AD Connect Health | Wykonywanie testów syntetycznych, zbieranie informacji o topologii, metadane replikacji |  -Dodaje-TopologyInfo-JSON <br /> -Common-TestData-JSON (tworzy wyniki testu)   | 
|  | Usługa monitorowania usług AD DS programu Azure AD Connect Health | Zbieranie danych liczników wydajności, śledzenia ETW, plików | -Licznik wydajności  <br /> -Common-TestData-JSON (przekazuje wyniki testu)  |
| AD FS | Usługa diagnostyczna usług AD FS programu Azure AD Connect Health | Wykonaj testy syntetyczne | TestResult (tworzy wyniki testu) | 
| | Usługa szczegółowych informacji usług AD FS programu Azure AD Connect Health  | Zbieranie metryk użycia usług ADFS | Adfs-UsageMetrics |
| | Usługa monitorowania usług AD FS programu Azure AD Connect Health | Zbieranie liczników wydajności dotyczących usług AD FS, śledzenia ETW, plików | TestResult (przekazuje wyniki testu) |

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów 

Kroki wymagane do zdiagnozowania problemu są podane poniżej. Pierwszy to zestaw podstawowych testów, które są wspólne dla wszystkich typów usług. 

> [!IMPORTANT] 
> Ten alert następuje po nawiązaniu połączenia z [zasadami przechowywania danych](reference-connect-health-user-privacy.md#data-retention-policy) dotyczących kondycji

* Upewnij się, że zainstalowano najnowsze wersje agentów. Wyświetl [historię wersji](reference-connect-health-version-history.md). 
* Upewnij się, że usługi agentów Azure AD Connect Health są **uruchomione** na komputerze. Na przykład program Connect Health dla usługi AD FS powinien mieć trzy usługi.
  ![Weryfikowanie programu Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Upewnij się, że przejdziesz do [sekcji wymagania](how-to-connect-health-agent-install.md#requirements)i spełnisz ją.
* Za pomocą [Narzędzia do testowania połączeń](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) można wykrywać problemy z łącznością.
* Jeśli masz serwer proxy HTTP, wykonaj następujące [czynności konfiguracyjne](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Następne kroki
Jeśli którykolwiek z powyższych kroków zidentyfikował problem, napraw go i poczekaj na jego rozwiązanie. Proces w tle alertu jest uruchamiany co 2 godziny, więc rozwiązanie alertu będzie trwać do 2 godzin. 

* [Zasady przechowywania danych Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
