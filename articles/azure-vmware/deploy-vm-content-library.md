---
title: Tworzenie biblioteki zawartości służącej do wdrażania maszyn wirtualnych w rozwiązaniu Azure VMware (Automatyczna synchronizacja)
description: W tym samouczku dotyczącym rozwiązania Azure VMware (Automatyczna synchronizacja) można utworzyć bibliotekę zawartości służącą do wdrażania maszyny wirtualnej w chmurze prywatnej o automatycznej synchronizacji.
ms.topic: how-to
ms.date: 06/16/2020
ms.openlocfilehash: 6390c29184d1eccacc668d07e0e7a829908482f3
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2020
ms.locfileid: "86014234"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution-avs"></a>Tworzenie biblioteki zawartości służącej do wdrażania maszyn wirtualnych w rozwiązaniu Azure VMware (Automatyczna synchronizacja)

Biblioteka zawartości przechowuje zawartość w postaci elementów biblioteki i zarządza nią. Pojedynczy element biblioteki składa się z co najmniej jednego pliku, który służy do wdrażania maszyn wirtualnych. 
 
Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

Omawiane kwestie:
> [!div class="checklist"]
> * Tworzenie biblioteki zawartości
> * Przekazywanie obrazu ISO do biblioteki zawartości
> * Wdróż maszynę wirtualną przy użyciu obrazu ISO w bibliotece zawartości

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są wymagane segmenty przełącznika logicznego NSX-T i zarządzana usługa DHCP.  Aby uzyskać szczegółowe informacje, zobacz artykuł [jak zarządzać serwerem DHCP w programie Azure VMware (wersja zapoznawcza)](manage-dhcp.md) .

## <a name="create-a-content-library"></a>Tworzenie biblioteki zawartości

1. Z poziomu klienta lokalnego vSphere wybierz pozycję **Menu > biblioteki zawartości**.

   ![Menu wybierz > biblioteki zawartości](./media/content-library/vsphere-menu-content-libraries.png)

1. Kliknij przycisk **Dodaj** , aby utworzyć nową bibliotekę zawartości.

   ![Kliknij przycisk Dodaj, aby utworzyć nową bibliotekę zawartości.](./media/content-library/create-new-content-library.png)

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