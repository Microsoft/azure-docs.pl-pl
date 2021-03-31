---
title: Azure Lab Services a Azure DevTest Labs
description: Porównaj Azure DevTest Labs i Azure Lab Services.
ms.topic: overview
ms.date: 06/26/2020
ms.openlocfilehash: b1cd476faf6c457033ffeace03cd2e37b51e8578
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480086"
---
# <a name="compare-azure-devtest-labs-and-azure-lab-services"></a>Porównaj Azure DevTest Labs i Azure Lab Services
Na platformie Azure dostępne są dwie usługi, które umożliwiają Konfigurowanie środowisk laboratoryjnych w chmurze. 

- **Azure DevTest Labs** — ta usługa umożliwia szybkie skonfigurowanie środowiska dla zespołu (na przykład środowisko programistyczne lub środowisko testowe w chmurze). Właściciel laboratorium tworzy laboratorium, aprowizuje maszyny wirtualne z systemem Windows lub Linux, instaluje niezbędne oprogramowanie i narzędzia oraz udostępnia je użytkownikom laboratorium. Użytkownicy laboratorium nawiązują połączenie z maszynami wirtualnymi w laboratorium i używają ich do codziennej pracy, krótkoterminowych projektów. Gdy użytkownicy rozpoczną korzystanie z zasobów w laboratorium, administrator laboratorium może analizować koszty i użycie w wielu laboratoriach, a następnie ustawić nadrzędne zasady w celu zoptymalizowania kosztów organizacji lub zespołu.
- **Azure Lab Services** — ta usługa umożliwia tworzenie zarządzanych typów laboratorium. Obecnie laboratoria klas jest jedynym typem zarządzanego laboratorium obsługiwanego przez Azure Lab Services. Sama usługa obsługuje całe zarządzanie infrastrukturą w typach laboratoriów zarządzanych, od inicjowania maszyn wirtualnych do obsługi błędów i skalowania infrastruktury. Po utworzeniu przez administratora IT konta laboratorium w Azure Lab Services instruktor może szybko skonfigurować laboratorium dla swojej klasy, określić liczbę i typ maszyn wirtualnych, które są potrzebne do ćwiczeń w klasie, i dodać użytkowników do klasy. Gdy użytkownik rejestruje się w klasie, użytkownik może uzyskać dostęp do maszyny wirtualnej w celu wykonania ćwiczeń dla klasy.  

## <a name="key-capabilities"></a>Najważniejsze możliwości

Te usługi (Azure DevTest Labs i Azure Lab Services) obsługują następujące kluczowe funkcje/funkcje:

