---
title: Tworzenie maszyny wirtualnej z systemem Windows na podstawie wyspecjalizowanego wirtualnego dysku twardego w Azure Portal
description: Utwórz nową maszynę wirtualną z systemem Windows na podstawie dysku VHD w Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: a93ab207960133ecbf5a68927c5d16a6daf94fd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562218"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Tworzenie maszyny wirtualnej na podstawie dysku VHD przy użyciu Azure Portal

Istnieje kilka sposobów tworzenia maszyny wirtualnej na platformie Azure: 

- Jeśli masz już wirtualny dysk twardy (VHD) lub chcesz skopiować dysk VHD z istniejącej maszyny wirtualnej do użycia, możesz utworzyć nową maszynę wirtualną przez *dołączenie* dysku VHD do nowej maszyny wirtualnej jako dysku systemu operacyjnego. 

- Można utworzyć nową maszynę wirtualną na podstawie wirtualnego dysku twardego maszyny wirtualnej, która została usunięta. Na przykład jeśli masz maszynę wirtualną platformy Azure, która nie działa prawidłowo, możesz usunąć maszynę wirtualną i użyć jej wirtualnego dysku twardego, aby utworzyć nową maszynę wirtualną. Możesz użyć ponownie tego samego dysku VHD lub utworzyć kopię dysku VHD przez utworzenie migawki, a następnie utworzenie nowego dysku zarządzanego na podstawie migawki. Mimo że utworzenie migawki trwa kilka kroków, zachowuje oryginalny dysk VHD i zapewnia powrotu.

- Utwórz klasyczną maszynę wirtualną i użyj wirtualnego dysku twardego, aby utworzyć nową maszynę wirtualną, która używa modelu wdrażania Menedżer zasobów i dysków zarządzanych. Aby uzyskać najlepsze wyniki, przed utworzeniem migawki **Zatrzymaj** KLASYCZNą maszynę wirtualną w Azure Portal.
 
