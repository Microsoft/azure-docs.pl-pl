---
title: Tworzenie biblioteki zawartości w celu wdrożenia maszyn wirtualnych w rozwiązaniu VMware platformy Azure
description: Utwórz bibliotekę zawartości, aby wdrożyć maszynę wirtualną w chmurze prywatnej rozwiązania Azure VMware.
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: a8e2f1911899e659724760cff514a33c8f0740a5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347586"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Tworzenie biblioteki zawartości w celu wdrożenia maszyn wirtualnych w rozwiązaniu VMware platformy Azure

Biblioteka zawartości przechowuje zawartość w postaci elementów biblioteki i zarządza nią. Pojedynczy element biblioteki składa się z co najmniej jednego pliku, który służy do wdrażania maszyn wirtualnych. 

W tym artykule omówiono procedurę tworzenia biblioteki zawartości.  Następnie zajmiemy się wdrażaniem maszyny wirtualnej przy użyciu obrazu ISO z biblioteki zawartości.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są wymagane segmenty przełącznika logicznego NSX-T i zarządzana usługa DHCP.  Aby uzyskać więcej informacji, zobacz artykuł [dotyczący zarządzania serwerem DHCP w programie Azure VMware](manage-dhcp.md) .

## <a name="create-a-content-library"></a>Tworzenie biblioteki zawartości

1. Z poziomu klienta lokalnego vSphere wybierz pozycję **Menu > biblioteki zawartości**.

   ![Menu wybierz > biblioteki zawartości](./media/content-library/vsphere-menu-content-libraries.png)

1. Wybierz przycisk **Dodaj** , aby utworzyć nową bibliotekę zawartości.

   ![Wybierz przycisk Dodaj, aby utworzyć nową bibliotekę zawartości.](./media/content-library/create-new-content-library.png)

1. Określ nazwę i Potwierdź adres IP serwera vCenter, a następnie wybierz przycisk **dalej**.

   ![Określ nazwę i wybrane uwagi, a następnie wybierz przycisk Dalej.](./media/content-library/new-content-library-step1.png)

1. Wybierz **bibliotekę zawartości lokalnej** i wybierz pozycję **dalej**.

   ![Na potrzeby tego przykładu będziemy tworzyć lokalną bibliotekę zawartości, wybierając dalej.](./media/content-library/new-content-library-step2.png)

1. Wybierz magazyn danych, w którym będzie przechowywana Biblioteka zawartości, a następnie wybierz pozycję **dalej**.

   ![Wybierz magazyn danych, który chcesz hostować bibliotekę zawartości, a następnie wybierz pozycję Dalej.](./media/content-library/new-content-library-step3.png)

1. Przejrzyj i sprawdź ustawienia biblioteki zawartości, a następnie wybierz pozycję **Zakończ**.

   ![Sprawdź ustawienia, a następnie wybierz pozycję Zakończ.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Przekazywanie obrazu ISO do biblioteki zawartości

Teraz, po utworzeniu biblioteki zawartości, można dodać obraz ISO, aby wdrożyć maszynę wirtualną w klastrze chmury prywatnej. 

1. Z poziomu klienta vSphere wybierz pozycję **Menu > biblioteki zawartości**.

1. Kliknij prawym przyciskiem myszy bibliotekę zawartości, której chcesz użyć dla nowego ISO, i wybierz pozycję **Importuj element**.

1. Zaimportuj element biblioteki dla źródła, wykonując jedną z następujących czynności, a następnie wybierz pozycję **Importuj**:
   1. Wybierz pozycję adres URL i podaj adres URL, aby pobrać plik ISO.

   1. Wybierz **plik lokalny** do przekazania z systemu lokalnego.

   > [!TIP]
   > Opcjonalnie można zdefiniować niestandardową nazwę elementu i uwagi dla miejsca docelowego.

1. Otwórz bibliotekę i wybierz kartę **Inne typy** , aby sprawdzić, czy plik ISO został pomyślnie przekazany.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Wdrażanie maszyny wirtualnej w klastrze chmury prywatnej

1. Z poziomu klienta vSphere wybierz pozycję **Menu > hosty i klastry**.

1. W lewym panelu rozwiń drzewo i wybierz klaster.

1. Wybierz **akcje > nowej maszyny wirtualnej**.

1. Przejdź przez kreatora i zmodyfikuj ustawienia.

1. Wybierz pozycję **Nowa stacja dysków CD/DVD > urządzenie klienckie > pliku ISO biblioteki zawartości**.

1. Wybierz plik ISO przekazany w poprzedniej sekcji, a następnie wybierz przycisk **OK**.

1. Zaznacz pole wyboru **Połącz** , aby zamontować plik ISO w czasie włączania.

1. Wybierz kolejno pozycje **Nowy sieć > wybierz listę rozwijaną > Przeglądaj**.

1. Wybierz **przełącznik logiczny (segment)** , a następnie wybierz **przycisk OK**.

1. Zmodyfikuj wszystkie inne ustawienia sprzętu i wybierz pozycję **dalej**.

1. Sprawdź ustawienia i wybierz pozycję **Zakończ**.


## <a name="next-steps"></a>Następne kroki

Jeśli planujesz używanie rozszerzenia chmury hybrydowej (HCX) do migrowania obciążeń maszyn wirtualnych do chmury prywatnej, użyj procedury [rozwiązania Install HCX for Azure VMware](hybrid-cloud-extension-installation.md) .

<!-- LINKS - external-->

<!-- LINKS - internal -->
