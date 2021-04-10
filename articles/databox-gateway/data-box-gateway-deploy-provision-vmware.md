---
title: Samouczek dotyczący aprowizacji usługi Azure Data Box Gateway w programie VMware | Microsoft Docs
description: W drugim samouczku dotyczącym wdrażania usługi Azure Data Box Gateway omówiono aprowizację urządzenia wirtualnego w programie VMware.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 948005942be193c8b15c363282aa06c006eb244e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727621"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-vmware"></a>Samouczek: aprowizowanie usługi Azure Data Box Gateway w oprogramowaniu VMware

## <a name="overview"></a>Omówienie

W tym samouczku opisano sposób aprowizacji usługi Data Box Gateway w systemie hosta, w którym działa program VMware ESXi 6.0, 6.5 lub 6.7. 

Do aprowizowania urządzenia wirtualnego i nawiązania z nim połączenia wymagane są uprawnienia administratora. Aprowizacja i wstępna konfiguracja może zająć około 10 minut. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie, czy host spełnia minimalne wymagania dotyczące urządzenia
> * Aprowizowanie urządzenia wirtualnego w programie VMware
> * Uruchamianie urządzenia wirtualnego i uzyskiwanie adresu IP

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne, które muszą być spełnione, aby można było aprowizować urządzenie wirtualne na systemie hosta z programem VMware ESXi 6.0, 6.5 lub 6.7 są następujące.

### <a name="for-the-data-box-gateway-resource"></a>Zasób usługi Data Box Gateway

Przed rozpoczęciem upewnij się, że:

* Zostały wykonane wszystkie kroki samouczka [Prepare the portal for Data Box Gateway (Przygotowywanie portalu pod kątem usługi Data Box Gateway)](data-box-gateway-deploy-prep.md).
* Pobrano obraz urządzenia wirtualnego dla programu VMware z witryny Azure Portal zgodnie z opisem w samouczku [Prepare the portal for Data Box Gateway (Przygotowywanie portalu pod kątem usługi Data Box Gateway)](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > Oprogramowania uruchomionego w usłudze Data Box Gateway można używać tylko z zasobem usługi Data Box Gateway.

### <a name="for-the-data-box-gateway-virtual-device"></a>Urządzenie wirtualne usługi Data Box Gateway

Przed wdrożeniem urządzenia wirtualnego upewnij się, że są spełnione następujące warunki:

* Masz dostęp do systemu hosta z programem VMware (ESXi 6.0, 6.5 lub 6.7), którego można użyć do aprowizacji urządzenia.
* System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby aprowizacji urządzenia wirtualnego:

  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Zdecydowanie zalecamy co najmniej 16 GB pamięci RAM.
  * Jeden interfejs sieciowy.
  * Dysk systemu operacyjnego o rozmiarze 250 GB.
  * Dysk wirtualny o rozmiarze 2 TB do przechowywania danych systemu.

### <a name="for-the-network-in-datacenter"></a>Sieć w centrum danych

Przed rozpoczęciem:

- Zapoznaj się z wymaganiami dotyczącymi przygotowania sieci pod kątem wdrożenia usługi Data Box Gateway i skonfiguruj sieć w centrum danych zgodnie z tymi wymaganiami. Aby uzyskać więcej informacji, zobacz [Data Box Gateway networking requirements (Wymagania dotyczące sieci dla usługi Data Box Gateway)](data-box-gateway-system-requirements.md#networking-port-requirements).
- Aby umożliwić optymalne działanie urządzenia, przepustowość połączenia internetowego musi wynosić co najmniej 20 Mb/s.

## <a name="check-the-host-system"></a>Sprawdzanie systemu hosta

Do utworzenia urządzenia wirtualnego potrzebne są następujące elementy:

* Dostęp do systemu hosta z programem VMware ESXi Server 6.0, 6.5 lub 6.7. System hosta może przeznaczyć następujące zasoby wyłącznie na potrzeby urządzenia wirtualnego:
 
  * Co najmniej 4 procesory wirtualne.
  * Co najmniej 8 GB pamięci RAM. 
  * Jeden interfejs sieciowy połączony z siecią umożliwiającą kierowanie ruchu do Internetu.
  * Dysk systemu operacyjnego o rozmiarze 250 GB.
  * Dysk wirtualny o rozmiarze 2 TB do przechowywania danych.
* Klient VMware vSphere w systemie na potrzeby zarządzania hostem ESXi.


## <a name="provision-a-virtual-device-in-hypervisor"></a>Aprowizowanie urządzenia wirtualnego w funkcji hypervisor

Wykonaj następujące czynności, aby aprowizować urządzenie wirtualne w funkcji hypervisor.

1. Skopiuj obraz urządzenia wirtualnego do swojego systemu. Ten obraz urządzenia wirtualnego (dwa pliki) został pobrany za pośrednictwem witryny Azure Portal. Zanotuj lokalizację, do której został skopiowany obraz, ponieważ będzie on używany w dalszej części tej procedury.

2. Zaloguj się na serwer ESXi za pomocą przeglądarki pod tym adresem URL: `https://<IP address of the ESXi server>`. Musisz mieć uprawnienia administratora, aby utworzyć maszynę wirtualną.

   ![Strona logowania](./media/data-box-gateway-deploy-provision-vmware/image1.png)
  
3. Przekaż dysk VMDK na serwer ESXi. W okienku Navigator (Nawigator) wybierz pozycję **Storage** (Magazyn).

   ![Zrzut ekranu strony w witrynie serwera ESXi, który pokazuje okienko Nawigator z wybraną opcją magazyn.](./media/data-box-gateway-deploy-provision-vmware/image2.png)

4. W okienku po prawej stronie, w obszarze **Datastores** (Magazyny danych), wybierz magazyn danych, do którego chcesz przekazać dysk VMDK. 

    - Magazyn danych musi być typu VMFS5. 
    - Magazyn danych musi również mieć wystarczającą ilość wolnego miejsca dla dysków systemu operacyjnego i danych.
   
5. Kliknij prawym przyciskiem myszy i wybierz polecenie **Browse Datastore** (Przeglądaj magazyn danych).

   ![Przeglądaj magazyn danych](./media/data-box-gateway-deploy-provision-vmware/image3.png)

6. Zostanie wyświetlone okno **Datastore Browser** (Przeglądarka magazynu danych).

   ![Przeglądarka magazynu danych](./media/data-box-gateway-deploy-provision-vmware/image4.png)

7. Na pasku narzędzi kliknij ikonę **Create directory** (Utwórz katalog), aby utworzyć nowy folder. Określ nazwę folderu i zanotuj ją. Ta nazwa folderu będzie używana później, podczas tworzenia maszyny wirtualnej (zalecane najlepsze rozwiązanie). Kliknij pozycję **Create directory** (Utwórz katalog).

   ![Tworzenie katalogu](./media/data-box-gateway-deploy-provision-vmware/image5.png)

8. Nowy folder zostanie wyświetlony w okienku po lewej stronie okna **Datastore Browser** (Przeglądarka magazynu danych). Kliknij ikonę **Upload** (Przekaż), a następnie wybierz pozycję **Upload File** (Przekaż plik).

    ![Przekazywanie pliku](./media/data-box-gateway-deploy-provision-vmware/image6.png)

9. Wskaż pobrane pliki VMDK. Pliki są dwa. Wybierz plik do przekazania.

    ![Wybierz plik do przekazania](./media/data-box-gateway-deploy-provision-vmware/image7.png)

10. Kliknij przycisk **Otwórz**. Rozpocznie się przekazywanie pliku VMDK do określonego magazynu danych. Przekazanie pliku może zająć kilka minut.
11. Po zakończeniu przekazywania plik będzie widoczny w magazynie danych w utworzonym folderze. Teraz przekaż drugi plik VMDK do tego samego magazynu danych. Po przekazaniu obu plików zostaną one scalone w jeden plik. Wtedy w katalogu będzie widoczny pojedynczy plik.

    ![Dwa pliki VMDK są scalane w jeden plik](./media/data-box-gateway-deploy-provision-vmware/image8.png)

12. Wróć do okna klienta vSphere. W okienku Navigator (Nawigator) wybierz pozycję **Virtual Machines** (Maszyny wirtualne). W okienku po prawej stronie kliknij pozycję **Create/Register VM** (Utwórz/zarejestruj maszynę wirtualną).

    ![Utwórz lub Zarejestruj maszynę wirtualną](./media/data-box-gateway-deploy-provision-vmware/image9.png)

13. Zostanie wyświetlone okno **New Virtual Machine** (Nowa maszyna wirtualna). W obszarze Select creation type (Wybór typu tworzenia) wybierz pozycję **Create a new virtual machine** (Utwórz nową maszynę wirtualną) i kliknij przycisk **Next** (Dalej).
    ![Wybieranie strony typu tworzenia](./media/data-box-gateway-deploy-provision-vmware/image10.png)

14. Na stronie **Select a Name and OS Name and Location** (Wybór nazwy, nazwy systemu operacyjnego i lokalizacji) określ nazwę maszyny wirtualnej w polu **Name** (Nazwa). Ta nazwa powinna odpowiadać nazwie folderu (zalecane najlepsze rozwiązanie), którą podano wcześniej w kroku 7. W polu **Guest OS family** (Rodzina systemu operacyjnego gościa) wybierz pozycję Windows, a w polu **Guest OS version** (Wersja systemu operacyjnego gościa) — Microsoft Windows Server 2016 (64-bit) (Microsoft Windows Server 2016 [64-bitowy]). Kliknij przycisk **Dalej**.

    ![Wybierz nazwę i nazwę systemu operacyjnego oraz stronę lokalizacji](./media/data-box-gateway-deploy-provision-vmware/image11.png)

15. Na stronie **Select storage** (Wybór magazynu) wybierz magazyn danych, którego chcesz użyć do aprowizowania maszyny wirtualnej. Kliknij przycisk **Dalej**.

    ![Wybierz stronę magazynu](./media/data-box-gateway-deploy-provision-vmware/image12.png)
16. Na stronie **Customize settings** (Dostosowywanie ustawień) ustaw wartość **CPU** (Procesor CPU) na 4, **Memory** (Pamięć) na co najmniej 8192 MB, a **Hard disk 1** (Dysk twardy 1) na co najmniej 2 TB. Wybierz **dysk twardy SCSI** do dodania. W tym przypadku był to typ LSI Logic SAS. **Statyczne dyski IDE nie są obsługiwane.** **Hard disk 1** (Dysk twardy 1) jest wirtualnym dyskiem danych. Pamiętaj, że po aprowizowaniu dysku nie można zmniejszyć jego rozmiaru. Próba zmniejszenia dysku spowoduje utratę wszystkich danych lokalnych na urządzeniu. 

    ![Dostosuj stronę ustawień](./media/data-box-gateway-deploy-provision-vmware/image13.png)

    Na tej samej stronie kliknij pozycję **Add hard disk** (Dodaj dysk twardy), a następnie wybierz pozycję **Existing hard disk** (Istniejący dysk twardy). Wybierz plik VMDK w magazynie danych. Spowoduje to dodanie dysku systemu operacyjnego. 

     ![Dostosowywanie ustawień — Dodawanie dysku twardego](./media/data-box-gateway-deploy-provision-vmware/customize-settings-add-hard-disk.png)

    Przewiń w dół, aż zobaczysz **nowy dysk twardy** i rozwiń go, aby wyświetlić ustawienia. Ustaw opcję **Virtual Device Node** (Węzeł urządzenia wirtualnego) na wartość **IDE controller 0** (Kontroler IDE 0).

     ![Dostosowywanie ustawień — Konfigurowanie nowego dysku twardego](./media/data-box-gateway-deploy-provision-vmware/customize-settings-new-disk.png)

17. (Opcjonalnie) *Wykonaj ten krok tylko wtedy, jeśli korzystasz z oprogramowania VMware ESXi Server 6.7*. Na stronie **Customize settings** (Dostosowywanie ustawień) kliknij pozycję **VM options** (Opcje maszyny wirtualnej). Wybierz pozycję **Boot options > Firmware** (Opcje rozruchu > Oprogramowanie układowe) i zmień wartość dla tej opcji na **BIOS**. Domyślnie ustawiona wartość to EFI. Kliknij przycisk **Dalej**.

    ![Dostosuj stronę ustawień w przypadku uruchamiania VMware ESXi Server 6,7](./media/data-box-gateway-deploy-provision-vmware/customize-settings-new-disk-esxi.png)

18. Na stronie **Ready to Complete** (Gotowe do ukończenia) przejrzyj wszystkie ustawienia skojarzone z nową maszyną wirtualną. Sprawdź, czy ustawienie procesora CPU ma wartość 4, pamięci — 8192 MB, a interfejsu sieciowego — 1 oraz czy dysk twardy 2 ma wybrany kontroler IDE 0. Kliknij przycisk **Finish** (Zakończ).
   
    ![Gotowe do ukończenia strony ](./media/data-box-gateway-deploy-provision-vmware/image16.png)
     ![ gotowej do ukończenia strony 2](./media/data-box-gateway-deploy-provision-vmware/image17.png)

Maszyna wirtualna jest teraz aprowizowana. Zostanie wyświetlone odpowiednie powiadomienie, a nowa maszyna wirtualna zostanie dodana do listy maszyn wirtualnych.

![Dodano nową maszynę wirtualną do listy maszyn wirtualnych](./media/data-box-gateway-deploy-provision-vmware/image17.png)

Następnym krokiem jest włączenie tej maszyny wirtualnej i uzyskanie adresu IP.

> [!NOTE]
> Nie zalecamy instalowania narzędzi VMware na urządzeniu wirtualnym (aprowizowanym zgodnie z powyższymi informacjami). Zainstalowanie narzędzi VMware spowoduje powstanie nieobsługiwanej konfiguracji.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Uruchamianie urządzenia wirtualnego i uzyskiwanie adresu IP

Wykonaj poniższe kroki, aby uruchomić urządzenie wirtualne i nawiązać z nim połączenie.

#### <a name="to-start-the-virtual-device"></a>Aby uruchomić urządzenie wirtualne
1. Uruchom urządzenie wirtualne. W okienku po prawej stronie wybierz urządzenie z listy maszyn wirtualnych, a następnie kliknij prawym przyciskiem myszy, aby wyświetlić menu kontekstowe. Wybierz pozycję **Power** (Zasilanie), a następnie pozycję **Power on** (Włącz). Maszyna wirtualna powinna zostać włączona. Stan możesz zobaczyć w dolnym okienku klienta internetowego.

    ![Włącz urządzenie wirtualne](./media/data-box-gateway-deploy-provision-vmware/image19.png)

2. Ponownie wybierz maszynę wirtualną. Kliknij prawym przyciskiem myszy i wybierz polecenie **Console** (Konsola), a następnie **Open in a new window** (Otwórz w nowym oknie).

    ![Otwórz konsolę urządzenia wirtualnego](./media/data-box-gateway-deploy-provision-vmware/image20.png)

3. Konsola maszyny wirtualnej zostanie otwarta w nowym oknie. 

    ![Konsola urządzenia wirtualnego](./media/data-box-gateway-deploy-provision-vmware/image21.png)

4. Po uruchomieniu urządzenia wskaż i kliknij kartę w górnej, środkowej części okna konsoli. Wybierz pozycję **Guest OS > Send keys > Ctrl+Alt+Delete** (System operacyjny gościa > Wyślij klawisze > Ctrl+Alt+Delete). Spowoduje to odblokowanie maszyny wirtualnej.

   ![Odblokuj urządzenie wirtualne](./media/data-box-gateway-deploy-provision-vmware/image22.png)

5. Podaj hasło, aby zalogować się do maszyny. Domyślne hasło to *Password1*.

   ![Wprowadź hasło urządzenia wirtualnego](./media/data-box-gateway-deploy-provision-vmware/image23.png)

6. Kroki od 6 do 8 mają zastosowanie tylko w przypadku uruchamiania w środowisku bez protokołu DHCP. W przypadku środowiska protokołu DHCP należy pominąć te kroki i przejść do kroku 9. Jeśli urządzenie zostało uruchomione w środowisku bez protokołu DHCP, zostanie wyświetlony komunikat podobny do następującego: **Use the Set-HcsIPAddress cmdlet to configure the network** (Skonfiguruj sieć za pomocą polecenia cmdlet Set-HcsIPAddress). 
   
7. Aby skonfigurować sieć, w wierszu polecenia wydaj polecenie `Get-HcsIpAddress` w celu wyświetlenia listy interfejsów sieciowych włączonych na urządzeniu wirtualnym. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

8. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Przykład przedstawiono poniżej:

    `Set-HcsIpAddress –Name Ethernet0 –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

9. Po zakończeniu początkowej konfiguracji i uruchomieniu urządzenia zobaczysz tekst baneru urządzenia. Zanotuj adresy IP i URL wyświetlane w tekście baneru. Posłużą one do zarządzania urządzeniem. Za pomocą tego adresu IP nawiążesz połączenie z internetowym interfejsem użytkownika urządzenia wirtualnego oraz dokończysz lokalną konfigurację i aktywację urządzenia.

   ![Tekst baneru i adres URL połączenia dla urządzenia wirtualnego](./media/data-box-gateway-deploy-provision-vmware/image24.png)

Jeśli urządzenie nie spełnia minimalnych wymagań dotyczących konfiguracji, w tekście baneru zostanie wyświetlony błąd (widoczny poniżej). Trzeba zmodyfikować konfigurację urządzenia tak, aby zapewnić zasoby spełniające minimalne wymagania. Następnie możesz ponownie uruchomić urządzenie i połączyć się z nim. Minimalne wymagania konfiguracji opisano w sekcji [Sprawdzanie, czy system hosta spełnia minimalne wymagania dotyczące urządzenia wirtualnego](#check-the-host-system).

Jeśli wystąpi jakikolwiek inny błąd podczas początkowej konfiguracji przy użyciu lokalnego interfejsu użytkownika sieci Web, zapoznaj się z następującymi przepływami pracy:

- [Uruchom testy diagnostyczne, aby rozwiązać problemy z konfiguracją interfejsu użytkownika sieci Web](data-box-gateway-troubleshoot.md#run-diagnostics).
- [Generuj pakiet dzienników i Wyświetl pliki dziennika](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Gateway, takie jak:

> [!div class="checklist"]
> * Sprawdzanie, czy host spełnia minimalne wymagania dotyczące urządzenia
> * Aprowizowanie urządzenia wirtualnego w programie VMware
> * Uruchamianie urządzenia wirtualnego i uzyskiwanie adresu IP

Przejdź do następnego samouczka, aby dowiedzieć się, jak nawiązać połączenie z urządzeniem wirtualnym oraz skonfigurować i aktywować je.

* [Set up and connect to shares on your Data Box Gateway (Konfigurowanie udziałów w usłudze Data Box Gateway i łączenie się z nimi)](data-box-gateway-deploy-connect-setup-activate.md)

