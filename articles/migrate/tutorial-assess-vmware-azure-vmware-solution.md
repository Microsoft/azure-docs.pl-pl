---
title: Ocenianie maszyn wirtualnych VMware na potrzeby migracji do rozwiązania Azure VMware (Automatyczna synchronizacja) za pomocą Azure Migrate
description: Dowiedz się, jak ocenić maszyny wirtualne VMware pod kątem migracji na potrzeby automatycznej synchronizacji z Azure Migrate oceny serwera.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 29f7f824d96aedd80e490ba84c390be4d9493683
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604244"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>Samouczek: Ocena maszyn wirtualnych VMware pod kątem migracji do wersji zaautomatycznej

W ramach kursu migracji do platformy Azure oceniasz swoje obciążenia lokalne, aby mierzyć gotowość do chmury, identyfikować zagrożenia i oceniać koszty i złożoność.

W tym artykule opisano sposób oceny odnalezionych maszyn wirtualnych VMware na potrzeby migracji do rozwiązania Azure VMware (Automatyczna synchronizacja) za pomocą narzędzia do oceny serwera, Azure Migrate:. Automatyczna synchronizacja to usługa zarządzana, która umożliwia uruchamianie platformy VMware na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
- Uruchom ocenę na podstawie metadanych maszyn i informacji o konfiguracji.
- Uruchom ocenę na podstawie danych wydajności.

> [!NOTE]
> Samouczki przedstawiają najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych, tam gdzie to możliwe. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).



## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem tego samouczka w celu oceny maszyn w celu przeprowadzenia migracji do automatycznej synchronizacji upewnij się, że wykryto maszyny, które chcesz ocenić:

