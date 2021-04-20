---
title: Plik dyrektywy include
description: " — plik"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: e119d40cd0b8f482d33c3c86c644cf6a0846390a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727140"
---
## <a name="generalize-the-image"></a>Uogólnij obraz

Wszystkie obrazy w Azure Marketplace muszą być wielokrotnego użytku w sposób ogólny. Aby to osiągnąć, należy uogólnić wirtualny dysk twardy systemu operacyjnego, czyli operację, która usuwa wszystkie identyfikatory specyficzne dla wystąpienia i sterowniki oprogramowania z maszyny wirtualnej.

### <a name="for-windows"></a>W przypadku systemu Windows

Dyski systemu operacyjnego Windows są uogólnione za pomocą [narzędzia sysprep.](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) W przypadku późniejszej aktualizacji lub ponownego skonfigurowania systemu operacyjnego należy ponownie uruchomić narzędzie sysprep.

> [!WARNING]
> Po uruchomieniu narzędzia sysprep wyłącz maszynę wirtualną do momentu jej wdrożenia, ponieważ aktualizacje mogą być uruchamiane automatycznie. To zamknięcie pozwoli uniknąć kolejnych aktualizacji przed wprowadzeniem zmian specyficznych dla wystąpienia w systemie operacyjnym lub zainstalowanych usługach. Aby uzyskać więcej informacji na temat uruchamiania narzędzia Sysprep, zobacz [Kroki uogólniania wirtualnego dysku twardego.](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

### <a name="for-linux"></a>W przypadku systemu Linux

Poniższy proces uogólnia maszynę wirtualną z systemem Linux i ponownie ją wdepnie jako oddzielną maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz How to create an image of a virtual machine or VHD (Jak utworzyć obraz maszyny [wirtualnej lub dysku VHD).](../../virtual-machines/linux/capture-image.md) Możesz zatrzymać się, gdy dotrzesz do sekcji "Tworzenie maszyny wirtualnej na pomocą przechwyconego obrazu".

1. Usuń agenta platformy Azure dla systemu Linux.
    1. Nawiązywanie połączenia z maszyną wirtualną z systemem Linux przy użyciu klienta SSH.
    2. W oknie SSH wprowadź to polecenie: `sudo waagent –deprovision+user` .
    3. Wpisz Y, aby kontynuować (możesz dodać parametr -force do poprzedniego polecenia, aby uniknąć kroku potwierdzenia).
    4. Po zakończeniu polecenia wprowadź polecenie **Exit (Zakończ),** aby zamknąć klienta SSH.
2. Zatrzymaj maszynę wirtualną.
    1. W Azure Portal wybierz grupę zasobów (RG) i cokoń przydzielanie maszyny wirtualnej.
    2. Maszyna wirtualna jest teraz uogólniona i można utworzyć nową maszynę wirtualną przy użyciu tego dysku maszyny wirtualnej.

### <a name="capture-image"></a>Przechwytywanie obrazu

> [!NOTE]
> Subskrypcja platformy Azure zawierająca usługę SIG musi znajdować się w tej samej dzierżawie co konto wydawcy, aby można było ją opublikować. Ponadto konto wydawcy musi mieć co najmniej dostęp współautora do subskrypcji zawierającej usługę SIG.

Gdy maszyna wirtualna będzie gotowa, możesz przechwycić ją w galerii obrazów udostępnionych platformy Azure. Wykonaj poniższe kroki, aby przechwycić następujące informacje:

1. Na [Azure Portal](https://ms.portal.azure.com/)przejdź do strony maszyny wirtualnej.
2. Wybierz pozycję **Przechwyć.**
3. W **obszarze Udostępnianie obrazu do galerii obrazów udostępnionych** wybierz pozycję Tak, udostępnij go w **galerii jako wersję obrazu.**
4. W **obszarze Stan systemu operacyjnego** wybierz pozycję Uogólnione.
5. Wybierz galerię obrazów docelowych **lub pozycję Utwórz nową.**
6. Wybierz definicję obrazu docelowego lub **pozycję Utwórz nowy.**
7. Podaj **numer wersji** obrazu.
8. Wybierz **pozycję Przejrzyj i utwórz,** aby przejrzeć wybrane opcje.
9. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

Aby udzielić dostępu:

1. Przejdź do Shared Image Gallery.
2. Wybierz **pozycję Kontrola dostępu** (IAM) w panelu po lewej stronie.
3. Wybierz **pozycję Dodaj,** a następnie **pozycję Dodaj przypisanie roli.**
4. Wybierz rolę **lub** **właściciela.**
5. W **obszarze Przypisz dostęp wybierz** pozycję **Użytkownik, grupa lub nazwa główna usługi.**
6. Wybierz adres e-mail platformy Azure osoby, która będzie publikować obraz.
7. Wybierz pozycję **Zapisz**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Wyświetla okno dodawania przypisania roli.":::

> [!NOTE]
> Nie musisz generować sygnatur dostępu współdzielonego, ponieważ teraz możesz opublikować obraz SIG na Partner Center. Jeśli jednak nadal musisz zapoznać się z krokami generowania identyfikatorów URI sygnatury dostępu współdzielonego, zobacz Jak wygenerować identyfikator URI sygnatury dostępu współdzielonego [dla obrazu maszyny wirtualnej.](../azure-vm-get-sas-uri.md)
