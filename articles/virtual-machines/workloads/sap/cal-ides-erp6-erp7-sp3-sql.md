---
title: Wdrażanie oprogramowania SAP środowisk IDE EHP7 SP3 dla oprogramowania SAP ERP 6,0 na platformie Azure | Microsoft Docs
description: Wdrażanie programu SAP środowisk IDE EHP7 SP3 dla oprogramowania SAP ERP 6,0 na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 626c1523-1026-478f-bd8a-22c83b869231
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/16/2016
ms.author: hermannd
ms.openlocfilehash: 56f5072c90a699270a92af317976c8f56bd3e2cb
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536532"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Wdrażanie programu SAP środowisk IDE EHP7 SP3 dla oprogramowania SAP ERP 6,0 na platformie Azure
W tym artykule opisano sposób wdrażania systemu SAP środowisk IDE z systemem SQL Server i systemem operacyjnym Windows na platformie Azure za pośrednictwem biblioteki urządzeń SAP w chmurze (SAP CAL) 3,0. Zrzuty ekranu przedstawiają proces krok po kroku. Aby wdrożyć inne rozwiązanie, wykonaj te same czynności.

Aby rozpocząć od licencji SAP CAL, przejdź do witryny sieci Web z [biblioteką urządzeń SAP Cloud](https://cal.sap.com/) . W oprogramowaniu SAP znajduje się również Blog dotyczący nowej [biblioteki urządzeń w chmurze sap 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> Od 29 maja 2017, można użyć modelu wdrażania Azure Resource Manager oprócz niepreferowanego klasycznego modelu wdrażania do wdrożenia SAP CAL. Zalecamy używanie nowego modelu wdrażania Menedżer zasobów i pomijanie klasycznego modelu wdrażania.

Jeśli utworzono już konto SAP CAL, które korzysta z modelu klasycznego, należy *utworzyć inne konto SAP cal*. To konto musi być wdrażane wyłącznie na platformie Azure przy użyciu modelu Menedżer zasobów.

Po zalogowaniu się do licencji SAP CAL pierwsza strona zwykle prowadzi do strony **rozwiązania** . Rozwiązania oferowane w oprogramowaniu SAP CAL są stale zwiększane, więc konieczne może być przewinięcie w jakiś sposób, aby znaleźć potrzebne rozwiązanie. Wyróżnione rozwiązanie SAP środowisk IDE oparte na systemie Windows, które jest dostępne wyłącznie na platformie Azure, demonstruje proces wdrażania:

![Rozwiązania SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Utwórz konto w licencji SAP CAL
1. Aby zalogować się do licencji SAP CAL po raz pierwszy, użyj użytkownika SAP S lub innego użytkownika zarejestrowanego w oprogramowaniu SAP. Następnie Zdefiniuj konto SAP CAL, które jest używane przez SAP CAL do wdrożenia urządzeń na platformie Azure. W definicji konta należy:

    a. Wybierz model wdrażania na platformie Azure (Menedżer zasobów lub klasyczny).

    b. Wprowadź subskrypcję platformy Azure. Konto SAP CAL może być przypisane tylko do jednej subskrypcji. Jeśli potrzebujesz więcej niż jednej subskrypcji, musisz utworzyć inne konto SAP CAL.
    
    c. Nadaj uprawnienia SAP CAL do wdrożenia w ramach subskrypcji platformy Azure.

   > [!NOTE]
   >  W następnych krokach pokazano, jak utworzyć konto SAP CAL dla wdrożeń Menedżer zasobów. Jeśli masz już konto SAP CAL, które jest połączone z klasycznym modelem wdrażania *, musisz wykonać* następujące kroki, aby utworzyć nowe konto SAP cal. Nowe konto SAP CAL musi zostać wdrożone w modelu Menedżer zasobów.

1. Aby utworzyć nowe konto SAP CAL, na stronie **konta** są wyświetlane dwie opcje platformy Azure: 

    a. **Microsoft Azure (klasyczny)** to klasyczny model wdrażania i nie jest już preferowany.

    b. **Microsoft Azure** jest nowym Menedżer zasobów modelem wdrażania.

    ![Zrzut ekranu przedstawia konta A P CAL z Microsoft Azure o nazwie.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Aby wdrożyć w modelu Menedżer zasobów, wybierz pozycję **Microsoft Azure**.

    ![Zrzut ekranu przedstawia konta A P CAL.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Wprowadź **Identyfikator subskrypcji** platformy Azure, który można znaleźć na Azure Portal. 

    ![Identyfikator subskrypcji SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Aby autoryzować licencję SAP CAL do wdrożenia w zdefiniowanej subskrypcji platformy Azure, kliknij przycisk **Autoryzuj**. Na karcie Przeglądarka zostanie wyświetlona następująca strona:

    ![Logowanie do usług w chmurze programu Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Jeśli na liście znajduje się więcej niż jeden użytkownik, wybierz konto Microsoft, który jest powiązany z tym współadministratorem wybranej subskrypcji platformy Azure. Na karcie Przeglądarka zostanie wyświetlona następująca strona:

    ![Potwierdzenie usług w chmurze programu Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Kliknij przycisk **Zaakceptuj**. Jeśli autoryzacja zakończyła się pomyślnie, definicja konta SAP CAL zostanie wyświetlona ponownie. Po krótkim czasie komunikat potwierdza, że proces autoryzacji zakończył się pomyślnie.

1. Aby przypisać nowo utworzone konto SAP CAL do użytkownika, wprowadź swój **Identyfikator użytkownika** w polu tekstowym po prawej stronie, a następnie kliknij przycisk **Dodaj**. 

    ![Skojarzenie konta z użytkownikiem](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Aby skojarzyć konto z użytkownikiem, którego używasz do logowania się do platformy SAP CAL, kliknij przycisk **Przeglądaj**. 

1. Aby utworzyć skojarzenie między użytkownikiem i nowo utworzonym kontem SAP CAL, kliknij przycisk **Utwórz**.

    ![Skojarzenie użytkownika z kontem](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Pomyślnie utworzono konto SAP CAL, które jest w stanie:

- Użyj modelu wdrażania usługi Resource Manager.
- Wdróż systemy SAP w ramach subskrypcji platformy Azure.

> [!NOTE]
> Aby można było wdrożyć rozwiązanie SAP środowisk IDE w oparciu o system Windows i SQL Server, może być konieczne zarejestrowanie się w celu skorzystania z subskrypcji SAP CAL. W przeciwnym razie rozwiązanie może być widoczne jako **zablokowane** na stronie Przegląd.

### <a name="deploy-a-solution"></a>Wdróż rozwiązanie
1. Po skonfigurowaniu konta SAP CAL wybierz **rozwiązanie SAP środowisk IDE w systemie Windows i SQL Server** rozwiązanie. Kliknij pozycję **Utwórz wystąpienie**i Potwierdź warunki użytkowania. 

1. Na stronie **Tryb podstawowy: Tworzenie wystąpienia** należy wykonać następujące instrukcje:

    a. Wprowadź **nazwę**wystąpienia.

    b. Wybierz **region**platformy Azure. Aby uzyskać dostęp do wielu dostępnych regionów platformy Azure, może być potrzebna subskrypcja SAP CAL.

    c.  Wprowadź **hasło** główne dla rozwiązania, jak pokazano poniżej:

    ![System SAP CAL Basic: Create instance](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Kliknij pozycję **Utwórz**. Po pewnym czasie, w zależności od rozmiaru i złożoności rozwiązania (system SAP CAL zawiera oszacowanie), stan jest pokazywany jako aktywny i gotowy do użycia: 

    ![Wystąpienia SAP CAL](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Aby znaleźć grupę zasobów i wszystkie obiekty, które zostały utworzone przez SAP CAL, przejdź do Azure Portal. Maszynę wirtualną można znaleźć, rozpoczynając od tej samej nazwy wystąpienia, która została dostarczona w licencji SAP CAL.

    ![Obiekty grupy zasobów](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. W portalu SAP CAL przejdź do wdrożonych wystąpień, a następnie kliknij przycisk **Połącz**. Zostanie wyświetlone następujące okno podręczne: 

    ![Połącz z wystąpieniem](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Aby można było użyć jednej z opcji nawiązywania połączenia ze wdrożonymi systemami, kliknij opcję **przewodnik wprowadzenie**. Dokumentacja nazywa użytkowników dla każdej z metod łączności. Hasła dla tych użytkowników są ustawiane na hasło główne zdefiniowane na początku procesu wdrażania. W dokumentacji znajdują się inni użytkownicy o większej funkcjonalności z hasłami, których można użyć do zalogowania się do wdrożonego systemu.

    ![Dokumentacja powitalna oprogramowania SAP](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

W ciągu kilku godzin kondycja systemu SAP środowisk IDE jest wdrażana na platformie Azure.

W przypadku zakupu subskrypcji SAP CAL system SAP w pełni obsługuje wdrożenia za pomocą SAP CAL na platformie Azure. Kolejka pomocy technicznej to BC-VCM-CAL.

