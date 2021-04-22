---
title: Tworzenie biblioteki zawartości w celu wdrażania maszyn wirtualnych w Azure VMware Solution
description: Tworzenie biblioteki zawartości w celu wdrożenia maszyny wirtualnej w Azure VMware Solution prywatnej.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: b27d2682d8799bec6b09a08e5063359113b20a88
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873887"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Tworzenie biblioteki zawartości w celu wdrażania maszyn wirtualnych w Azure VMware Solution

Biblioteka zawartości przechowuje zawartość i zarządza nimi w postaci elementów biblioteki. Pojedynczy element biblioteki składa się z co najmniej jednego pliku używanego do wdrażania maszyn wirtualnych. 

W tym artykule otworzymy procedurę tworzenia biblioteki zawartości.  Następnie przejedzemy przez proces wdrażania maszyny wirtualnej przy użyciu obrazu ISO z biblioteki zawartości.

## <a name="prerequisites"></a>Wymagania wstępne

Segment NSX-T (przełącznik logiczny) i zarządzana usługa DHCP są wymagane do ukończenia tego samouczka.  Aby uzyskać więcej informacji, zobacz [artykuł How to manage DHCP in Azure VMware Solution (Jak](manage-dhcp.md) zarządzać Azure VMware Solution DHCP w sieci).

## <a name="create-a-content-library"></a>Tworzenie biblioteki zawartości

1. Z lokalnego klienta vSphere wybierz **pozycję Menu > bibliotek zawartości.**

   ![Wybierz menu -> bibliotek zawartości](./media/content-library/vsphere-menu-content-libraries.png)

1. Wybierz przycisk **Dodaj,** aby utworzyć nową bibliotekę zawartości.

   ![Wybierz przycisk Dodaj, aby utworzyć nową bibliotekę zawartości.](./media/content-library/create-new-content-library.png)

1. Określ nazwę i potwierdź adres IP serwera vCenter, a następnie wybierz pozycję **Dalej.**

   ![Określ nazwę i notatki, a następnie wybierz przycisk Dalej.](./media/content-library/new-content-library-step1.png)

1. Wybierz **lokalną bibliotekę zawartości i** wybierz pozycję **Dalej.**

   ![W tym przykładzie utworzymy lokalną bibliotekę zawartości i wybierzemy przycisk Dalej.](./media/content-library/new-content-library-step2.png)

1. Wybierz magazyn danych, w których będzie przechowywać bibliotekę zawartości, a następnie wybierz pozycję **Dalej.**

   ![Wybierz magazyn danych, który chcesz hostować bibliotekę zawartości, a następnie wybierz pozycję Dalej.](./media/content-library/new-content-library-step3.png)

1. Przejrzyj i sprawdź ustawienia biblioteki zawartości, a następnie wybierz pozycję **Zakończ.**

   ![Sprawdź ustawienia i wybierz pozycję Zakończ.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Przekazywanie obrazu ISO do biblioteki zawartości

Po utworzeniu biblioteki zawartości możesz dodać obraz ISO, aby wdrożyć maszynę wirtualną w klastrze chmury prywatnej. 

1. W kliencie vSphere wybierz **pozycję Menu > bibliotek zawartości.**

1. Kliknij prawym przyciskiem myszy bibliotekę zawartości, której chcesz użyć dla nowego obrazu ISO, a następnie wybierz **pozycję Importuj element**.

1. Zaimportuj element biblioteki dla źródła, wykonując jedną z następujących czynności, a następnie wybierz pozycję **Importuj**:
   1. Wybierz pozycję Adres URL i podaj adres URL, aby pobrać obraz ISO.

   1. Wybierz **pozycję Plik lokalny** do przekazania z systemu lokalnego.

   > [!TIP]
   > Opcjonalnie możesz zdefiniować niestandardową nazwę elementu i notatki dla pola Miejsce docelowe.

1. Otwórz bibliotekę i wybierz **kartę Inne typy,** aby sprawdzić, czy obraz ISO został przekazany pomyślnie.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Wdrażanie maszyny wirtualnej w klastrze chmury prywatnej

1. Z klienta vSphere wybierz pozycję Menu > **hosty i klastry.**

1. W panelu po lewej stronie rozwiń drzewo i wybierz klaster.

1. Wybierz **pozycję Akcje > nową maszynę wirtualną.**

1. Przejdź przez kreatora i zmodyfikuj ustawienia.

1. Wybierz **pozycję Nowy dysk CD/DVD > urządzenia klienckiego > pliku ISO biblioteki zawartości.**

1. Wybierz obraz ISO przekazany w poprzedniej sekcji, a następnie wybierz przycisk **OK.**

1. Zaznacz pole **wyboru Połącz,** aby obraz ISO został zainstalowany podczas do zasilania.

1. Wybierz **pozycję New Network > (Nowa sieć) i wybierz > Przeglądaj.**

1. Wybierz przełącznik **logiczny (segment), a** następnie wybierz przycisk **OK.**

1. Zmodyfikuj inne ustawienia sprzętu i wybierz pozycję **Dalej.**

1. Sprawdź ustawienia i wybierz pozycję **Zakończ.**


## <a name="next-steps"></a>Następne kroki

Teraz, po utworzeniu biblioteki zawartości w celu wdrożenia maszyn wirtualnych w Azure VMware Solution, możesz dowiedzieć się więcej na temat:

- [Jak migrować obciążenia maszyn wirtualnych do chmury prywatnej](tutorial-deploy-vmware-hcx.md)
- [Zarządzanie cyklem życia maszyn Azure VMware Solution wirtualnych](lifecycle-management-of-azure-vmware-solution-vms.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