- **Szybka i elastyczna konfiguracja laboratorium**. Przy użyciu usługi Azure Lab Services właściciele laboratoriów mogą szybko skonfigurować laboratorium odpowiednio do swoich potrzeb. Usługa może zająć się całą pracą związaną z infrastrukturą platformy Azure w typach laboratoriów zarządzanych. Alternatywnie właściciele laboratoriów mogą samodzielnie zarządzać infrastrukturą i dostosowywać ją w swojej subskrypcji. Usługa zapewnia wbudowane skalowanie i odporność infrastruktury w laboratoriach zarządzanych przez usługę.
- **Uproszczone środowisko dla użytkowników laboratorium**. W typie laboratorium zarządzanego, takim jak laboratorium na potrzeby zajęć, użytkownicy laboratorium mogą się zarejestrować za pomocą kodu rejestracji, a następnie uzyskać dostęp do laboratorium w dowolnym czasie, aby korzystać z zasobów laboratorium. W laboratorium utworzonym w usłudze DevTest Labs właściciel laboratorium może nadać użytkownikom laboratorium uprawnienia do tworzenia maszyn wirtualnych i uzyskiwania do nich dostępu, ponownego używania dysków z danymi i zarządzania nimi oraz konfigurowania wpisów tajnych wielokrotnego użytku.  
- **Optymalizacja i analiza kosztów**. Właściciel laboratorium może ustawić harmonogramy laboratorium w celu automatycznego zamykania i uruchamiania maszyn wirtualnych. Właściciel laboratorium może ustawić harmonogram, aby określić przedziały czasu, gdy maszyny wirtualne laboratorium są dostępne dla użytkowników, ustawić zasady użytkowania dla poszczególnych użytkowników lub laboratoriów w celu optymalizacji kosztów oraz analizować trendy używania i działań w laboratorium. W typach laboratoriów zarządzanych, takich jak laboratoria na potrzeby zajęć, obecnie dostępny jest mniejszy podzbiór opcji optymalizacji i analizy kosztów.
- **Osadzone zabezpieczenia**. Właściciel laboratorium może skonfigurować prywatne sieci i podsieci wirtualne dla laboratorium oraz włączyć udostępniony publiczny adres IP. Użytkownicy laboratorium mogą bezpiecznie uzyskać dostęp do zasobów przy użyciu sieci wirtualnej skonfigurowanej za pomocą usługi ExpressRoute i sieci VPN typu lokacja–lokacja. (Obecnie są one dostępne tylko w usłudze DevTest Labs).
- **Integracja z przepływami pracy i narzędziami**. Usługa Azure Lab Services umożliwia integrację laboratoriów z systemami witryny internetowej i zarządzania organizacji. Możesz automatycznie aprowizować środowiska za pomocą narzędzi ciągłej integracji/ciągłego wdrażania. (Obecnie są one dostępne tylko w usłudze DevTest Labs).

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono niektóre scenariusze, w których Azure DevTest Labs i Azure Lab Services support:

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Konfigurowanie laboratorium komputerowego o zmiennym rozmiarze w chmurze na potrzeby zajęć  

- Utwórz laboratorium zarządzane na potrzeby zajęć. Po prostu poinformuj usługę, czego dokładnie potrzebujesz, a ona utworzy infrastrukturę laboratorium i będzie nią zarządzać, dzięki czemu można będzie się skupić na nauczaniu, a nie na technicznych szczegółach laboratorium.
- Zapewnij uczniom laboratorium maszyn wirtualnych, na których skonfigurowano dokładnie to, co jest potrzebne na zajęciach. Przyznaj każdemu uczniowi ograniczoną liczbę godzin używania maszyn wirtualnych do pracy na zajęciach.  
- Przenieś fizyczne laboratorium komputerowe swojej szkoły do chmury. Automatycznie skaluj liczbę maszyn wirtualnych tylko do maksymalnego progu użycia i kosztów ustawionych dla laboratorium.
- Gdy skończysz pracę, usuń laboratorium jednym kliknięciem.

### <a name="use-devtest-labs-for-development-environments"></a>Używanie usługi DevTest Labs w środowiskach deweloperskich

Usługi Azure DevTest Labs można używać do wdrażania wielu kluczowych scenariuszy, ale jeden z podstawowych scenariuszy obejmuje używanie usługi DevTest Labs do hostowania maszyn deweloperskich dla deweloperów. W tym scenariuszu usługa DevTest Labs zapewnia następujące korzyści:

- Umożliwienie deweloperom szybkiej aprowizacji ich maszyn deweloperskich na żądanie.
- Aprowizacja środowiska systemu Windows i Linux za pomocą szablonów wielokrotnego użytku i artefaktów.
- Deweloperzy mogą łatwo dostosowywać swoje maszyny deweloperskie w miarę potrzeb.
- Administratorzy mogą kontrolować koszty, zapewniając, że deweloperzy nie mogą uzyskać więcej maszyn wirtualnych niż to niezbędne do programowania, a ponadto maszyny wirtualne są zamykane, gdy nie są używane.

Aby uzyskać więcej informacji, zobacz [Używanie usługi DevTest Labs do programowania](devtest-lab-developer-lab.md).

### <a name="use-devtest-labs-for-test-environments"></a>Korzystanie z usługi DevTest Labs na potrzeby środowisk testowych

