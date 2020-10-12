---
title: Samouczek — Tworzenie kopii zapasowych maszyn wirtualnych z systemem Linux w Azure Portal
description: W tym samouczku dowiesz się, jak za pomocą witryny Azure Portal chronić maszyny wirtualne z systemem Linux przy użyciu usługi Azure Backup.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 08e6491e1d8d94e8e6e9112e5a19682018103325
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400353"
---
# <a name="tutorial-back-up-and-restore-files-for-linux-virtual-machines-in-azure"></a>Samouczek: tworzenie kopii zapasowych i przywracanie plików dla maszyn wirtualnych z systemem Linux na platformie Azure

Możesz chronić swoje dane, tworząc kopie zapasowe w regularnych odstępach czasu. Usługa Azure Backup tworzy punkty odzyskiwania przechowywane w geograficznie nadmiarowych magazynach odzyskiwania. Z punktu odzyskiwania można przywrócić całą maszynę wirtualną lub poszczególne pliki. W tym artykule opisano sposób przywracania pojedynczego pliku na maszynę wirtualną z systemem Linux i uruchomionym serwerem nginx. Jeśli nie masz jeszcze maszyny wirtualnej gotowej do użycia, możesz ją utworzyć, korzystając z [przewodnika Szybki start dla systemu Linux](quick-create-cli.md). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie kopii zapasowej maszyny wirtualnej
> * Harmonogram codziennego wykonywania kopii zapasowej
> * Przywracanie pliku z kopii zapasowej

## <a name="backup-overview"></a>Omówienie usługi Backup

Gdy usługa Azure Backup inicjuje kopię zapasową, wyzwala rozszerzenie kopii zapasowej, aby utworzyć migawkę punktu w czasie. Usługa Azure Backup używa rozszerzenia _VMSnapshotLinux_ w systemie Linux. Rozszerzenie jest instalowane podczas tworzenia pierwszej kopii zapasowej maszyny wirtualnej, jeśli maszyna wirtualna jest uruchomiona. Jeśli maszyna wirtualna nie jest uruchomiona, usługa Backup utworzy migawkę powiązanego magazynu (ponieważ gdy maszyna wirtualna jest zatrzymana, nie występują zapisy aplikacji).

Domyślnie usługa Azure Backup tworzy kopię zapasową maszyny wirtualnej z systemem Linux spójną pod względem systemu plików, ale można ją skonfigurować do tworzenia [kopii zapasowej spójnej pod względem aplikacji przy użyciu struktury skryptów poprzedzających i skryptów używanych po utworzeniu kopii zapasowej](../../backup/backup-azure-linux-app-consistent.md). Po utworzeniu migawki w usłudze Azure Backup dane są przesyłane do magazynu. Aby zmaksymalizować wydajność, usługa rozpoznaje i przesyła jedynie te bloki danych, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej.

Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.


## <a name="create-a-backup"></a>Tworzenie kopii zapasowej
Utwórz zaplanowaną, codzienną operację tworzenia kopii zapasowych w magazynie usługi Recovery Services:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie wybierz pozycję **Maszyny wirtualne**. 
3. Z listy wybierz maszynę wirtualną do utworzenia kopii zapasowej.
4. W bloku maszyny wirtualnej w sekcji **Operacje** kliknij pozycję **Kopia zapasowa**. Spowoduje to otwarcie bloku **Włącz kopię zapasową**.
5. W **Magazynie usługi Recovery Services** kliknij pozycję **Utwórz nowy** i podaj nazwę nowego magazynu. Nowy magazyn zostanie utworzony w tej samej lokalizacji i tej samej grupie zasobów co maszyna wirtualna.
6. Kliknij opcję **Zasady tworzenia kopii zapasowych**. W tym przykładzie zachowaj ustawienia domyślne i kliknij przycisk **OK**.
7. W bloku **Włącz kopię zapasową** kliknij pozycję **Włącz kopię zapasową**. Spowoduje to codzienne tworzenie kopii zapasowej na podstawie harmonogramu domyślnego.
10. Aby utworzyć początkowy punkt odzyskiwania, w bloku **Kopia zapasowa** kliknij opcję **Utwórz kopię zapasową teraz**.
11. W bloku **Utwórz kopię zapasową teraz** kliknij ikonę kalendarza, użyj kontrolki kalendarz, aby wybrać ostatni dzień zachowywania tego punktu odzyskiwania, a następnie kliknij pozycję **kopia zapasowa**.
12. W bloku **Kopia zapasowa** dla maszyny wirtualnej wyświetlana jest liczba ukończonych punktów odzyskiwania.

    ![Punkty odzyskiwania](./media/tutorial-backup-vms/backup-complete.png)

Tworzenie pierwszej kopii zapasowej zajmuje około 20 minut. Przejdź do następnej części tego samouczka po utworzeniu kopii zapasowej.

## <a name="restore-a-file"></a>Przywracanie pliku

