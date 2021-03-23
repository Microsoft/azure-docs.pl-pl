---
title: Tworzenie oceny automatycznej wersji zaAzure Migrateej | Microsoft Docs
description: Opisuje sposób tworzenia oceny automatycznej wersji z Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 72372e6365a2535e449681549a515c3f8594f2f1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786604"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Tworzenie oceny rozwiązania Azure VMware (Automatyczna synchronizacja)

W tym artykule opisano sposób tworzenia oceny rozwiązania Azure VMware (Automatyczna synchronizacja) dla serwerów lokalnych w środowisku VMware przy użyciu Azure Migrate: odnajdywania i oceny.

[Azure Migrate](migrate-services-overview.md) ułatwia Migrowanie do platformy Azure. Azure Migrate udostępnia scentralizowany centrum do śledzenia odnajdywania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure. Centrum udostępnia narzędzia platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że [utworzono](./create-manage-projects.md) projekt Azure Migrate.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) Azure Migrate: narzędzie odnajdywania i oceny.
- Aby utworzyć ocenę, należy skonfigurować urządzenie Azure Migrate dla oprogramowania [VMware](how-to-set-up-appliance-vmware.md), które umożliwia odnajdywanie serwerów lokalnych i wysyłanie metadanych oraz danych wydajności do Azure Migrate: odnajdywanie i ocenianie. [Dowiedz się więcej](migrate-appliance.md).
- Można również [zaimportować metadane serwera](./tutorial-discover-import.md) w formacie wartości rozdzielanych przecinkami (CSV).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Przegląd oceny rozwiązań VMware na platformie Azure (Automatyczna synchronizacja)

Istnieją trzy typy ocen, które można utworzyć przy użyciu Azure Migrate: odnajdywania i oceny.