Usługi Azure DevTest Labs możesz używać do wdrażania wielu kluczowych scenariuszy, ale podstawowy scenariusz polega na używaniu usługi DevTest Labs do hostowania maszyn dla testerów. W tym scenariuszu usługa DevTest Labs zapewnia następujące korzyści:

- Testerzy mogą przetestować najnowszą wersję swoich aplikacji dzięki możliwości szybkiej aprowizacji środowisk systemów Windows i Linux przy użyciu szablonów wielokrotnego użytku i artefaktów.
- Testerzy mogą skalować w górę swoje testowane obciążenie, aprowizując wielu agentów testowych.
- Administratorzy mogą kontrolować koszty, zapewniając, że testerzy nie mogą uzyskać więcej maszyn wirtualnych niż to niezbędne do testowania, a ponadto maszyny wirtualne są zamykane, gdy nie są używane.

Aby uzyskać więcej informacji, zobacz [Używanie usługi DevTest Labs do testowania](devtest-lab-test-env.md).

## <a name="types-of-labs"></a>Typy laboratoriów
Tworzyć możesz dwa typy laboratoriów: **typy laboratoriów zarządzanych** za pomocą usługi Azure Lab Services i **laboratoria** za pomocą usługi Azure Lab Services. Jeśli chcesz tylko wprowadzić to, czego potrzebujesz w laboratorium, i pozwolić usłudze na skonfigurowanie infrastruktury wymaganej w laboratorium oraz zarządzanie nią, wybierz jeden z **typów laboratoriów zarządzanych**. Obecnie **laboratorium na potrzeby zajęć** to jedyny typ laboratorium zarządzanego, który możesz utworzyć za pomocą usługi Azure Lab Services. Jeśli chcesz zarządzać własną infrastrukturą, Utwórz laboratorium przy użyciu **Azure DevTest Labs**.

Poniższe sekcje zawierają więcej szczegółowych informacji o tych laboratoriach. 

## <a name="managed-lab-types"></a>Typy laboratoriów zarządzanych
Usługa Azure Lab Services umożliwia tworzenie laboratoriów, których infrastruktura jest zarządzana przez platformę Azure. Ten artykuł mówi o nich jako o typach laboratoriów zarządzanych. Typy laboratoriów zarządzanych to różnego rodzaju laboratoria, które spełniają Twoje specyficzne wymagania. Obecnie jedynym obsługiwanym typem laboratorium zarządzanego jest **laboratorium na potrzeby zajęć**. 

Typy laboratoriów zarządzanych pozwalają od razu rozpocząć działanie przy minimalnej konfiguracji. Sama usługa obsługuje całe zarządzanie infrastrukturą w laboratorium, od inicjowania maszyn wirtualnych do obsługi błędów i skalowania infrastruktury. Aby utworzyć typ laboratorium zarządzanego, taki jak laboratorium na potrzeby zajęć, musisz najpierw utworzyć konto laboratorium dla Twojej organizacji. Konto laboratorium służy jako centralne konto, w ramach którego są zarządzane wszystkie laboratoria w organizacji. 

Podczas tworzenia i korzystania z zasobów platformy Azure w tych typach laboratoriów zarządzanych usługa tworzy zasoby i zarządza nimi w wewnętrznych subskrypcjach firmy Microsoft. Nie są one tworzone w ramach Twojej własnej subskrypcji platformy Azure. Usługa przechowuje informacje o użyciu tych zasobów w wewnętrznych subskrypcjach firmy Microsoft. To użycie jest rozliczane w ramach Twojej subskrypcji platformy Azure, która zawiera konto laboratorium.   

Poniżej przedstawiono niektóre **przypadki użycia typów laboratoriów zarządzanych**: 

