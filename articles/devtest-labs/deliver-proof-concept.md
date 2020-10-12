---
title: Dostarcz potwierdzenie koncepcji — Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak dostarczać weryfikację koncepcji, aby Azure DevTest Labs można było pomyślnie włączyć w środowisku przedsiębiorstwa.
ms.topic: article
ms.date: 06/2/2020
ms.openlocfilehash: 9c28cf9eebd8a39a2edce48e4fb8b96dc7608d80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288026"
---
# <a name="deliver-a-proof-of-concept"></a>Dostarczenie dowodu koncepcji 

Jednym z kluczowych scenariuszy dla Azure DevTest Labs jest włączenie środowisk deweloperskich i testowych w chmurze. Przykłady:

* Tworzenie pulpitów deweloperów w chmurze.
* Konfigurowanie środowisk do testowania.
* Włączanie dostępu do maszyn wirtualnych i innych zasobów platformy Azure.
* Konfigurowanie obszaru piaskownicy dla deweloperów do uczenia się i eksperymentowania.

DevTest Labs również udostępniają guardrails zasad i kontrolę kosztów, aby zapewnić przedsiębiorstwom możliwość dostarczania samoobsługowego systemu Azure do deweloperów, którzy stosują się do firmowych zasad związanych z bezpieczeństwem, regulacją i zgodnością. 

Każde przedsiębiorstwo ma inne wymagania dotyczące sposobu, w jaki Azure DevTest Labs można pomyślnie włączyć do ich środowiska. W tym artykule opisano najczęstsze kroki, które muszą zostać wykonane przez przedsiębiorstwa, aby zapewnić pomyślne potwierdzenie koncepcji. Potwierdzenie koncepcji to pierwszy krok w kierunku kompleksowego wdrożenia. 

## <a name="getting-started"></a>Wprowadzenie 

Aby rozpocząć świadczenie, zapoznaj się z weryfikacją koncepcji. Ważne jest, aby poświęcać trochę czasu na zapoznanie się z platformą Azure i DevTest Labs.  Oto kilka zasobów początkowych: 

