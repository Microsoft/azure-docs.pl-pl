---
title: Plik dyrektywy include
description: " — plik"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 03/25/2021
ms.openlocfilehash: 8898a762e8a1e7a2d5c104f99d12032c676a5ca4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630089"
---
## <a name="generalize-the-image"></a>Uogólnianie obrazu

Wszystkie obrazy w portalu Azure Marketplace muszą być wielokrotnego użytku w ogólny sposób. Aby to osiągnąć, musi być uogólniony wirtualny dysk twardy systemu operacyjnego, operacja, która usuwa wszystkie identyfikatory specyficzne dla wystąpienia i sterowniki oprogramowania z maszyny wirtualnej.

### <a name="for-windows"></a>W przypadku systemu Windows

Dyski systemu operacyjnego Windows są uogólnione za pomocą narzędzia [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Jeśli później zaktualizujesz lub ponownie skonfigurujesz system operacyjny, należy ponownie uruchomić program Sysprep.

> [!WARNING]
> Po uruchomieniu programu Sysprep należy wyłączyć maszynę wirtualną, dopóki nie zostanie wdrożona, ponieważ aktualizacje mogą być uruchamiane automatycznie. To zamknięcie spowoduje uniknięcie kolejnych aktualizacji w celu wprowadzenia zmian specyficznych dla wystąpienia do systemu operacyjnego lub zainstalowanych usług. Aby uzyskać więcej informacji na temat uruchamiania programu Sysprep, zobacz [procedura uogólniania dysku VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>W przypadku systemu Linux

Poniższy proces uogólni maszynę wirtualną z systemem Linux i ponownie wdraża ją jako oddzielną maszynę wirtualną. Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego](../../virtual-machines/linux/capture-image.md). Możesz zatrzymać, gdy dojdziesz do sekcji o nazwie "Tworzenie maszyny wirtualnej z przechwyconego obrazu".

1. Usuń agenta platformy Azure dla systemu Linux.
    1. Nawiązywanie połączenia z maszyną wirtualną z systemem Linux przy użyciu klienta SSH.
    2. W oknie SSH wprowadź następujące polecenie: `sudo waagent –deprovision+user` .
    3. Wpisz Y, aby kontynuować (parametr-Force można dodać do poprzedniego polecenia, aby uniknąć kroku potwierdzenia).
    4. Po zakończeniu wykonywania polecenia wpisz **Exit** , aby zamknąć klienta SSH.
2. Zatrzymaj maszynę wirtualną.
    1. W Azure Portal wybierz grupę zasobów (RG), a następnie usuń przydział maszyny wirtualnej.
    2. Maszyna wirtualna jest teraz uogólniona i można utworzyć nową maszynę wirtualną przy użyciu tego dysku maszyny wirtualnej.

### <a name="capture-image"></a>Przechwyć obraz

Gdy maszyna wirtualna będzie gotowa, możesz ją przechwycić w galerii udostępnionych obrazów systemu Azure. Wykonaj poniższe kroki, aby przechwycić:

1. Na [Azure Portal](https://ms.portal.azure.com/)przejdź do strony swojej maszyny wirtualnej.
2. Wybierz pozycję **Przechwyć**.
3. W obszarze **udostępnianie obrazu do galerii obrazów udostępnionych** wybierz pozycję **tak, Udostępnij ją w galerii jako wersję obrazu**.
4. W obszarze **stan systemu operacyjnego** wybierz opcję uogólnione.
5. Wybierz docelową galerię obrazów lub **Utwórz nową**.
6. Wybierz definicję obrazu docelowego lub **Utwórz nową**.
7. Podaj **numer wersji** obrazu.
8. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje.
9. Po przekazaniu walidacji wybierz pozycję **Utwórz**.

Subskrypcja platformy Azure zawierająca SIG musi znajdować się w tej samej dzierżawie co konto wydawcy, aby można było ją opublikować. Ponadto konto wydawcy musi mieć dostęp właściciela do SIG. 

Aby udzielić dostępu:

1. Przejdź do galerii obrazów udostępnionych.
2. Na panelu po lewej stronie wybierz pozycję **Kontrola dostępu** (IAM).
3. Wybierz pozycję **Dodaj** , a następnie **Dodaj przypisanie roli**.
4. Wybierz **rolę** lub **właściciela**.
5. W obszarze **Przypisz dostęp do** wybierz **użytkownika, grupę lub nazwę główną usługi**.
6. Wybierz adres e-mail platformy Azure osoby, która będzie publikować obraz.
7. Wybierz pozycję **Zapisz**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Wyświetla okno Dodawanie przypisania roli.":::

> [!NOTE]
> Nie musisz generować identyfikatorów URI sygnatury dostępu współdzielonego, ponieważ teraz możesz publikować obraz SIG w centrum partnerskim. Jeśli jednak nadal musisz odwołać się do procedury generowania identyfikatora URI sygnatury dostępu współdzielonego, zobacz [jak wygenerować identyfikator URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej](../azure-vm-get-sas-uri.md).
