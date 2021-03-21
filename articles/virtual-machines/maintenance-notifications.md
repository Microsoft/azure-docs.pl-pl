---
title: Powiadomienia dotyczące konserwacji
description: Omówienie powiadomień dotyczących konserwacji dla maszyn wirtualnych działających na platformie Azure.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 8/12/2020
ms.author: shants
ms.openlocfilehash: 92cb780a80f1010fd1c2f5d19fe616e0285de73b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564530"
---
# <a name="handling-planned-maintenance-notifications"></a>Obsługa powiadomień dotyczących planowanej konserwacji

Platforma Azure jest co pewien czas aktualizowana w celu poprawy niezawodności, wydajności i zabezpieczeń infrastruktury hostowania maszyn wirtualnych. Aktualizacje są zmianami, takimi jak poprawianie środowiska hostingu lub uaktualnianie i likwidowanie sprzętu. Większość tych aktualizacji jest przeprowadzana bez żadnego wpływu na hostowane maszyny wirtualne. Istnieją jednak przypadki, w których aktualizacje mają wpływ:

- Jeśli konserwacja nie wymaga ponownego uruchomienia, platforma Azure wstrzymuje maszynę wirtualną przez kilka sekund, gdy host zostanie zaktualizowany. Te typy operacji konserwacyjnych są stosowane w domenie błędów przez domenę błędów. Postęp jest zatrzymany w przypadku odebrania ostrzeżeń o ostrzeżeniach o kondycji.

- Jeśli konserwacja wymaga ponownego uruchomienia, otrzymasz powiadomienie o zaplanowaniu konserwacji. Otrzymujesz przedział czasu o około 35 dni, w którym możesz samodzielnie rozpocząć konserwację, gdy będzie ona działać.


Planowana konserwacja wymagająca ponownego uruchomienia jest zaplanowana na fale. Każda fala ma inny zakres (regiony).

- Fala rozpoczyna się od powiadomienia do klientów. Powiadomienie jest domyślnie wysyłane do administratora subskrypcji i współadministratorów. Za pomocą [alertów dziennika aktywności](../service-health/alerts-activity-log-service-notifications-portal.md)można dodać więcej opcji adresatów i komunikatów, takich jak wiadomości e-mail, wiadomości SMS i elementy webhook.  
- Po przeprowadzeniu powiadomienia zostanie udostępnione *okno* samoobsługowe. W tym oknie możesz wykonywać zapytania dotyczące maszyn wirtualnych, których dotyczy ta operacja, i rozpocząć konserwację na podstawie własnych potrzeb związanych z planowaniem. Okno samoobsługowe jest zwykle około 35 dni.
- Po włączeniu okna samoobsługowego zostanie rozpoczęte *zaplanowane okno obsługi* . W pewnym momencie w tym oknie usługa Azure planuje i stosuje wymaganą konserwację na maszynie wirtualnej. 

Celem dwóch okien jest udostępnienie wystarczającej ilości czasu na rozpoczęcie konserwacji i ponowne uruchomienie maszyny wirtualnej, wiedząc, że platforma Azure automatycznie rozpocznie konserwację.


Za pomocą Azure Portal, programu PowerShell, interfejsu API REST i wiersza polecenia można wysyłać zapytania dotyczące okien obsługi dla maszyn wirtualnych i uruchamiać konserwację samoobsługową.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Czy należy uruchomić konserwację przy użyciu w trakcie korzystania z okna samoobsługowego?  

Poniższe wskazówki powinny ułatwić podjęcie decyzji o tym, czy korzystać z tej funkcji, i zacząć konserwację w swoim czasie. 

> [!NOTE] 
> Konserwacja samoobsługowa może być niedostępna dla wszystkich maszyn wirtualnych. Aby określić, czy aktywne ponowne wdrażanie jest dostępne dla maszyny wirtualnej, Wyszukaj pozycję **Rozpocznij teraz** w stanie konserwacji. Samoobsługowe konserwacje nie są obecnie dostępne dla Cloud Services (roli sieć Web/proces roboczy) i Service Fabric.


Konserwacja samoobsługowa nie jest zalecana w przypadku wdrożeń korzystających z **zestawów dostępności**. Zestawy dostępności są już aktualizowane tylko jedną domeną aktualizacji jednocześnie. 

- Pozwól, aby platforma Azure wyzwolił konserwację. Aby przeprowadzić konserwację wymagającą ponownego uruchomienia, konserwacja zostanie zaimplementowana przez domenę aktualizacji. Domeny aktualizacji nie zawsze odbierają konserwację i że istnieje 30-minutowe wstrzymywanie między domenami aktualizacji. 
- Jeśli nieprzerwana utrata pewnej pojemności (1 domena aktualizacji) jest istotna, można dodać wystąpienia w okresie konserwacji. 
- W przypadku konserwacji, która nie wymaga ponownego uruchomienia, aktualizacje są stosowane na poziomie domeny błędów. 

