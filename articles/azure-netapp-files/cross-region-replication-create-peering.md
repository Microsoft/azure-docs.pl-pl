---
title: Tworzenie replikacji woluminu dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób tworzenia komunikacji równorzędnej replikacji woluminu dla Azure NetApp Files w celu skonfigurowania replikacji między regionami.
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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 2a3c788ce50ccc1d537fd2903fe05acffd079b0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591014"
---
# <a name="create-volume-replication-for-azure-netapp-files"></a>Utwórz replikację woluminu dla Azure NetApp Files

> [!IMPORTANT]
> Funkcja replikacji między regionami jest obecnie w publicznej wersji zapoznawczej. Musisz przesłać żądanie waitlist, aby uzyskać dostęp do funkcji za Azure NetApp Files pomocą [strony przesyłania waitlist replikacji między regionami](https://aka.ms/anfcrrpreviewsignup). Przed rozpoczęciem korzystania z funkcji replikacji między regionami poczekaj oficjalne powiadomienie e-mail z zespołu Azure NetApp Files.

W tym artykule opisano sposób konfigurowania replikacji między regionami przez tworzenie komunikacji równorzędnej replikacji. 

Skonfigurowanie komunikacji równorzędnej replikacji umożliwia asynchroniczną replikację danych z woluminu Azure NetApp Files (Źródło) do innego woluminu Azure NetApp Files (miejsce docelowe). Wolumin źródłowy i wolumin docelowy muszą zostać wdrożone w oddzielnych regionach. Poziom usługi dla docelowej puli pojemności można dopasować do wartości źródłowej puli pojemności lub wybrać inny poziom usługi.   

Azure NetApp Files replikacja nie obsługuje obecnie wielu subskrypcji; wszystkie replikacje muszą być wykonywane w ramach jednej subskrypcji.

Przed rozpoczęciem upewnij się, że zostały sprawdzone [wymagania i zagadnienia dotyczące korzystania z replikacji między regionami](cross-region-replication-requirements-considerations.md).  

## <a name="locate-the-source-volume-resource-id"></a>Lokalizowanie identyfikatora zasobu woluminu źródłowego  

Należy uzyskać identyfikator zasobu woluminu źródłowego, który ma zostać zreplikowany. 

1. Przejdź do woluminu źródłowego, a następnie wybierz pozycję **Właściwości** w obszarze Ustawienia, aby wyświetlić identyfikator zasobu woluminu źródłowego.   
    ![Lokalizowanie identyfikatora zasobu woluminu źródłowego](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. Skopiuj identyfikator zasobu do Schowka.  Będzie potrzebny później.

## <a name="create-the-data-replication-volume-the-destination-volume"></a>Tworzenie woluminu replikacji danych (wolumin docelowy)

Należy utworzyć wolumin docelowy, do którego mają być replikowane dane z woluminu źródłowego.  Przed utworzeniem woluminu docelowego należy mieć konto NetApp i pulę pojemności w regionie docelowym. 

1. Konto docelowe musi znajdować się w innym regionie niż region woluminu źródłowego. W razie potrzeby utwórz konto NetApp w regionie świadczenia usługi Azure, które będzie używane na potrzeby replikacji, wykonując kroki opisane w temacie [Tworzenie konta NetApp](azure-netapp-files-create-netapp-account.md).   
Możesz również wybrać istniejące konto NetApp w innym regionie.  

2. W razie potrzeby Utwórz pulę pojemności na nowo utworzonym koncie NetApp, wykonując czynności opisane w temacie [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md).   

    Możesz również wybrać istniejącą pulę pojemności do hostowania woluminu docelowego replikacji.  

    Poziom usługi dla docelowej puli pojemności można dopasować do wartości źródłowej puli pojemności lub wybrać inny poziom usługi.

3. Delegowanie podsieci w regionie, który ma być używany na potrzeby replikacji, wykonując czynności opisane w sekcji [delegowanie podsieci do Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

4. Utwórz wolumin replikacji danych, wybierając pozycję **woluminy** w obszarze usługa magazynu na docelowym koncie NetApp. Następnie kliknij przycisk **+ Dodaj replikację danych** .  

    ![Dodawanie replikacji danych](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. Na wyświetlonej stronie Tworzenie woluminu wykonaj następujące pola w obszarze kartę **podstawowe** :
    * Nazwa woluminu
    * Pula pojemności
    * Przydział woluminu
        > [!NOTE] 
        > Przydział woluminu (rozmiar) woluminu docelowego powinien dublować wolumin źródłowy. W przypadku określenia rozmiaru, który jest mniejszy niż wolumin źródłowy, rozmiar woluminu docelowego zostanie automatycznie zmieniony na rozmiar woluminu źródłowego. 
    * Sieć wirtualna 
    * Podsieć

    Aby uzyskać szczegółowe informacje o polach, zobacz [Tworzenie woluminu NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume). 

6. Na karcie **Protokół** wybierz ten sam protokół, który jest woluminem źródłowym.  
W przypadku protokołu NFS upewnij się, że reguły eksportowania zasad spełniają wymagania wszystkich hostów w sieci zdalnej, które będą miały dostęp do eksportu.  

7. Na karcie **Tagi** Utwórz pary klucz/wartość stosownie do potrzeb.  

8. Na karcie **replikacja** wklej wartość w polu Identyfikator zasobu woluminu źródłowego uzyskany w polu [Znajdź identyfikator zasobu woluminu źródłowego](#locate-the-source-volume-resource-id), a następnie wybierz żądany harmonogram replikacji. Opcje harmonogramu replikacji obejmują: co 10 minut, co godzinę, codziennie, co tydzień i co miesiąc.  

    ![Tworzenie replikacji woluminu](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. Kliknij przycisk **Przegląd + Utwórz**, a następnie kliknij przycisk **Utwórz** , aby utworzyć wolumin replikacji danych.   

    ![Przeglądanie i tworzenie replikacji](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>Autoryzuj replikację z woluminu źródłowego  

Aby autoryzować replikację, należy uzyskać identyfikator zasobu woluminu docelowego replikacji i wkleić go do pola Autoryzuj w woluminie źródłowym replikacji. 

1. W Azure Portal przejdź do Azure NetApp Files.

2. Przejdź do docelowego konta NetApp i docelowej puli pojemności, w której znajduje się wolumin docelowy replikacji.

3. Wybierz wolumin docelowy replikacji, przejdź do pozycji **Właściwości** w obszarze Ustawienia i Znajdź **Identyfikator zasobu** woluminu docelowego. Skopiuj docelowy identyfikator zasobu woluminu do Schowka.

    ![Identyfikator zasobu właściwości](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. W Azure NetApp Files przejdź do konta źródłowego replikacji i źródłowej puli pojemności. 

5. Zlokalizuj wolumin źródłowy replikacji i wybierz go. Przejdź do pozycji **replikacja** w obszarze usługa magazynu i kliknij pozycję **Autoryzuj**.

    ![Autoryzuj replikację](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. W polu Autoryzuj wklej zasób woluminu replikacji docelowej, który został uzyskany w kroku 3, a następnie kliknij przycisk **OK**.

## <a name="next-steps"></a>Następne kroki  

* [Replikacja między regionami](cross-region-replication-introduction.md)
* [Wymagania i zagadnienia dotyczące korzystania z replikacji między regionami](cross-region-replication-requirements-considerations.md)
* [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md)
* [Metryki replikacji woluminów](azure-netapp-files-metrics.md#replication)
* [Zarządzanie odzyskiwaniem po awarii](cross-region-replication-manage-disaster-recovery.md)
* [Usuwanie replikacji woluminów lub woluminów](cross-region-replication-delete.md)
* [Rozwiązywanie problemów z replikacją między regionami](troubleshoot-cross-region-replication.md)

