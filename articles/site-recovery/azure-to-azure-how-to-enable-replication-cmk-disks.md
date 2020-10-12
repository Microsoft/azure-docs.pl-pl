---
title: Włącz replikację szyfrowanych maszyn wirtualnych platformy Azure w Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania replikacji dla maszyn wirtualnych z dyskami z włączonym kluczem zarządzanym przez klienta (CMK) z jednego regionu świadczenia usługi Azure do innego przy użyciu Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/10/2020
ms.author: mayg
ms.openlocfilehash: 2dc7bbd060d77b8f90d16e2a336edc1eb6dbf651
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86528999"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replikowanie maszyn z dyskami mającymi włączone klucze zarządzane przez klienta (CMK)

W tym artykule opisano sposób replikowania maszyn wirtualnych platformy Azure z włączonymi kluczami Customer-Managed (CMK) z poziomu jednego regionu świadczenia usługi Azure do innego.

## <a name="prerequisite"></a>Wymaganie wstępne
Aby włączyć replikację dla maszyn wirtualnych z dyskami zarządzanymi przy użyciu usługi CMK, należy utworzyć zestawy szyfrowania dysków w regionie docelowym dla subskrypcji docelowej.

## <a name="enable-replication"></a>Włączanie replikacji

W tym przykładzie głównym regionem świadczenia usługi Azure jest Azja Wschodnia, a region pomocniczy to południowo Azja Wschodnia.

1. W magazynie wybierz pozycję **+ Replikuj**.
2. Zwróć uwagę na następujące pola.
    - **Źródło**: punkt pochodzenia maszyn wirtualnych, który jest w tym przypadku **platformą Azure**.
    - **Lokalizacja źródłowa**: region świadczenia usługi Azure, w którym chcesz chronić maszyny wirtualne. W tym przykładzie lokalizacja źródłowa to "Azja Wschodnia".
    - **Model wdrażania**: model wdrażania platformy Azure dla maszyn źródłowych.
    - **Subskrypcja źródłowa**: subskrypcja, do której należą źródłowe maszyny wirtualne. Może to być dowolna subskrypcja, która znajduje się w tej samej dzierżawie Azure Active Directory, co magazyn usługi Recovery Services.
    - **Grupa zasobów**: Grupa zasobów, do której należą źródłowe maszyny wirtualne. W następnym kroku zostaną wyświetlone wszystkie maszyny wirtualne w wybranej grupie zasobów do ochrony.

3. W obszarze **Virtual Machines**  >  **Wybierz pozycję Maszyny wirtualne**zaznacz każdą maszynę wirtualną, którą chcesz zreplikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie wybierz przycisk **OK**.