- Maszynę wirtualną platformy Azure można utworzyć na podstawie lokalnego wirtualnego dysku twardego przez przekazanie lokalnego dysku VHD i dołączenie go do nowej maszyny wirtualnej. Za pomocą programu PowerShell lub innego narzędzia można przekazać dysk VHD do konta magazynu, a następnie utworzyć dysk zarządzany na podstawie wirtualnego dysku twardego. Aby uzyskać więcej informacji, zobacz [przekazywanie wyspecjalizowanego wirtualnego dysku twardego](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

> [!IMPORTANT]
> 
> W przypadku tworzenia nowej maszyny wirtualnej przy użyciu wyspecjalizowanego dysku Nowa maszyna wirtualna zachowuje nazwę komputera oryginalnej maszyny wirtualnej. Inne informacje specyficzne dla komputera (np. Identyfikator CMID) również są przechowywane i w niektórych przypadkach te zduplikowane informacje mogą powodować problemy. Podczas kopiowania maszyny wirtualnej należy wiedzieć o typach informacji specyficznych dla komputera, na których zależą Twoje aplikacje.  
> W tym celu nie należy używać wyspecjalizowanego dysku, jeśli chcesz utworzyć wiele maszyn wirtualnych. Zamiast tego, w przypadku większych wdrożeń [Utwórz obraz](capture-image-resource.md) , a następnie [Użyj tego obrazu do utworzenia wielu maszyn wirtualnych](create-vm-generalized-managed.md).

Zalecamy ograniczenie liczby współbieżnych wdrożeń do 20 maszyn wirtualnych z pojedynczej migawki lub dysku VHD. 

## <a name="copy-a-disk"></a>Kopiowanie dysku

Utwórz migawkę, a następnie Utwórz dysk na podstawie migawki. Ta strategia umożliwia zachowanie oryginalnego wirtualnego dysku twardego jako powrotu:

1. Z [Azure Portal](https://portal.azure.com)z menu po lewej stronie wybierz pozycję **wszystkie usługi**.
2. W polu wyszukiwania **wszystkie usługi** wprowadź **dyski** , a następnie wybierz pozycję **dyski** , aby wyświetlić listę dostępnych dysków.
3. Wybierz dysk, którego chcesz użyć. Zostanie wyświetlona strona **dysk** dla tego dysku.
4. Z menu u góry wybierz pozycję **Utwórz migawkę**. 
5. Wprowadź **nazwę** migawki.
6. Wybierz **grupę zasobów** dla migawki. Możesz użyć istniejącej grupy zasobów lub utworzyć nową.
7. W obszarze **Typ konta** wybierz opcję magazyn **Standardowy (dysk twardy)** lub **Premium (SSD)** .
8. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz** , aby utworzyć migawkę.
9. Po utworzeniu migawki wybierz pozycję **Utwórz zasób** w menu po lewej stronie.
10. W polu wyszukiwania wprowadź **dysk zarządzany** , a następnie wybierz z listy pozycję **Managed disks** .
11. Na stronie **Managed disks** wybierz pozycję **Utwórz**.
12. Wprowadź **nazwę** dysku.
13. Wybierz **grupę zasobów** dla dysku. Możesz użyć istniejącej grupy zasobów lub utworzyć nową. Ten wybór będzie również używany jako Grupa zasobów, w której tworzysz maszynę wirtualną z dysku.
14. W obszarze **Typ konta** wybierz opcję magazyn **Standardowy (dysk twardy)** lub **Premium (SSD)** .
15. W polu **Typ źródła** upewnij się, że wybrano **migawkę** .
16. Z listy rozwijanej **migawka źródłowa** wybierz migawkę, której chcesz użyć.
17. Wprowadź inne zmiany w razie potrzeby, a następnie wybierz pozycję **Utwórz** , aby utworzyć dysk.

## <a name="create-a-vm-from-a-disk"></a>Tworzenie maszyny wirtualnej na podstawie dysku

Gdy masz dysk VHD dysku zarządzanego, którego chcesz użyć, możesz utworzyć maszynę wirtualną w portalu:

1. Z [Azure Portal](https://portal.azure.com)z menu po lewej stronie wybierz pozycję **wszystkie usługi**.
2. W polu wyszukiwania **wszystkie usługi** wprowadź **dyski** , a następnie wybierz pozycję **dyski** , aby wyświetlić listę dostępnych dysków.
3. Wybierz dysk, którego chcesz użyć. Zostanie otwarta strona **dysk** dla tego dysku.
4. Na stronie **Przegląd** upewnij się, że **stan dysku** jest wymieniony jako **niedołączony**. Jeśli tak nie jest, może być konieczne odłączenie dysku od maszyny wirtualnej lub usunięcie maszyny wirtualnej w celu zwolnienia dysku.
4. W menu w górnej części strony wybierz pozycję **Utwórz maszynę wirtualną**.
5. Na stronie **podstawowe informacje** o nowej maszynie wirtualnej wprowadź **nazwę maszyny wirtualnej** , a następnie wybierz istniejącą **grupę zasobów** lub Utwórz nową.
6. W obszarze **rozmiar** wybierz pozycję **Zmień rozmiar** , aby uzyskać dostęp do strony **rozmiar** .
7. Wybierz wiersz rozmiaru maszyny wirtualnej, a następnie wybierz **pozycję Wybierz**.
8. Na stronie **Sieć** możesz pozwolić, aby Portal utworzył wszystkie nowe zasoby, lub wybrać istniejącą **sieć wirtualną** i **grupę zabezpieczeń sieci**. Portal zawsze tworzy nowy interfejs sieciowy i publiczny adres IP dla nowej maszyny wirtualnej. 
9. Na stronie **Zarządzanie** Wprowadź wszelkie zmiany w opcjach monitorowania.
10. Na stronie **Konfiguracja gościa** Dodaj dowolne rozszerzenia zgodnie z wymaganiami.
11. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**. 
12. Jeśli konfiguracja maszyny wirtualnej przebiega pomyślnie, wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie.


## <a name="next-steps"></a>Następne kroki

Możesz również użyć programu PowerShell, aby [przekazać dysk VHD do platformy Azure i utworzyć wyspecjalizowaną maszynę wirtualną](create-vm-specialized.md).


