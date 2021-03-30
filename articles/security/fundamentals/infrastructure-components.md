---
title: Składniki i granice systemu informacji platformy Azure
description: Ten artykuł zawiera ogólny opis architektury Microsoft Azure i zarządzania nimi.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727213"
---
# <a name="azure-information-system-components-and-boundaries"></a>Składniki i granice systemu informacji platformy Azure
Ten artykuł zawiera ogólny opis architektury i zarządzania platformą Azure. Środowisko systemu Azure składa się z następujących sieci:

- Sieć produkcyjna Microsoft Azure (sieć platformy Azure)
- Sieć firmowa firmy Microsoft (CorpNet)

Oddzielne zespoły IT są odpowiedzialne za operacje i konserwację tych sieci.

## <a name="azure-architecture"></a>Architektura platformy Azure
Platforma Azure to platforma obliczeniowa w chmurze i infrastruktura służąca do kompilowania i wdrażania aplikacji i usług oraz zarządzania nimi za pomocą sieci centrów danych. Firma Microsoft zarządza tymi centrami danych. Na podstawie liczby określonych zasobów platforma Azure tworzy maszyny wirtualne na podstawie potrzeb zasobów. Te maszyny wirtualne działają w ramach funkcji hypervisor platformy Azure, która jest przeznaczona do użytku w chmurze i nie jest dostępna publicznie.

W każdym węźle serwera fizycznego platformy Azure istnieje funkcja hypervisor, która jest uruchamiana bezpośrednio przez sprzęt. Funkcja hypervisor dzieli węzeł na zmienną liczbę maszyn wirtualnych gościa. Każdy węzeł ma również jedną główną maszynę wirtualną, w której jest uruchomiony system operacyjny hosta. Zapora systemu Windows jest włączona na każdej maszynie wirtualnej. Można zdefiniować, które porty są adresowane przez skonfigurowanie pliku definicji usługi. Te porty są jedynymi otwartymi i adresowanymi, wewnętrznymi lub zewnętrznymi. Cały ruch i dostęp do dysku i sieci jest korygowany przez funkcję hypervisor i główny system operacyjny.

W warstwie hosta maszyny wirtualne platformy Azure uruchamiają dostosowaną i zaostrzoną wersję najnowszego systemu Windows Server. Platforma Azure korzysta z wersji systemu Windows Server, która zawiera tylko te składniki, które są niezbędne do hostowania maszyn wirtualnych. Zwiększa to wydajność i zmniejsza podatność na ataki. Granice maszyn są wymuszane przez funkcję hypervisor, która nie zależy od zabezpieczeń systemu operacyjnego.

### <a name="azure-management-by-fabric-controllers"></a>Zarządzanie platformą Azure przez kontrolery sieci szkieletowej

Na platformie Azure maszyny wirtualne działające na serwerach fizycznych (blokach/węzłach) są pogrupowane w klastry o około 1000. Maszyny wirtualne są niezależnie zarządzane przez skalowalny w poziomie i nadmiarowy składnik oprogramowania platformy o nazwie Kontroler sieci szkieletowej (FC).

Każda FC zarządza cyklem życia aplikacji działających w jej klastrze, a ponadto udostępnia i monitoruje kondycję sprzętu w ramach jego kontroli. Wykonuje operacje autonomii, takie jak wystąpienia maszyn wirtualnych reincarnating na serwerach w dobrej kondycji, gdy określa, że serwer nie powiódł się. FC wykonuje również operacje zarządzania aplikacjami, takie jak wdrażanie, aktualizowanie i skalowanie aplikacji w poziomie.

Centrum danych jest podzielone na klastry. Klastry izolują błędy na poziomie FC i zapobiegają niektórym klasom błędów, które mają wpływ na serwery wykraczające poza klaster, w którym występują. FCs, który obsługuje określony klaster platformy Azure, jest pogrupowany w klastrze FC.

