---
title: Resource Health Cloud Services (klasyczny)
description: W tym artykule omówiono obsługę Resource Health Check (systemie RHC występuje) dla Microsoft Azure Cloud Services (klasyczny)
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 37294e681066eb27ace69bcacee3a813b750b8eb
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743512"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Obsługa Resource Health Check (systemie RHC występuje) dla platformy Azure Cloud Services (wersja klasyczna)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

W tym artykule omówiono obsługę Resource Health Check (systemie RHC występuje) dla [Microsoft Azure Cloud Services (klasyczny)](https://azure.microsoft.com/services/cloud-services)

[Azure Resource Health](../service-health/resource-health-overview.md) dla usług w chmurze pomaga zdiagnozować i uzyskać pomoc techniczną dotyczącą problemów z usługami, które mają wpływ na wdrożenie usługi w chmurze, role & wystąpienia roli. Raporty są raportowane w bieżącej i wcześniejszej kondycji usług w chmurze w ramach wdrożenia, roli & poziomie wystąpienia roli.

Raporty o stanie platformy Azure dotyczące problemów, które mają wpływ na szeroki zestaw klientów platformy Azure. Resource Health zapewnia spersonalizowany pulpit nawigacyjny kondycji zasobów. Resource Health przedstawia wszystkie czasy braku dostępu do zasobów z powodu problemów z usługą platformy Azure. Te dane ułatwiają sprawdzenie, czy umowa SLA została naruszona.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="Obraz przedstawia blok Sprawdzanie kondycji zasobów w Azure Portal.":::

## <a name="how-health-is-checked-and-reported"></a>W jaki sposób kondycja jest sprawdzana i raportowana?
Kondycja zasobów jest raportowana na poziomie wdrożenia lub roli. Sprawdzanie kondycji odbywa się na poziomie wystąpienia roli, agreguje stan i raportuje go na poziomie roli. Na przykład Jeśli wszystkie wystąpienia roli są dostępne, stan roli jest dostępny. Analogicznie agreguje stan kondycji wszystkich ról i raportuje je na poziomie wdrożenia. Na przykład Jeśli wszystkie role są dostępne, stan wdrożenia stanie się dostępny. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>Dlaczego nie widzę stanu kondycji wdrożenia miejsca przemieszczania?
Testy kondycji zasobów działają tylko dla wdrożenia miejsca produkcyjnego. Wdrożenie miejsca przejściowego nie jest jeszcze obsługiwane. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>Czy Resource Health sprawdzać również kondycję aplikacji?
Nie, sprawdzanie kondycji odbywa się tylko dla wystąpień roli i nie monitoruje kondycji aplikacji. Na przykład Nawet jeśli 1 z 3 wystąpień roli jest w złej kondycji, aplikacja nadal może być dostępna. SYSTEMIE RHC występuje nie używa [sondy usługi równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md) ani sondy agenta gościa. W związku z tym klienci powinni nadal używać sond modułu równoważenia obciążenia do monitorowania kondycji aplikacji. 

## <a name="what-are-the-annotations-for-cloud-services"></a>Co to są adnotacje dla Cloud Services?
Adnotacje to stan kondycji wdrożenia lub ról. Istnieją różne adnotacje na podstawie stanu kondycji, przyczyny zmiany stanu itp. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>Co oznacza, że wystąpienie roli jest niedostępne?
Oznacza to, że wystąpienie roli nie emituje sygnału w dobrej kondycji do platformy. Sprawdź stan wystąpienia roli, aby uzyskać szczegółowe informacje o tym, dlaczego sygnał w dobrej kondycji nie jest emitowany.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>Co oznacza "nieznany" wdrożenia?
Nieznany oznacza, że nie można ustalić zagregowanej kondycji wdrożenia usługi w chmurze. Zazwyczaj oznacza to, że nie utworzono żadnego wdrożenia produkcyjnego dla usługi w chmurze, wdrożenie zostało nowo utworzone (oraz że platforma Azure zaczyna zbierać zdarzenia dotyczące kondycji) lub platforma ma problemy z zbieraniem zdarzeń dotyczących kondycji dla tego wdrożenia.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>Dlaczego adnotacje wystąpień ról wymieniają maszyny wirtualne zamiast wystąpień roli?
Ponieważ wystąpienia roli są zasadniczo maszynami wirtualnymi, a Sprawdzanie kondycji maszyn wirtualnych jest ponownie używane dla wystąpień roli, termin maszyny wirtualnej jest używany do reprezentowania wystąpień roli. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Cloud Services (poziom wdrożenia) adnotacje & ich znaczenia
| Adnotacja | Opis | 
| --- | --- | 
| Dostępne| Nie ma żadnych znanych problemów z platformą Azure wpływających na to wdrożenie usługi w chmurze |
| Nieznane | Obecnie nie można określić kondycji tego wdrożenia usługi w chmurze | 
| Konfigurowanie Resource Health | Konfigurowanie usługi Resource Health dla tego zasobu. Usługa Resource Health obserwuje zasoby platformy Azure, aby zapewnić szczegółowe informacje o bieżących i przeszłych zdarzeniach, które miały na nie wpływ|
| Obniżona wydajność | Twoje wdrożenie usługi w chmurze ma obniżoną sprawność. Pracujemy nad automatycznym odzyskaniem wdrożenia usługi w chmurze i ustaleniem źródła problemu. W tej chwili nie jest wymagana żadna dodatkowa akcja |
| Nieprawidłowy | Wdrożenie usługi w chmurze jest w złej kondycji {0} , ponieważ nie {1} są dostępne wystąpienia roli |
| Obniżona wydajność | Wdrożenie usługi w chmurze zostało obniżone, ponieważ {0} {1} wystąpienia roli są niedostępne | 
| Dostępne i może mieć wpływ | Wdrożenie usługi w chmurze jest uruchomione, jednak trwająca awaria usługi platformy Azure może uniemożliwiać połączenie z nim. Łączność zostanie przywrócona po rozwiązaniu awarii |
| Niedostępne i może mieć wpływ | Awaria usługi platformy Azure może mieć wpływ na kondycję tego wdrożenia usługi w chmurze. Wdrożenie usługi w chmurze zostanie automatycznie odzyskane po rozwiązaniu awarii |
| Nieznane i może mieć wpływ | Obecnie nie jesteśmy w stanie określić kondycji tego wdrożenia usługi w chmurze. Może to być spowodowane trwającą awarią usługi platformy Azure, która może mieć wpływ na tę maszynę wirtualną, która zostanie automatycznie odzyskana po rozwiązaniu awarii |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Cloud Services (poziom wystąpienia roli) adnotacje & ich znaczenia
| Adnotacja | Opis | 
| --- | --- | 
| Dostępne | Nie ma żadnych znanych problemów z platformą Azure wpływających na tę maszynę wirtualną | 
| Nieznane | Obecnie nie można określić kondycji tej maszyny wirtualnej |
| Zatrzymywanie i cofanie przydziału | Ta maszyna wirtualna jest zatrzymywana i jej alokacja jest cofana zgodnie z żądaniem autoryzowanego użytkownika lub procesu |
| Konfigurowanie Resource Health | Konfigurowanie usługi Resource Health dla tego zasobu. Usługa Resource Health obserwuje zasoby platformy Azure, aby zapewnić szczegółowe informacje o bieżących i przeszłych zdarzeniach, które miały na nie wpływ |
| Niedostępny | Maszyna wirtualna jest niedostępna. Pracujemy nad automatycznym odzyskaniem Twojej maszyny wirtualnej i ustaleniem źródła problemu. W tej chwili nie jest wymagana żadna dodatkowa akcja |
| Obniżona wydajność | Twoja maszyna wirtualna ma obniżoną wydajność. Pracujemy nad automatycznym odzyskaniem Twojej maszyny wirtualnej i ustaleniem źródła problemu. W tej chwili nie jest wymagana żadna dodatkowa akcja |
| Awaria sprzętowa serwera hosta | Krytyczny błąd {HardwareCategory} na serwerze hosta ma wpływ na tę maszynę wirtualną. Platforma Azure będzie ponownie wdrażać maszynę wirtualną na serwerze hosta w dobrej kondycji |
| Zaplanowano migrację ze względu na zdegradowany sprzęt | Platforma Azure ustaliła, że serwer hosta ma element {0} o obniżonej sprawności, który może wkrótce ulec awarii. Jeśli to możliwe, jak najszybciej przeprowadzimy migrację na żywo Twojej maszyny wirtualnej lub wdrożymy ją ponownie po godzinie {1} czasu UTC. Aby zminimalizować ryzyko dla usługi, a w przypadku niepowodzenia sprzętowego przed zainicjowaniem migracji systemu zalecamy, aby szybko wdrożyć maszynę wirtualną najszybciej, jak to możliwe |
| Dostępne i może mieć wpływ | Maszyna wirtualna jest uruchomiona, jednak trwająca awaria usługi platformy Azure może uniemożliwić połączenie się z nią. Łączność zostanie przywrócona po rozwiązaniu awarii |
| Niedostępne i może mieć wpływ | Awaria usługi platformy Azure może mieć wpływ na kondycję tej maszyny wirtualnej. Twoja maszyna wirtualna zostanie automatycznie odzyskana po rozwiązaniu awarii |
| Nieznane i może mieć wpływ | Obecnie nie można określić kondycji tej maszyny wirtualnej. Może to być spowodowane trwającą awarią usługi platformy Azure, która może mieć wpływ na tę maszynę wirtualną, która zostanie automatycznie odzyskana po rozwiązaniu awarii |
| Przydzielono zasoby sprzętowe | Zasoby sprzętowe zostały przypisane do maszyny wirtualnej i wkrótce będzie ona w trybie online |
| Zatrzymywanie i cofanie przydziału | Ta maszyna wirtualna jest zatrzymywana i jej alokacja jest cofana zgodnie z żądaniem autoryzowanego użytkownika lub procesu |
| Konfiguracja jest aktualizowana | Konfiguracja tej maszyny wirtualnej jest aktualizowana zgodnie z żądaniem autoryzowanego użytkownika lub procesu |
| Uruchomione ponownie przez użytkownika | Ta maszyna wirtualna jest uruchamiana ponownie zgodnie z żądaniem autoryzowanego użytkownika lub procesu. Po ponownym uruchomieniu nastąpi powrót do trybu online |
| Ponowne wdrażanie na innym hoście | Ta maszyna wirtualna jest wdrażana ponownie na innym hoście zgodnie z żądaniem autoryzowanego użytkownika lub procesu. Po zakończeniu ponownego wdrożenia zostanie on przywrócony do trybu online |
| Zatrzymane przez użytkownika | Ta maszyna wirtualna jest zatrzymywana zgodnie z żądaniem autoryzowanego użytkownika lub procesu |
| Zatrzymane przez użytkownika lub proces | Ta maszyna wirtualna jest zatrzymywana zgodnie z żądaniem autoryzowanego użytkownika lub procesu lub przez proces uruchomiony wewnątrz maszyny wirtualnej |
| Uruchomione przez użytkownika | Ta maszyna wirtualna jest uruchamiana zgodnie z żądaniem autoryzowanego użytkownika lub procesu. Wkrótce będzie ona dostępna w trybie online |
| Ponowne wdrażanie konserwacji na innym hoście | Ta maszyna wirtualna jest wdrażana ponownie na innym serwerze hosta w ramach zaplanowanego działania konserwacji. Po zakończeniu ponownego wdrożenia zostanie on przywrócony do trybu online |
| Zainicjowano ponowne uruchomienie z wnętrza maszyny | Ponowny rozruch został wyzwolony z poziomu maszyny wirtualnej. Może to być spowodowane błędem systemu operacyjnego maszyny wirtualnej bądź żądaniem autoryzowanego użytkownika lub procesu. Po ponownym uruchomieniu maszyna wirtualna będzie w trybie online. |
| Zmieniono rozmiar użytkownika | Rozmiar tej maszyny wirtualnej jest zmieniany zgodnie z żądaniem autoryzowanego użytkownika lub procesu. Po zakończeniu zmiany rozmiarów będzie ona powracać do trybu online |
| Maszyna uległa awarii | Ponowny rozruch został wyzwolony z poziomu maszyny wirtualnej. Może to być spowodowane błędem systemu operacyjnego maszyny wirtualnej bądź żądaniem autoryzowanego użytkownika lub procesu. Po ponownym uruchomieniu maszyna wirtualna będzie w trybie online. |
| Ponowne uruchamianie konserwacji dla aktualizacji hosta | Aktualizacje konserwacji są stosowane dla serwera hosta, na którym działa ta maszyna wirtualna. Nie musisz wykonywać teraz żadnych dodatkowych akcji. Po zakończeniu konserwacji będzie można wrócić do trybu online |
| Ponowne wdrażanie konserwacji na nowym sprzęcie | Ta maszyna wirtualna jest niedostępna, ponieważ jest ponownie wdrażana na nowszym sprzęcie w ramach zdarzenia planowanej konserwacji. Nie musisz wykonywać teraz żadnych dodatkowych akcji. Po ukończeniu planowanej konserwacji będzie ona powracać do trybu online |
| Komputer o niskim priorytecie został przeniesiona | Ta maszyna wirtualna została przeniesiona. Ta maszyna wirtualna o niskim priorytecie jest zatrzymywana i cofnięta alokacja |
| Ponowne uruchomienie serwera hosta | Niestety, maszyna wirtualna nie jest dostępna z powodu nieoczekiwanego ponownego uruchomienia serwera hosta. Serwer hosta jest obecnie ponownie uruchamiany. Po ponownym uruchomieniu maszyna wirtualna wróci do trybu online. W tej chwili nie jest wymagana żadna dodatkowa akcja |
| Ponowne wdrażanie z powodu niepowodzenia hosta | Niestety, Twoja maszyna wirtualna jest niedostępna i jest wdrażana ponownie z powodu nieoczekiwanego błędu na serwerze hosta. Platforma Azure rozpoczęła proces autoodzyskiwania i aktualnie uruchamia maszynę wirtualną na innym hoście. W tej chwili nie jest wymagana żadna dodatkowa akcja |
| Nieoczekiwany błąd hosta | Niestety, maszyna wirtualna nie jest dostępna z powodu nieoczekiwanego błędu na serwerze hosta. Na platformie Azure rozpoczął się proces automatycznego odzyskiwania. Aktualnie trwa ponowny rozruch serwera hosta. Nie musisz wykonywać teraz żadnych dodatkowych akcji. Po ponownym uruchomieniu maszyna wirtualna będzie w trybie online. |
| Ponowne wdrażanie z powodu nieplanowanej konserwacji hosta | Niestety, Twoja maszyna wirtualna jest niedostępna i jest wdrażana ponownie z powodu nieoczekiwanego błędu na serwerze hosta. Platforma Azure rozpoczęła proces autoodzyskiwania i aktualnie uruchamia maszynę wirtualną na innym serwerze hosta. W tej chwili nie jest wymagana żadna dodatkowa akcja |
| Niepowodzenie aprowizacji | Niestety, Twoja maszyna wirtualna jest niedostępna z powodu nieoczekiwanych problemów z aprowizacją. Inicjowanie obsługi maszyny wirtualnej nie powiodło się z powodu nieoczekiwanego błędu |
| Migracja na żywo | Ta maszyna wirtualna została wstrzymana z powodu operacji migracji na żywo zachowującej zawartość pamięci. Maszyna wirtualna zazwyczaj wznawia działanie w ciągu 10 sekund. W tej chwili nie jest wymagana żadna dodatkowa akcja |
| Migracja na żywo | Ta maszyna wirtualna została wstrzymana z powodu operacji migracji na żywo zachowującej zawartość pamięci. Maszyna wirtualna zazwyczaj wznawia działanie w ciągu 10 sekund. W tej chwili nie jest wymagana żadna dodatkowa akcja | 
| Dysk zdalny został odłączony | Niestety, Twoja maszyna wirtualna jest niedostępna z powodu utraty łączności z dyskiem zdalnym. Pracujemy nad ponownym nawiązaniem połączenia z dyskami. W tej chwili nie jest wymagana żadna dodatkowa akcja |
| Problem z usługą platformy Azure | Problem z usługą platformy Azure ma wpływ na Twoją maszynę wirtualną |
| Problem z siecią | Na tę maszynę wirtualną ma wpływ urządzenie sieciowe najwyższego poziomu |
| Niedostępny | Maszyna wirtualna jest niedostępna. Obecnie nie możemy określić przyczyny tego przestoju |
| Ponowne uruchomienie serwera hosta | Niestety, maszyna wirtualna nie jest dostępna z powodu nieoczekiwanego ponownego uruchomienia serwera hosta. Nieoczekiwany problem z serwerem hosta uniemożliwia nam automatyczne odzyskanie maszyny wirtualnej |
| Ponowne wdrażanie z powodu niepowodzenia hosta | Niestety, maszyna wirtualna nie jest dostępna z powodu nieoczekiwanego błędu na serwerze hosta. Nieoczekiwany problem z hostem uniemożliwia nam automatyczne odzyskanie maszyny wirtualnej |
| Nieoczekiwany błąd hosta | Niestety, maszyna wirtualna nie jest dostępna z powodu nieoczekiwanego błędu na serwerze hosta. Nieoczekiwany problem z hostem uniemożliwia nam automatyczne odzyskanie maszyny wirtualnej |
| Ponowne wdrażanie z powodu nieplanowanej konserwacji hosta | Niestety, maszyna wirtualna nie jest dostępna z powodu nieoczekiwanego błędu na serwerze hosta. Nieoczekiwany problem z hostem uniemożliwia nam automatyczne odzyskanie maszyny wirtualnej |
| Niepowodzenie aprowizacji | Niestety, Twoja maszyna wirtualna jest niedostępna z powodu nieoczekiwanych problemów z aprowizacją. Inicjowanie obsługi maszyny wirtualnej nie powiodło się z powodu nieoczekiwanego błędu |
| Dysk zdalny został odłączony | Niestety, Twoja maszyna wirtualna jest niedostępna z powodu utraty łączności z dyskiem zdalnym. Nieoczekiwany problem uniemożliwia nam automatyczne odzyskanie maszyny wirtualnej |
| Ponowny rozruch z powodu aktualizacji systemu operacyjnego gościa | Platforma Azure zainicjowała ponowne uruchomienie w celu zastosowania nowej aktualizacji systemu operacyjnego gościa. Po ponownym uruchomieniu maszyna wirtualna będzie w trybie online. |