***Typ oceny** | **Szczegóły**
--- | --- 
**Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. Można ocenić serwery lokalne w środowisku [VMware](how-to-set-up-appliance-vmware.md) i [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md) oraz [serwery fizyczne](how-to-set-up-appliance-physical.md) do migracji do maszyn wirtualnych platformy Azure przy użyciu tego typu oceny.
**Azure SQL** | Ocenianie migracji lokalnych serwerów SQL ze środowiska VMware do Azure SQL Database lub wystąpienia zarządzanego Azure SQL.
**Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające migrację serwerów lokalnych do usługi [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Możesz ocenić serwery lokalne w [środowisku VMware](how-to-set-up-appliance-vmware.md) , aby przeprowadzić migrację do rozwiązania Azure VMware (Automatyczna synchronizacja) przy użyciu tego typu oceny. [Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Ocenę rozwiązań VMware (Automatyczna synchronizacja) można utworzyć tylko dla serwerów w środowisku VMware.


Istnieją dwa typy kryteriów ustalania rozmiarów, których można użyć do tworzenia ocen rozwiązania Azure VMware (Automatyczna synchronizacja):

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny na podstawie zebranych danych wydajności serwerów lokalnych. | **Zalecany rozmiar węzła**: w oparciu o dane użycia procesora CPU i pamięci oraz typ węzła, typ magazynu i ustawienie FTT wybrane do oceny.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar węzła**: w zależności od rozmiaru serwera lokalnego wraz z typem węzła, typem magazynu i USTAWIENIEm FTT, które zostało wybrane do oceny.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Uruchamianie oceny rozwiązania Azure VMware (Automatyczna synchronizacja)

1.  Na stronie **przegląd** > **Windows, Linux i SQL Server** kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Strona przeglądu Azure Migrate":::

1. W **Azure Migrate: odnajdywanie i ocenianie** kliknij pozycję **Oceń**.

   ![Lokalizacja przycisku oceny](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. W obszarze **Szacuj**  >  **Typ oceny** serwerów, wybierz pozycję **Azure VMware Solution (Automatyczna synchronizacja)**.

1. W **źródle odnajdywania**:

    - Jeśli wykryto serwery korzystające z urządzenia, wybierz pozycję **serwery odnalezione z urządzenia Azure Migrate**.
    - W przypadku wykrycia serwerów przy użyciu zaimportowanego pliku CSV wybierz pozycję **zaimportowane serwery**. 
    
1. Kliknij przycisk **Edytuj** , aby przejrzeć właściwości oceny.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Strona do wybierania ustawień oceny":::
 

1. We właściwościach  >  **elementu docelowego** właściwości oceny:

    - W polu **Lokalizacja docelowa** Określ region platformy Azure, do którego chcesz przeprowadzić migrację.
       - Zalecenia dotyczące rozmiaru i kosztu są zależne od określonej lokalizacji.
   - **Typ magazynu** jest domyślnie **sieci vSAN**. Jest to domyślny typ magazynu dla chmury prywatnej automatycznej synchronizacji.
   - **Wystąpienia zarezerwowane** nie są obecnie obsługiwane w przypadku węzłów automatycznej synchronizacji.
1. **Rozmiar maszyny wirtualnej**:
    - **Typ węzła** jest domyślnie **AV36**. Azure Migrate zaleca węzeł węzłów wymaganych do migracji serwerów do wersji zaautomatycznej.
    - W **ustawieniu FTT, na poziomie RAID**, wybierz niepowodzenie i kombinację RAID.  Wybrana opcja FTT, w połączeniu z wymaganiem dysku serwera lokalnego, określa łączny magazyn sieci vSAN wymagany w ramach automatycznej synchronizacji.
    - W obszarze **nadsubskrypcja procesora CPU** Określ stosunek rdzeni wirtualnych skojarzonych z jednym rdzeniem fizycznym w WĘŹLE automatyczna synchronizacja. Nadsubskrypcja większa niż 4:1 może powodować spadek wydajności, ale może być używana do obciążeń typu serwer sieci Web.
    - W obszarze współczynnik nadmiernej **zatwierdzeń pamięci** Określ stosunek ilości pamięci przez zatwierdzenie w klastrze. Wartość 1 reprezentuje 100% wykorzystania pamięci, 0,5 na przykład jest 50%, a 2 będzie używać 200% dostępnej pamięci. Można dodawać tylko wartości z prze0,5 do 10 do jednego miejsca dziesiętnego.
    - W polu **Deduplikacja i współczynnik kompresji** Określ przewidywany współczynnik deduplikacji i kompresji dla obciążeń. Rzeczywista wartość może być uzyskana z lokalnej konfiguracji sieci vSAN lub magazynu i może się to różnić w zależności od obciążenia. Wartość 3 oznacza, że dla dysku 300 GB zostanie użyty dysk o pojemności 100 GB. Wartość 1 oznacza brak dekompresu lub kompresji. Można dodawać wartości od 1 do 10 do jednego miejsca dziesiętnego.
1. W obszarze **rozmiar węzła**: 
    - W **kryterium ustalania wielkości** wybierz, czy chcesz oprzeć ocenę metadanych statycznych, czy też na danych na podstawie wydajności. W przypadku korzystania z danych wydajności:
        - W obszarze **historia wydajności** wskaż czas trwania danych, dla którego chcesz oprzeć ocenę
        - W polu **użycie percentyla** Określ wartość percentylu, która ma być używana dla przykładu wydajności. 
    - W polu **czynnik komfortu** wskaż bufor, który ma być używany podczas oceny. Te konta dotyczące problemów, takich jak sezonowe użycie, krótka historia wydajności i prawdopodobnie wzrastają w przyszłości. Na przykład, jeśli jest używany współczynnik komfortu równy dwa:
    
        **Składnik** | **Efektywne wykorzystanie** | **Dodaj współczynnik komfortu (2,0)**
        --- | --- | ---
        Rdzenie | 2  | 4
        Pamięć | 8 GB | 16 GB  

1. W **cenniku**:
    - Oferta [platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , która została zarejestrowana w **programie, jest** wyświetlana w systemie. Ocena szacuje koszt tej oferty.
    - W polu **Waluta** Wybierz walutę rozliczeń dla Twojego konta.
    - W polu **Rabat (%)** Dodaj wszelkie zniżki specyficzne dla subskrypcji otrzymane w oparciu o ofertę platformy Azure. Ustawienie domyślne to 0%.

1. Jeśli wprowadzisz zmiany, kliknij przycisk **Zapisz** .

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Właściwości oceny":::

1. W obszarze **ocenianie serwerów** kliknij przycisk **dalej**.

1. Na stronie **Wybierz serwery do oceny**  >  **nazwy oceny** > Określ nazwę oceny. 
 
1. W obszarze **Wybierz lub Utwórz grupę** > wybierz pozycję **Utwórz nową** i określ nazwę grupy. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Dodawanie serwerów do grupy":::
 
1. Wybierz urządzenie i wybierz serwery, które chcesz dodać do grupy. Następnie kliknij przycisk **Dalej**.

1. W obszarze **Przegląd + tworzenie oceny** Przejrzyj szczegóły oceny, a następnie kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę i uruchomić ocenę.

    > [!NOTE]
    > W przypadku ocen opartych na wydajności zalecamy odczekanie co najmniej dnia od momentu rozpoczęcia odnajdywania przed utworzeniem oceny. Zapewnia to czas na zbieranie danych o wydajności z wyższym zaufaniem. Najlepiej po rozpoczęciu odnajdywania poczekaj na określenie czasu trwania wydajności (dzień/tydzień/miesiąc) w celu uzyskania oceny o wysokim poziomie zaufania.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Zapoznaj się z oceną rozwiązania Azure VMware (Automatyczna synchronizacja)

Ocena rozwiązania Azure VMware (Automatyczna synchronizacja) zawiera opis:

- **Gotowość do rozwiązania Azure VMware (Automatyczna synchronizacja)**: czy serwery lokalne są odpowiednie do migracji do rozwiązania Azure VMware (Automatyczna synchronizacja).
- **Liczba węzłów automatycznej synchronizacji**: Szacowana liczba węzłów automatycznej synchronizacji wymaganych do uruchomienia serwerów.
- **Wykorzystanie w węzłach automatycznej synchronizacji**: przewidywany procesor CPU, pamięć i wykorzystanie magazynu we wszystkich węzłach.
    - Użycie obejmuje czołową fabrykę w poniższych oddziałach zarządzania klastrami, takimi jak vCenter Server, NSX Manager (duże), NSX Edge, jeśli HCX jest wdrożona, również w Menedżerze HCX i w IXm wykorzystaniu urządzenia ~ 44vCPU (11 CPU), 75 GB pamięci RAM i 722GB magazynu przed kompresją i deduplikacją.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty wszystkich węzłów platformy Azure VMware (Automatyczna synchronizacja) z uruchomionymi lokalnymi maszynami wirtualnymi.


### <a name="view-an-assessment"></a>Widok oceny

1. W **systemach Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i ocenianie** kliknij liczbę obok * * rozwiązanie Azure VMware * *.

1. W pozycji **Oceny** wybierz ocenę, aby ją otworzyć. Przykładowo (oszacowania i koszty tylko na przykład): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Podsumowanie oceny automatycznej synchronizacji":::

1. Przejrzyj podsumowanie oceny. Możesz również edytować właściwości oceny lub ponownie obliczyć ocenę.

### <a name="review-azure-vmware-solution-avs-readiness"></a>Przegląd gotowości rozwiązania Azure VMware (Automatyczna synchronizacja)

1. W obszarze **gotowość do platformy Azure** Sprawdź, czy serwery są gotowe do migracji do wersji zastosowanej do synchronizacji.

2. Sprawdź stan serwera:
    - **Gotowe do automatycznej synchronizacji**: serwer można migrować na platformę Azure (Automatyczna synchronizacja) bez wprowadzania żadnych zmian. Rozpocznie się to za pomocą automatycznej synchronizacji z pełną obsługą wersji zaautomatycznej.
    - **Gotowe z warunkami**: serwer może mieć problemy ze zgodnością z bieżącą wersją vSphere, a także wymaganie narzędzi VMware i innych ustawień, zanim będzie można uzyskać pełną funkcjonalność z serwera w ramach automatycznej synchronizacji.
    - **Nie jest gotowe do automatycznej synchronizacji**: serwer nie zostanie uruchomiony w wersji zaautomatycznej. Na przykład jeśli serwer lokalny ma dołączone urządzenie zewnętrzne, takie jak dysk CD-ROM, operacja VMotion zakończy się niepowodzeniem (jeśli jest używana VMware VMotion).
    - **Nieznane gotowość**: Azure Migrate nie mógł ustalić gotowości serwera z powodu niewystarczających metadanych zebranych ze środowiska lokalnego.

3. Przejrzyj sugerowane narzędzie:
    - **VMware HCX lub Enterprise**: w przypadku serwerów VMware, rozwiązanie hybrydowe chmury VMware (HCX) to sugerowane narzędzie do migracji w celu migrowania obciążenia lokalnego do chmury prywatnej Azure VMware (Automatyczna synchronizacja). [Dowiedz się więcej](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Nieznane**: w przypadku serwerów zaimportowanych za pośrednictwem pliku CSV, domyślne narzędzie migracji jest nieznane. W przypadku serwerów VMware zaleca się korzystanie z rozwiązania hybrydowego chmury VMware (HCX). 

4. Kliknij stan **gotowości do automatycznej synchronizacji** . Możesz wyświetlić szczegóły gotowości maszyn wirtualnych i przejść do szczegółów, aby wyświetlić szczegóły dotyczące maszyn wirtualnych, w tym ustawienia obliczeń, magazynu i sieci.

### <a name="review-cost-details"></a>Przegląd szczegółów dotyczących kosztów

Ten widok przedstawia szacowany koszt uruchamiania serwerów w ramach rozwiązania Azure VMware (Automatyczna synchronizacja).

1. Zapoznaj się z miesięczną sumą kosztów. Koszty są agregowane dla wszystkich serwerów w ocenianej grupie. 

    - Oszacowania kosztów są uzależnione od liczby węzłów synchronizacji wymaganych do uwzględnienia wymagań dotyczących zasobów wszystkich serwerów w sumie.
    - Zgodnie z cennikiem rozwiązania Azure VMware (Automatyczna synchronizacja) na węzeł łączny koszt nie ma kosztu obliczeniowego i dystrybucji kosztów magazynu.
    - Oszacowanie kosztów umożliwia uruchamianie serwerów lokalnych w ramach automatycznej synchronizacji. Ocena automatycznej wersji nie uwzględnia kosztów PaaS ani SaaS.
    
2. Możesz przejrzeć szacunkowe oszacowanie kosztów magazynowania. Ten widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu.

3. Możesz przejść do szczegółów, aby zobaczyć szczegóły określonych serwerów.


### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Po uruchomieniu ocen opartych na wydajności, do oceny jest przypisywany rating zgodności.

![Ocena zaufania](./media/how-to-create-assessment/confidence-rating.png)

- Nadawana jest Ocena z 1 – gwiazdka (najmniejsza) do 5-gwiazdka (najwyższa).
- Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru zapewnianych przez ocenę.
- Ocena zaufania jest oparta na dostępności punktów danych potrzebnych do obliczenia oceny.
- W przypadku ustalania wielkości na podstawie wydajności oceny automatycznej synchronizacji wymagają danych użycia dla procesora CPU i pamięci serwera. Następujące dane dotyczące wydajności są zbierane, ale nie są używane w zastosowaniach zaleceń dotyczących oceny automatycznej synchronizacji:
  - Liczba operacji we/wy na sekundę dla każdego dysku podłączonego do serwera.
  - We/wy sieci do obsługi ustalania rozmiarów na podstawie wydajności dla każdej karty sieciowej podłączonej do serwera.

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