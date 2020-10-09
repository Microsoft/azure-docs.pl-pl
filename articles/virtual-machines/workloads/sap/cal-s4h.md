---
title: Wdrażanie oprogramowania SAP S/4HANA lub BW/4HANA na maszynie wirtualnej platformy Azure | Microsoft Docs
description: Wdrażanie oprogramowania SAP S/4HANA lub BW/4HANA na maszynie wirtualnej platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: c110a4e0429ba52e01c472097a2241f91d504cf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77616205"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Wdrażanie oprogramowania SAP S/4HANA lub BW/4HANA na platformie Azure
W tym artykule opisano sposób wdrażania usługi S/4HANA na platformie Azure przy użyciu biblioteki urządzeń SAP Cloud (SAP CAL) 3,0. W celu wdrożenia innych rozwiązań opartych na SAP HANA, takich jak BW/4HANA, wykonaj te same czynności.

> [!NOTE]
> Aby uzyskać więcej informacji na temat platformy SAP CAL, przejdź do witryny sieci Web z [biblioteką urządzeń SAP Cloud](https://cal.sap.com/) . W oprogramowaniu SAP znajduje się również Blog dotyczący [biblioteki urządzenia SAP Cloud Library 3,0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> Od 29 maja 2017, można użyć modelu wdrażania Azure Resource Manager oprócz niepreferowanego klasycznego modelu wdrażania do wdrożenia SAP CAL. Zalecamy używanie nowego modelu wdrażania Menedżer zasobów i pomijanie klasycznego modelu wdrażania.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Proces krok po kroku dotyczący wdrażania rozwiązania

Poniższa sekwencja zrzutów ekranu pokazuje, jak wdrożyć usługę S/4HANA na platformie Azure przy użyciu licencji SAP CAL. Proces działa tak samo jak w przypadku innych rozwiązań, takich jak BW/4HANA.

Na stronie **rozwiązania** są wyświetlane niektóre rozwiązania SAP cal Hana dostępne na platformie Azure. **SAP S/4HANA 1610 FPS01, urządzenie Fully-Activated** znajduje się w środkowym wierszu:

![Rozwiązania SAP CAL](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Utwórz konto w licencji SAP CAL
1. Aby zalogować się do licencji SAP CAL po raz pierwszy, użyj użytkownika SAP S lub innego użytkownika zarejestrowanego w oprogramowaniu SAP. Następnie Zdefiniuj konto SAP CAL, które jest używane przez SAP CAL do wdrożenia urządzeń na platformie Azure. W definicji konta należy:

    a. Wybierz model wdrażania na platformie Azure (Menedżer zasobów lub klasyczny).

    b. Wprowadź subskrypcję platformy Azure. Konto SAP CAL może być przypisane tylko do jednej subskrypcji. Jeśli potrzebujesz więcej niż jednej subskrypcji, musisz utworzyć inne konto SAP CAL.

    c. Nadaj uprawnienia SAP CAL do wdrożenia w ramach subskrypcji platformy Azure.

   > [!NOTE]
   >  W następnych krokach pokazano, jak utworzyć konto SAP CAL dla wdrożeń Menedżer zasobów. Jeśli masz już konto SAP CAL, które jest połączone z klasycznym modelem wdrażania *, musisz wykonać* następujące kroki, aby utworzyć nowe konto SAP cal. Nowe konto SAP CAL musi zostać wdrożone w modelu Menedżer zasobów.

1. Utwórz nowe konto SAP CAL. Na stronie **konta** są wyświetlane trzy opcje platformy Azure: 

    a. **Microsoft Azure (klasyczny)** to klasyczny model wdrażania i nie jest już preferowany.

    b. **Microsoft Azure** jest nowym Menedżer zasobów modelem wdrażania.

    c. **Platforma Microsoft Azure obsługiwana przez firmę 21Vianet** jest opcją w Chinach, która korzysta z klasycznego modelu wdrażania.

    Aby wdrożyć w modelu Menedżer zasobów, wybierz pozycję **Microsoft Azure**.

    ![Szczegóły konta SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

1. Wprowadź **Identyfikator subskrypcji** platformy Azure, który można znaleźć na Azure Portal.

   ![Konta SAP CAL](./media/cal-s4h/s4h-pic3c.png)

1. Aby autoryzować licencję SAP CAL do wdrożenia w zdefiniowanej subskrypcji platformy Azure, kliknij przycisk **Autoryzuj**. Na karcie Przeglądarka zostanie wyświetlona następująca strona:

   ![Logowanie do usług w chmurze programu Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

1. Jeśli na liście znajduje się więcej niż jeden użytkownik, wybierz konto Microsoft, który jest powiązany z tym współadministratorem wybranej subskrypcji platformy Azure. Na karcie Przeglądarka zostanie wyświetlona następująca strona:

   ![Potwierdzenie usług w chmurze programu Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Kliknij przycisk **Zaakceptuj**. Jeśli autoryzacja zakończyła się pomyślnie, definicja konta SAP CAL zostanie wyświetlona ponownie. Po krótkim czasie komunikat potwierdza, że proces autoryzacji zakończył się pomyślnie.

1. Aby przypisać nowo utworzone konto SAP CAL do użytkownika, wprowadź swój **Identyfikator użytkownika** w polu tekstowym po prawej stronie, a następnie kliknij przycisk **Dodaj**.

   ![Skojarzenie konta z użytkownikiem](./media/cal-s4h/s4h-pic8a.png)

1. Aby skojarzyć konto z użytkownikiem, którego używasz do logowania się do platformy SAP CAL, kliknij przycisk **Przeglądaj**. 
 
1. Aby utworzyć skojarzenie między użytkownikiem i nowo utworzonym kontem SAP CAL, kliknij przycisk **Utwórz**.

   ![Skojarzenie użytkownika z kontem SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Pomyślnie utworzono konto SAP CAL, które jest w stanie:

- Użyj modelu wdrażania usługi Resource Manager.
- Wdróż systemy SAP w ramach subskrypcji platformy Azure.

Teraz możesz rozpocząć wdrażanie S/4HANA w ramach subskrypcji użytkownika na platformie Azure.

> [!NOTE]
> Przed kontynuowaniem Ustal, czy masz przydziały platformy Azure vCPU dla maszyn wirtualnych z serii H platformy Azure. W tej chwili licencja SAP CAL używa maszyn wirtualnych serii H platformy Azure do wdrażania niektórych rozwiązań opartych na SAP HANA. Twoja subskrypcja platformy Azure może nie mieć żadnych przydziałów vCPU serii H dla serii H. Jeśli tak, może być konieczne skontaktowanie się z pomocą techniczną platformy Azure w celu uzyskania przydziału co najmniej 16 procesorów wirtualnych vCPU serii H.
> 
> [!NOTE]
> Po wdrożeniu rozwiązania na platformie Azure w systemie SAP CAL może się okazać, że można wybrać tylko jeden region świadczenia usługi Azure. Aby wdrożyć w regionach platformy Azure innych niż sugerowane przez SAP CAL, należy zakupić subskrypcję z licencją CAL od platformy SAP. Może być również konieczne otwarcie komunikatu przy użyciu SAP, aby konto CAL mogło dostarczyć do regionów platformy Azure innych niż te, które zostały początkowo zasugerowane.

### <a name="deploy-a-solution"></a>Wdróż rozwiązanie

Wdróżmy rozwiązanie na stronie **rozwiązania** SAP cal. System SAP CAL ma dwie sekwencje do wdrożenia:

- Podstawowa sekwencja korzystająca z jednej strony do definiowania systemu do wdrożenia
- Zaawansowana sekwencja, która zapewnia pewne opcje dotyczące rozmiarów maszyn wirtualnych 

W tym miejscu zademonstrowano ścieżkę podstawową do wdrożenia.

1. Na stronie **szczegóły konta** należy:

    a. Wybierz konto SAP CAL. (Użyj konta, które jest skojarzone do wdrożenia z modelem wdrażania Menedżer zasobów).

    b. Wprowadź **nazwę**wystąpienia.

    c. Wybierz **region**platformy Azure. System SAP CAL sugeruje region. Jeśli potrzebujesz innego regionu platformy Azure i nie masz subskrypcji SAP CAL, musisz zamówić subskrypcję z licencją CAL przy użyciu oprogramowania SAP.

    d. Wprowadź **hasło** główne dla rozwiązania składającego się z ośmiu lub dziewięciu znaków. Hasło jest używane dla administratorów różnych składników programu.

   ![System SAP CAL Basic: Create instance](./media/cal-s4h/s4h-pic10a.png)

1. Kliknij przycisk **Utwórz**, a następnie w wyświetlonym oknie komunikatu kliknij przycisk **OK**.

   ![Obsługiwane rozmiary maszyn wirtualnych SAP CAL](./media/cal-s4h/s4h-pic10b.png)

1. W oknie dialogowym **klucz prywatny** kliknij pozycję **Zapisz** , aby zapisać klucz prywatny w licencji SAP cal. Aby użyć ochrony hasłem dla klucza prywatnego, kliknij pozycję **Pobierz**. 

   ![Klucz prywatny SAP CAL](./media/cal-s4h/s4h-pic10c.png)

1. Przeczytaj komunikat **ostrzegawczy** SAP cal, a następnie kliknij przycisk **OK**.

   ![Ostrzeżenie SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Wdrażanie odbywa się teraz. Po pewnym czasie, w zależności od rozmiaru i złożoności rozwiązania (system SAP CAL zawiera oszacowanie), stan jest pokazywany jako aktywny i gotowy do użycia.

1. Aby znaleźć maszyny wirtualne zebrane z innymi skojarzonymi zasobami w jednej grupie zasobów, przejdź do Azure Portal: 

   ![Obiekty SAP CAL wdrożone w nowym portalu](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. W portalu SAP CAL stan jest wyświetlany jako **aktywny**. Aby nawiązać połączenie z rozwiązaniem, kliknij przycisk **Połącz**. Różne opcje łączenia się z różnymi składnikami są wdrażane w ramach tego rozwiązania.

   ![Wystąpienia SAP CAL](./media/cal-s4h/active_solution.png)

1. Aby można było użyć jednej z opcji nawiązywania połączenia ze wdrożonymi systemami, kliknij opcję **przewodnik wprowadzenie**. 

   ![Połącz z wystąpieniem](./media/cal-s4h/connect_to_solution.png)

    Dokumentacja nazywa użytkowników dla każdej z metod łączności. Hasła dla tych użytkowników są ustawiane na hasło główne zdefiniowane na początku procesu wdrażania. W dokumentacji znajdują się inni użytkownicy o większej funkcjonalności z hasłami, których można użyć do zalogowania się do wdrożonego systemu. 

    Jeśli na przykład korzystasz z graficznego interfejsu użytkownika SAP, który jest wstępnie zainstalowany na komputerze z systemem Windows Pulpit zdalny, system S/4 może wyglądać następująco:

   ![SM50 w preinstalowanym graficznym interfejsie użytkownika SAP](./media/cal-s4h/gui_sm50.png)

    Lub jeśli używasz DBACockpit, wystąpienie może wyglądać następująco:

   ![SM50 w interfejsie GUI DBACockpit SAP](./media/cal-s4h/dbacockpit.png)

W ciągu kilku godzin prawidłowe urządzenie SAP S/4 zostanie wdrożone na platformie Azure.

W przypadku zakupu subskrypcji SAP CAL system SAP w pełni obsługuje wdrożenia za pomocą SAP CAL na platformie Azure. Kolejka pomocy technicznej to BC-VCM-CAL.







