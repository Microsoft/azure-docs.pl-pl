---
title: Informacje o usłudze Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak usługa Lab Services może ułatwić tworzenie i zabezpieczanie laboratoriów z maszynami wirtualnymi, które mogą być używane przez deweloperów, testerów, nauczycieli, uczniów itd., a także zarządzanie tymi laboratoriami.
ms.topic: overview
ms.date: 09/16/2020
ms.openlocfilehash: ad17ebb3a803a15d1ac9ef8cb71cf8ca7976243b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91333940"
---
# <a name="an-introduction-to-azure-lab-services"></a>Wprowadzenie do usługi Azure Lab Services
**Azure Lab Services** umożliwia tworzenie laboratoriów, których infrastruktura jest zarządzana przez platformę Azure. Obecnie laboratorium klasy jest jedynym typem zarządzanego laboratorium, które jest obsługiwane przez Azure Lab Services. Sama usługa obsługuje wszystkie zarządzanie infrastrukturą dla zarządzanego typu laboratorium, od nadawania maszyn wirtualnych do obsługi błędów i skalowania infrastruktury. Po utworzeniu przez administratora IT konta laboratorium w Azure Lab Services instruktor może szybko skonfigurować laboratorium dla klasy, określić liczbę i typ maszyn wirtualnych, które są niezbędne do ćwiczeń w klasie, i dodać użytkowników do klasy. Gdy użytkownik rejestruje się w klasie, użytkownik może uzyskać dostęp do maszyny wirtualnej w celu wykonania ćwiczeń dla klasy.  

## <a name="key-capabilities"></a>Najważniejsze możliwości
Usługa Azure Lab Services obsługuje następujące kluczowe możliwości/funkcje:

- **Szybka i elastyczna konfiguracja laboratorium**. Przy użyciu usługi Azure Lab Services właściciele laboratoriów mogą szybko skonfigurować laboratorium odpowiednio do swoich potrzeb. Usługa oferuje możliwość skorzystania z całej infrastruktury platformy Azure dla zarządzanych typów laboratorium. Usługa zapewnia wbudowane skalowanie i odporność infrastruktury w laboratoriach zarządzanych przez usługę.
- **Uproszczone środowisko dla użytkowników laboratorium**. Użytkownicy laboratorium mogą rejestrować się w laboratorium z kodem rejestracyjnym i uzyskiwać dostęp do laboratorium w dowolnym momencie, aby korzystać z zasobów laboratorium. 
- **Optymalizacja i analiza kosztów**. Właściciel laboratorium może ustawić harmonogramy laboratorium w celu automatycznego zamykania i uruchamiania maszyn wirtualnych. Właściciel laboratorium może ustawić harmonogram, aby określić czas, w którym maszyny wirtualne laboratorium są dostępne dla użytkowników, i ustawić zasady użycia na użytkownika lub dla laboratorium w celu optymalizacji kosztów. 

Jeśli chcesz tylko wprowadzić to, czego potrzebujesz w laboratorium, i pozwolić usłudze na skonfigurowanie infrastruktury wymaganej w laboratorium oraz zarządzanie nią, wybierz jeden z **typów laboratoriów zarządzanych**. Obecnie **laboratorium na potrzeby zajęć** to jedyny typ laboratorium zarządzanego, który możesz utworzyć za pomocą usługi Azure Lab Services.

Poniższe sekcje zawierają więcej szczegółowych informacji o tych laboratoriach. 

## <a name="managed-lab-types"></a>Typy laboratoriów zarządzanych
Usługa Azure Lab Services umożliwia tworzenie laboratoriów, których infrastruktura jest zarządzana przez platformę Azure. Ten artykuł mówi o nich jako o typach laboratoriów zarządzanych. Typy laboratoriów zarządzanych to różnego rodzaju laboratoria, które spełniają Twoje specyficzne wymagania. Obecnie jedynym obsługiwanym typem laboratorium jest " **pracownie**". 

Typy laboratoriów zarządzanych pozwalają od razu rozpocząć działanie przy minimalnej konfiguracji. Sama usługa obsługuje wszystkie zarządzanie infrastrukturą w laboratorium, od wydawania maszyn wirtualnych do obsługi błędów i skalowania infrastruktury.Aby utworzyć typ laboratorium zarządzanego, taki jak laboratorium na potrzeby zajęć, musisz najpierw utworzyć konto laboratorium dla Twojej organizacji. Konto laboratorium służy jako centralne konto, w ramach którego są zarządzane wszystkie laboratoria w organizacji. 

Podczas tworzenia i korzystania z zasobów platformy Azure w tych typach laboratoriów zarządzanych usługa tworzy zasoby i zarządza nimi w wewnętrznych subskrypcjach firmy Microsoft. Nie są one tworzone w ramach Twojej własnej subskrypcji platformy Azure. Usługa przechowuje informacje o użyciu tych zasobów w wewnętrznych subskrypcjach firmy Microsoft. To użycie jest rozliczane w ramach Twojej subskrypcji platformy Azure, która zawiera konto laboratorium.   

Poniżej przedstawiono niektóre **przypadki użycia typów laboratoriów zarządzanych**: 

- Zapewnij uczniom laboratorium maszyn wirtualnych, na których skonfigurowano dokładnie to, co jest potrzebne na zajęciach. Przyznaj każdemu uczniowi ograniczoną liczbę godzin używania maszyn wirtualnych do prac domowych lub projektów osobistych.
- Skonfiguruj pulę maszyn wirtualnych o wysokiej wydajności obliczeniowej do wykonywania badań wymagających znacznej mocy obliczeniowej lub graficznej. Uruchamiaj maszyny wirtualne zgodnie z potrzebami, a następnie usuwaj je po zakończeniu pracy. 
- Przenieś fizyczne laboratorium komputerowe swojej szkoły do chmury. Automatycznie skaluj liczbę maszyn wirtualnych tylko do maksymalnego progu użycia i kosztów ustawionych dla laboratorium.  
- Szybko aprowizuj laboratorium maszyn wirtualnych na potrzeby hostowania maratonu programistycznego. Gdy skończysz pracę, usuń laboratorium jednym kliknięciem. 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższymi samouczkami, aby uzyskać instrukcje krok po kroku dotyczące tworzenia konta laboratorium i tworzenia laboratorium zajęć.

- [Samouczek: Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)
- [Samouczek: Tworzenie laboratorium zajęć](tutorial-setup-classroom-lab.md)
