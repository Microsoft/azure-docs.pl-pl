---
title: Zarządzanie migawkami przy użyciu Azure NetApp Files | Microsoft Docs
description: Opisuje sposób tworzenia migawek i zarządzania nimi za pomocą Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/04/2020
ms.author: b-juche
ms.openlocfilehash: e1be0879af02fac0f7ae926a02ea23fd6be84de4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325693"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Zarządzanie migawkami przy użyciu usługi Azure NetApp Files

Azure NetApp Files obsługuje tworzenie migawek na żądanie i Używanie zasad migawek do planowania automatycznego tworzenia migawek.  Możesz również przywrócić migawkę do nowego woluminu lub przywrócić pojedynczy plik przy użyciu klienta programu.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Tworzenie migawki na żądanie dla woluminu

Migawki woluminów można tworzyć na żądanie. 

1.  Przejdź do woluminu, dla którego chcesz utworzyć migawkę. Kliknij pozycję **migawki**.

    ![Przejdź do migawek](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kliknij pozycję **+ Dodaj migawkę** , aby utworzyć migawkę na żądanie dla woluminu.

    ![Dodaj migawkę](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  W oknie Nowa migawka Podaj nazwę nowej tworzonej migawki.   

    ![Nowa migawka](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Kliknij przycisk **OK**. 

## <a name="manage-snapshot-policies"></a>Zarządzanie zasadami migawek

Można zaplanować automatyczne podejmowanie migawek woluminów przy użyciu zasad migawek. Możesz również zmodyfikować zasady migawek odpowiednio do potrzeb lub usunąć zasady migawek, które nie są już potrzebne.  

### <a name="register-the-feature"></a>Rejestrowanie funkcji

Funkcja **zasad migawek** jest obecnie w wersji zapoznawczej. Jeśli używasz tej funkcji po raz pierwszy, musisz najpierw zarejestrować funkcję. 

1. Zarejestruj funkcję: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Sprawdź stan rejestracji funkcji: 

    > [!NOTE]
    > **RegistrationState** może być w stanie od `Registering` do 60 minut przed zmianą na `Registered` . Przed kontynuowaniem Zaczekaj na **zarejestrowanie** stanu.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
Możesz również użyć [poleceń interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` , `az feature show` Aby zarejestrować funkcję i wyświetlić stan rejestracji. 

### <a name="create-a-snapshot-policy"></a>Tworzenie zasad migawek 

Zasady migawek umożliwiają określenie częstotliwości tworzenia migawek w cyklach co godzinę, codziennie, co tydzień lub co miesiąc. Należy również określić maksymalną liczbę migawek do zachowania dla woluminu.  

1.  W widoku konta NetApp kliknij pozycję **zasady migawek**.

    ![Nawigacja zasad migawek](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  W oknie Zasady migawek Ustaw stan zasady na **włączone**. 

3.  Kliknij kartę **co godzinę**, **codziennie**, **co tydzień**lub **co miesiąc** , aby utworzyć zasady co godzinę, codziennie, co tydzień lub co miesiąc. Określ **liczbę migawek do zachowania**.  

    Zapoznaj się z [limitami zasobów dla Azure NetApp Files](azure-netapp-files-resource-limits.md) o maksymalnej liczbie migawek dozwolonych dla woluminu. 

    W poniższym przykładzie przedstawiono konfigurację zasad migawek co godzinę. 

    ![Zasady migawek co godzinę](../media/azure-netapp-files/snapshot-policy-hourly.png)

    Poniższy przykład przedstawia dzienną konfigurację zasad migawek.

    ![Codzienne zasady migawek](../media/azure-netapp-files/snapshot-policy-daily.png)

    W poniższym przykładzie pokazano cotygodniową konfigurację zasad migawek.

    ![Zasady migawek co tydzień](../media/azure-netapp-files/snapshot-policy-weekly.png)

    W poniższym przykładzie pokazano miesięczną konfigurację zasad migawek.

    ![Zasady migawek miesięcznie](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Kliknij przycisk **Zapisz**.  

Jeśli konieczne jest utworzenie dodatkowych zasad migawek, Powtórz krok 3.
Utworzone zasady są wyświetlane na stronie zasady migawek.

Jeśli wolumin ma używać zasad migawek, należy [zastosować zasady do woluminu](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Stosowanie zasad migawek do woluminu

Jeśli chcesz, aby wolumin używał utworzonych zasad migawek, należy zastosować zasady do woluminu. 

1.  Przejdź do strony **woluminy** , kliknij prawym przyciskiem myszy wolumin, do którego chcesz zastosować zasady migawek, a następnie wybierz polecenie **Edytuj**.

    ![Woluminy menu po kliknięciu prawym przyciskiem myszy](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  W oknie Edycja w obszarze **zasady migawek**wybierz zasady, które mają być używane dla woluminu.  Kliknij przycisk **OK** , aby zastosować zasady.  

    ![Edytowanie zasad migawek](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Modyfikowanie zasad migawek 

Istniejące zasady migawek można modyfikować, aby zmienić stan zasad, częstotliwość migawek (co godzinę, codziennie, co tydzień lub co miesiąc) lub liczbę migawek do zachowania.  
 
1.  W widoku konta NetApp kliknij pozycję **zasady migawek**.

2.  Kliknij prawym przyciskiem myszy zasady migawek, które chcesz zmodyfikować, a następnie wybierz pozycję **Edytuj**.

    ![Menu po kliknięciu prawym przyciskiem myszy](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Wprowadź zmiany w oknie Zasady migawek, które zostanie wyświetlone, a następnie kliknij przycisk **Zapisz**. 

### <a name="delete-a-snapshot-policy"></a>Usuwanie zasad migawek 

Można usunąć zasady migawek, które nie powinny już być zachowywane.   

1.  W widoku konta NetApp kliknij pozycję **zasady migawek**.

2.  Kliknij prawym przyciskiem myszy zasady migawek, które chcesz zmodyfikować, a następnie wybierz pozycję **Usuń**.

    ![Menu po kliknięciu prawym przyciskiem myszy](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Kliknij przycisk **tak** , aby potwierdzić, że chcesz usunąć zasady migawek.   

    ![Potwierdzenie usunięcia zasad migawek](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Przywracanie migawki do nowego woluminu

Obecnie można przywrócić migawkę tylko do nowego woluminu. 
1. Wybierz opcję **migawki** z bloku wolumin, aby wyświetlić listę migawek. 
2. Kliknij prawym przyciskiem myszy migawkę, która ma zostać przywrócona, a następnie wybierz polecenie **Przywróć do nowego woluminu** z opcji menu.  

    ![Przywróć migawkę do nowego woluminu](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. W oknie Tworzenie woluminu podaj informacje o nowym woluminie:  
    * **Nazwij**   
        Określ nazwę tworzonego woluminu.  
        
        Nazwa musi być unikatowa w obrębie grupy zasobów. Musi zawierać co najmniej trzy znaki.  Dozwolone są dowolne znaki alfanumeryczne.

    * **limit przydziału**  
        Określ ilość pamięci logicznej, która ma zostać przydzielona do woluminu.  

    ![Przywróć do nowego woluminu](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Kliknij przycisk **Przegląd + Utwórz**.  Kliknij przycisk **Utwórz**.   
    Nowy wolumin używa tego samego protokołu, który jest wykorzystywany przez migawkę.   
    Nowy wolumin, do którego zostanie przywrócona migawka, pojawia się w bloku woluminy.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Przywracanie pliku z migawki przy użyciu klienta programu

Jeśli nie chcesz [przywracać całej migawki do woluminu](#restore-a-snapshot-to-a-new-volume), możesz przywrócić plik z migawki przy użyciu klienta, na którym jest zainstalowany wolumin.  

Zainstalowany wolumin zawiera katalog migawek o nazwie  `.snapshot` (w klientach NFS) lub `~snapshot` (w klientach SMB), który jest dostępny dla klienta. Katalog migawek zawiera podkatalogi odpowiadające migawkom woluminu. Każdy podkatalog zawiera pliki migawki. Jeśli przypadkowo usuniesz lub zastąpisz plik, możesz przywrócić plik do nadrzędnego katalogu do odczytu i zapisu, kopiując plik z podkatalogu migawek do katalogu do odczytu i zapisu. 

W przypadku wybrania pola wyboru Ukryj ścieżkę migawki podczas tworzenia woluminu katalog migawek jest ukryty. Można wyświetlić stan ścieżki Ukryj migawkę woluminu, wybierając wolumin. Opcję Ukryj ścieżkę migawki można edytować, klikając pozycję **Edytuj** na stronie woluminu.  

![Edycja opcji migawek woluminów](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Przywracanie pliku przy użyciu klienta systemu plików NFS z systemem Linux 

1. Użyj `ls` polecenia systemu Linux, aby wyświetlić listę plików, które chcesz przywrócić z `.snapshot` katalogu. 

    Na przykład:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Użyj `cp` polecenia, aby skopiować plik do katalogu nadrzędnego.  

    Na przykład: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Przywracanie pliku przy użyciu klienta systemu Windows 

1. Jeśli `~snapshot` katalog woluminu jest ukryty, [Pokaż ukryte elementy](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) w katalogu nadrzędnym do wyświetlenia `~snapshot` .

    ![Wyświetlanie ukrytych elementów](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Przejdź do podkatalogu w programie `~snapshot` , aby znaleźć plik, który chcesz przywrócić.  Kliknij plik prawym przyciskiem myszy. Wybierz polecenie **Kopiuj**.  

    ![Kopiuj plik do przywrócenia](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Wróć do katalogu nadrzędnego. Kliknij prawym przyciskiem myszy w katalogu nadrzędnym i wybierz polecenie, `Paste` Aby wkleić plik do katalogu.

    ![Wklej plik do przywrócenia](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. Możesz również kliknąć prawym przyciskiem myszy katalog nadrzędny, wybrać pozycję **Właściwości**, kliknij kartę **poprzednie wersje** , aby wyświetlić listę migawek, a następnie wybierz pozycję **Przywróć** , aby przywrócić plik.  

    ![Właściwości poprzednie wersje](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z zasadami migawek](troubleshoot-snapshot-policies.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Film Azure NetApp Files migawek 101](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