**Nie** należy używać samoobsługowej konserwacji w następujących scenariuszach: 
- W przypadku częstego wyłączania maszyn wirtualnych ręcznie przy użyciu usługi DevTest Labs przy użyciu automatycznego zamykania lub zgodnie z harmonogramem można przywrócić stan konserwacji i w związku z tym spowodować dodatkowy Przestój.
- Na krótkoterminowych maszynach wirtualnych, które znasz, zostaną usunięte przed końcem fazy obsługi. 
- W przypadku obciążeń o dużym stanie przechowywanym na dysku lokalnym (tymczasowych), które mają być utrzymywane po aktualizacji. 
- W przypadkach, w których często zmienia się rozmiar maszyny wirtualnej, może to spowodować przywrócenie stanu konserwacji. 
- W przypadku zaakceptowania zaplanowanych zdarzeń, które umożliwiają aktywne przejście w tryb failover lub bezpieczne zamknięcie obciążenia, 15 minut przed rozpoczęciem zamykania konserwacji

**Użyj** samoobsługowej konserwacji, jeśli planujesz uruchomić maszynę wirtualną w trybie nieprzerwanym w fazie zaplanowanej konserwacji, a żadne z powyższych wskazań liczników nie są stosowane. 

Najlepszym rozwiązaniem jest użycie samoobsługowej konserwacji w następujących przypadkach:
- Należy skontaktować się z dokładnym oknem obsługi, aby zarządzać lub klientem końcowym. 
- Należy przeprowadzić konserwację według danego dnia. 
- Należy sterować sekwencją czynności konserwacyjnych, na przykład aplikacji wielowarstwowej w celu zagwarantowania bezpiecznego odzyskiwania.
- Między dwiema domenami aktualizacji jest wymagana więcej niż 30 minut czasu odzyskiwania maszyny wirtualnej. Aby kontrolować czas między domenami aktualizacji, należy wyzwolić konserwację na maszynach wirtualnych w jednej domenie aktualizacji (UD) naraz.


## <a name="faq"></a>Często zadawane pytania


**P: Dlaczego musisz teraz ponownie uruchomić moje maszyny wirtualne?**

Odp **.:** Chociaż większość aktualizacji i uaktualnień na platformie Azure nie ma wpływu na dostępność maszyny wirtualnej, istnieją przypadki, w których nie można uniknąć ponownego uruchamiania maszyn wirtualnych hostowanych na platformie Azure. Zebrano kilka zmian, które wymagają ponownego uruchomienia naszych serwerów, co spowoduje ponowne uruchomienie maszyn wirtualnych.

**P: Jeśli obserwuję zalecenia dotyczące wysokiej dostępności przy użyciu zestawu dostępności, czy jest to bezpieczne?**

Odp **.:** Maszyny wirtualne wdrożone w zestawie dostępności lub w zestawach skalowania maszyn wirtualnych mają koncepcję domen aktualizacji (UD). Podczas przeprowadzania konserwacji platforma Azure uznaje ograniczenie UD i nie będzie ponownie uruchamiać maszyn wirtualnych z różnych UD (w ramach tego samego zestawu dostępności).  Platforma Azure czeka także przez co najmniej 30 minut przed przejściem do następnej grupy maszyn wirtualnych. 

Aby uzyskać więcej informacji na temat wysokiej dostępności, zobacz [dostępność dla maszyn wirtualnych na platformie Azure](availability.md).

**P: Jak mogę Otrzymuj powiadomienia o planowanej konserwacji?**

Odp **.:** Planowana fala konserwacji jest uruchamiana przez ustawienie harmonogramu do co najmniej jednego regionu świadczenia usługi Azure. Wkrótce po otrzymaniu powiadomienia e-mail do administratorów subskrypcji, współadministratorów, właścicieli i współautorów (jednej wiadomości e-mail na subskrypcję). Dodatkowe kanały i adresatów dla tego powiadomienia można skonfigurować przy użyciu alertów dziennika aktywności. W przypadku wdrożenia maszyny wirtualnej w regionie, w którym zaplanowano zaplanowaną konserwację, nie otrzymasz powiadomienia, ale konieczne jest sprawdzenie stanu konserwacji maszyny wirtualnej.

**P: nie widzę żadnego wskazania planowanej konserwacji w portalu, programie PowerShell lub interfejsie wiersza polecenia. Co jest nie tak?**

