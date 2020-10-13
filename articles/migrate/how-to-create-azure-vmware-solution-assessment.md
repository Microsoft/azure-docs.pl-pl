---
title: Tworzenie oceny wersji zaautomatycznej z użyciem oceny serwera Azure Migrate | Microsoft Docs
description: Opisuje sposób tworzenia oceny automatycznej synchronizacji za pomocą narzędzia do oceny Azure Migrate Server
author: rashi-ms
ms.service: azure-migrate
ms.topic: article
ms.date: 06/26/2020
ms.author: rajosh
ms.openlocfilehash: 89dc962c73a6446afc79eec2a0fcfd5c0e32e84e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576714"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Tworzenie oceny rozwiązania Azure VMware (Automatyczna synchronizacja)

W tym artykule opisano sposób tworzenia oceny rozwiązania Azure VMware (Automatyczna synchronizacja) dla lokalnych maszyn wirtualnych VMware z Azure Migrate: Ocena serwera.

[Azure Migrate](migrate-services-overview.md) ułatwia Migrowanie do platformy Azure. Azure Migrate udostępnia scentralizowany centrum do śledzenia odnajdywania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure. Centrum udostępnia narzędzia platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera.
- Aby utworzyć ocenę, należy skonfigurować urządzenie Azure Migrate dla oprogramowania [VMware](how-to-set-up-appliance-vmware.md), które umożliwia odnajdywanie maszyn lokalnych i wysyłanie metadanych i danych wydajności do Azure Migrate: Ocena serwera. [Dowiedz się więcej](migrate-appliance.md).
- Można również [zaimportować metadane serwera](tutorial-assess-import.md) w formacie wartości rozdzielanych przecinkami (CSV).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Przegląd oceny rozwiązań VMware na platformie Azure (Automatyczna synchronizacja)

Istnieją dwa typy ocen, które można utworzyć przy użyciu Azure Migrate: Ocena serwera.

**Typ oceny** | **Szczegóły**
--- | --- 
**Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. <br/><br/> Możesz ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md), [maszyny wirtualne funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md)i [serwery fizyczne](how-to-set-up-appliance-physical.md) do migracji na platformę Azure przy użyciu tego typu oceny. [Dowiedz się więcej](concepts-assessment-calculation.md)
**Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające migrację serwerów lokalnych do usługi [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Przy użyciu tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md) pod kątem migracji do usługi Azure VMware Solution (AVS).[Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Ocena rozwiązań VMware (wersja zapoznawcza) systemu Azure jest obecnie dostępna w wersji zapoznawczej i można ją utworzyć tylko dla maszyn wirtualnych VMware.


Istnieją dwa typy kryteriów ustalania rozmiarów, których można użyć do tworzenia ocen rozwiązania Azure VMware (Automatyczna synchronizacja):

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Ocenia na podstawie zebranych danych wydajności lokalnych maszyn wirtualnych. | **Zalecany rozmiar węzła**: w oparciu o dane użycia procesora CPU i pamięci oraz typ węzła, typ magazynu i ustawienie FTT wybrane do oceny.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar węzła**: na podstawie rozmiaru lokalnego maszyny wirtualnej wraz z typem węzła, typem magazynu i USTAWIENIEm FTT, które zostało wybrane do oceny.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Uruchamianie oceny rozwiązania Azure VMware (Automatyczna synchronizacja)

Uruchom ocenę rozwiązania Azure VMware (Automatyczna synchronizacja) w następujący sposób:

1. Przejrzyj [najlepsze rozwiązania](best-practices-assessment.md) dotyczące tworzenia ocen.

2. Na karcie **serwery** w **Azure Migrate: kafelek Ocena serwera** kliknij pozycję **Oceń**.

    ![Zrzut ekranu przedstawia Azure Migrate serwery z oceną wybraną w obszarze narzędzia do oceny.](./media/how-to-create-assessment/assess.png)

3. W obszarze **ocenianie serwerów**wybierz typ oceny jako "rozwiązanie Azure VMware (Automatyczna synchronizacja)", wybierz źródło odnajdywania i określ nazwę oceny.

    ![Podstawowe informacje dotyczące oceny](./media/how-to-create-avs-assessment/assess-servers-avs.png)

4. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny automatycznej synchronizacji](./media/how-to-create-avs-assessment/avs-view-all.png)

