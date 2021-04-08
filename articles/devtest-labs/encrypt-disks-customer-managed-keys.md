---
title: Szyfrowanie dysków systemu operacyjnego przy użyciu kluczy zarządzanych przez klienta w programie Azure DevTest Labs
description: Dowiedz się, jak szyfrować dyski systemu operacyjnego (OS) przy użyciu kluczy zarządzanych przez klienta w programie Azure DevTest Labs.
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 26ef4ff1529483da9956c6dcc43807af0ffd6463
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341212"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Szyfruj dyski systemu operacyjnego (OS) przy użyciu kluczy zarządzanych przez klienta w Azure DevTest Labs
Szyfrowanie po stronie serwera (SSE) chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji. Funkcja SSE automatycznie szyfruje dane przechowywane na dyskach zarządzanych na platformie Azure (na dyskach z systemem operacyjnym i danych) domyślnie, gdy są utrwalane w chmurze. Dowiedz się więcej o [szyfrowaniu dysków](../virtual-machines/disk-encryption.md) na platformie Azure. 

W ramach programu DevTest Labs wszystkie dyski systemu operacyjnego i dyski danych utworzone w ramach laboratorium są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Jednak jako właściciel laboratorium możesz wybrać opcję szyfrowania dysków systemu operacyjnego maszyny wirtualnej laboratorium przy użyciu własnych kluczy. Jeśli zdecydujesz się na zarządzanie szyfrowaniem przy użyciu własnych kluczy, możesz określić **klucz zarządzany przez klienta** , który będzie używany do szyfrowania danych na dyskach systemu operacyjnego laboratorium. Aby dowiedzieć się więcej na temat szyfrowania po stronie serwera (SSE) z kluczami zarządzanymi przez klienta i innych typów szyfrowania dysków zarządzanych, zobacz [klucze zarządzane przez klienta](../virtual-machines/disk-encryption.md#customer-managed-keys). Ponadto zobacz [ograniczenia związane z korzystaniem z kluczy zarządzanych przez klienta](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions).

> [!NOTE]
> - Obecnie szyfrowanie dysków z kluczem zarządzanym przez klienta jest obsługiwane tylko w przypadku dysków systemu operacyjnego w DevTest Labs. 
> 
> - To ustawienie ma zastosowanie do nowo utworzonych dysków systemu operacyjnego w środowisku laboratoryjnym. Jeśli zdecydujesz się zmienić zestaw szyfrowanie dysków w pewnym momencie, starsze dyski w laboratorium będą nadal szyfrowane przy użyciu poprzedniego zestawu szyfrowania dysków. 

W poniższej sekcji pokazano, jak właściciel laboratorium może skonfigurować szyfrowanie przy użyciu klucza zarządzanego przez klienta.

## <a name="pre-requisites"></a>Wymagania wstępne

1. Jeśli nie masz zestawu szyfrowania dysków, postępuj zgodnie z tym artykułem, aby [skonfigurować Key Vault i zestaw szyfrowanie dysków](../virtual-machines/disks-enable-customer-managed-keys-portal.md). Należy pamiętać o następujących wymaganiach dotyczących zestawu szyfrowania dysków: 

    - Zestaw szyfrowania dysków musi znajdować się **w tym samym regionie i subskrypcji co laboratorium**. 
    - Upewnij się, że (właściciel laboratorium) ma co najmniej **dostęp na poziomie czytnika** do zestawu szyfrowania dysku, który będzie używany do szyfrowania dysków systemu operacyjnego laboratorium. 
1. W przypadku laboratoriów utworzonych przed 8/1/2020, właściciel laboratorium będzie musiał zapewnić, że tożsamość przypisana przez system laboratorium jest włączona. W tym celu właściciel laboratorium może przejść do laboratorium, kliknąć pozycję **Konfiguracja i zasady**, kliknąć pozycję Karta **tożsamość (wersja zapoznawcza)** , zmienić pozycję **stan** tożsamości przypisane do systemu **na włączone** i kliknąć pozycję **Zapisz**. W przypadku nowych laboratoriów utworzonych po rozpoczęciu przez system 8/1/2020 laboratorium tożsamości przypisanej do systemu będzie domyślnie włączona. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="Klucze zarządzane":::
1. Aby laboratorium obsługiwało szyfrowanie wszystkich dysków systemu operacyjnego laboratorium, właściciel laboratorium musi jawnie udzielić roli czytnika **tożsamości przypisanej przez system** do laboratorium w zestawie szyfrowania dysków oraz roli współautor maszyny wirtualnej w podstawowej subskrypcji platformy Azure. Właściciel laboratorium może to zrobić, wykonując następujące czynności:

   
    1. Upewnij się, że jesteś członkiem [roli administratora dostępu użytkowników](../role-based-access-control/built-in-roles.md#user-access-administrator) na poziomie subskrypcji platformy Azure, dzięki czemu możesz zarządzać dostępem użytkowników do zasobów platformy Azure. 
    1. Na stronie **zestaw szyfrowania dysków** wybierz pozycję **Kontrola dostępu (IAM)** w menu po lewej stronie. 
    1. Wybierz pozycję **+ Dodaj** na pasku narzędzi i wybierz pozycję **Dodaj przypisanie roli**.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Dodaj Zarządzanie rolami — menu":::
    1. Na stronie **Dodawanie przypisania roli** wybierz rolę **czytnika** lub rolę, która zapewnia większy dostęp. 
    1. Wpisz nazwę laboratorium, dla którego zostanie użyty zestaw szyfrowania dysku, a następnie wybierz nazwę laboratorium (tożsamość przypisana przez system dla laboratorium) z listy rozwijanej. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="Wybieranie tożsamości zarządzanej przez system dla laboratorium":::        
    1. Wybierz pozycję **Zapisz** na pasku narzędzi. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Zapisz przypisanie roli":::
3. Dodaj **tożsamość przypisaną do systemu** laboratorium do roli **współautor maszyny wirtualnej** za pomocą strony kontroli dostępu do **subskrypcji**  ->  **(IAM)** . Kroki są podobne do tych w poprzednich krokach. 

    
    1. Przejdź do strony **subskrypcji** w Azure Portal. 
    1. Wybierz pozycję **Kontrola dostępu (IAM)** . 
    1. Wybierz pozycję **+ Dodaj** na pasku narzędzi, a następnie wybierz pozycję **Dodaj przypisanie roli**. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="Subskrypcja — > kontroli dostępu (IAM)":::
    1. Na stronie **Dodaj przypisanie roli** wybierz opcję **współautor maszyny wirtualnej** dla roli.
    1. Wpisz nazwę laboratorium i wybierz **nazwę laboratorium** (tożsamość przypisana do systemu dla laboratorium) z listy rozwijanej. 
    1. Wybierz pozycję **Zapisz** na pasku narzędzi. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>Szyfrowanie dysków systemu operacyjnego laboratorium z kluczem zarządzanym przez klienta 

1. Na stronie głównej laboratorium w Azure Portal wybierz pozycję **Konfiguracja i zasady** w menu po lewej stronie. 
1. Na stronie **Konfiguracja i zasady** wybierz pozycję **dyski (wersja zapoznawcza)** w sekcji **szyfrowanie** . Domyślnie **typ szyfrowania** jest ustawiony na **szyfrowanie przy użyciu klucza zarządzanego platformy**.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="Karta dyski na stronie konfiguracji i zasad":::
1. W obszarze **typ szyfrowania** wybierz pozycję **szyfrowanie w systemie z użyciem klucza zarządzanego przez klienta** z listy rozwijanej. 
1. W obszarze **zestaw szyfrowania dysków** wybierz utworzony wcześniej zestaw dysków. Jest to ten sam zestaw szyfrowania dysków, do którego może uzyskać dostęp tożsamość laboratorium przypisana przez system.
1. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Włącz szyfrowanie z kluczem zarządzanym przez klienta":::
1. W polu komunikat z następującym tekstem: *to ustawienie będzie stosowane do nowo utworzonych maszyn w laboratorium. Stary dysk systemu operacyjnego pozostanie zaszyfrowany przy użyciu starego zestawu szyfrowania dysków*, a następnie wybierz **przycisk OK**. 

    Po skonfigurowaniu dyski systemu operacyjnego laboratorium zostaną zaszyfrowane za pomocą klucza zarządzanego przez klienta za pomocą zestawu szyfrowanie dysków. 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>Sprawdzanie, czy dyski są szyfrowane

1. Przejdź do maszyny wirtualnej laboratorium utworzonej po włączeniu szyfrowania dysku z kluczem zarządzanym przez klienta w laboratorium.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="Maszyna wirtualna z włączonym szyfrowaniem dysków":::
1. Kliknij grupę zasobów maszyny wirtualnej, a następnie kliknij dysk systemu operacyjnego.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="Grupa zasobów maszyny wirtualnej":::
1. Przejdź do pozycji szyfrowanie i sprawdź, czy szyfrowanie jest ustawione na klucz zarządzany przez klienta z wybranym zestawem szyfrowania dysków.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="Weryfikuj szyfrowanie":::
  
## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły: 

- [Azure Disk Encryption](../virtual-machines/disk-encryption.md). 
- [Klucze zarządzane przez klienta](../virtual-machines/disk-encryption.md#customer-managed-keys)