4. W obszarze **Ustawienia**można skonfigurować następujące ustawienia lokacji docelowej.

    - **Lokalizacja docelowa**: lokalizacja, w której będą replikowane dane źródłowej maszyny wirtualnej. Site Recovery zawiera listę odpowiednich regionów docelowych na podstawie lokalizacji wybranej maszyny. Zalecamy używanie tej samej lokalizacji co lokalizacja magazynu Recovery Services.
    - **Subskrypcja docelowa**: subskrypcja docelowa używana na potrzeby odzyskiwania po awarii. Domyślnie subskrypcja docelowa jest taka sama jak w przypadku subskrypcji źródłowej.
    - **Docelowa Grupa zasobów**: Grupa zasobów, do której należą wszystkie zreplikowane maszyny wirtualne. Domyślnie Site Recovery tworzy nową grupę zasobów w regionie docelowym. Nazwa pobiera `asr` sufiks. Jeśli grupa zasobów już istnieje, która została utworzona przez Azure Site Recovery, zostanie ponownie użyta. Możesz również wybrać opcję dostosowywania, jak pokazano w poniższej sekcji. Lokalizacją docelowej grupy zasobów może być dowolny region świadczenia usługi Azure, z wyjątkiem regionu, w którym są hostowane źródłowe maszyny wirtualne.
    - **Docelowa sieć wirtualna**: domyślnie Site Recovery tworzy nową sieć wirtualną w regionie docelowym. Nazwa pobiera `asr` sufiks. Jest on mapowany do sieci źródłowej i używany do ochrony w przyszłości. [Dowiedz się więcej](./azure-to-azure-network-mapping.md) na temat mapowania sieci.
    - **Docelowe konta magazynu (jeśli źródłowa maszyna wirtualna nie używa dysków zarządzanych)**: domyślnie Site Recovery tworzy nowe docelowe konto magazynu, naśladując konfigurację magazynu ŹRÓDŁOWEJ maszyny wirtualnej. Jeśli konto magazynu już istnieje, jest ponownie używane.
    - **Dyski zarządzane repliki (jeśli źródłowa maszyna wirtualna korzysta z dysków zarządzanych)**: Site Recovery tworzy nowe dyski zarządzane repliki w regionie docelowym, aby dublować dyski zarządzane ŹRÓDŁOWEJ maszyny wirtualnej tego samego typu magazynu (w warstwie Standardowa lub Premium) jako dyski zarządzane ŹRÓDŁOWEJ maszyny wirtualnej.
    - **Konta magazynu pamięci podręcznej**: Site Recovery potrzebuje dodatkowego konta magazynu o nazwie *Magazyn pamięci podręcznej* w regionie źródłowym. Wszystkie zmiany na źródłowych maszynach wirtualnych są śledzone i wysyłane do konta magazynu pamięci podręcznej. Są one następnie replikowane do lokalizacji docelowej.
    - **Zestaw dostępności**: domyślnie Site Recovery tworzy nowy zestaw dostępności w regionie docelowym. Nazwa ma `asr` sufiks. Jeśli zestaw dostępności, który został utworzony przez Site Recovery już istnieje, jest ponownie używany.
    - **Zestawy szyfrowania dysków (des)**: Site Recovery wymaga, aby zestawy szyfrowania dysków były używane na potrzeby replik i docelowych dysków zarządzanych. Przed włączeniem replikacji należy najpierw utworzyć algorytm DES w subskrypcji docelowej i w regionie docelowym. Domyślnie algorytm DES nie jest zaznaczony. Aby wybrać DES na dysku źródłowym, należy kliknąć przycisk "Dostosuj".
    - **Zasady replikacji**: określa ustawienia dla historii przechowywania punktów odzyskiwania i częstotliwości migawek spójnych na poziomie aplikacji. Domyślnie program Site Recovery tworzy nowe zasady replikacji z domyślnymi ustawieniami *24 godzin* w przypadku przechowywania punktów odzyskiwania i *60 minut* w przypadku częstotliwości migawek spójnych na poziomie aplikacji.

    ![Włącz replikację dla maszyny z włączonymi dyskami CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Dostosowywanie zasobów docelowych

Wykonaj następujące kroki, aby zmodyfikować domyślne ustawienia obiektu docelowego Site Recovery.

1. Wybierz pozycję **Dostosuj** obok pozycji "subskrypcja docelowa", aby zmodyfikować domyślną subskrypcję docelową. Wybierz subskrypcję z listy subskrypcji dostępnych w dzierżawie usługi Azure AD.

2. Wybierz pozycję **Dostosuj** obok pozycji "Grupa zasobów, Sieć, magazyn i zestawy dostępności", aby zmodyfikować następujące ustawienia domyślne:
    - Dla **docelowej grupy zasobów**wybierz grupę zasobów z listy grup zasobów w lokalizacji docelowej subskrypcji.
    - W polu **docelowa sieć wirtualna**wybierz sieć z listy sieci wirtualnych w lokalizacji docelowej.
    - W przypadku **zestawu dostępności**można dodać ustawienia zestawu dostępności do maszyny wirtualnej, jeśli są one częścią zestawu dostępności w regionie źródłowym.
    - W przypadku **docelowych kont magazynu**wybierz konto, które ma być używane.

3. Wybierz pozycję **Dostosuj** obok pozycji "ustawienia szyfrowania magazynu", aby wybrać docelowy algorytm DES dla każdego włączonego klucza zarządzanego przez klienta (CMK). W czasie zaznaczania zobaczysz również, który docelowy Magazyn kluczy jest skojarzony z algorytmem DES.

4. Wybierz pozycję **Utwórz zasób docelowy**  >  **Włącz replikację**.
5. Po włączeniu maszyn wirtualnych na potrzeby replikacji można sprawdzić stan kondycji maszyn wirtualnych w obszarze **zreplikowane elementy**.

![Włącz replikację dla maszyny z włączonymi dyskami CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Podczas replikacji początkowej stan może zająć trochę czasu, bez pozornego postępu. Kliknij przycisk **Odśwież**  , aby pobrać najnowszy stan.

## <a name="faqs"></a>Często zadawane pytania

* Włączono CMK na istniejącym replikowanym elemencie, jak upewnić się, że CMK jest stosowana również w regionie docelowym?

    Możesz sprawdzić nazwę dysku zarządzanego repliki (utworzonego przez Azure Site Recovery w regionie docelowym) i dołączyć algorytm DES do tego dysku repliki. Nie będzie jednak można zobaczyć szczegółów DES w bloku dysków po dołączeniu. Alternatywnie można wyłączyć replikację maszyny wirtualnej i włączyć ją ponownie. Zapewni to, że w bloku dyski dla zreplikowanego elementu zobaczysz szczegóły dotyczące algorytmu DES i magazynu kluczy.

* Dodano nowy dysk z włączonym CMK do zreplikowanego elementu. Jak można replikować ten dysk z Azure Site Recovery?

    Dodanie nowego dysku z obsługą CMK do istniejącego zreplikowanego elementu nie jest obsługiwane. Wyłącz replikację i ponownie Włącz replikację dla maszyny wirtualnej.

* Mam włączone klucze zarządzane przez platformę i klienta, jak mogę chronić moje dyski?

    Włączenie podwójnego szyfrowania z kluczami zarządzanymi przez platformę i klienta jest suppprted przez Site Recovery. Postępuj zgodnie z instrukcjami w tym artykule, aby chronić maszynę. Należy najpierw utworzyć algorytm DES z włączonym szyfrowaniem w regionie docelowym. Podczas włączania replikacji dla takiej maszyny wirtualnej można podać ten algorytm DES Site Recovery.

