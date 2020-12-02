---
title: Tworzenie oceny maszyn wirtualnych platformy Azure za pomocą oceny serwera Azure Migrate | Microsoft Docs
description: Opisuje sposób tworzenia oceny maszyn wirtualnych platformy Azure za pomocą narzędzia do oceny Azure Migrate Server
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 534619ace09b4e11934062a591adf8d9ab6f77ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500805"
---
# <a name="create-an-azure-vm-assessment"></a>Tworzenie oceny maszyny wirtualnej platformy Azure

W tym artykule opisano sposób tworzenia oceny maszyn wirtualnych platformy Azure dla lokalnych maszyn wirtualnych VMware lub maszyn wirtualnych funkcji Hyper-V z Azure Migrate: Ocena serwera.

[Azure Migrate](migrate-services-overview.md) ułatwia Migrowanie do platformy Azure. Azure Migrate udostępnia scentralizowany centrum do śledzenia odnajdywania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure. Centrum udostępnia narzędzia platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm. 

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że [utworzono](./create-manage-projects.md) projekt Azure Migrate.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera.
- Aby utworzyć ocenę, należy skonfigurować urządzenie Azure Migrate dla oprogramowania [VMware](how-to-set-up-appliance-vmware.md) lub [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md). Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do Azure Migrate: Ocena serwera. [Dowiedz się więcej](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Przegląd oceny maszyn wirtualnych platformy Azure
Istnieją dwa typy kryteriów ustalania rozmiarów, których można użyć do utworzenia oceny maszyny wirtualnej platformy Azure przy użyciu Azure Migrate: Ocena serwera.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny oparte na zebranych danych wydajności | **Zalecany rozmiar maszyny wirtualnej**: na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany w warstwie Standardowa lub Premium)**: w zależności od liczby operacji we/wy na sekundę i przepływności dysków lokalnych.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar maszyny wirtualnej**: na podstawie rozmiaru lokalnego maszyny wirtualnej<br/><br> **Zalecany typ dysku**: na podstawie ustawienia typu magazynu wybieranego do oceny.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.

## <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Przejrzyj [najlepsze rozwiązania](best-practices-assessment.md) dotyczące tworzenia ocen.
2. Na karcie **serwery** w **Azure Migrate: kafelek Ocena serwera** kliknij pozycję **Oceń**.

    ![Zrzut ekranu przedstawia Azure Migrate serwery z oceną wybraną w obszarze narzędzia do oceny.](./media/how-to-create-assessment/assess.png)

3. W obszarze **ocenianie serwerów** wybierz typ oceny jako "maszyna wirtualna platformy Azure", wybierz źródło odnajdywania i określ nazwę oceny.

    ![Podstawowe informacje dotyczące oceny](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/how-to-create-assessment//view-all.png)

5. Kliknij pozycję **Dalej**, aby **wybrać maszyny do oceny**. W obszarze **Wybierz lub utwórz grupę** wybierz pozycję **Utwórz nową** i określ nazwę grupy. Grupa zbiera jedną lub więcej maszyn wirtualnych w celu oceny.
6. W obszarze **Dodawanie maszyn do grupy** Wybierz Maszyny wirtualne, które mają zostać dodane do grupy.
7. Kliknij przycisk **Dalej**, aby przejść do pozycji **Przejrzyj i utwórz ocenę**, aby przejrzeć szczegóły dotyczące oceny.
8. Kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę, i uruchom ocenę.

    ![Tworzenie oceny](./media/how-to-create-assessment//assessment-create.png)

9. Po utworzeniu oceny możesz ją wyświetlić w pozycji **Serwery** > **Azure Migrate: Server Assessment** > **Oceny**.
10. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.



## <a name="review-an-azure-vm-assessment"></a>Przegląd oceny maszyny wirtualnej platformy Azure

Ocena maszyny wirtualnej platformy Azure opisuje:

- **Gotowość platformy Azure**: czy maszyny wirtualne są odpowiednie do migracji na platformę Azure.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty obliczeniowe i magazynowe związane z uruchamianiem maszyn wirtualnych na platformie Azure.
- **Miesięczne szacowanie kosztów magazynu**: Szacowane koszty magazynu dyskowego po migracji.

### <a name="view-an-azure-vm-assessment"></a>Wyświetlanie oceny maszyny wirtualnej platformy Azure

1. W obszarze serwery **celów migracji**  >   **Servers** kliknij pozycję **oceny** w **Azure Migrate: Ocena serwera**.
2. W obszarze **oceny** kliknij ocenę, aby go otworzyć.

    ![Podsumowanie oceny](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przegląd gotowości na platformę Azure

1. W obszarze **gotowość platformy Azure** Sprawdź, czy maszyny wirtualne są gotowe do migracji na platformę Azure.
2. Sprawdź stan maszyny wirtualnej:
    - **Gotowe na platformę Azure**: Usługa Azure Migrate zaleca oszacowanie rozmiaru maszyny wirtualnej i kosztów dla maszyn wirtualnych w ocenie.
    - **Gotowe warunki**: pokazuje problemy i sugerowane korygowanie.
    - **Nie gotowy na platformę Azure**: zawiera problemy i sugerowane korygowanie.
    - **Nieznane gotowość**: używany, gdy Azure Migrate nie może ocenić gotowości ze względu na problemy z dostępnością danych.

3. Kliknij stan **gotowości platformy Azure** . Możesz wyświetlić szczegóły gotowości maszyn wirtualnych i przejść do szczegółów, aby wyświetlić szczegóły dotyczące maszyn wirtualnych, w tym ustawienia obliczeń, magazynu i sieci.



### <a name="review-cost-details"></a>Przegląd szczegółów dotyczących kosztów

Ten widok przedstawia szacowane koszty zasobów obliczeniowych i magazynowych w przypadku korzystania z maszyn wirtualnych na platformie Azure.

1. Zapoznać się z miesięcznymi kosztami obliczeń i magazynu. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie.

    - Oszacowania kosztów opierają się na rekomendacjach dotyczących rozmiaru maszyny i jej dysków oraz na właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Oszacowanie kosztów służy do uruchamiania lokalnych maszyn wirtualnych jako maszyn wirtualnych IaaS. Ocena serwera Azure Migrate nie uwzględnia kosztów PaaS ani SaaS.

2. Możesz przejrzeć szacunkowe oszacowanie kosztów magazynowania. Ten widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu.
3. Możesz przejść do szczegółów, aby wyświetlić szczegóły dla określonych maszyn wirtualnych.


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