---
title: Oceń maszyny wirtualne funkcji Hyper-V do migracji na maszyny wirtualne platformy Azure z oceną serwera w Azure Migrate
description: Dowiedz się, jak ocenić maszyny wirtualne funkcji Hyper-V do migracji na maszyny wirtualne platformy Azure z oceną serwera.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: be5099aa515a2331cb05fa8bf6ea76c7544ec1df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089238"
---
# <a name="tutorial-assess-hyper-v-vms-for-migration-to-azure"></a>Samouczek: ocenianie maszyn wirtualnych funkcji Hyper-V do migracji na platformę Azure

W ramach kursu migracji do platformy Azure oceniasz swoje obciążenia lokalne, aby mierzyć gotowość do chmury, identyfikować zagrożenia i oceniać koszty i złożoność.

W tym artykule opisano sposób oceny odnalezionych maszyn wirtualnych funkcji Hyper-V na potrzeby migracji na platformę Azure przy użyciu narzędzia do oceny serwera Azure Migrate:.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
- Uruchom ocenę.
- Analizuj ocenę.

> [!NOTE]
> Samouczki przedstawiają najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych, tam gdzie to możliwe. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

- Zanim skorzystasz z tego samouczka, aby ocenić maszyny do migracji do maszyn wirtualnych platformy Azure, upewnij się, że wykryto maszyny, które chcesz ocenić:
    - Aby odnaleźć maszyny korzystające z urządzenia Azure Migrate, [postępuj zgodnie z tym samouczkiem](tutorial-discover-hyper-v.md). 
    - Aby odnajdywać maszyny przy użyciu zaimportowanego pliku CSV, [postępuj zgodnie z tym samouczkiem](tutorial-discover-import.md).



## <a name="decide-which-assessment-to-run"></a>Wybór oceny do uruchomienia


Zdecyduj, czy chcesz uruchomić ocenę przy użyciu kryteriów ustalania rozmiarów na podstawie danych konfiguracji komputera/metadanych zebranych jako lokalne lub na danych dotyczących wydajności dynamicznej.

**Ocena** | **Szczegóły** | **Zalecenie**
--- | --- | ---
**Zgodnie ze środowiskiem lokalnym** | Oceń dane na podstawie konfiguracji komputera/metadanych.  | Zalecany rozmiar maszyny wirtualnej platformy Azure jest oparty na rozmiarze lokalnego maszyny wirtualnej.<br/><br> Zalecany typ dysku platformy Azure jest oparty na tym, co zostało wybrane w ustawieniu typ magazynu w ocenie.
**Na podstawie wydajności** | Oceniaj na podstawie zebranych danych o wydajności dynamicznej. | Zalecany rozmiar maszyny wirtualnej platformy Azure jest oparty na danych użycia procesora i pamięci.<br/><br/> Zalecany typ dysku jest oparty na liczbie IOPS i przepływności dysków lokalnych.