- Zapewnij uczniom laboratorium maszyn wirtualnych, na których skonfigurowano dokładnie to, co jest potrzebne na zajęciach. Przyznaj każdemu uczniowi ograniczoną liczbę godzin używania maszyn wirtualnych do prac domowych lub projektów osobistych.
- Skonfiguruj pulę maszyn wirtualnych o wysokiej wydajności obliczeniowej do wykonywania badań wymagających znacznej mocy obliczeniowej lub graficznej. Uruchamiaj maszyny wirtualne zgodnie z potrzebami, a następnie usuwaj je po zakończeniu pracy. 
- Przenieś fizyczne laboratorium komputerowe swojej szkoły do chmury. Automatycznie skaluj liczbę maszyn wirtualnych tylko do maksymalnego progu użycia i kosztów ustawionych dla laboratorium.  
- Szybko aprowizuj laboratorium maszyn wirtualnych na potrzeby hostowania maratonu programistycznego. Gdy skończysz pracę, usuń laboratorium jednym kliknięciem. 


## <a name="devtest-labs"></a>DevTest Labs
W przypadku niektórych scenariuszy możesz chcieć samodzielnie zarządzać całą infrastrukturą i konfiguracją w ramach własnej subskrypcji. Aby to zrobić, możesz utworzyć laboratorium za pomocą usługi Azure DevTest Labs w witrynie Azure Portal. Dla tych laboratoriów nie jest konieczne tworzenie konta laboratorium. Te laboratoria nie są wyświetlane na koncie laboratorium (które istnieje dla typów laboratoriów zarządzanych).  

Poniżej przedstawiono niektóre **przypadki użycia dotyczące korzystania z DevTest Labs**: 

- Szybko aprowizuj laboratorium maszyn wirtualnych w celu hostowania maratonu programistycznego albo praktycznej sesji na konferencji. Gdy skończysz pracę, usuń laboratorium jednym kliknięciem. 
- Utwórz pulę maszyn wirtualnych ze skonfigurowaną Twoją aplikacją i umożliw zespołowi łatwe korzystanie z maszyn wirtualnych do usuwania usterek.  
- Zapewnij deweloperom maszyny wirtualne, na których skonfigurowano wszystkie potrzebne narzędzia. Zaplanuj automatyczne uruchamianie i zamykanie, aby zminimalizować koszty. 
- Wielokrotnie twórz laboratorium maszyn testowych jako część Twojego wdrożenia. Testuj najnowsze fragmenty i usuń maszyny testowe, gdy skończysz pracę. 
- Skonfiguruj wiele różnorodnie skonfigurowanych maszyn wirtualnych i wielu agentów testowych na potrzeby skalowania i testowania wydajności. 
- Oferuj sesje szkoleniowe swoim klientom, używając laboratorium skonfigurowanego przy użyciu najnowszej wersji swojego produktu. Przyznaj każdemu klientowi ograniczoną liczbę godzin korzystania z laboratorium. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Zarządzane typy laboratorium a DevTest Labs
W poniższej tabeli porównano dwa rodzaje laboratoriów, które są obsługiwane przez usługę Azure Lab Services: 

| Funkcje | Typy laboratoriów zarządzanych | DevTest Labs |
| -------- | ----------------- | ---------- |
| Zarządzanie infrastrukturą platformy Azure w laboratorium. |  Automatycznie zarządzane przez usługę | Zarządzane samodzielnie  |
| Wbudowana odporność na problemy z infrastrukturą | Automatycznie obsługiwane przez usługę | Zarządzane samodzielnie  |
| Zarządzanie subskrypcjami | Usługa obsługuje alokację zasobów w ramach subskrypcji firmy Microsoft wspierających usługę. Skalowanie jest obsługiwane automatycznie przez usługę. | Samodzielnie zarządzasz w ramach własnej subskrypcji platformy Azure. Brak automatycznego skalowania subskrypcji. |
| Wdrożenie usługi Azure Resource Manager w laboratorium | Niedostępne | Dostępne |

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły: 

- [Informacje na temat laboratoriów na potrzeby zajęć](../lab-services/classroom-labs-overview.md)
- [Informacje o usłudze DevTest Labs](devtest-lab-overview.md)