5. Kliknij pozycję **Dalej**, aby **wybrać maszyny do oceny**. W obszarze **Wybierz lub utwórz grupę** wybierz pozycję **Utwórz nową** i określ nazwę grupy. Grupa zbiera jedną lub więcej maszyn wirtualnych w celu oceny.

6. W obszarze **Dodawanie maszyn do grupy**Wybierz Maszyny wirtualne, które mają zostać dodane do grupy.

7. Kliknij przycisk **Dalej**, aby przejść do pozycji **Przejrzyj i utwórz ocenę**, aby przejrzeć szczegóły dotyczące oceny.

8. Kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę, i uruchom ocenę.

    ![Tworzenie oceny w usłudze AVS](./media/how-to-create-avs-assessment/avs-assessment-create.png)

9. Po utworzeniu oceny możesz ją wyświetlić w pozycji **Serwery** > **Azure Migrate: Server Assessment** > **Oceny**.

10. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Zapoznaj się z oceną rozwiązania Azure VMware (Automatyczna synchronizacja)

Ocena rozwiązania Azure VMware (Automatyczna synchronizacja) zawiera opis:

- **Gotowość do rozwiązania Azure VMware (Automatyczna synchronizacja)**: czy lokalne maszyny wirtualne są odpowiednie do migracji do rozwiązania Azure VMware (Automatyczna synchronizacja).
- **Liczba węzłów automatycznej synchronizacji**: Szacowana liczba węzłów synchronizacji wymaganych do uruchomienia maszyn wirtualnych.
- **Wykorzystanie w węzłach automatycznej synchronizacji**: przewidywany procesor CPU, pamięć i wykorzystanie magazynu we wszystkich węzłach.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty wszystkich węzłów platformy Azure VMware (Automatyczna synchronizacja) z uruchomionymi lokalnymi maszynami wirtualnymi.


### <a name="view-an-assessment"></a>Widok oceny

1. W obszarze serwery **celów migracji**  >   **Servers**kliknij pozycję **oceny** w **Azure Migrate: Ocena serwera**.

2. W obszarze **oceny**kliknij ocenę, aby go otworzyć.

    ![Podsumowanie oceny automatycznej synchronizacji](./media/how-to-create-avs-assessment/avs-assessment-summary.png)

### <a name="review-azure-vmware-solution-avs-readiness"></a>Przegląd gotowości rozwiązania Azure VMware (Automatyczna synchronizacja)

1. W obszarze **gotowość platformy Azure**Sprawdź, czy maszyny wirtualne są gotowe do migracji na potrzeby automatycznej synchronizacji.

2. Sprawdź stan maszyny wirtualnej:
    - **Gotowe do automatycznej synchronizacji**: maszynę można migrować na platformę Azure (Automatyczna synchronizacja) bez wprowadzania żadnych zmian. Rozpocznie się to za pomocą automatycznej synchronizacji z pełną obsługą wersji zaautomatycznej.
    - **Gotowe z warunkami**: maszyna wirtualna może mieć problemy ze zgodnością z bieżącą wersją vSphere, a także wymaganie narzędzi VMware i innych ustawień, zanim będzie można uzyskać pełną funkcjonalność z maszyny wirtualnej w ramach automatycznej synchronizacji.
    - **Nie jest gotowe do automatycznej synchronizacji**: maszyna wirtualna nie będzie uruchamiana w wersji zaautomatycznej. Jeśli na przykład lokalna maszyna wirtualna VMware ma dołączone urządzenie zewnętrzne, takie jak dysk CD-ROM, operacja VMotion zakończy się niepowodzeniem (jeśli jest używana VMware VMotion).
    - **Nieznane gotowość**: Azure Migrate nie mógł ustalić gotowości maszyny z powodu niewystarczających metadanych zebranych ze środowiska lokalnego.