## <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Na stronie **serwery** > **serwery z systemami Windows i Linux**kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

   ![Przycisk lokalizacji oceny i migracji serwerów](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. W programie * * Azure Migrate: Ocena serwera, kliknij przycisk **Oceń**.

    ![Lokalizacja przycisku oceny](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. W obszarze **ocenianie serwerów**  >  **Typ oceny**wybierz **maszynę wirtualną platformy Azure**.
4. W **źródle odnajdywania**:

    - W przypadku wykrycia maszyn przy użyciu urządzenia wybierz pozycję **maszyny odnalezione z urządzenia Azure Migrate**.
    - W przypadku wykrycia maszyn przy użyciu zaimportowanego pliku CSV wybierz pozycję **zaimportowane maszyny**. 
    
5. Określ nazwę oceny. 
6. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Lokalizacja przycisku Wyświetl wszystko, aby przejrzeć właściwości oceny](./media/tutorial-assess-vmware-azure-vm/assessment-name.png)

7. We **Assessment properties**właściwościach  >  **elementu docelowego**właściwości oceny:
    - W polu **Lokalizacja docelowa**Określ region platformy Azure, do którego chcesz przeprowadzić migrację.
        - Zalecenia dotyczące rozmiaru i kosztu są zależne od określonej lokalizacji.
        - W Azure Government można docelowo oceny w [tych regionach](migrate-support-matrix.md#supported-geographies-azure-government)
    - W polu **Typ magazynu**
        - Jeśli chcesz użyć danych opartych na wydajności w ocenie, wybierz opcję **Automatyczne** dla Azure Migrate, aby zalecić typ magazynu na podstawie liczby operacji we/wy na sekundę na dysku.
        - Alternatywnie można wybrać typ magazynu, który ma być używany przez maszynę wirtualną podczas jego migrowania.
    - W przypadku **wystąpień zarezerwowanych**Określ, czy podczas migrowania ma być używane wystąpienie rezerwowe dla maszyny wirtualnej.
        - Jeśli wybierzesz użycie zarezerwowanego wystąpienia, nie możesz określić "**rabatu (%)** ani czasu działania **maszyny wirtualnej**. 
        - [Dowiedz się więcej](https://aka.ms/azurereservedinstances).
8. **Rozmiar maszyny wirtualnej**:
 
    - W polu **kryterium ustalania wielkości**wybierz, czy chcesz oprzeć ocenę danych/metadanych konfiguracji komputera, czy też na danych opartych na wydajności. W przypadku korzystania z danych wydajności:
        - W obszarze **historia wydajności**wskaż czas trwania danych, dla którego chcesz oprzeć ocenę
        - W polu **użycie percentyla**Określ wartość percentylu, która ma być używana dla przykładu wydajności. 
    - W obszarze **Seria maszyn wirtualnych**Określ serię maszyn wirtualnych platformy Azure, którą chcesz uwzględnić.
        - Jeśli używasz oceny wydajności, Azure Migrate sugeruje wartość.
        - Dostrajanie ustawień zgodnie z wymaganiami. Na przykład jeśli nie masz środowiska produkcyjnego wymagającego maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię z listy serii.
    - W polu **czynnik komfortu**wskaż bufor, który ma być używany podczas oceny. Te konta dotyczące problemów, takich jak sezonowe użycie, krótka historia wydajności i prawdopodobnie wzrastają w przyszłości. Na przykład, jeśli używasz współczynnika komfortu równym 2 **Component**:  |  **wykorzystanie efektywnego**składnika  |  **Dodawanie współczynnika komfortu (2,0)** rdzeni | 2 | 4 pamięć | 8 GB | 16 GB     
   
9. W **cenniku**:
    - W obszarze **Oferta**Określ [ofertę platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , Jeśli zarejestrowano. Ocena serwera szacuje koszt dla tej oferty.
    - W polu **Waluta**Wybierz walutę rozliczeń dla Twojego konta.
    - W polu **Rabat (%)** Dodaj wszelkie zniżki specyficzne dla subskrypcji otrzymane w oparciu o ofertę platformy Azure. Ustawienie domyślne to 0%.
    - W obszarze **czas działania maszyny wirtualnej**Określ czas trwania (dni na miesiąc/godzinę dziennie), które będą uruchamiane na maszynach wirtualnych.
        - Jest to przydatne w przypadku maszyn wirtualnych platformy Azure, które nie będą działać w sposób ciągły.
        - Oszacowania kosztów są uzależnione od określonego czasu trwania.
        - Wartość domyślna to 31 dni miesięcznie/24 godziny dziennie.

    - W obszarze **subskrypcja umowy EA**Określ, czy ma zostać uwzględniony rabat za subskrypcję Umowa Enterprise (EA) w celu oszacowania kosztów. 
    - W polu **korzyść użycia hybrydowego platformy Azure**Określ, czy masz już licencję systemu Windows Server. Jeśli to zrobisz, a zostaną objęte aktywnym programem Software Assurance subskrypcji systemu Windows Server, możesz zastosować [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/) po przeniesieniu licencji na platformę Azure.

10. Jeśli wprowadzisz zmiany, kliknij przycisk **Zapisz** .

    ![Właściwości oceny](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

11. W obszarze **ocenianie serwerów**kliknij przycisk **dalej**.
12. W obszarze **Wybieranie maszyn do oceny**wybierz pozycję **Utwórz nową**, a następnie określ nazwę grupy. 
13. Wybierz urządzenie i wybierz maszyny wirtualne, które chcesz dodać do grupy. Następnie kliknij przycisk **Dalej**.
14. W programie * * Przejrzyj i Utwórz ocenę, przejrzyj szczegóły oceny, a następnie kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę i uruchomić ocenę.


    > [!NOTE]
    > W przypadku ocen opartych na wydajności zalecamy odczekanie co najmniej dnia od momentu rozpoczęcia odnajdywania przed utworzeniem oceny. Zapewnia to czas na zbieranie danych o wydajności z wyższym zaufaniem. Najlepiej po rozpoczęciu odnajdywania poczekaj na określenie czasu trwania wydajności (dzień/tydzień/miesiąc) w celu uzyskania oceny o wysokim poziomie zaufania.

## <a name="review-an-assessment"></a>Przegląd oceny

Ocena zawiera opis:

- **Gotowość platformy Azure**: czy maszyny wirtualne są odpowiednie do migracji na platformę Azure.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty obliczeniowe i magazynowe związane z uruchamianiem maszyn wirtualnych na platformie Azure.
- **Miesięczne szacowanie kosztów magazynu**: Szacowane koszty magazynu dyskowego po migracji.

Aby wyświetlić ocenę:

1. W obszarze **serwery**  >  **Azure Migrate: Ocena serwera**, kliknij liczbę obok pozycji **oceny**.
2. W pozycji **Oceny** wybierz ocenę, aby ją otworzyć. Przykładowo (oszacowania i koszty tylko na przykład): 

    ![Podsumowanie oceny](./media/tutorial-assess-vmware-azure-vm/assessment-summary.png)

3. Przejrzyj podsumowanie oceny. Możesz również edytować właściwości oceny lub ponownie obliczyć ocenę.
 
 
### <a name="review-readiness"></a>Przegląd gotowości

1. Kliknij pozycję **gotowość platformy Azure**.
2. W obszarze **gotowość do platformy Azure**Przejrzyj stan maszyny wirtualnej:
    - **Gotowe do użycia na platformie Azure**: używane, gdy Azure Migrate zaleca rozmiar maszyny wirtualnej i oszacowania kosztów w przypadku maszyn wirtualnych w ocenie.
    - **Gotowe warunki**: pokazuje problemy i sugerowane korygowanie.
    - **Nie gotowy na platformę Azure**: zawiera problemy i sugerowane korygowanie.
    - **Nieznane gotowość**: używany, gdy Azure Migrate nie może ocenić gotowości z powodu problemów z dostępnością danych.

3. Wybierz stan **Gotowość na platformę Azure**. Możesz wyświetlić szczegóły gotowości maszyn wirtualnych. Możesz również przejść do szczegółów, aby wyświetlić szczegóły dotyczące maszyn wirtualnych, w tym ustawienia obliczeń, magazynu i sieci.

### <a name="review-cost-estimates"></a>Przegląd szacowanych kosztów

Podsumowanie oceny przedstawia szacowany koszt obliczeń i magazynu dla uruchomionych maszyn wirtualnych na platformie Azure. 

1. Zapoznaj się z miesięczną sumą kosztów. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie.

    - Oszacowania kosztów są oparte na zaleceniach dotyczących rozmiaru maszyny, jej dysków i właściwości.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Oszacowanie kosztów służy do uruchamiania lokalnych maszyn wirtualnych na maszynach wirtualnych platformy Azure. Oszacowanie nie uwzględnia kosztów PaaS ani SaaS.

2. Zapoznaj się z miesięcznymi kosztami magazynowania. Widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu. 
3. Możesz przejść do szczegółów, aby wyświetlić szczegóły kosztów dla określonych maszyn wirtualnych.

### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Ocena serwera przypisuje ocenę zaufania do ocen opartych na wydajności. Klasyfikacja jest z jednej gwiazdki (najniższej) do pięciu gwiazdek (najwyższa).

![Ocena zaufania](./media/tutorial-assess-vmware-azure-vm/confidence-rating.png)

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
- Skonfiguruj mapowanie zależności [oparte na agentach](how-to-create-group-machine-dependencies.md) .