### <a name="hardware-inventory"></a>Spis sprzętu

FC przygotowuje spis sprzętu i urządzeń sieciowych platformy Azure podczas procesu konfiguracji ładowania początkowego. Każdy nowy sprzęt i składniki sieciowe wprowadzane do środowiska produkcyjnego platformy Azure muszą być zgodne z procesem konfiguracji ładowania początkowego. FC jest odpowiedzialny za zarządzanie całym spisem wymienionym w pliku konfiguracji datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Obrazy systemu operacyjnego zarządzane przez FC

Zespół systemu operacyjnego udostępnia obrazy w postaci wirtualnych dysków twardych wdrożonych na wszystkich maszynach wirtualnych hosta i gościa w środowisku produkcyjnym platformy Azure. Zespół tworzy te obrazy podstawowe za pomocą zautomatyzowanego procesu kompilacji w trybie offline. Podstawowy obraz to wersja systemu operacyjnego, w którym jądro i inne podstawowe składniki zostały zmodyfikowane i zoptymalizowane pod kątem obsługi środowiska platformy Azure.

Istnieją trzy typy obrazów systemu operacyjnego zarządzanych przez sieć szkieletową:

- Host: dostosowany system operacyjny uruchomiony na maszynach wirtualnych hosta.
- Natywny: natywny system operacyjny, który działa w dzierżawach (na przykład Azure Storage). Ten system operacyjny nie ma żadnej funkcji hypervisor.
- Gość: system operacyjny gościa, który działa na maszynach wirtualnych gościa.

Host i natywne systemy operacyjne zarządzane przez FC są przeznaczone do użytku w chmurze i nie są publicznie dostępne.

#### <a name="host-and-native-operating-systems"></a>Hostowanie i natywne systemy operacyjne

Host i natywne są obrazami systemu operacyjnego z ograniczeniami, które obsługują agentów sieci szkieletowej, i działają w węźle obliczeniowym (działają jako pierwsza maszyna wirtualna w węźle) i węzły magazynu. Zaletą korzystania z zoptymalizowanych obrazów podstawowych hosta i natywnego jest zmniejszenie obszaru powierzchni widocznego dla interfejsów API lub nieużywanych składników. Mogą one stwarzać wysokie zagrożenia bezpieczeństwa i zwiększyć zasięg systemu operacyjnego. Zredukowane systemy operacyjne zawierają tylko składniki niezbędne do korzystania z platformy Azure.

#### <a name="guest-operating-system"></a>System operacyjny gościa

Wewnętrzne składniki platformy Azure działające na maszynach wirtualnych systemu operacyjnego gościa nie mają możliwości uruchamiania Remote Desktop Protocol. Wszelkie zmiany ustawień konfiguracji linii bazowej muszą przechodzić przez proces zarządzania zmianami i wydaniami.

## <a name="azure-datacenters"></a>Centra danych platformy Azure
Zespół infrastruktury Microsoft Cloud i Operations (MCIO) zarządza infrastrukturą fizyczną i funkcjami centrum danych dla wszystkich Usługi online firmy Microsoft. MCIO jest przede wszystkim odpowiedzialny za zarządzanie kontrolami fizycznymi i środowiskami w centrach danych, a także zarządzanie i obsługa zewnętrznych urządzeń sieciowych (takich jak routery brzegowe i routery centrum danych). MCIO jest również odpowiedzialny za skonfigurowanie minimalnego sprzętu serwerowego na stojakach w centrum danych. Klienci nie mają bezpośredniej interakcji z platformą Azure.

## <a name="service-management-and-service-teams"></a>Zarządzanie usługami i zespoły usług
Różne grupy inżynieryjne, znane jako zespoły usługi, zarządzają obsługą usługi platformy Azure. Każdy zespół usługi jest odpowiedzialny za obszar pomocy technicznej dla platformy Azure. Każdy zespół usług musi udostępnić inżynierowi 24x7 do zbadania i rozwiązania błędów w usłudze. Zespoły usługi nie domyślnie mają fizyczny dostęp do sprzętu działającego na platformie Azure.