- Aby odnaleźć maszyny korzystające z urządzenia Azure Migrate, [postępuj zgodnie z tym samouczkiem](tutorial-discover-vmware.md). 
- Aby odnajdywać maszyny przy użyciu zaimportowanego pliku CSV, [postępuj zgodnie z tym samouczkiem](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Wybór oceny do uruchomienia


Zdecyduj, czy chcesz uruchomić ocenę przy użyciu kryteriów ustalania rozmiarów na podstawie danych konfiguracji komputera/metadanych zebranych jako lokalne lub na danych dotyczących wydajności dynamicznej.

**Ocena** | **Szczegóły** | **Zalecenie**
--- | --- | ---
**Zgodnie ze środowiskiem lokalnym** | Oceń dane na podstawie konfiguracji komputera/metadanych.  | Zalecany rozmiar węzła w ramach automatycznej synchronizacji zależy od rozmiaru lokalnego maszyny wirtualnej, a także ustawień określonych w ocenie dla typu węzła, typu magazynu i ustawienia niedopuszczalnego do tolerowania.
**Na podstawie wydajności** | Oceniaj na podstawie zebranych danych o wydajności dynamicznej. | Zalecany rozmiar węzła w ramach automatycznej synchronizacji jest oparty na danych użycia procesora i pamięci, a także ustawień określonych w ocenie dla typu węzła, typu magazynu i ustawienia odporności na uszkodzenia.

## <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Na stronie **serwery** > **serwery z systemami Windows i Linux**kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

   ![Przycisk lokalizacji oceny i migracji serwerów](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

2. W **Azure Migrate: Ocena serwera**, kliknij przycisk **Oceń**.

3. W obszarze **Szacuj**  >  **Typ oceny**serwerów wybierz pozycję **Azure VMware Solution (Automatyczna synchronizacja) (wersja zapoznawcza**).
4. W **źródle odnajdywania**:

    - W przypadku wykrycia maszyn przy użyciu urządzenia wybierz pozycję **maszyny odnalezione z urządzenia Azure Migrate**.
    - W przypadku wykrycia maszyn przy użyciu zaimportowanego pliku CSV wybierz pozycję **zaimportowane maszyny**. 
    
5. Określ nazwę oceny. 
6. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Strona do wybierania ustawień oceny](./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png)


7. właściwości **oceny 1N ocen**  >  **Target Properties**:

    - W polu **Lokalizacja docelowa**Określ region platformy Azure, do którego chcesz przeprowadzić migrację.
       - Zalecenia dotyczące rozmiaru i kosztu są zależne od określonej lokalizacji.
       - Obecnie można ocenić trzy regiony (Wschodnie stany USA, zachodnie stany USA, Europa Zachodnia)
   - W polu **Typ magazynu**pozostaw **sieci vSAN**. Jest to domyślny typ magazynu dla chmury prywatnej automatycznej synchronizacji.
   - **Wystąpienia zarezerwowane** nie są obecnie obsługiwane w przypadku węzłów automatycznej synchronizacji.
8. **Rozmiar maszyny wirtualnej**:
    - W obszarze **Typ węzła**wybierz typ węzła na podstawie obciążeń uruchomionych na lokalnych maszynach wirtualnych.
        - Azure Migrate zaleca węzeł węzłów wymaganych do migrowania maszyn wirtualnych do wersji zaautomatycznej.
        - Domyślny typ węzła to AV36.
    - **Ustawienie FTT, poziom RAID**, wybierz niepowodzenie i kombinację RAID.  Wybrana opcja FTT, połączona z wymaganiami dotyczącymi lokalnego dysku maszyny wirtualnej, określa łączny magazyn sieci vSAN wymagany w ramach automatycznej synchronizacji.
    - W obszarze **nadsubskrypcja procesora CPU**Określ stosunek rdzeni wirtualnych skojarzonych z jednym rdzeniem fizycznym w WĘŹLE automatyczna synchronizacja. Nadsubskrypcja większa niż 4:1 może powodować spadek wydajności, ale może być używana do obciążeń typu serwer sieci Web.

9. W obszarze **rozmiar węzła**: 
    - W **kryterium ustalania wielkości**wybierz, czy chcesz oprzeć ocenę metadanych statycznych, czy też na danych na podstawie wydajności. W przypadku korzystania z danych wydajności:
        - W obszarze **historia wydajności**wskaż czas trwania danych, dla którego chcesz oprzeć ocenę
        - W polu **użycie percentyla**Określ wartość percentylu, która ma być używana dla przykładu wydajności. 
    - W polu **czynnik komfortu**wskaż bufor, który ma być używany podczas oceny. Te konta dotyczące problemów, takich jak sezonowe użycie, krótka historia wydajności i prawdopodobnie wzrastają w przyszłości. Na przykład, jeśli jest używany współczynnik komfortu równy dwa:
    
        **Składnik** | **Efektywne wykorzystanie** | **Dodaj współczynnik komfortu (2,0)**
        --- | --- | ---  
        Rdzenie | 2 | 4
        Pamięć | 8 GB | 16 GB     

10. W **cenniku**:
    - Oferta [platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , która jest zarejestrowana w **programie, jest**wyświetlana w obszarze Ocena serwera szacuje koszt tej oferty.
    - W polu **Waluta**Wybierz walutę rozliczeń dla Twojego konta.
    - W polu **Rabat (%)** Dodaj wszelkie zniżki specyficzne dla subskrypcji otrzymane w oparciu o ofertę platformy Azure. Ustawienie domyślne to 0%.

11. Jeśli wprowadzisz zmiany, kliknij przycisk **Zapisz** .

    ![Właściwości oceny](./media/tutorial-assess-vmware-azure-vmware-solution/view-all.png)

12. W obszarze **ocenianie serwerów**kliknij przycisk **dalej**.
13. W obszarze **ocenianie serwerów**  >  **Wybierz Maszyny do oceny**, aby utworzyć nową grupę serwerów do oceny, wybierz pozycję **Utwórz nową**, a następnie określ nazwę grupy. 
14. Wybierz urządzenie i wybierz maszyny wirtualne, które chcesz dodać do grupy. Następnie kliknij przycisk **Dalej**.
15. W obszarze **Przegląd + tworzenie oceny**Przejrzyj szczegóły oceny, a następnie kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę i uruchomić ocenę.

    > [!NOTE]
    > W przypadku ocen opartych na wydajności zalecamy odczekanie co najmniej dnia od momentu rozpoczęcia odnajdywania przed utworzeniem oceny. Zapewnia to czas na zbieranie danych o wydajności z wyższym zaufaniem. Najlepiej po rozpoczęciu odnajdywania poczekaj na określenie czasu trwania wydajności (dzień/tydzień/miesiąc) w celu uzyskania oceny o wysokim poziomie zaufania.

## <a name="review-an-assessment"></a>Przegląd oceny

W ramach oceny automatycznej wersji zamieszczono następujące informacje:

- Gotowość do automatycznej synchronizacji: czy lokalne maszyny wirtualne są odpowiednie do migracji do rozwiązania Azure VMware (Automatyczna synchronizacja).
- Liczba węzłów automatycznej synchronizacji: Szacowana liczba węzłów synchronizacji wymaganych do uruchomienia maszyn wirtualnych.
- Wykorzystanie w węzłach automatycznej synchronizacji: przewidywany procesor CPU, pamięć i wykorzystanie magazynu we wszystkich węzłach.
- Oszacowanie kosztów miesięcznych: szacowane miesięczne koszty wszystkich węzłów platformy Azure VMware (Automatyczna synchronizacja) z uruchomionymi lokalnymi maszynami wirtualnymi.

## <a name="view-an-assessment"></a>Widok oceny

Aby wyświetlić ocenę:

1. W obszarze **serwery**  >  **Azure Migrate: Ocena serwera**, kliknij liczbę obok pozycji **oceny**.
2. W pozycji **Oceny** wybierz ocenę, aby ją otworzyć. 
3. Przejrzyj podsumowanie oceny. Możesz również edytować właściwości oceny lub ponownie obliczyć ocenę.
 

### <a name="review-readiness"></a>Przegląd gotowości

1. Kliknij pozycję **gotowość platformy Azure**.
2. W obszarze **gotowość do platformy Azure**Przejrzyj stan maszyny wirtualnej.

    - **Gotowe do automatycznej synchronizacji**: można migrować maszynę do wersji zastosowanej do automatycznej synchronizacji z platformą Azure bez wprowadzania żadnych zmian. Na maszynie rozpocznie się automatyczna synchronizacja z pełną obsługą wersji zaautomatycznej.
    - **Gotowe warunki**: komputer może mieć problemy ze zgodnością z bieżącą wersją vSphere. Może być wymagane zainstalowanie narzędzi VMware lub innych ustawień, zanim będzie mieć pełną funkcję w wersji zaautomatycznej.
    - **Nie jest gotowe do automatycznej synchronizacji**: maszyna wirtualna nie rozpocznie się w trakcie automatycznej synchronizacji. Jeśli na przykład lokalna maszyna wirtualna VMware ma dołączone urządzenie zewnętrzne (takie jak dysk CD-ROM) i używasz VMware VMotion, operacja VMotion kończy się niepowodzeniem.
 - **Nieznane gotowość**: Azure Migrate nie może określić gotowości maszyny z powodu niewystarczających metadanych zebranych ze środowiska lokalnego.

3. Przejrzyj sugerowane narzędzie.

    - VMware HCX lub Enterprise: w przypadku maszyn VMware, rozwiązanie hybrydowe w chmurze VMWare (HCX) to sugerowane narzędzie do migracji, które umożliwia migrowanie lokalnego obciążenia do chmury prywatnej platformy Azure VMware (Automatyczna synchronizacja). Dowiedz się więcej.
    - Nieznane: w przypadku maszyn zaimportowanych za pośrednictwem pliku CSV domyślne narzędzie do migracji jest nieznane. Mimo że w przypadku maszyn VMware zaleca się użycie rozwiązania hybrydowego chmury VMware (HCX).
4. Kliknij stan gotowości do automatycznej synchronizacji. Możesz wyświetlić szczegóły gotowości maszyn wirtualnych i przejść do szczegółów, aby wyświetlić szczegóły dotyczące maszyn wirtualnych, w tym ustawienia obliczeń, magazynu i sieci.

### <a name="review-cost-estimates"></a>Przegląd szacowanych kosztów

Podsumowanie oceny przedstawia szacowany koszt obliczeń i magazynu dla uruchomionych maszyn wirtualnych na platformie Azure. 

1. Zapoznaj się z miesięczną sumą kosztów. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie.

    - Oszacowania kosztów są uzależnione od liczby węzłów synchronizacji wymaganych do uwzględnienia wymagań dotyczących zasobów wszystkich maszyn wirtualnych.
    - W ramach cennika automatycznej synchronizacji na węzeł łączny koszt nie ma kosztu obliczeniowego i dystrybucji kosztów magazynu.
    - Oszacowanie kosztów dotyczy uruchamiania lokalnych maszyn wirtualnych w wersji zaautomatycznej. Ocena serwera Azure Migrate nie uwzględnia kosztów PaaS ani SaaS.

2. Przejrzyj miesięczne oszacowania magazynu. Widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu. 
3. Możesz przejść do szczegółów, aby wyświetlić szczegóły kosztów dla określonych maszyn wirtualnych.

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

- Znajdowanie zależności maszyn przy użyciu [mapowania zależności](concepts-dependency-visualization.md).
- Skonfiguruj odwzorowanie zależności [od](how-to-create-group-machine-dependencies.md) [agenta](how-to-create-group-machine-dependencies-agentless.md) lub agenta.