* [Informacje o Azure Portal](https://azure.microsoft.com/features/azure-portal/)
* [Podstawy DevTest Labs](devtest-lab-overview.md)
* [Scenariusze obsługiwane przez DevTest Labs](devtest-lab-guidance-get-started.md)
* [Dokumentacja DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Wprowadzenie do sieci platformy Azure](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne 

Aby pomyślnie ukończyć pilotaż lub weryfikację koncepcji z DevTest Labs, istnieje kilka wymagań wstępnych: 

* **Subskrypcja platformy Azure**: przedsiębiorstwa często mają istniejące [Umowa Enterprise](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) , które umożliwiają dostęp do platformy Azure i mogą korzystać z istniejącej lub nowej subskrypcji usługi DevTest Labs. Alternatywnie przedsiębiorstwa mogą korzystać z [subskrypcji programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) podczas pilotażu (korzystając z bezpłatnych kredytów na korzystanie z platformy Azure). Jeśli żadna z tych opcji nie jest dostępna, przedsiębiorstwo może utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)i korzystać z niego. Jeśli istnieje Umowa Enterprise, korzystanie z [subskrypcji Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/) jest doskonałym rozwiązaniem umożliwiającym uzyskanie dostępu do systemów operacyjnych klienta Windows 10/Windows 8.1 oraz obniżonych stawek za tworzenie i testowanie obciążeń. 
* **Azure Active Directory dzierżawca**: aby umożliwić zarządzanie użytkownikami (na przykład dodawania użytkowników lub dodawania właścicieli laboratorium), Ci użytkownicy muszą być częścią [dzierżawy Azure Active Directory](https://azure.microsoft.com/services/active-directory/) używanej w subskrypcji platformy Azure dla programu pilotażowego. Często przedsiębiorstwa skonfigurują [tożsamość hybrydową](../active-directory/hybrid/whatis-hybrid-identity.md) , aby umożliwić użytkownikom korzystanie z lokalnej tożsamości w chmurze, ale nie jest to wymagane w przypadku pilotażu DevTest Labs. 

## <a name="scoping-of-the-pilot"></a>Określanie zakresu pilotażu 

Ważne jest, aby zaplanować pilotaż przed rozpoczęciem wdrażania. Zadbaj o to, aby zasoby nie pozostały w sposób nieokreślony dla użytkowników pilotażu. 

> [!IMPORTANT]
> Nie możemy wyróżnić wystarczająco dużo znaczenia dla pilotażu i ustawienia oczekiwań z góry.

Odpowiedz na te kluczowe pytania przed rozpoczęciem pilotażu: 

* Co chcesz dowiedzieć się, jak wygląda sukces dla pilotażu? 
* Jakie obciążenia lub scenariusze zostaną omówione w programie pilotażowym? Ważne jest, aby zdefiniować tylko niewielki zestaw, aby upewnić się, że pilotaż może być w zasięgu i szybko zakończony. 
* Jakie zasoby muszą być dostępne w środowisku laboratoryjnym? Na przykład: obrazy niestandardowe, obrazy z witryny Marketplace, zasady, topologia sieci. 
* Kto jest użytkownikami i zespołami, którzy będą uczestniczyć w pilotażu do sprawdzenia środowiska?  
* Jaki jest czas trwania pilotażu? Wybierz przedział czasu, który jest wyrównany do planowanego zakresu, takiego jak dwa tygodnie lub jeden miesiąc. 
* Co się stanie z przydzielonymi zasobami, które były używane podczas pilotażu, po zakończeniu pilotażu? Czy planujesz odrzucanie zasobów pilotażowych? Można wziąć pod uwagę:
   
   "Jeśli możemy zaplanować Przerzucanie maszyn wirtualnych i laboratoriów na końcu pilotażu, można skonfigurować jedną subskrypcję dla pilotażu i wszystko to, co pomoże nam w rozwiązaniu problemów z wdrażaniem skalowania." 

Istnieje ogólna tendencja do prowadzenia pilotażu "idealny", tak samo jak reprezentuje stan końcowy po przeprowadzeniu usługi w firmie. Jest to fałszywe założenie. Bliżej "idealny", tym więcej, co musisz zrobić *przed* rozpoczęciem pracy pilotażowej. Celem pilotażu jest podjęcie odpowiednich decyzji dotyczących skalowania w górę i wdrożenia ostatniej usługi. 

Zespół pilotażowy powinien mieć możliwość wyboru minimalnych wymaganych obciążeń i zależności, aby odpowiedzieć na pytanie, czy Azure DevTest Labs to odpowiednia usługa dla danego przedsiębiorstwa. Zalecamy wybranie najprostszego obciążenia z najniższymi zależnościami, aby pomóc zapewnić szybkie i czyste sukcesy. Jeśli to nie jest możliwe, należy wybrać najbardziej reprezentatywne obciążenie, które ujawnia potencjalną złożoność, aby sukces w fazie pilotażowej mógł zostać zreplikowany w fazie skalowania w poziomie. 

Poniższy przykład ilustruje dobrze określony zakres weryfikacji koncepcji. 

## <a name="example-proof-of-concept-plan"></a>Przykład: plan dowodu koncepcji 

W tej sekcji przedstawiono przykład służący do określania zakresu weryfikacji koncepcji pilotażowej dla DevTest Labs. 

> [!TIP]
> Aby zminimalizować możliwość skonfigurowania projektu pod kątem awarii, zdecydowanie zalecamy, aby nie pominąć przykładu opisanego w tej sekcji. 

### <a name="overview"></a>Omówienie 

Planujemy opracować nowe środowisko na platformie Azure na podstawie DevTest Labs for Vendors, które będzie używane jako izolowane środowisko z sieci firmowej. Aby określić, czy rozwiązanie będzie spełniało wymagania, zostanie opracowana Weryfikacja koncepcji w celu zweryfikowania kompleksowego rozwiązania. Dodaliśmy kilku dostawców, aby wypróbować i sprawdzić środowisko. 

### <a name="outcomes"></a>Wyników 

Podczas kompilowania weryfikacji koncepcji najpierw koncentrujemy się na rezultatach (co próbujemy uzyskać). Na koniec weryfikacji koncepcji Oczekujemy: 

* Kompleksowe rozwiązanie umożliwiające dostawcom korzystanie z kont Gości w usłudze Azure Active Directory (Azure AD) w celu uzyskania dostępu do środowiska izolowanego na platformie Azure. Środowisko ma zasoby wymagane do wydajnej pracy. 
* Wszelkie potencjalne problemy z blokowaniem, które mają wpływ na szersze użycie i wdrażanie, są wyliczane i zrozumiałe.
* Osoby mające na celu rozwój koncepcji sprawdzają poprawność wszystkich kodów. Są one również zrozumiałe i są świadome szerszego zastosowania.

### <a name="open-questions-and-prerequisites"></a>Otwarte pytania i wymagania wstępne 

* Czy mamy utworzoną subskrypcję, której możemy użyć dla tego projektu? 
* Czy mamy dzierżawę usługi Azure AD i zidentyfikowano administratora globalnego usługi Azure AD, kto może zapewnić pomoc i wskazówki dotyczące pytań związanych z usługą Azure AD? 
* Czy mamy do współpracy nad osobami, które pracują nad projektem? 

   * Kod źródłowy i skrypty (takie jak Azure Repos) 
   * Dokumenty (takie jak Microsoft Teams lub SharePoint)  
   * Konwersacje (takie jak Microsoft Teams) 
   * Elementy robocze (takie jak Azure Boards) 
* Jakie zasoby są wymagane dla dostawców? Obejmuje to aplikacje dostępne w sieci zarówno lokalnie na maszynach wirtualnych, jak i na innych wymaganych serwerach. 
* Czy maszyny wirtualne będą przyłączone do domeny na platformie Azure? Jeśli tak, to będzie Azure Active Directory Domain Services (Azure AD DS) lub coś innego? 
* Czy zidentyfikowano zespół lub dostawców, którzy będą celem weryfikacji koncepcji? Kto będzie klientami środowiska?
* Którego regionu świadczenia usługi Azure będziemy używać do weryfikacji koncepcji? 
* Czy mamy listę usług, których mogą używać dostawcy za pośrednictwem DevTest Labs poza IaaS (maszyny wirtualne)? 
* Jak planujemy uczenie dostawców/użytkowników przy użyciu laboratorium? 

### <a name="components-of-the-proof-of-concept-solution"></a>Składniki rozwiązania sprawdzającego koncepcje 

Oczekujemy, że rozwiązanie będzie miało następujące składniki: 

* Różne zespoły dostawców będą korzystać z zestawu laboratoriów na platformie Azure.
* Laboratorium są połączone z infrastrukturą sieciową, która spełnia wymagania.
* Dostawcy mają dostęp do laboratoriów za pośrednictwem usługi Azure AD i przypisań ról.
* Dostawcy mają możliwość pomyślnego nawiązania połączenia z zasobami. Lokacja sieci VPN typu lokacja-lokacja umożliwia dostęp do maszyn wirtualnych bezpośrednio bez publicznych adresów IP.
* Zestaw artefaktów obejmuje oprogramowanie wymagane przez dostawców na maszynach wirtualnych.

## <a name="additional-planning-and-design-decisions"></a>Dodatkowe decyzje dotyczące planowania i projektowania 

Przed udostępnieniem pełnego rozwiązania DevTest Labs należy podjąć pewne ważne decyzje dotyczące planowania i projektowania. Środowisko pracy z weryfikacją koncepcji może ułatwić podejmowanie tych decyzji. Dalsze zagadnienia obejmują: 

* **Topologia subskrypcji**: wymagania poziomu przedsiębiorstwa dla zasobów platformy Azure mogą wykraczać poza [dostępne przydziały w ramach jednej subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md). Wymaga to wielu subskrypcji platformy Azure i/lub żądań obsługi, aby zwiększyć początkowe limity subskrypcji. Ważne jest, aby podjąć decyzję o sposobie dystrybucji zasobów między subskrypcjami. Jeden cenny zasób to [Przewodnik po decyzji subskrypcji](/azure/architecture/cloud-adoption/decision-guides/subscriptions/) , ponieważ trudno jest przenieść zasoby do innej subskrypcji później. Na przykład nie można przenieść laboratorium do innej subskrypcji po jej utworzeniu.  
* **Topologia sieci**: [Domyślna infrastruktura sieci](../app-service/networking-features.md) tworzona automatycznie przez usługi DevTest Labs może nie być wystarczająca do spełnienia wymagań i ograniczeń dla użytkowników w przedsiębiorstwie. Często można zobaczyć, jak [usługa Azure ExpressRoute połączone sieci wirtualne](/azure/architecture/reference-architectures/hybrid-networking/), [Hub i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) w celu łączności między subskrypcjami, a nawet [wymuszone Routing](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) , aby zapewnić tylko łączność lokalną. DevTest Labs umożliwia podłączenie istniejących sieci wirtualnych do laboratorium, aby umożliwić ich używanie podczas tworzenia nowych maszyn wirtualnych w laboratorium. 
* **Zdalny dostęp do maszyn wirtualnych**: istnieje wiele opcji zdalnego dostępu do maszyn wirtualnych znajdujących się w DevTest Labs. Najłatwiej jest używać publicznych adresów IP lub udostępnionych publicznych adresów IP. Są to [Ustawienia dostępne w środowisku laboratoryjnym](devtest-lab-shared-ip.md). Jeśli te opcje nie są wystarczające, użycie bramy dostępu zdalnego jest również opcją. Ta opcja jest wyświetlana na [architekturze referencyjnej DevTest Labs Enterprise](devtest-lab-reference-architecture.md) i opisana dokładniej w [dokumentacji bramy usług pulpitu zdalnego DevTest Labs](configure-lab-remote-desktop-gateway.md). Przedsiębiorstwa mogą również używać ExpressRoute lub sieci VPN typu lokacja-lokacja do łączenia ich laboratoriów z siecią lokalną. Ta opcja umożliwia bezpośrednie połączenia pulpitu zdalnego lub SSH z maszynami wirtualnymi na podstawie ich prywatnego adresu IP bez narażania się na Internet. 
* **Obsługa uprawnień**: dwa kluczowe uprawnienia często używane w DevTest Labs są [użytkownikami właściciela i laboratorium](devtest-lab-add-devtest-user.md). Ważne jest, aby podjąć decyzję przed przeprowadzeniem DevTestych laboratoriów w szerokim stopniu, którzy będą powierzać każdy poziom dostępu w laboratorium. Typowym modelem jest właściciel budżetu (np. lider zespołu) jako właściciel laboratorium i członkowie zespołu jako użytkownicy laboratorium. Ten model umożliwia osobie (liderowi zespołu) odpowiedzialny za budżet na dostosowanie ustawień zasad i utrzymanie zespołu w ramach budżetu.  

## <a name="completing-the-proof-of-concept"></a>Ukończenie weryfikacji koncepcji 

Po pomyślnym przeprowadzeniu takich operacji dowiesz się, jak ukończyć pilotaż. Jest to czas na zebranie opinii użytkowników, określenie, czy pilotaż zakończył się pomyślnie, i decyduje o tym, czy organizacja będzie przenoszona na etapy skalowania DevTest laboratoriów w przedsiębiorstwie. Jest to świetny moment, aby rozważyć automatyzację wdrażania DevTest Labs i skojarzonych zasobów w celu zapewnienia spójności w całym wdrożeniu. 

## <a name="next-steps"></a>Następne kroki 

* [Dokumentacja DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Architektura referencyjna dla przedsiębiorstwa](devtest-lab-reference-architecture.md)
* [Skalowanie w górę wdrożenia DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Organizuj implementację Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
