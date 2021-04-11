---
title: Tworzenie oceny maszyn wirtualnych platformy Azure za pomocą narzędzia do odnajdywania i oceny Azure Migrate | Microsoft Docs
description: Opisuje sposób tworzenia oceny maszyn wirtualnych platformy Azure za pomocą narzędzia do odnajdywania i oceny Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: f901fe23d2ff04e7ad9ba920dd90ebab8a39246c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786723"
---
# <a name="create-an-azure-vm-assessment"></a>Tworzenie oceny maszyny wirtualnej platformy Azure

W tym artykule opisano sposób tworzenia oceny maszyn wirtualnych platformy Azure dla serwerów lokalnych w środowisku VMware, funkcji Hyper-V lub fizycznych/innych środowiskach chmurowych z Azure Migrate: odnajdywania i oceny.

[Azure Migrate](migrate-services-overview.md) ułatwia Migrowanie do platformy Azure. Azure Migrate udostępnia scentralizowany centrum do śledzenia odnajdywania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure. Centrum udostępnia narzędzia platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. 

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że [utworzono](./create-manage-projects.md) projekt Azure Migrate.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) Azure Migrate: narzędzie odnajdywania i oceny.
- Aby utworzyć ocenę, należy skonfigurować urządzenie Azure Migrate dla oprogramowania [VMware](how-to-set-up-appliance-vmware.md) lub [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md). Urządzenie odnajduje serwery lokalne i wysyła metadane i dane wydajności do Azure Migrate: odnajdywanie i ocenianie. [Dowiedz się więcej](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Przegląd oceny maszyn wirtualnych platformy Azure
Istnieją dwa typy kryteriów ustalania rozmiarów, których można użyć do tworzenia oceny maszyn wirtualnych platformy Azure przy użyciu Azure Migrate: odnajdywania i oceny.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny oparte na zebranych danych wydajności | **Zalecany rozmiar maszyny wirtualnej**: na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany w warstwie Standardowa lub Premium)**: w zależności od liczby operacji we/wy na sekundę i przepływności dysków lokalnych.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar maszyny wirtualnej**: na podstawie rozmiaru lokalnego maszyny wirtualnej<br/><br> **Zalecany typ dysku**: na podstawie ustawienia typu magazynu wybieranego do oceny.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.

