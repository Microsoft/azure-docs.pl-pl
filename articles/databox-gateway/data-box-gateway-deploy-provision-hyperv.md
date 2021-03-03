---
title: Samouczek dotyczący aprowizacji usługi Azure Data Box Gateway w funkcji Hyper-V | Microsoft Docs
description: W drugim samouczku dotyczącym wdrażania usługi Azure Data Box Gateway omówiono aprowizację urządzenia wirtualnego w funkcji Hyper-V.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 2e54b8e58e8139c46639187957906790ffb0fe54
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727689"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v"></a>Samouczek: Inicjowanie obsługi Azure Data Box Gateway w funkcji Hyper-V

## <a name="overview"></a>Omówienie

W tym samouczku przedstawiono sposób aprowizowania usługi Azure Data Box Gateway na hoście funkcji Hyper-V z systemem Windows Server 2016, Windows Server 2012 R2 lub Windows Server 2012.

Do aprowizowania i skonfigurowania urządzenia wirtualnego wymagane są uprawnienia administratora. Aprowizacja i wstępna konfiguracja może zająć około 10 minut. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Sprawdzanie, czy host spełnia minimalne wymagania dotyczące urządzenia
> * Aprowizowanie urządzenia wirtualnego w funkcji hypervisor
> * Uruchamianie urządzenia wirtualnego i uzyskiwanie adresu IP

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Przed aprowizowaniem urządzenia wirtualnego na hoście funkcji Hyper-V z systemem Windows Server 2016 lub Windows Server 2012 R2 muszą zostać spełnione następujące wymagania.

### <a name="for-the-data-box-gateway-resource"></a>Zasób usługi Data Box Gateway

Przed rozpoczęciem upewnij się, że:

