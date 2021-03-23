---
title: Oceniaj serwery VMware na potrzeby migracji do rozwiązania Azure VMware (Automatyczna synchronizacja) za pomocą Azure Migrate
description: Dowiedz się, jak ocenić serwery w środowisku VMware, aby przeprowadzić migrację do automatycznej synchronizacji z Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 31bf3909012231996bd340cfa4d388f0fe20a4f5
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782149"
---
# <a name="tutorial-assess-vmware-servers-for-migration-to-avs"></a>Samouczek: Ocena serwerów VMware na potrzeby migracji do automatycznej synchronizacji

W ramach kursu migracji do platformy Azure oceniasz swoje obciążenia lokalne, aby mierzyć gotowość do chmury, identyfikować zagrożenia i oceniać koszty i złożoność.

W tym artykule przedstawiono sposób oceny odnalezionych maszyn wirtualnych VMware/serwerów na potrzeby migracji do rozwiązania Azure VMware (Automatyczna synchronizacja) przy użyciu Azure Migrate. Automatyczna synchronizacja to usługa zarządzana, która umożliwia uruchamianie platformy VMware na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
- Uruchom ocenę na podstawie metadanych serwera i informacji konfiguracyjnych.
- Uruchom ocenę na podstawie danych wydajności.

> [!NOTE]
> Samouczki przedstawiają najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych, tam gdzie to możliwe. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).



## <a name="prerequisites"></a>Wymagania wstępne

Przed skorzystaniem z tego samouczka, aby ocenić serwery do celów migracji do automatycznej synchronizacji, upewnij się, że zostały wykryte serwery, które chcesz ocenić:

- Aby odnajdywać serwery przy użyciu urządzenia Azure Migrate, [postępuj zgodnie z tym samouczkiem](tutorial-discover-vmware.md). 
- Aby odnajdywać serwery przy użyciu zaimportowanego pliku CSV, [postępuj zgodnie z tym samouczkiem](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Wybór oceny do uruchomienia


Zdecyduj, czy chcesz uruchomić ocenę przy użyciu kryteriów ustalania rozmiarów na podstawie danych konfiguracji serwera/metadanych zebranych jako lokalne lub na danych dotyczących wydajności dynamicznej.

**Ocena** | **Szczegóły** | **Zalecenie**
--- | --- | ---
**Zgodnie ze środowiskiem lokalnym** | Oceń dane na podstawie konfiguracji serwera/metadanych.  | Zalecany rozmiar węzła w ramach automatycznej synchronizacji jest określany na podstawie lokalnego rozmiaru maszyny wirtualnej/serwera, a także ustawień określonych w ocenie dla typu węzła, typu magazynu i ustawienia odporności na uszkodzenia.
**Na podstawie wydajności** | Oceniaj na podstawie zebranych danych o wydajności dynamicznej. | Zalecany rozmiar węzła w ramach automatycznej synchronizacji jest oparty na danych użycia procesora i pamięci, a także ustawień określonych w ocenie dla typu węzła, typu magazynu i ustawienia odporności na uszkodzenia.

> [!NOTE]
> Ocenę rozwiązań VMware (Automatyczna synchronizacja) można utworzyć tylko dla maszyn wirtualnych VMware/serwerów.

## <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

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

## <a name="review-an-assessment"></a>Przegląd oceny

W ramach oceny automatycznej wersji zamieszczono następujące informacje:

- Gotowość do automatycznej synchronizacji: czy serwery lokalne są odpowiednie do migracji do rozwiązania Azure VMware (Automatyczna synchronizacja).
- Liczba węzłów automatycznej synchronizacji: Szacowana liczba węzłów automatycznej synchronizacji wymaganych do uruchomienia serwerów.
- Wykorzystanie w węzłach automatycznej synchronizacji: przewidywany procesor CPU, pamięć i wykorzystanie magazynu we wszystkich węzłach.
    - Użycie obejmuje przejście z góry do następujących kosztów zarządzania klastrami, takich jak vCenter Server, NSX Manager (duże), NSX Edge, jeśli HCX jest wdrożona, również w Menedżerze HCX i IX wykorzystano ~ 44vCPU (11 procesorów), 75 GB pamięci RAM i 722GB magazynu przed kompresją i deduplikacją. 
- Oszacowanie kosztów miesięcznych: szacowane miesięczne koszty wszystkich węzłów platformy Azure VMware (Automatyczna synchronizacja), na których działają serwery lokalne.

## <a name="view-an-assessment"></a>Widok oceny

Aby wyświetlić ocenę:

1. W **systemach Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i ocenianie** kliknij liczbę obok * * rozwiązanie Azure VMware * *.

1. W pozycji **Oceny** wybierz ocenę, aby ją otworzyć. Przykładowo (oszacowania i koszty tylko na przykład): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Podsumowanie oceny automatycznej synchronizacji":::

1. Przejrzyj podsumowanie oceny. Możesz również edytować właściwości oceny lub ponownie obliczyć ocenę.
 

### <a name="review-readiness"></a>Przegląd gotowości

1. Kliknij pozycję **gotowość platformy Azure**.
2. W obszarze **gotowość do platformy Azure** Przejrzyj stan gotowości.

    - **Gotowe do automatycznej synchronizacji**: serwer można migrować do wersji próbnej systemu Azure, bez wprowadzania żadnych zmian. Serwer zostanie uruchomiony w ramach automatycznej synchronizacji z obsługą pełnej automatycznej synchronizacji.
    - **Gotowe warunki**: serwer może mieć problemy ze zgodnością z bieżącą wersją vSphere. Może być wymagane zainstalowanie narzędzi VMware lub innych ustawień, zanim będzie mieć pełną funkcję w wersji zaautomatycznej.
    - **Nie jest gotowe do automatycznej synchronizacji**: maszyna wirtualna nie rozpocznie się w trakcie automatycznej synchronizacji. Na przykład jeśli lokalny serwer VMware ma dołączone urządzenie zewnętrzne (takie jak dysk CD-ROM) i używasz VMware VMotion, operacja VMotion kończy się niepowodzeniem.
 - **Nieznane gotowość**: Azure Migrate nie może określić gotowości serwera z powodu niewystarczających metadanych zebranych ze środowiska lokalnego.

3. Przejrzyj sugerowane narzędzie.

    - VMware HCX lub Enterprise: w przypadku serwerów VMware, rozwiązanie hybrydowe chmury VMware (HCX) to sugerowane narzędzie do migracji w celu migrowania obciążenia lokalnego do chmury prywatnej Azure VMware (Automatyczna synchronizacja). Dowiedz się więcej.
    - Nieznane: w przypadku serwerów zaimportowanych za pośrednictwem pliku CSV, domyślne narzędzie migracji jest nieznane. W przypadku serwerów VMware zaleca się korzystanie z rozwiązania hybrydowego chmury VMware (HCX).
4. Kliknij stan gotowości do automatycznej synchronizacji. Możesz wyświetlić szczegóły gotowości serwera i przejść do szczegółów, aby zobaczyć szczegóły serwera, w tym ustawienia obliczeń, magazynu i sieci.

### <a name="review-cost-estimates"></a>Przegląd szacowanych kosztów

Podsumowanie oceny przedstawia szacowany koszt obliczeń i magazynu dla uruchomionych serwerów na platformie Azure. 

1. Zapoznaj się z miesięczną sumą kosztów. Koszty są agregowane dla wszystkich serwerów w ocenianej grupie.

    - Oszacowania kosztów są uzależnione od liczby węzłów synchronizacji wymaganych do uwzględnienia wymagań dotyczących zasobów wszystkich serwerów w sumie.
    - W ramach cennika automatycznej synchronizacji na węzeł łączny koszt nie ma kosztu obliczeniowego i dystrybucji kosztów magazynu.
    - Oszacowanie kosztów umożliwia uruchamianie serwerów lokalnych w ramach automatycznej synchronizacji. Ocena automatycznej wersji nie uwzględnia kosztów PaaS ani SaaS.

2. Przejrzyj miesięczne oszacowania magazynu. Widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu. 
3. Możesz przejść do szczegółów, aby wyświetlić szczegóły kosztów dla określonych serwerów.

### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Ocena serwera przypisuje ocenę zaufania do ocen opartych na wydajności. Klasyfikacja jest z jednej gwiazdki (najniższej) do pięciu gwiazdek (najwyższa).

![Ocena zaufania](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru w ocenie. Klasyfikacja jest oparta na dostępności punktów danych potrzebnych do obliczenia oceny.

> [!NOTE]
> Klasyfikacje zaufania nie są przypisywane w przypadku tworzenia oceny opartej na pliku CSV.

Klasyfikacje zaufania są następujące.

**Dostępność punktu danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

[Dowiedz się więcej](concepts-assessment-calculation.md#confidence-ratings-performance-based) na temat klasyfikacji zaufania.

## <a name="next-steps"></a>Następne kroki

- Znajdź zależności serwera przy użyciu [mapowania zależności](concepts-dependency-visualization.md).
- Skonfiguruj odwzorowanie zależności [od](how-to-create-group-machine-dependencies.md) [agenta](how-to-create-group-machine-dependencies-agentless.md) lub agenta.
