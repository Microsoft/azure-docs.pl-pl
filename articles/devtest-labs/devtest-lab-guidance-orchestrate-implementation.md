---
title: Organizuj implementację Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące organizowania implementacji Azure DevTest Labs w organizacji.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1958e818f014b7419a1a33e9453fbad460dfc159
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330618"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Organizuj implementację Azure DevTest Labs
Ten artykuł zawiera zalecane podejście do szybkiego wdrażania i wdrażania Azure DevTest Labs. Na poniższej ilustracji przedstawiono ogólny proces zgodnie ze wskazówkami, które zapewnią elastyczność obsługi różnych wymagań branżowych i scenariuszy.

![Kroki wdrażania Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Założenia
W tym artykule założono, że masz następujące elementy na miejscu przed wdrożeniem pilotażu DevTest Labs:

- **Subskrypcja platformy Azure**: zespół pilotażowy ma dostęp do wdrażania zasobów w ramach subskrypcji platformy Azure. Jeśli obciążenia są tylko programowaniem i testowaniem, zaleca się wybranie oferty Enterprise DevTest dla dodatkowych dostępnych obrazów i niższych stawek w przypadku maszyn wirtualnych z systemem Windows.
- **Dostęp lokalny**: w razie potrzeby dostęp lokalny został już skonfigurowany. Dostęp do zasobów lokalnych można wykonać za pośrednictwem połączenia sieci VPN typu lokacja-lokacja lub za pośrednictwem usługi Express Route. Łączność za pośrednictwem usługi Express Route może zwykle trwać wiele tygodni, dlatego przed rozpoczęciem projektu zaleca się utworzenie trasy Express.
- **Zespoły pilotażowe**: projekty początkowych zespołów projektowych, które korzystają z DevTest Labs, zostały zidentyfikowane wraz z odpowiednimi działaniami deweloperskimi lub testowymi i określają wymagania/cele/przeznaczenie dla tych zespołów.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Punkt kontrolny 1: Ustanów początkową topologię sieci i projekt
Pierwszym obszarem fokusu podczas wdrażania rozwiązania Azure DevTest Labs jest ustanowienie planowanej łączności dla maszyn wirtualnych. Poniższe kroki przedstawiają niezbędne procedury:

1. Zdefiniuj **początkowe zakresy adresów IP** , które są przypisane do subskrypcji DevTest Labs na platformie Azure. Ten krok wymaga prognozowania oczekiwanego użycia w liczbie maszyn wirtualnych, dzięki czemu można dostarczyć wystarczająco duży blok do późniejszego rozwinięcia.
2. Zidentyfikuj **metody żądanego dostępu** do DevTest Labs (na przykład zewnętrzny/wewnętrzny dostęp). Najważniejszym punktem w tym kroku jest określenie, czy maszyny wirtualne mają publiczne adresy IP (to znaczy dostępne bezpośrednio z Internetu).
3. Zidentyfikuj i Ustanów **metody łączności** z resztą środowiska chmury platformy Azure i lokalnie. Jeśli wymuszone Routing z usługą Express Route jest włączony, prawdopodobnie maszyny wirtualne muszą mieć odpowiednie konfiguracje proxy, aby przechodzenie przez zaporę firmową.
4. Jeśli maszyny wirtualne mają zostać **przyłączone do domeny**, ustal, czy dołączą do domeny opartej na chmurze (na przykład usługi katalogowej AAD) lub domeny lokalnej. W przypadku lokalnego określania jednostki organizacyjnej (OU) w usłudze Active Directory, która jest przyłączana do maszyn wirtualnych. Ponadto upewnij się, że użytkownicy mają dostęp do przyłączania (lub ustanawiania konta usługi, które ma możliwość tworzenia rekordów maszyn w domenie).

## <a name="milestone-2-deploy-the-pilot-lab"></a>Punkt kontrolny 2: wdrażanie laboratorium pilotażowego
Gdy topologia sieci jest na miejscu, pierwsze/pilotażowe laboratorium można utworzyć, wykonując następujące czynności:

1. Utwórz początkowe środowisko DevTest Labs.
2. Określ dozwolone obrazy maszyn wirtualnych i rozmiary do użycia w laboratorium. Zdecyduj, czy obrazy niestandardowe można przekazać do platformy Azure w celu użycia z DevTest Labs.
3. Bezpieczny dostęp do laboratorium przez utworzenie początkowej kontroli dostępu opartej na rolach platformy Azure (RBAC) dla laboratorium (właściciele laboratorium i użytkownicy laboratorium). Zalecamy używanie zsynchronizowanych kont usługi Active Directory z usługą Azure Active Directory for Identity with DevTest Labs.
4. Skonfiguruj DevTest Labs, aby używać zasad, takich jak harmonogramy, zarządzanie kosztami, maszyny wirtualne do zajmowania, obrazy niestandardowe lub formuły.
5. Ustanów repozytorium online, takie jak Azure Repos/git.
6. Zdecyduj o użyciu publicznych lub prywatnych repozytoriów lub kombinacji obu tych elementów. Organizuj szablony JSON na potrzeby wdrożeń i długoterminowych warunków utrzymania.
7. W razie konieczności Utwórz niestandardowe artefakty. Ta czynność jest opcjonalna. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Punkt kontrolny 3: dokumentacja, pomoc techniczna, nauka i poprawa
Początkowe zespoły pilotażowe mogą wymagać szczegółowej pomocy technicznej dotyczącej rozpoczynania pracy. Skorzystaj z ich środowiska, aby upewnić się, że odpowiednia dokumentacja i pomoc techniczna są przeznaczone do ciągłego wdrażania Azure DevTest Labs.

1. Wprowadzenie zespołów pilotażowych do nowych zasobów DevTest Labs (demonstracje, dokumentacja)
2. W oparciu o doświadczenia zespołów pilotażowych Zaplanuj i dostarcz dokumentację zgodnie z wymaganiami
3. Prace prowadzone proces dołączania nowych zespołów (Tworzenie i Konfigurowanie laboratoriów, zapewnianie dostępu itp.)
4. W oparciu o początkowe pobieranie Sprawdź, czy pierwotna Prognoza przestrzeni adresów IP jest nadal uzasadniona i dokładna
5. Upewnij się, że zostały wykonane odpowiednie przeglądy zgodności i zabezpieczeń

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następnym artykułem z tej serii: [ładu of Azure DevTest Labs Infrastructure](devtest-lab-guidance-governance-resources.md)