3. Przejrzyj sugerowane narzędzie:
    - **VMware HCX lub Enterprise**: w przypadku maszyn VMware, rozwiązanie hybrydowe w chmurze VMware (HCX) to sugerowane narzędzie do migracji, które umożliwia migrowanie lokalnego obciążenia do chmury prywatnej platformy Azure VMware (Automatyczna synchronizacja). [Dowiedz się więcej](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Nieznane**: w przypadku maszyn zaimportowanych za pośrednictwem pliku CSV domyślne narzędzie do migracji jest nieznane. Mimo że w przypadku maszyn VMware zaleca się użycie rozwiązania hybrydowego chmury VMware (HCX). 

4. Kliknij stan **gotowości do automatycznej synchronizacji** . Możesz wyświetlić szczegóły gotowości maszyn wirtualnych i przejść do szczegółów, aby wyświetlić szczegóły dotyczące maszyn wirtualnych, w tym ustawienia obliczeń, magazynu i sieci.



### <a name="review-cost-details"></a>Przegląd szczegółów dotyczących kosztów

Ten widok przedstawia szacowany koszt uruchamiania maszyn wirtualnych w usłudze Azure VMware Solution (Automatyczna synchronizacja).

1. Zapoznaj się z miesięczną sumą kosztów. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie. 

    - Oszacowania kosztów są uzależnione od liczby węzłów synchronizacji wymaganych do uwzględnienia wymagań dotyczących zasobów wszystkich maszyn wirtualnych.
    - Zgodnie z cennikiem rozwiązania Azure VMware (Automatyczna synchronizacja) na węzeł łączny koszt nie ma kosztu obliczeniowego i dystrybucji kosztów magazynu.
    - Oszacowanie kosztów dotyczy uruchamiania lokalnych maszyn wirtualnych w wersji zaautomatycznej. Ocena serwera Azure Migrate nie uwzględnia kosztów PaaS ani SaaS.
    
2. Możesz przejrzeć szacunkowe oszacowanie kosztów magazynowania. Ten widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu.

3. Możesz przejść do szczegółów, aby wyświetlić szczegóły dla określonych maszyn wirtualnych.


### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Po uruchomieniu ocen opartych na wydajności, do oceny jest przypisywany rating zgodności.

![Ocena zaufania](./media/how-to-create-assessment/confidence-rating.png)

- Nadawana jest Ocena z 1 – gwiazdka (najmniejsza) do 5-gwiazdka (najwyższa).
- Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru zapewnianych przez ocenę.
- Ocena zaufania jest oparta na dostępności punktów danych potrzebnych do obliczenia oceny.
- W przypadku ustalania wielkości na podstawie wydajności oceny wersji zabudowanej w ocenie serwera potrzebują danych użycia dla procesora CPU i pamięci maszyny wirtualnej. Następujące dane dotyczące wydajności są zbierane, ale nie są używane w zastosowaniach zaleceń dotyczących oceny automatycznej synchronizacji:
  - Liczba operacji we/wy na sekundę dla każdego dysku podłączonego do maszyny wirtualnej.
  - We/wy sieci do obsługi ustalania rozmiarów na podstawie wydajności dla każdej karty sieciowej podłączonej do maszyny wirtualnej.

Klasyfikacje zaufania dla oceny są następujące.

**Dostępność punktu danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

[Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md) o danych wydajności 


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [mapowania zależności](how-to-create-group-machine-dependencies.md) do tworzenia grup o wysokim poziomie ufności.
- [Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md) o tym, jak są obliczane oceny automatycznej synchronizacji.
