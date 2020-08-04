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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 271c3c9f63ee3f761826e214f3bf32a8df5f1cbe
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533295"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Zarządzanie migawkami przy użyciu usługi Azure NetApp Files

Azure NetApp Files obsługuje tworzenie migawek na żądanie i Używanie zasad migawek do planowania automatycznego tworzenia migawek.  Możesz również przywrócić migawkę do nowego woluminu.  

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
    > **RegistrationState** może być w `Registering` stanie przez kilka minut przed zmianą na `Registered` . Przed kontynuowaniem Zaczekaj na **zarejestrowanie** stanu.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

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

4.  Kliknij pozycję **Zapisz**.  

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

## <a name="next-steps"></a>Następne kroki

* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
