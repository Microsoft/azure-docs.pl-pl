---
title: Skonfiguruj domyślną domenę NFSv 4.1 dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób konfigurowania klienta NFS do korzystania z NFSv 4.1 z Azure NetApp Files.
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
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: e749f27875612136c50938712fded6a371f8c7ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325627"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Konfigurowanie domyślnej domeny NFSv4.1 dla usługi Azure NetApp Files

NFSv4 wprowadza koncepcję domeny uwierzytelniania. Azure NetApp Files obecnie obsługuje mapowanie użytkownika tylko do katalogu głównego z usługi do klienta NFS. Aby można było używać funkcji NFSv 4.1 z Azure NetApp Files, należy zaktualizować klienta systemu plików NFS.

## <a name="default-behavior-of-usergroup-mapping"></a>Domyślne zachowanie mapowania użytkownika/grupy

Wartość domyślna mapowania dla użytkownika to, `nobody` ponieważ domena NFSv4 jest ustawiona na wartość `localdomain` . Po zainstalowaniu woluminu Azure NetApp Files NFSv 4.1 jako katalogu głównego zostaną wyświetlone następujące uprawnienia:  

![Domyślne zachowanie mapowania użytkownika/grupy dla NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Jak pokazano na powyższym przykładzie, użytkownik `file1` powinien być `root` , ale domyślnie mapuje na `nobody` .  W tym artykule pokazano, jak ustawić `file1` użytkownika na `root` .  

## <a name="steps"></a>Kroki 

1. Edytuj `/etc/idmapd.conf` plik na kliencie NFS.   
    Usuń komentarz z wiersza `#Domain` (oznacza to, aby usunąć `#` z wiersza), i zmień wartość `localdomain` na `defaultv4iddomain.com` . 

    Początkowa konfiguracja: 
    
    ![Początkowa konfiguracja dla NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Zaktualizowana konfiguracja:
    
    ![Zaktualizowana konfiguracja dla NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Odinstaluj wszystkie aktualnie zainstalowane woluminy systemu plików NFS.
3. Zaktualizuj `/etc/idmapd.conf` plik.
4. Uruchom ponownie `rpcbind` usługę na hoście ( `service rpcbind restart` ) lub po prostu uruchom ponownie hosta.
5. Zainstaluj woluminy NFS zgodnie z potrzebami.   

    Zobacz [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

Poniższy przykład pokazuje zmianę wyniku zmiany użytkownika/grupy: 

![Zrzut ekranu przedstawiający przykład zmiany użytkownika/grupy.](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Jak pokazano w przykładzie, użytkownik/Grupa zmieniły się z `nobody` na `root` .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Zachowanie innych użytkowników (innych niż główne) i grup

Azure NetApp Files obsługuje lokalnych użytkowników (użytkowników utworzonych lokalnie na hoście), którzy mają uprawnienia skojarzone z plikami lub folderami na woluminach NFSv 4.1. Jednak usługa obecnie nie obsługuje mapowania użytkowników/grup w wielu węzłach. W związku z tym użytkownicy utworzeni na jednym hoście nie są domyślnie zamapowane dla użytkowników utworzonych na innym hoście. 

W poniższym przykładzie `Host1` ma trzy istniejące konta użytkowników testowych ( `testuser01` , `testuser02` , `testuser03` ): 

![Zrzut ekranu pokazujący, że host1 ma trzy istniejące konta użytkowników testowych.](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

`Host2`Należy pamiętać, że konta użytkowników testowych nie zostały utworzone, ale ten sam wolumin jest instalowany na obu hostach:

![Konfiguracja powstająca dla NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Następny krok 

[Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

