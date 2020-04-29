---
title: Zarządzanie migawkami przy użyciu Azure NetApp Files | Microsoft Docs
description: Opisuje sposób tworzenia migawek dla woluminu lub przywracania z migawki do nowego woluminu przy użyciu Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: 48055a774808aea86452e8410b7e717f5019d172
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78267902"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Zarządzanie migawkami przy użyciu Azure NetApp Files

Za pomocą Azure NetApp Files można ręcznie utworzyć migawkę na żądanie dla woluminu lub przywrócić ją z migawki do nowego woluminu. Usługa Azure NetApp Files nie tworzy automatycznie migawek woluminów.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Tworzenie migawki na żądanie dla woluminu

Migawki można tworzyć tylko na żądanie. Zasady migawek nie są obecnie obsługiwane.

1.  W bloku wolumin kliknij pozycję **migawki**.

    ![Przejdź do migawek](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kliknij pozycję **+ Dodaj migawkę** , aby utworzyć migawkę na żądanie dla woluminu.

    ![Dodaj migawkę](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  W oknie Nowa migawka Podaj nazwę nowej tworzonej migawki.   

    ![Nowa migawka](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Kliknij przycisk **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Przywracanie migawki do nowego woluminu

Obecnie można przywrócić migawkę tylko do nowego woluminu. 
1. Przejdź do bloku **Zarządzanie migawkami** w bloku wolumin, aby wyświetlić listę migawek. 
2. Wybierz migawkę do przywrócenia.  
3. Kliknij prawym przyciskiem myszy nazwę migawki i wybierz polecenie **Przywróć do nowego woluminu** z opcji menu.  

    ![Przywróć migawkę do nowego woluminu](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. W oknie Nowy wolumin podaj informacje o nowym woluminie:  
    * **Nazwij**   
        Określ nazwę tworzonego woluminu.  
        
        Nazwa musi być unikatowa w obrębie grupy zasobów. Musi zawierać co najmniej trzy znaki.  Dozwolone są dowolne znaki alfanumeryczne.

    * **Ścieżka pliku**     
        Określ ścieżkę pliku, na podstawie której zostanie utworzona ścieżka eksportu dla nowego woluminu. Ścieżka eksportu służy do instalowania woluminu oraz uzyskiwania do niego dostępu.   
        
        Miejsce docelowe instalacji to punkt końcowy adresu IP usługi NFS. Ta wartość jest generowana automatycznie.   
        
        Nazwa ścieżki pliku może zawierać tylko litery, cyfry i łączniki („-”). Musi mieć długość od 16 do 40 znaków. 

    * **Limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.

    *   **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskiwać dostęp do woluminu.  
        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Dostęp do Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej, która znajduje się w tym samym regionie co wolumin za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Możesz uzyskać dostęp do woluminu z sieci lokalnej za pośrednictwem usługi Express Route. 

    * **Podsieci**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określona podsieć musi być delegowana do usługi Azure NetApp Files. Nową podsieć można utworzyć, wybierając pozycję **Utwórz nową** w polu podsieć.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Kliknij przycisk **OK**.   
    Nowy wolumin, do którego zostanie przywrócona migawka, pojawia się w bloku woluminy.

## <a name="next-steps"></a>Następne kroki

[Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