Zespoły usługi:

- Platforma aplikacji
- Azure Active Directory
- Azure Compute
- Usługa Azure NET
- Usługi w chmurze
- ISSD: zabezpieczenia
- Uwierzytelnianie wieloskładnikowe
- SQL Database
- Storage

## <a name="types-of-users"></a>Typy użytkowników
Pracownicy (lub wykonawcy) firmy Microsoft są uznawani za użytkowników wewnętrznych. Wszyscy inni użytkownicy są uważani za użytkowników zewnętrznych. Wszyscy użytkownicy wewnętrzni platformy Azure mają swój stan pracownika kategorii według poziomu czułości, który definiuje ich dostęp do danych klienta (dostęp lub brak dostępu). Uprawnienia użytkownika do platformy Azure (uprawnienie autoryzacji po uzyskaniu uwierzytelnienia) są opisane w poniższej tabeli:

| Rola | Wewnętrzne lub zewnętrzne | Poziom czułości | Autoryzowane uprawnienia i funkcje | Typ dostępu
| --- | --- | --- | --- | --- |
| Inżynier centrum danych platformy Azure | Wewnętrzny | Brak dostępu do danych klienta | Zarządzaj zabezpieczeniami fizycznymi w środowisku lokalnym. Przeprowadzaj patrolowanie do i z centrum danych oraz Monitoruj wszystkie punkty wejścia. Eskorty do i z centrum danych niektórych wyczyszczonych pracowników, którzy świadczą usługi ogólne (na przykład rekomendowanych lokali lub czyszcząc) lub działają w centrum danych. Przeprowadzaj rutynowe monitorowanie i konserwacja sprzętu sieciowego. Wykonywanie zadań związanych z zarządzaniem zdarzeniami i przerywaniem działania przy użyciu różnych narzędzi. Przeprowadzaj rutynowe monitorowanie i konserwację fizycznego sprzętu w centrach danych. Dostęp do środowiska na żądanie od właścicieli właściwości. Możliwość przeprowadzania dochodzeń śledczej, rejestrowania raportów zdarzeń i wymagania obowiązkowych szkoleń w zakresie zabezpieczeń i zasad. Własność operacyjna i obsługa krytycznych narzędzi zabezpieczeń, takich jak skanery i zbieranie dzienników. | Stały dostęp do środowiska. |
| Azure incydent Klasyfikacja (inżynierowie szybkich odpowiedzi) | Wewnętrzny | Dostęp do danych klienta | Zarządzanie komunikacją między MCIO, wsparcie i zespoły inżynieryjne. Zdarzenia platformy klasyfikacja, problemy z wdrażaniem i żądania obsługi. | Dostęp just in Time do środowiska z ograniczonym stałym dostępem do systemów nieobsługujących klientów. |
| Inżynierowie wdrożeń platformy Azure | Wewnętrzny | Dostęp do danych klienta | Wdrażaj i uaktualniaj składniki platformy, oprogramowanie i zaplanowane zmiany konfiguracji w ramach obsługi platformy Azure. | Dostęp just in Time do środowiska z ograniczonym stałym dostępem do systemów nieobsługujących klientów. |
| Obsługa awarii klienta platformy Azure (dzierżawa) | Wewnętrzny | Dostęp do danych klienta | Debugowanie i diagnozowanie awarii platformy i błędów dla indywidualnych dzierżawców obliczeniowych i kont platformy Azure. Analizuj błędy. Ważne poprawki dotyczące platformy lub klienta oraz zapewnianie ulepszeń technicznych w ramach pomocy technicznej. | Dostęp just in Time do środowiska z ograniczonym stałym dostępem do systemów nieobsługujących klientów. |
| Inżynierowie usługi Azure Live site (inżynierowie monitorowania) i incydent | Wewnętrzny | Dostęp do danych klienta | Diagnozuj i łagodzenie kondycji platformy przy użyciu narzędzi diagnostycznych. Poprawki dysków dla sterowników woluminów, Naprawianie elementów wynikających z awarii i pomoc w akcjach przywracania przestojów. | Dostęp just in Time do środowiska z ograniczonym stałym dostępem do systemów nieobsługujących klientów. |
|Klienci platformy Azure | Zewnętrzna | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY |

Platforma Azure korzysta z unikatowych identyfikatorów do uwierzytelniania użytkowników w organizacji i klientów (lub procesów działających w imieniu użytkowników w organizacji). Dotyczy to wszystkich zasobów i urządzeń, które są częścią środowiska platformy Azure.

### <a name="azure-internal-authentication"></a>Uwierzytelnianie wewnętrzne platformy Azure

Komunikacja między składnikami wewnętrznymi platformy Azure jest chroniona za pomocą szyfrowania TLS. W większości przypadków certyfikaty X. 509 są z podpisem własnym. Certyfikaty z połączeniami, do których można uzyskać dostęp spoza sieci platformy Azure, to wyjątek, jako certyfikaty dla programu FCs. Program FCs ma certyfikaty wystawione przez certyfikat firmy Microsoft dla urzędu certyfikacji, który jest objęty zaufanym głównym urzędem certyfikacji. Dzięki temu klucze publiczne FC mogą być łatwo rzutowane. Ponadto narzędzia programistyczne firmy Microsoft używają kluczy publicznych FC. Gdy deweloperzy przesyłają nowe obrazy aplikacji, obrazy są szyfrowane za pomocą klucza publicznego FC, aby chronić wszystkie osadzone klucze tajne.

### <a name="azure-hardware-device-authentication"></a>Uwierzytelnianie urządzeń sprzętowych platformy Azure

FC utrzymuje zestaw poświadczeń (kluczy i/lub haseł) służących do samodzielnego uwierzytelnienia na różnych urządzeniach sprzętowych pod kontrolą. Firma Microsoft używa systemu, aby uniemożliwić dostęp do tych poświadczeń. W odróżnieniu od tego, że transport, trwałość i korzystanie z tych poświadczeń jest zaprojektowana tak, aby uniemożliwić deweloperom platformy Azure, administratorom i usługom kopii zapasowych oraz dostęp do poufnych, poufnych lub prywatnych informacji.

Firma Microsoft używa szyfrowania opartego na kluczu publicznym głównej tożsamości FC. Dzieje się tak w przypadku konfiguracji FC i czasu ponownej konfiguracji FC, aby przetransferować poświadczenia używane do uzyskiwania dostępu do urządzeń sprzętowych. Gdy FC wymaga poświadczeń, FC pobiera i odszyfrowuje je.

### <a name="network-devices"></a>Urządzenia sieciowe

Zespół sieci platformy Azure konfiguruje konta usług sieciowych, aby umożliwić klientowi platformy Azure uwierzytelnianie na urządzeniach sieciowych (routerach, przełącznikach i modułach równoważenia obciążenia).

## <a name="secure-service-administration"></a>Administrowanie usługą Secure Service
Pracownicy operacji platformy Azure są zobowiązani do korzystania z bezpiecznych stacji roboczych administratora (piły). Klienci mogą zaimplementować podobne kontrolki przy użyciu uprzywilejowanych stacji roboczych dostępu. W przypadku, gdy pracownicy administracyjni korzystają z pojedynczego przypisanego konta administracyjnego, które jest oddzielone od konta użytkownika standardowego użytkownika. Piła została opracowana na podstawie tej rozdzielenia konta przez udostępnienie zaufanej stacji roboczej dla tych kont poufnych.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tego, co firma Microsoft pomaga w zabezpieczeniu infrastruktury platformy Azure, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)
