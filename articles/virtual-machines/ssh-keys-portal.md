---
title: Utwórz klucze SSH w Azure Portal
description: Dowiedz się, jak generować i przechowywać klucze SSH w Azure Portal na potrzeby łączenia maszyn wirtualnych z systemem Linux.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/25/2020
ms.author: cynthn
ms.openlocfilehash: abc9a2ae130d987c90ce87ffaecbf2bb44b06010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88929446"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>Generowanie i przechowywanie kluczy SSH w Azure Portal

Jeśli Portal jest często używany do wdrażania maszyn wirtualnych z systemem Linux, można uprościć korzystanie z kluczy SSH przez utworzenie ich bezpośrednio w portalu lub przekazanie ich z komputera.

Klucze SSH można utworzyć podczas pierwszej tworzenia maszyny wirtualnej, a następnie użyć ich ponownie dla innych maszyn wirtualnych. Można też utworzyć klucze SSH oddzielnie, dzięki czemu masz zestaw kluczy przechowywanych na platformie Azure, aby dopasować się do potrzeb organizacji. 

Jeśli masz istniejące klucze i chcesz uprościć korzystanie z nich w portalu, możesz je przekazać i zapisać na platformie Azure do ponownego użycia.

Aby uzyskać szczegółowe informacje na temat tworzenia i używania kluczy SSH z maszynami wirtualnymi z systemem Linux, zobacz [Używanie kluczy SSH do łączenia z maszynami wirtualnymi z systemem Linux](./linux/ssh-from-windows.md).

## <a name="generate-new-keys"></a>Generuj nowe klucze

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

1. W górnej części strony wpisz *SSH* , aby wyszukać. W obszarze **Marketplace** wybierz pozycję **klucze SSH**.

1. Na stronie **klucz SSH** wybierz pozycję **Utwórz**.

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="Tworzenie nowej grupy zasobów i generowanie pary kluczy SSH":::

1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów, w której mają być przechowywane klucze. Wpisz nazwę grupy zasobów, a następnie wybierz przycisk **OK**.

1. W **obszarze region** wybierz region, w którym mają być przechowywane klucze. Możesz użyć kluczy w dowolnym regionie, to tylko region, w którym będą przechowywane.

1. Wpisz nazwę klucza w polu **Nazwa pary kluczy**.

1. W polu **Źródło klucza publicznego SSH** wybierz pozycję **Generuj Źródło klucza publicznego**. 

1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**.

1. Po przeprowadzeniu walidacji wybierz pozycję **Utwórz**.

1. Następnie zostanie wyświetlone okno podręczne, a następnie wybierz pozycję **Pobierz klucz prywatny i utwórz zasób**. Spowoduje to pobranie klucza SSH jako pliku PEM.

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="Pobierz klucz prywatny jako plik PEM":::

1. Po pobraniu pliku PEM warto go przenieść w dowolnym miejscu na komputerze, na którym można łatwo wskazywać od klienta SSH.


## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Na komputerze lokalnym Otwórz wiersz polecenia programu PowerShell i wpisz:

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

Na przykład wpisz: `ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>Przekazywanie klucza SSH

Możesz również przekazać publiczny klucz SSH do przechowywania na platformie Azure. Aby uzyskać informacje o sposobie tworzenia pary kluczy SSH, zobacz [Używanie kluczy SSH do łączenia się z maszynami wirtualnymi z systemem Linux](./linux/ssh-from-windows.md).

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

1. W górnej części strony wpisz *SSH* , aby wyszukać. W obszarze **Marketplace* wybierz pozycję **klucze SSH**.

1. Na stronie **klucz SSH** wybierz pozycję **Utwórz**.

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="Przekazywanie klucza publicznego SSH do przechowywania na platformie Azure":::

1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów, w której mają być przechowywane klucze. Wpisz nazwę grupy zasobów, a następnie wybierz przycisk **OK**.

1. W **obszarze region** wybierz region, w którym mają być przechowywane klucze. Możesz użyć kluczy w dowolnym regionie, to tylko region, w którym będą przechowywane.

1. Wpisz nazwę klucza w polu **Nazwa pary kluczy**.

1. W polu **Źródło klucza publicznego SSH** wybierz opcję **Przekaż istniejący klucz publiczny**. 

1. Wklej pełną zawartość klucza publicznego w polu **klucz przekazywania** , a następnie wybierz pozycję **Przegląd + Utwórz**.

1. Po zakończeniu walidacji wybierz pozycję **Utwórz**. 

Po przekazaniu klucza można go użyć podczas tworzenia maszyny wirtualnej.

## <a name="list-keys"></a>Klucze list

Klucze SSH utworzone w portalu są przechowywane jako zasoby, dzięki czemu można filtrować widok zasobów, aby wyświetlić wszystkie z nich.

1. W portalu wybierz pozycję **wszystkie zasoby**.
1. W obszarze Filtry wybierz opcję **Typ**, usuń zaznaczenie opcji **Zaznacz wszystko** , aby wyczyścić listę.
1. W filtrze wpisz **SSH** , a następnie wybierz pozycję **klucz SSH**.

   :::image type="content" source="./media/ssh-keys/filter.png" alt-text="Zrzut ekranu przedstawiający sposób filtrowania listy w celu wyświetlenia wszystkich kluczy SSH.":::

## <a name="get-the-public-key"></a>Pobierz klucz publiczny

Jeśli potrzebujesz klucza publicznego, możesz łatwo skopiować go ze strony portalu dla klucza. Po prostu Utwórz listę kluczy (przy użyciu procesu w ostatniej sekcji), a następnie wybierz klucz z listy. Zostanie otwarta strona klucza, a następnie można kliknąć ikonę **Kopiuj do schowka** obok klawisza, aby skopiować.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat używania kluczy SSH z maszynami wirtualnymi platformy Azure, zobacz [Używanie kluczy SSH do łączenia z maszynami wirtualnymi z systemem Linux](./linux/ssh-from-windows.md)
