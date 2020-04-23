---
title: Tworzenie maszyny Wirtualnej systemu Windows na podstawie wyspecjalizowanej sieci VHD w witrynie Azure portal
description: Utwórz nową maszynę wirtualną systemu Windows z dysku wirtualnego w witrynie Azure portal.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: d360ac5a57fe72b092a6694721905c066527bba3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086457"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Tworzenie maszyny wirtualnej z dysku wirtualnego przy użyciu portalu Azure

Istnieje kilka sposobów tworzenia maszyny wirtualnej (VM) na platformie Azure: 

- Jeśli masz już wirtualny dysk twardy (VHD) do użycia lub chcesz skopiować dysk VHD z istniejącej maszyny Wirtualnej do użycia, możesz utworzyć nową maszynę wirtualną, *dołączając* dysk WIRTUALNY do nowej maszyny Wirtualnej jako dysk systemu operacyjnego. 

- Można utworzyć nową maszynę wirtualną z dysku VHD maszyny Wirtualnej, która została usunięta. Na przykład jeśli masz maszynę wirtualną platformy Azure, która nie działa poprawnie, można usunąć maszynę wirtualną i użyć jej wirtualnej maszyny Wirtualnej do utworzenia nowej maszyny Wirtualnej. Można ponownie użyć tego samego dysku VHD lub utworzyć kopię dysku VHD, tworząc migawkę, a następnie tworząc nowy dysk zarządzany z migawki. Chociaż tworzenie migawki ma kilka kroków więcej, zachowuje oryginalny dysk wirtualny i zapewnia rezerwowe.

- Weź klasyczną maszynę wirtualną i użyj dysku VHD, aby utworzyć nową maszynę wirtualną, która używa modelu wdrażania Menedżera zasobów i dysków zarządzanych. Aby uzyskać najlepsze wyniki, **zatrzymaj** klasyczną maszynę wirtualną w witrynie Azure portal przed utworzeniem migawki.
 
- Maszynę wirtualną platformy Azure można utworzyć z lokalnego dysku wirtualnego, przekazując lokalny dysk wirtualny i dołączając ją do nowej maszyny Wirtualnej. Program PowerShell lub inne narzędzie służy do przekazywania dysku VHD na konto magazynu, a następnie tworzenia dysku zarządzanego z dysku VHD. Aby uzyskać więcej informacji, zobacz [Przekazywanie specjalistycznego dysku VHD](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Nie używaj specjalistycznego dysku, jeśli chcesz utworzyć wiele maszyn wirtualnych. Zamiast tego w przypadku większych wdrożeń [utwórz obraz,](capture-image-resource.md) a następnie [użyj tego obrazu do utworzenia wielu maszyn wirtualnych](create-vm-generalized-managed.md).

Zaleca się ograniczenie liczby równoczesnych wdrożeń do 20 maszyn wirtualnych z jednej migawki lub dysku wirtualnego. 

## <a name="copy-a-disk"></a>Kopiowanie dysku

Utwórz migawkę, a następnie utwórz dysk z migawki. Ta strategia pozwala zachować oryginalny dysk WIRTUALNY jako rezerwowy:

1. Z [witryny Azure portal](https://portal.azure.com), w menu po lewej stronie wybierz pozycję **Wszystkie usługi**.
2. W polu wyszukiwania **Wszystkie usługi** wprowadź **dyski,** a następnie wybierz pozycję **Dyski,** aby wyświetlić listę dostępnych dysków.
3. Wybierz dysk, którego chcesz użyć. Zostanie wyświetlona strona **Dysk** dla tego dysku.
4. Z menu u góry wybierz pozycję **Utwórz migawkę**. 
5. Wprowadź **nazwę** migawki.
6. Wybierz **grupę zasobów** dla migawki. Można użyć istniejącej grupy zasobów lub utworzyć nową.
7. W **przypadku opcji Typ konta**wybierz miejsce do przechowywania **standardowego (HDD)** lub **Premium (SSD).**
8. Po zakończeniu wybierz pozycję **Utwórz,** aby utworzyć migawkę.
9. Po utworzeniu migawki wybierz pozycję **Utwórz zasób** w menu po lewej stronie.
10. W polu wyszukiwania wprowadź **dysk zarządzany,** a następnie wybierz z listy **pozycję Dyski zarządzane.**
11. Na stronie **Dyski zarządzane** wybierz pozycję **Utwórz**.
12. Wprowadź **nazwę** dysku.
13. Wybierz **grupę zasobów** dla dysku. Można użyć istniejącej grupy zasobów lub utworzyć nową. To zaznaczenie będzie również używane jako grupa zasobów, w której utworzysz maszynę wirtualną z dysku.
14. W **przypadku opcji Typ konta**wybierz miejsce do przechowywania **standardowego (HDD)** lub **Premium (SSD).**
15. W **typie źródła**upewnij się, że wybrano **opcję Migawka.**
16. Z **listy rozwijanej Migawka źródło** wybierz migawkę, której chcesz użyć.
17. W razie potrzeby należy wprowadzić inne zmiany, a następnie wybrać pozycję **Utwórz,** aby utworzyć dysk.

## <a name="create-a-vm-from-a-disk"></a>Tworzenie maszyny wirtualnej z dysku

Po dysku zarządzanym VHD, który chcesz użyć, można utworzyć maszynę wirtualną w portalu:

1. Z [witryny Azure portal](https://portal.azure.com), w menu po lewej stronie wybierz pozycję **Wszystkie usługi**.
2. W polu wyszukiwania **Wszystkie usługi** wprowadź **dyski,** a następnie wybierz pozycję **Dyski,** aby wyświetlić listę dostępnych dysków.
3. Wybierz dysk, którego chcesz użyć. Zostanie otwarta strona **Dysk** dla tego dysku.
4. Na stronie **Przegląd** upewnij się, że **stan dysku** jest wyświetlany jako **Niepodłączony**. Jeśli tak nie jest, może być konieczne odłączenie dysku od maszyny Wirtualnej lub usunięcie maszyny wirtualnej, aby zwolnić dysk.
4. W menu u góry strony wybierz pozycję **Utwórz maszynę wirtualną**.
5. Na stronie **Podstawy** dla nowej maszyny wirtualnej wprowadź **nazwę maszyny wirtualnej** i wybierz istniejącą **grupę zasobów** lub utwórz nową.
6. W polu **Rozmiar**wybierz pozycję **Zmień rozmiar,** aby uzyskać dostęp do strony **Rozmiar.**
7. Wybierz wiersz rozmiaru maszyny Wirtualnej, a następnie wybierz pozycję **Wybierz**.
8. Na stronie **Sieć** można pozwolić portalowi utworzyć wszystkie nowe zasoby lub wybrać istniejącą **grupę zabezpieczeń** **Sieć wirtualna** i Sieć . Portal zawsze tworzy nowy interfejs sieciowy i publiczny adres IP dla nowej maszyny Wirtualnej. 
9. Na stronie **Zarządzanie** należy wprowadzić wszelkie zmiany w opcjach monitorowania.
10. Na stronie **Konfiguracje gości** dodaj w razie potrzeby wszystkie rozszerzenia.
11. Po zakończeniu wybierz pozycję **Przejrzyj + utwórz**. 
12. Jeśli konfiguracja maszyny Wirtualnej przechodzi sprawdzanie poprawności, wybierz **pozycję Utwórz,** aby rozpocząć wdrażanie.


## <a name="next-steps"></a>Następne kroki

Za pomocą programu PowerShell można również [przekazać dysk VHD na platformę Azure i utworzyć wyspecjalizowaną maszynę wirtualną.](create-vm-specialized.md)