* Zostały wykonane wszystkie kroki samouczka [Prepare the portal for Data Box Gateway (Przygotowywanie portalu pod kątem usługi Data Box Gateway)](data-box-gateway-deploy-prep.md).
* Pobrano obraz urządzenia wirtualnego dla funkcji Hyper-V z witryny Azure Portal zgodnie z opisem w samouczku [Prepare the portal for Data Box Gateway (Przygotowywanie portalu pod kątem usługi Data Box Gateway)](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > Oprogramowania uruchomionego w usłudze Data Box Gateway można używać tylko z zasobem usługi Data Box Gateway.

### <a name="for-the-data-box-gateway-virtual-device"></a>Urządzenie wirtualne usługi Data Box Gateway

Przed wdrożeniem urządzenia upewnij się, że:

* Masz dostęp do hosta funkcji Hyper-V z systemem Windows Server 2012 R2 lub nowszym, którego można użyć do aprowizacji urządzenia.
* System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby aprowizacji urządzenia wirtualnego:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Zdecydowanie zalecamy co najmniej 16 GB pamięci RAM.
  * Jeden interfejs sieciowy.
  * Dysk systemu operacyjnego 250 GB.
  * Dysk wirtualny o pojemności 2 TB dla danych.

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych

Przed rozpoczęciem:

* Zapoznaj się z wymaganiami dotyczącymi przygotowania sieci pod kątem wdrożenia usługi Data Box Gateway i skonfiguruj sieć w centrum danych zgodnie z tymi wymaganiami. Aby uzyskać więcej informacji, zobacz [Data Box Gateway networking requirements (Wymagania dotyczące sieci dla usługi Data Box Gateway)](data-box-gateway-system-requirements.md#networking-port-requirements).
* Aby umożliwić optymalne działanie urządzenia, przepustowość połączenia internetowego musi wynosić co najmniej 20 Mb/s.

## <a name="check-the-host-system"></a>Sprawdzanie systemu hosta

Do utworzenia urządzenia wirtualnego potrzebne są następujące elementy:

* Rola funkcji Hyper-V zainstalowana w systemie Windows Server 2016, Windows Server 2012 R2 lub Windows Server 2012.
* Menedżer funkcji Microsoft Hyper-V na kliencie z systemem Microsoft Windows połączonym z hostem.
* Upewnij się, że sprzęt (host), na którym tworzysz urządzenie wirtualne, może przeznaczyć następujące zasoby wyłącznie na potrzeby urządzenia wirtualnego:

  * Co najmniej 4 procesory wirtualne.
  * Co najmniej 8 GB pamięci RAM.
  * Jeden interfejs sieciowy połączony z siecią umożliwiającą kierowanie ruchu do Internetu.
  * Dysk systemu operacyjnego 250 GB.
  * Dysk wirtualny o pojemności 2 TB dla danych systemu.

## <a name="bitlocker-considerations"></a>Uwagi dotyczące funkcji BitLocker

* Zalecamy włączenie funkcji BitLocker na Data Box Gateway maszynie wirtualnej. Domyślnie funkcja BitLocker nie jest włączona. Aby uzyskać więcej informacji, zobacz:
  * [Ustawienia szyfrowania pomocy technicznej w Menedżerze funkcji Hyper-V](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
  * [Obsługa funkcji BitLocker na maszynie wirtualnej](https://kb.vmware.com/s/article/2036142)

## <a name="provision-a-virtual-device-in-hypervisor"></a>Aprowizowanie urządzenia wirtualnego w funkcji hypervisor

Wykonaj następujące czynności, aby aprowizować urządzenie w funkcji hypervisor.

1. Na hoście z systemem Windows Server skopiuj obraz urządzenia wirtualnego na dysk lokalny. Możesz pobrać ten obraz w formacie VHDX z witryny Azure Portal. Zanotuj lokalizację, do której został skopiowany obraz, ponieważ będzie on używany w dalszej części tej procedury.

2. Otwórz **Menedżera serwera**. W prawym górnym rogu kliknij pozycję **Narzędzia** , a następnie wybierz pozycję **Menedżer funkcji Hyper-V**.

    ![Wybierz Menedżera funkcji Hyper-V w Menedżer serwera](./media/data-box-gateway-deploy-provision-hyperv/image1.png)

3. W **Menedżerze funkcji Hyper-V** w okienku zakresu kliknij prawym przyciskiem myszy węzeł systemu, aby otworzyć menu kontekstowe, a następnie kliknij kolejno pozycje **Nowy** > **Maszyna wirtualna**.

   ![Utwórz nową maszynę wirtualną w Menedżerze funkcji Hyper-V](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. Na stronie **Przed rozpoczęciem** w kreatorze nowej maszyny wirtualnej kliknij przycisk **Dalej**.
5. Na stronie **Określanie nazwy i lokalizacji** wprowadź **nazwę** urządzenia wirtualnego. Kliknij przycisk **Dalej**.

   ![Określ nazwę i stronę lokalizacji](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. Na stronie **Określanie generacji** wybierz pozycję **Generacja 2** jako typ obrazu urządzenia w formacie vhdx, a następnie kliknij przycisk **Dalej**.    

   ![Strona określ generację](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. Na stronie **Przypisywanie pamięci** w polu **Pamięć początkowa** wprowadź wartość nie mniejszą niż **8192 MB**, nie włączaj pamięci dynamicznej, a następnie kliknij przycisk **Dalej**.

   ![Strona przypisywanie pamięci](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. Na stronie **Konfigurowanie sieci** określ przełącznik wirtualny połączony z Internetem, a następnie kliknij przycisk **Dalej**.

   ![Strona Konfigurowanie sieci](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. Na stronie **Podłączanie wirtualnego dysku twardego** wybierz pozycję **Użyj istniejącego wirtualnego dysku twardego**, podaj lokalizację obrazu urządzenia wirtualnego, a następnie kliknij przycisk **Dalej**.

   ![Strona łączenie wirtualnego dysku twardego](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. Przejrzyj informacje na stronie **Podsumowanie**, a następnie kliknij pozycję **Zakończ**, aby utworzyć maszynę wirtualną.

    ![Ukończ pracę z kreatorem nowej maszyny wirtualnej](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. Aby spełnić minimalne wymagania, potrzebne są cztery procesory wirtualne. Aby dodać cztery procesory wirtualne, wybierz system hosta w oknie **Menedżera funkcji Hyper-V** . Na liście **Maszyny wirtualne** w okienku po prawej stronie znajdź utworzoną przed chwilą maszynę wirtualną. Zaznacz i kliknij prawym przyciskiem myszy nazwę maszyny, a następnie wybierz pozycję **Ustawienia**.

    ![Ustawienia maszyny wirtualnej](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. Na stronie **Ustawienia** w lewym okienku kliknij pozycję **Procesor**. W okienku po prawej stronie ustaw wartość 4 lub większą w polu **Liczba procesorów wirtualnych**. Kliknij pozycję **Zastosuj**.

    ![Ustawianie liczby procesorów wirtualnych na stronie ustawień](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. Aby spełnić wymagania minimalne, należy również dodać dysk z danymi wirtualnymi o pojemności 2 TB. Na stronie **Ustawienia**:

    1. W okienku po lewej stronie wybierz pozycję **Kontroler SCSI**.
    2. W okienku po prawej stronie wybierz pozycję **Dysk twardy** i kliknij przycisk **Dodaj**.

    ![Dodaj dysk twardy na stronie ustawień](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. Na stronie **Dysk twardy** wybierz opcję **Wirtualny dysk twardy**, a następnie kliknij przycisk **Nowy**. Zostanie uruchomiony **Kreator nowego wirtualnego dysku twardego**.

    ![Kreator nowego wirtualnego dysku twardego](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
15. Na stronie **Przed rozpoczęciem** w kreatorze nowego wirtualnego dysku twardego kliknij przycisk **Dalej**.
16. Na stronie **Wybieranie formatu dysku** zaakceptuj domyślną opcję formatu **VHDX**. Kliknij przycisk **Dalej**.
17. Na stronie **Wybieranie typu dysku** ustaw typ wirtualnego dysku twardego **Dynamicznie powiększający się** (zalecane). Dysk **O stałym rozmiarze** również byłby odpowiedni, ale jego tworzenie mogłoby zająć więcej czasu. Nie zalecamy używania opcji **Różnicowy**. Kliknij przycisk **Dalej**.

    ![Strona wybierania typu dysku](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. Na stronie **Określanie nazwy i lokalizacji** Podaj **nazwę** i **lokalizację** (możesz przejść do jednej z nich) dla dysku danych. Kliknij przycisk **Dalej**.

    ![Określ nazwę i stronę lokalizacji](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. Na stronie **Konfigurowanie dysku** wybierz opcję **Utwórz nowy pusty dysk twardy** , a następnie określ rozmiar na **2 TB** (lub więcej).

    2 TB to wymaganie minimalne — zawsze można aprowizować większy dysk. Należy pamiętać, że nie można zmniejszyć dysku po zainicjowaniu obsługi administracyjnej. Próba zmniejszenia dysku spowoduje utratę wszystkich danych lokalnych na urządzeniu. Rozszerzanie dysku danych nie jest obsługiwane. Kliknij przycisk **Dalej**.

    ![Strona Konfigurowanie dysku](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. Sprawdź dane dysku wirtualnego na stronie **Podsumowanie** i, jeśli wszystkie dane są prawidłowe, kliknij przycisk **Zakończ**, aby utworzyć dysk. Kreator zostanie zamknięty, a wirtualny dysk twardy zostanie dodany do maszyny.

    ![Na stronie Kreatora nowego wirtualnego dysku twardego](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. Wróć do strony **Ustawienia**. Kliknij przycisk **OK**, aby zamknąć stronę **Ustawienia**, a następnie wróć do okna Menedżera funkcji Hyper-V.

    ![Strona Ustawienia](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

Maszyna wirtualna jest teraz w pełni skonfigurowana.

> [!NOTE]
> Nie można zainicjować obsługi administracyjnej nowego Data Box Gateway przez skopiowanie skonfigurowanego wirtualnego dysku twardego. Każde nowe Data Box Gateway urządzenie wirtualne musi być inicjowane z obrazu urządzenia wirtualnego dla funkcji Hyper-V pobranej z Azure Portal.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Uruchamianie urządzenia wirtualnego i uzyskiwanie adresu IP

Wykonaj poniższe kroki, aby uruchomić urządzenie wirtualne i nawiązać z nim połączenie.

#### <a name="to-start-the-virtual-device"></a>Aby uruchomić urządzenie wirtualne

1. Uruchom urządzenie wirtualne.

   ![Uruchom urządzenie wirtualne](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. Po uruchomieniu urządzenia wybierz je, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Połącz**.

3. Przygotowanie urządzenia może potrwać około 10–15 minut. W konsoli zostanie wyświetlony komunikat o stanie, wskazujący postęp procesu. Gdy urządzenie będzie gotowe, przejdź do pozycji **Akcja**. Naciśnij klawisz `Ctrl + Alt + Delete` , aby zalogować się do urządzenia wirtualnego. Domyślna nazwa użytkownika to *EdgeUser*, a domyślne hasło to *Password1*.

   ![Zaloguj się do urządzenia wirtualnego](./media/data-box-gateway-deploy-provision-hyperv/image21.png)

4. Kroki od 5 do 7 mają zastosowanie tylko w przypadku uruchamiania w środowisku bez protokołu DHCP. W przypadku środowiska z protokołem DHCP pomiń te kroki. Jeśli urządzenie zostało uruchomione w środowisku bez protokołu DHCP, zostanie wyświetlony komunikat z taką informacją.

5. Aby skonfigurować sieć, użyj polecenia `Get-HcsIpAddress` do wyświetlenia listy interfejsów sieciowych włączonych na urządzeniu wirtualnym. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

6. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Zobacz poniższy przykład:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

7. Po zakończeniu początkowej konfiguracji i uruchomieniu urządzenia zobaczysz tekst baneru urządzenia. Zanotuj adresy IP i URL wyświetlane w tekście baneru. Posłużą one do zarządzania urządzeniem. Użyj tego adresu IP do nawiązania połączenia z internetowym interfejsem użytkownika urządzenia wirtualnego i dokończ lokalną konfigurację i aktywację urządzenia.

   ![Transparent urządzenia wirtualnego z adresem IP i adresem URL połączenia](./media/data-box-gateway-deploy-provision-hyperv/image23.png)

Jeśli urządzenie nie spełnia minimalnych wymagań dotyczących konfiguracji, w tekście transparentu zostanie wyświetlony błąd. Zmodyfikuj konfigurację urządzenia tak, aby zapewnić zasoby zgodne z wymaganiami minimalnymi. Następnie możesz ponownie uruchomić urządzenie i połączyć się z nim. Zapoznaj się z minimalnymi wymaganiami dotyczącymi konfiguracji w obszarze [Sprawdzanie, czy system hosta spełnia minimalne wymagania dotyczące urządzeń wirtualnych](#check-the-host-system).

Jeśli wystąpi jakikolwiek inny błąd podczas początkowej konfiguracji przy użyciu lokalnego interfejsu użytkownika sieci Web, zapoznaj się z następującymi przepływami pracy:

* [Uruchom testy diagnostyczne, aby rozwiązać problemy z konfiguracją interfejsu użytkownika sieci Web](data-box-gateway-troubleshoot.md#run-diagnostics).
* [Generuj pakiet dzienników i Wyświetl pliki dziennika](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Gateway, takie jak:

> [!div class="checklist"]
> * Sprawdzanie, czy host spełnia minimalne wymagania dotyczące urządzenia
> * Aprowizowanie urządzenia wirtualnego w funkcji hypervisor
> * Uruchamianie urządzenia wirtualnego i uzyskiwanie adresu IP

Przejdź do następnego samouczka, aby dowiedzieć się, jak nawiązać połączenie z urządzeniem wirtualnym oraz skonfigurować i aktywować je.

> [!div class="nextstepaction"]
> [Connect and set up your Data Box Gateway (Nawiązywanie połączenia z usługą Data Box Gateway i konfigurowanie jej)](./data-box-gateway-deploy-connect-setup-activate.md)
