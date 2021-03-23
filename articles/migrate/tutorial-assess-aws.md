---
title: Oceniaj wystąpienia AWS na potrzeby migracji na platformę Azure za pomocą Azure Migrate
description: Dowiedz się, jak uzyskać dostęp do wystąpień AWS na potrzeby migracji na platformę Azure za pomocą Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 942adacf5b9a3b1b717e28def5752591ef22ec52
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782337"
---
# <a name="tutorial-assess-aws-instances-for-migration-to-azure"></a>Samouczek: Ocena wystąpień AWS na potrzeby migracji na platformę Azure

W ramach kursu migracji do platformy Azure oceniasz swoje obciążenia lokalne, aby mierzyć gotowość do chmury, identyfikować zagrożenia i oceniać koszty i złożoność.

W tym artykule opisano sposób oceny wystąpień Amazon Web Services (AWS) na potrzeby migracji na platformę Azure za pomocą narzędzia Azure Migrate do odnajdywania i oceny.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
- Uruchom ocenę na podstawie metadanych serwera i informacji konfiguracyjnych.
- Uruchom ocenę na podstawie danych wydajności.

> [!NOTE]
> Samouczki przedstawiają najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych, tam gdzie to możliwe. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

- Przed wykonaniem kroków opisanych w tym samouczku Ukończ pierwszy samouczek z tej serii, aby [odnaleźć spis lokalny](tutorial-discover-aws.md). 
- Upewnij się, że wystąpienia AWS nie działają w systemie Windows Server 2003 lub SUSE Linux. Ocena nie jest obsługiwana dla tych serwerów.


## <a name="decide-which-assessment-to-run"></a>Wybór oceny do uruchomienia


Zdecyduj, czy chcesz uruchomić ocenę przy użyciu kryteriów ustalania rozmiarów na podstawie danych konfiguracji serwera/metadanych zebranych jako lokalne lub na danych dotyczących wydajności dynamicznej.

**Ocena** | **Szczegóły** | **Zalecenie**
--- | --- | ---
**Zgodnie ze środowiskiem lokalnym** | Oceń dane na podstawie konfiguracji serwera/metadanych.  | Zalecany rozmiar maszyny wirtualnej platformy Azure jest oparty na rozmiarze lokalnego maszyny wirtualnej.<br/><br> Zalecany typ dysku platformy Azure jest oparty na tym, co zostało wybrane w ustawieniu typ magazynu w ocenie.
**Na podstawie wydajności** | Oceniaj na podstawie zebranych danych o wydajności dynamicznej. | Zalecany rozmiar maszyny wirtualnej platformy Azure jest oparty na danych użycia procesora i pamięci.<br/><br/> Zalecany typ dysku jest oparty na liczbie IOPS i przepływności dysków lokalnych.