Odp **.:** Informacje związane z planowaną konserwacją są dostępne podczas planowanej fazy konserwacji tylko dla maszyn wirtualnych, na które mają mieć wpływ. Innymi słowy, jeśli nie widzisz danych, może to oznaczać, że fala konserwacji została już ukończona (lub nie została uruchomiona) albo że maszyna wirtualna jest już hostowana na zaktualizowanym serwerze.

**P: czy istnieje sposób, aby dokładnie poznać, kiedy wpłynie na moją maszynę wirtualną?**

Odp **.:** Podczas ustawiania harmonogramu definiujemy przedział czasu kilka dni. Jednak dokładne sekwencjonowanie serwerów (i maszyn wirtualnych) w tym oknie jest nieznane. Klienci, którzy chcą wiedzieć, jak dokładny czas na ich maszynach wirtualnych mogą używać [zaplanowanych zdarzeń](./linux/scheduled-events.md) i zapytań z poziomu maszyny wirtualnej, a następnie otrzymywać 15-minutowy powiadomienie przed ponownym uruchomieniem maszyny wirtualnej.

**P: jak długo zajmiemy się ponownym uruchomieniem maszyny wirtualnej?**

Odp **.:**  W zależności od rozmiaru maszyny wirtualnej ponowne uruchomienie usługi może potrwać kilka minut. Po zainicjowaniu ponownego uruchomienia platformy Azure w oknie zaplanowanej konserwacji ponowne uruchomienie zajmie zwykle około 25 minut. Należy pamiętać, że w przypadku korzystania z Cloud Services (roli sieć Web/proces roboczy), Virtual Machine Scale Sets lub zestawów dostępności otrzymasz 30 minut między każdą grupą maszyn wirtualnych (UD) w oknie zaplanowanej konserwacji.

**P: co to jest środowisko w przypadku Virtual Machine Scale Sets?**

Odp **.:** Planowana konserwacja jest teraz dostępna dla Virtual Machine Scale Sets. Aby uzyskać instrukcje dotyczące sposobu inicjowania samoobsługowej konserwacji, [zaplanowaną konserwację dokumentu dla zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) .

**P: co to jest środowisko w przypadku Cloud Services (rola sieć Web/proces roboczy) i Service Fabric?**

Odp **.:** Mimo że te platformy mają wpływ na zaplanowaną konserwację, klienci korzystający z tych platform są uważani za bezpieczną, ponieważ w danym momencie będzie miała wpływ tylko na maszyny wirtualne w pojedynczej domenie uaktualnienia (UD). Samoobsługowe konserwacje nie są obecnie dostępne dla Cloud Services (roli sieć Web/proces roboczy) i Service Fabric.

**P: nie widzę żadnych informacji o konserwacji na moich maszynach wirtualnych. Co się stało?**

Odp **.:** Istnieje kilka powodów, dla których nie widzisz żadnych informacji o konserwacji maszyn wirtualnych:
1.  Używasz subskrypcji oznaczonej jako wewnętrzne firmy Microsoft.
2.  Maszyny wirtualne nie są zaplanowane do konserwacji. Może się zdarzyć, że fala konserwacji zakończyła się, została anulowana lub zmodyfikowana, tak aby maszyny wirtualne nie miały już na nie wpływu.
3. Cofnięto przydzieloną maszynę wirtualną, a następnie ją uruchomiono. Może to spowodować przeniesienie maszyny wirtualnej do lokalizacji, w której zaplanowano nieplanowaną konserwację Wave. Na maszynie wirtualnej nie będą wyświetlane żadne dodatkowe informacje o obsłudze. 
4.  Do widoku listy maszyn wirtualnych nie została dodana kolumna **konserwacja** . Po dodaniu tej kolumny do widoku domyślnego klienci, którzy skonfigurowali wyświetlanie kolumn innych niż domyślne, muszą ręcznie dodać kolumnę **konserwacji** do widoku listy maszyn wirtualnych.

**P: zaplanowano konserwację maszyny wirtualnej po raz drugi. Zalet?**

Odp **.:** Istnieje kilka przypadków użycia, w których zaplanowano konserwację maszyny wirtualnej po zakończeniu konserwacji ponownej wdrożenia:
1.  Twoja fala została anulowana i uruchomiona ponownie z innym ładunkiem. Może być to, że wykryto błąd ładunku i po prostu musimy wdrożyć dodatkowy ładunek.
2.  Twoja maszyna wirtualna została zaakceptowana w innym *węźle z powodu* błędu sprzętowego.
3.  Wybrano zatrzymanie (Cofnięcie alokacji) i ponowne uruchomienie maszyny wirtualnej.
4.  Włączono **automatyczne zamykanie** dla maszyny wirtualnej.



## <a name="next-steps"></a>Następne kroki

Planowaną konserwację można obsługiwać przy użyciu [interfejsu wiersza polecenia platformy Azure](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) lub [portalu](maintenance-notifications-portal.md).