## <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Na stronie **przegląd** > **Windows, Linux i SQL Server** kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

   ![Przycisk lokalizacji oceny i migracji serwerów](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. W **Azure Migrate: odnajdywanie i ocenianie**, kliknij przycisk **Oceń** i wybierz **maszynę wirtualną platformy Azure**

    ![Lokalizacja przycisku oceny](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. **Ocenianie**  >  **typu oceny** serwerów
4. W **źródle odnajdywania**:

    - Jeśli wykryto serwery korzystające z urządzenia, wybierz pozycję **serwery odnalezione z urządzenia Azure Migrate**.
    - W przypadku wykrycia serwerów przy użyciu zaimportowanego pliku CSV wybierz pozycję **zaimportowane serwery**. 
    
1. Kliknij przycisk **Edytuj** , aby przejrzeć właściwości oceny.

    ![Lokalizacja przycisku Wyświetl wszystko, aby przejrzeć właściwości oceny](./media/tutorial-assess-vmware-azure-vm/assessment-name.png)

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
    
     ![Dodawanie maszyn wirtualnych do grupy](./media/tutorial-assess-vmware-azure-vm/assess-group.png)


1. Wybierz urządzenie i wybierz maszyny wirtualne, które chcesz dodać do grupy. Następnie kliknij przycisk **Dalej**.


1. W obszarze **Przegląd + tworzenie oceny** Przejrzyj szczegóły oceny, a następnie kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę i uruchomić ocenę.

1. Po utworzeniu oceny należy wyświetlić ją w obszarze **serwery**  >  **Azure Migrate: Ocena odnajdywania i oceny**  >  .

1. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.
    > [!NOTE]
    > W przypadku ocen opartych na wydajności zalecamy odczekanie co najmniej dnia od momentu rozpoczęcia odnajdywania przed utworzeniem oceny. Zapewnia to czas na zbieranie danych o wydajności z wyższym zaufaniem. Najlepiej po rozpoczęciu odnajdywania poczekaj na określenie czasu trwania wydajności (dzień/tydzień/miesiąc) w celu uzyskania oceny o wysokim poziomie zaufania.

## <a name="review-an-azure-vm-assessment"></a>Przegląd oceny maszyny wirtualnej platformy Azure

Ocena maszyny wirtualnej platformy Azure opisuje:

- **Gotowość na platformę Azure**: Czy serwery są odpowiednie do migracji na platformę Azure.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty obliczeniowe i magazynowe związane z uruchamianiem maszyn wirtualnych na platformie Azure.
- **Miesięczne szacowanie kosztów magazynu**: Szacowane koszty magazynu dyskowego po migracji.

### <a name="view-an-azure-vm-assessment"></a>Wyświetlanie oceny maszyny wirtualnej platformy Azure

1. W **systemach Windows, Linux i SQL Server**  >  **Azure Migrate: odnajdywanie i Ocena** kliknij liczbę obok pozycji **Ocena maszyny wirtualnej platformy Azure**.
2. W pozycji **Oceny** wybierz ocenę, aby ją otworzyć. Przykładowo (oszacowania i koszty tylko na przykład): 

    ![Podsumowanie oceny](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przegląd gotowości na platformę Azure

1. W obszarze **gotowość platformy Azure** Sprawdź, czy serwery są gotowe do migracji na platformę Azure.
2. Sprawdź stan serwera:
    - **Gotowe na platformę Azure**: Usługa Azure Migrate zaleca oszacowanie rozmiaru maszyny wirtualnej i kosztów dla maszyn wirtualnych w ocenie.
    - **Gotowe warunki**: pokazuje problemy i sugerowane korygowanie.
    - **Nie gotowy na platformę Azure**: zawiera problemy i sugerowane korygowanie.
    - **Nieznane gotowość**: używany, gdy Azure Migrate nie może ocenić gotowości ze względu na problemy z dostępnością danych.

3. Kliknij stan **gotowości platformy Azure** . Możesz wyświetlić szczegóły gotowości serwera i przejść do szczegółów, aby zobaczyć szczegóły serwera, w tym ustawienia obliczeń, magazynu i sieci.



### <a name="review-cost-details"></a>Przegląd szczegółów dotyczących kosztów

Ten widok przedstawia szacowane koszty zasobów obliczeniowych i magazynowych w przypadku korzystania z maszyn wirtualnych na platformie Azure.

1. Zapoznać się z miesięcznymi kosztami obliczeń i magazynu. Koszty są agregowane dla wszystkich serwerów w ocenianej grupie.

    - Oszacowania kosztów są oparte na zaleceniach dotyczących rozmiaru serwera oraz jego dyskach i właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Oszacowanie kosztów służy do uruchamiania serwerów lokalnych jako maszyn wirtualnych IaaS. Ocena maszyn wirtualnych platformy Azure nie uwzględnia kosztów PaaS ani SaaS.

2. Możesz przejrzeć szacunkowe oszacowanie kosztów magazynowania. Ten widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu.
3. Możesz przejść do szczegółów, aby zobaczyć szczegóły określonych serwerów.


### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Po uruchomieniu ocen opartych na wydajności, do oceny jest przypisywany rating zgodności.

![Ocena zaufania](./media/how-to-create-assessment/confidence-rating.png)

- Nadawana jest Ocena z 1 – gwiazdka (najmniejsza) do 5-gwiazdka (najwyższa).
- Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru zapewnianych przez ocenę.
- Ocena zaufania jest oparta na dostępności punktów danych potrzebnych do obliczenia oceny.

Klasyfikacje zaufania dla oceny są następujące.

**Dostępność punktu danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek




## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [mapowania zależności](how-to-create-group-machine-dependencies.md) do tworzenia grup o wysokim poziomie ufności.
- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.