## <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Na stronie **przegląd** > **Windows, Linux i SQL Server** kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

   ![Przycisk lokalizacji oceny i migracji serwerów](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. W **Azure Migrate: odnajdywanie i ocenianie** kliknij pozycję **Oceń**.

    ![Lokalizacja przycisku oceny](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. W obszarze **ocenianie serwerów**  >  **Typ oceny** wybierz **maszynę wirtualną platformy Azure**.
4. W **źródle odnajdywania**:

    - Jeśli wykryto serwery korzystające z urządzenia, wybierz pozycję **serwery odnalezione z urządzenia Azure Migrate**.
    - W przypadku wykrycia serwerów przy użyciu zaimportowanego pliku CSV wybierz pozycję **zaimportowane serwery**. 
    
1. Kliknij przycisk **Edytuj** , aby przejrzeć właściwości oceny.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Lokalizacja przycisku edycji służącego do przeglądania właściwości oceny":::

1. We właściwościach  >  **elementu docelowego** właściwości oceny:
    - W polu **Lokalizacja docelowa** Określ region platformy Azure, do którego chcesz przeprowadzić migrację.
        - Zalecenia dotyczące rozmiaru i kosztu są zależne od określonej lokalizacji. Po zmianie lokalizacji docelowej z domyślną zostanie wyświetlony monit o określenie **wystąpień zarezerwowanych** i **serii maszyn wirtualnych**.
        - W Azure Government można docelowo oceny w [tych regionach](migrate-support-matrix.md#supported-geographies-azure-government)
    - W polu **Typ magazynu**
        - Jeśli chcesz użyć danych opartych na wydajności w ocenie, wybierz opcję **Automatyczne** dla Azure Migrate, aby zalecić typ magazynu na podstawie liczby operacji we/wy na sekundę na dysku.
        - Alternatywnie można wybrać typ magazynu, który ma być używany przez maszynę wirtualną podczas jego migrowania.
    - W przypadku **wystąpień zarezerwowanych** Określ, czy podczas migrowania ma być używane wystąpienie rezerwowe dla maszyny wirtualnej.
        - Jeśli wybierzesz użycie zarezerwowanego wystąpienia, nie możesz określić "**rabatu (%)** ani czasu działania **maszyny wirtualnej**. 
        - [Dowiedz się więcej](https://aka.ms/azurereservedinstances).
 1. **Rozmiar maszyny wirtualnej**:
     - W polu **kryterium ustalania wielkości** wybierz, czy chcesz oprzeć ocenę danych konfiguracji serwera/metadanych lub na podstawie wydajności. W przypadku korzystania z danych wydajności:
        - W obszarze **historia wydajności** wskaż czas trwania danych, dla którego chcesz oprzeć ocenę
        - W polu **użycie percentyla** Określ wartość percentylu, która ma być używana dla przykładu wydajności. 
    - W obszarze **Seria maszyn wirtualnych** Określ serię maszyn wirtualnych platformy Azure, którą chcesz uwzględnić.
        - Jeśli używasz oceny wydajności, Azure Migrate sugeruje wartość.
        - Dostrajanie ustawień zgodnie z wymaganiami. Na przykład jeśli nie masz środowiska produkcyjnego wymagającego maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię z listy serii.
    - W polu **czynnik komfortu** wskaż bufor, który ma być używany podczas oceny. Te konta dotyczące problemów, takich jak sezonowe użycie, krótka historia wydajności i prawdopodobnie wzrastają w przyszłości. Na przykład, jeśli jest używany współczynnik komfortu równy dwa:
    
        **Składnik** | **Efektywne wykorzystanie** | **Dodaj współczynnik komfortu (2,0)**
        --- | --- | ---
        Rdzenie | 2  | 4
        Pamięć | 8 GB | 16 GB
   
1. W **cenniku**:
    - W obszarze **Oferta** Określ [ofertę platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , Jeśli zarejestrowano. Ocena szacuje koszt tej oferty.
    - W polu **Waluta** Wybierz walutę rozliczeń dla Twojego konta.
    - W polu **Rabat (%)** Dodaj wszelkie zniżki specyficzne dla subskrypcji otrzymane w oparciu o ofertę platformy Azure. Ustawienie domyślne to 0%.
    - W obszarze **czas działania maszyny wirtualnej** Określ czas trwania (dni na miesiąc/godzinę dziennie), które będą uruchamiane na maszynach wirtualnych.
        - Jest to przydatne w przypadku maszyn wirtualnych platformy Azure, które nie będą działać w sposób ciągły.
        - Oszacowania kosztów są uzależnione od określonego czasu trwania.
        - Wartość domyślna to 31 dni miesięcznie/24 godziny dziennie.
    - W obszarze **subskrypcja umowy EA** Określ, czy ma zostać uwzględniony rabat za subskrypcję Enterprise Agreement (EA) w celu oszacowania kosztów. 
    - W polu **korzyść użycia hybrydowego platformy Azure** Określ, czy masz już licencję systemu Windows Server. Jeśli to zrobisz, a zostaną objęte aktywnym programem Software Assurance subskrypcji systemu Windows Server, możesz zastosować [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/) po przeniesieniu licencji na platformę Azure.

1. Jeśli wprowadzisz zmiany, kliknij przycisk **Zapisz** .

    ![Właściwości oceny](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. W obszarze **ocenianie serwerów** > kliknij przycisk **dalej**.

1. Na stronie **Wybierz serwery do oceny**  >  **nazwy oceny** > Określ nazwę oceny. 

1. W obszarze **Wybierz lub Utwórz grupę** > wybierz pozycję **Utwórz nową** i określ nazwę grupy. 
    
1. Wybierz urządzenie i wybierz maszyny wirtualne, które chcesz dodać do grupy. Następnie kliknij przycisk **Dalej**.

1. W obszarze **Przegląd + tworzenie oceny** Przejrzyj szczegóły oceny, a następnie kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę i uruchomić ocenę.

1. Po utworzeniu oceny należy wyświetlić ją w obszarze **serwery**  >  **Azure Migrate: Ocena odnajdywania i oceny**  >  .

1. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.
    > [!NOTE]
    > W przypadku ocen opartych na wydajności zalecamy odczekanie co najmniej dnia od momentu rozpoczęcia odnajdywania przed utworzeniem oceny. Zapewnia to czas na zbieranie danych o wydajności z wyższym zaufaniem. Najlepiej po rozpoczęciu odnajdywania poczekaj na określenie czasu trwania wydajności (dzień/tydzień/miesiąc) w celu uzyskania oceny o wysokim poziomie zaufania.

## <a name="review-an-assessment"></a>Przegląd oceny

Ocena zawiera opis:

- **Gotowość platformy Azure**: czy maszyny wirtualne są odpowiednie do migracji na platformę Azure.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty obliczeniowe i magazynowe związane z uruchamianiem maszyn wirtualnych na platformie Azure.
- **Miesięczne szacowanie kosztów magazynu**: Szacowane koszty magazynu dyskowego po migracji.

Aby wyświetlić ocenę:

1. W **systemach Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i Ocena** kliknij liczbę obok pozycji **oceny**.
2. W pozycji **Oceny** wybierz ocenę, aby ją otworzyć. Przykładowo (oszacowania i koszty tylko na przykład): 

    ![Podsumowanie oceny](./media/tutorial-assess-aws/assessment-summary.png)

3. Przejrzyj podsumowanie oceny. Możesz również edytować właściwości oceny lub ponownie obliczyć ocenę.
 
 
### <a name="review-readiness"></a>Przegląd gotowości

1. Kliknij pozycję **gotowość platformy Azure**.
2. W obszarze **gotowość do platformy Azure** Przejrzyj stan maszyny wirtualnej:
    - **Gotowe do użycia na platformie Azure**: używane, gdy Azure Migrate zaleca rozmiar maszyny wirtualnej i oszacowania kosztów w przypadku maszyn wirtualnych w ocenie.
    - **Gotowe warunki**: pokazuje problemy i sugerowane korygowanie.
    - **Nie gotowy na platformę Azure**: zawiera problemy i sugerowane korygowanie.
    - **Nieznane gotowość**: używany, gdy Azure Migrate nie może ocenić gotowości z powodu problemów z dostępnością danych.

3. Wybierz stan **Gotowość na platformę Azure**. Możesz wyświetlić szczegóły gotowości maszyn wirtualnych. Możesz również przejść do szczegółów, aby wyświetlić szczegóły dotyczące maszyn wirtualnych, w tym ustawienia obliczeń, magazynu i sieci.

### <a name="review-cost-estimates"></a>Przegląd szacowanych kosztów

Podsumowanie oceny przedstawia szacowany koszt obliczeń i magazynu dla uruchomionych maszyn wirtualnych na platformie Azure. 

1. Zapoznaj się z miesięczną sumą kosztów. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie.

    - Oszacowania kosztów są oparte na zaleceniach dotyczących rozmiaru serwera, jego dysków i jego właściwości.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Oszacowanie kosztów służy do uruchamiania lokalnych maszyn wirtualnych na maszynach wirtualnych platformy Azure. Oszacowanie nie uwzględnia kosztów PaaS ani SaaS.

2. Zapoznaj się z miesięcznymi kosztami magazynowania. Widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu. 
3. Możesz przejść do szczegółów, aby wyświetlić szczegóły kosztów dla określonych maszyn wirtualnych.

### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Azure Migrate przypisuje ocenę zaufania do ocen opartych na wydajności. Klasyfikacja jest z jednej gwiazdki (najniższej) do pięciu gwiazdek (najwyższa).

![Ocena zaufania](./media/tutorial-assess-aws/confidence-rating.png)

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
- Skonfiguruj mapowanie zależności [oparte na agentach](how-to-create-group-machine-dependencies.md) .
