---
title: Organizowanie implementacji laboratoriów DevTest w usłudze Azure
description: Ten artykuł zawiera wskazówki dotyczące organizowania implementacji platformy Azure DevTest Labs w organizacji.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: fc6cbbd0aa9cb0750e497c7cc7edbd42f21bda55
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025003"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Organizowanie implementacji laboratoriów DevTest azure
Ten artykuł zawiera zalecane podejście do szybkiego wdrażania i wdrażania platformy Azure DevTest Labs. Poniższa ilustracja podkreśla ogólny proces jako wskazówki nakazowe, przestrzegając jednocześnie elastyczności obsługi różnych wymagań i scenariuszy branżowych.

![Kroki wdrażania laboratoriów DevTest Azure](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Założenia
W tym artykule założono, że masz następujące elementy w miejscu przed wdrożeniem pilotażu DevTest Labs:

- **Subskrypcja platformy Azure:** zespół pilotażowy ma dostęp do wdrażania zasobów w ramach subskrypcji platformy Azure. Jeśli obciążenia są tylko rozwoju i testowania, zaleca się, aby wybrać ofertę Enterprise DevTest dla dodatkowych dostępnych obrazów i niższych stawek na maszynach wirtualnych systemu Windows.
- **Dostęp lokalny**: W razie potrzeby dostęp lokalny został już skonfigurowany. Dostęp lokalny można uzyskać za pośrednictwem połączenia sieci VPN lokacja lokacja lub za pośrednictwem trasy ekspresowej. Połączenie za pośrednictwem trasy ekspresowej może zwykle potrwać wiele tygodni, aby ustalić, zaleca się, aby mieć express route w miejscu przed rozpoczęciem projektu.
- **Zespoły pilotażowe:** Początkowy zespół (zespoły) projektu rozwoju, który korzysta z DevTest Labs został zidentyfikowany wraz z odpowiednimi działaniami rozwojowymi lub testowymi i ustanawia wymagania/cele/cele dla tych zespołów.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Kamień milowy 1: Ustanowienie wstępnej topologii i projektowania sieci
Pierwszym obszarem zainteresowania podczas wdrażania rozwiązania Azure DevTest Labs jest ustanowienie planowanej łączności dla maszyn wirtualnych. Następujące kroki przedstawiają niezbędne procedury:

1. Zdefiniuj **początkowe zakresy adresów IP,** które są przypisane do subskrypcji DevTest Labs na platformie Azure. Ten krok wymaga prognozowania oczekiwanego użycia w liczbie maszyn wirtualnych, dzięki czemu można podać wystarczająco duży blok dla przyszłego rozszerzenia.
2. Identyfikowanie **metod żądanego dostępu** do Laboratoriów DevTest (na przykład dostęp zewnętrzny / wewnętrzny). Kluczowym punktem w tym kroku jest ustalenie, czy maszyny wirtualne mają publiczne adresy IP (czyli dostępne bezpośrednio z Internetu).
3. Identyfikowanie i **ustanawianie metod łączności** z pozostałymi środowiskach chmury platformy Azure i lokalnymi. Jeśli wymuszona routing z marszrutą ekspresową jest włączona, prawdopodobnie maszyny wirtualne potrzebują odpowiednich konfiguracji serwera proxy, aby przechodzić przez zaporę firmową.
4. Jeśli maszyny wirtualne mają być **przyłączone**do domeny, należy określić, czy dołączają do domeny opartej na chmurze (na przykład usługi katalogowe AAD) lub domeny lokalnej. W przypadku jednostek lokalnych należy określić, która jednostka organizacyjna (OU) w usłudze Active Directory, do której przyłączają się maszyny wirtualne. Ponadto upewnij się, że użytkownicy mają dostęp do dołączenia (lub ustanowić konto usługi, które ma możliwość tworzenia rekordów komputera w domenie)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Kamień milowy 2: Wdrożenie laboratorium pilotażowego
Po wprowadzeniu topologii sieci można utworzyć pierwsze/pilotażowe laboratorium, wykonując następujące kroki:

1. Utwórz początkowe środowisko DevTest Labs.
2. Określ dopuszczalne obrazy i rozmiary maszyn wirtualnych do użycia w laboratorium. Zdecyduj, czy obrazy niestandardowe mogą być przekazywane na platformę Azure do użytku z DevTest Labs.
3. Bezpieczny dostęp do laboratorium przez utworzenie początkowych kontroli dostępu do bazy ról (RBAC) dla laboratorium (właścicieli laboratoriów i użytkowników laboratorium). Zaleca się używanie zsynchronizowanych kont usługi Active Directory z usługą Azure Active Directory dla tożsamości w laboratoriach DevTest.
4. Skonfiguruj laboratoria DevTest, aby używały zasad, takich jak harmonogramy, zarządzanie kosztami, maszyny wirtualne, niestandardowe obrazy lub formuły.
5. Ustanowienie repozytorium online, takiego jak Repozytorium azure/Git.
6. Zdecyduj o korzystaniu z publicznych lub prywatnych repozytoriów lub kombinacji obu tych repozytoriów. Organizowanie szablonów JSON dla wdrożeń i długoterminowego utrzymania.
7. W razie potrzeby utwórz niestandardowe artefakty. Ten krok jest opcjonalny. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Kamień milowy 3: Dokumentacja, wsparcie, nauka i ulepszanie
Początkowe zespoły pilotażowe mogą wymagać dogłębnej pomocy technicznej przy rozpoczęciu pracy. Użyj ich środowiska, aby upewnić się, że odpowiednia dokumentacja i pomoc techniczna jest w miejscu do dalszego wdrażania platformy Azure DevTest Labs.

1. Przedstawianie zespołów pilotażowych do nowych zasobów DevTest Labs (wersje demonstracyjne, dokumentacja)
2. W oparciu o doświadczenia zespołów pilotażowych, planuj i dostarczaj dokumentację w razie potrzeby
3. Sformalizować proces dołączania nowych zespołów (tworzenie i konfigurowanie laboratoriów, zapewnianie dostępu itp.)
4. Na podstawie początkowego wykorzystania, sprawdź, czy pierwotna prognoza przestrzeni adresów IP jest nadal rozsądna i dokładna
5. Zapewnienie odpowiednich przeglądów zgodności i zabezpieczeń

## <a name="next-steps"></a>Następne kroki
Zobacz następny artykuł z tej serii: [Zarządzanie infrastrukturą laboratoriów azure devtest](devtest-lab-guidance-governance-resources.md)