Jeżeli przypadkowo usuniesz plik lub wprowadzisz w nim zmiany, możesz użyć opcji odzyskiwania plików, aby odzyskać plik z magazynu kopii zapasowej. Odzyskiwanie plików używa skryptu uruchamianego na maszynie wirtualnej, aby zainstalować punkt odzyskiwania jako dysk lokalny. Dyski te pozostają zainstalowane przez 12 godzin, co umożliwia kopiowanie plików z punktu odzyskiwania i przywracanie ich na maszynę wirtualną.  

W tym przykładzie przedstawiono sposób odzyskiwania domyślnej strony internetowej serwera nginx /var/www/html/index.nginx-debian.html. Publiczny adres IP maszyny wirtualnej w tym przykładzie to *13.69.75.209*. Możesz znaleźć adres IP maszyny wirtualnej w następujący sposób:

 ```azurecli
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. Na komputerze lokalnym otwórz przeglądarkę internetową i wpisz publiczny adres IP maszyny wirtualnej, aby wyświetlić domyślną stronę internetową serwera nginx.

    ![Zrzut ekranu pokazujący domyślną stronę sieci Web Nginx.](./media/tutorial-backup-vms/nginx-working.png)

1. Połącz się z maszyną wirtualną za pośrednictwem protokołu SSH.

    ```bash
    ssh 13.69.75.209
    ```

2. Usuń plik /var/www/html/index.nginx-debian.html.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. Na komputerze lokalnym odśwież przeglądarkę, naciskając klawisze CTRL + F5, aby zobaczyć, że domyślna strona serwera nginx została usunięta.

    ![Zrzut ekranu pokazujący domyślną stronę Nginx.](./media/tutorial-backup-vms/nginx-broken.png)
    
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) na komputerze lokalnym.
6. W menu po lewej stronie wybierz pozycję **Maszyny wirtualne**. 
7. Z listy wybierz maszynę wirtualną.
8. W bloku maszyny wirtualnej w sekcji **Ustawienia** kliknij pozycję **Kopia zapasowa**. Zostanie otwarty blok **Kopia zapasowa**. 
9. W menu w górnej części bloku wybierz opcję **Odzyskiwanie pliku**. Zostanie otwarty blok **Odzyskiwanie plików**.
10. W obszarze **Krok 1. Wybieranie punktu odzyskiwania** wybierz punkt odzyskiwania z listy rozwijanej.
11. W obszarze **Krok 2. Pobieranie skryptu na potrzeby przeglądania i odzyskiwania plików** kliknij przycisk **Pobierz plik wykonywalny**. Zapisz pobrany plik na komputerze lokalnym.
7. Kliknij przycisk **Pobierz skrypt**, aby pobrać lokalnie plik skryptu.
8. Otwórz wiersz Bash i wpisz następujące polecenie, zastępując fragmenty *Linux_myVM_05-05-2017.sh* prawidłową ścieżką i nazwą pliku pobranego skryptu, *azureuser* nazwą użytkownika maszyny wirtualnej oraz *13.69.75.209* publicznym adresem IP maszyny wirtualnej.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. Na komputerze lokalnym otwórz połączenie SSH z maszyną wirtualną.

    ```bash
    ssh 13.69.75.209
    ```
    
10. Na maszynie wirtualnej dodaj uprawnienia do wykonywania do pliku skryptu.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. Na maszynie wirtualnej uruchom skrypt, aby zainstalować punkt odzyskiwania jako system plików.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. Dane wyjściowe skryptu zawierają ścieżkę punktu instalacji. Dane wyjściowe wyglądają podobnie do następujących:

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. Na maszynie wirtualnej skopiuj domyślną stronę internetową serwera nginx z punktu instalacji z powrotem do miejsca, z którego usunięto plik.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. Na komputerze lokalnym otwórz kartę przeglądarki internetowej, na której nawiązano połączenie z adresem IP maszyny wirtualnej i wyświetlono domyślną stronę serwera nginx. Naciśnij klawisze CTRL + F5, aby odświeżyć stronę przeglądarki. Teraz można zobaczyć, że strona domyślna ponownie działa.

    ![Domyślna strona internetowa serwera nginx](./media/tutorial-backup-vms/nginx-working.png)

18. Na komputerze lokalnym wróć do karty przeglądarki internetowej z witryną Azure Portal i w obszarze **Krok 3. Odinstalowanie dysków po odzyskiwaniu** kliknij przycisk **Odinstaluj dyski**. Jeśli pominiesz ten krok, połączenie z punktem instalacji zostanie automatycznie zamknięte po 12 godzinach. Po upłynięciu tych 12 godzin musisz pobrać nowy skrypt, aby utworzyć nowy punkt instalacji.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie kopii zapasowej maszyny wirtualnej
> * Harmonogram codziennego wykonywania kopii zapasowej
> * Przywracanie pliku z kopii zapasowej

Przejdź do następnego samouczka, aby uzyskać informacje o monitorowaniu maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Zarządzanie maszynami wirtualnymi](tutorial-govern-resources.md)
