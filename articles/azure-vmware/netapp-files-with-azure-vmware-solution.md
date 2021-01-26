---
title: Azure NetApp Files z rozwiązaniem VMware platformy Azure
description: Używaj Azure NetApp Files z maszynami wirtualnymi rozwiązań VMware platformy Azure do migrowania i synchronizowania danych między serwerami lokalnymi, maszynami wirtualnymi rozwiązań VMware platformy Azure i infrastrukturami chmury.
ms.topic: how-to
ms.date: 01/20/2021
ms.openlocfilehash: 97a9172dfbed2722d4af0abbd6231a0367dc4e3c
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798460"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files z rozwiązaniem VMware platformy Azure

W tym artykule omówiono kroki integrowania Azure NetApp Files z obciążeniami opartymi na rozwiązaniach VMware platformy Azure. System operacyjny gościa będzie działać w ramach maszyn wirtualnych uzyskujących dostęp do Azure NetApp Files woluminów. 

## <a name="azure-netapp-files-overview"></a>Przegląd Azure NetApp Files

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) to usługa pierwszej platformy Azure do migracji i uruchamiania najbardziej wymagających obciążeń plików przedsiębiorstwa w chmurze, w tym baz danych, SAP i wysoko wydajnych aplikacji obliczeniowych, bez zmian w kodzie.

### <a name="features"></a>Funkcje
(Usługi, w których Azure NetApp Files są używane).

- **Połączenia Active Directory**: Azure NetApp Files obsługuje [Active Directory Domain Services i Azure Active Directory Domain Services](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use).

- **Protokół udostępniania**: Azure NetApp Files obsługuje protokoły SMB (Server Message Block) i Network File System (NFS). Ta obsługa oznacza, że woluminy mogą być zainstalowane na kliencie z systemem Linux i mogą być mapowane na klientach systemu Windows.

- **Rozwiązanie VMware platformy Azure**: udziały Azure NetApp Files mogą być instalowane z maszyn wirtualnych utworzonych w środowisku rozwiązań VMware platformy Azure.

Azure NetApp Files jest dostępna w wielu regionach świadczenia usługi Azure i obsługuje replikację między regionami. Aby uzyskać informacje na temat Azure NetApp Files metod konfiguracji, zobacz [Hierarchia magazynu Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Architektura referencyjna

Na poniższym diagramie przedstawiono połączenie za pośrednictwem platformy Azure ExpressRoute z chmurą prywatną rozwiązania Azure VMware. Przedstawia on użycie udziału Azure NetApp Files, zainstalowanego na maszynach wirtualnych rozwiązań VMware platformy Azure, do którego uzyskuje się dostęp za pośrednictwem środowiska rozwiązań VMware platformy Azure.

![Diagram przedstawiający pliki NetApp architektury rozwiązań VMware platformy Azure.](media/net-app-files/net-app-files-topology.png)

W tym artykule opisano instrukcje dotyczące konfigurowania, testowania i weryfikowania woluminu Azure NetApp Files jako udziału plików dla maszyn wirtualnych rozwiązań VMware platformy Azure. W tym scenariuszu użyto protokołu NFS. W tym samym regionie świadczenia usługi Azure są tworzone Azure NetApp Files i rozwiązanie VMware platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne 

> [!div class="checklist"]
> * Subskrypcja platformy Azure z włączonym Azure NetApp Files
> * Podsieć dla Azure NetApp Files
> * Maszyna wirtualna z systemem Linux na platformie Azure VMware
> * Maszyny wirtualne z systemem Windows na platformie Azure VMware

## <a name="regions-supported"></a>Obsługiwane regiony

Listę obsługiwanych regionów można znaleźć w [obszarze produkty platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all).

## <a name="verify-pre-configured-azure-netapp-files"></a>Sprawdź wstępnie skonfigurowane Azure NetApp Files 

Postępuj zgodnie z instrukcjami krok po kroku w poniższych artykułach, aby utworzyć i zainstalować woluminy Azure NetApp Files na maszynach wirtualnych rozwiązań VMware platformy Azure.

- [Tworzenie konta usługi NetApp](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Konfigurowanie puli pojemności](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Tworzenie woluminu SMB dla usługi Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Tworzenie woluminu NFS dla usługi Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Delegowanie podsieci do usługi Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

Poniższe kroki obejmują weryfikację wstępnie skonfigurowanego Azure NetApp Files utworzonego na platformie Azure na poziomie usługi Azure NetApp Files Premium.

1. W Azure Portal w obszarze **Magazyn** wybierz pozycję **Azure NetApp Files**. Zostanie wyświetlona lista skonfigurowanych Azure NetApp Files. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Zrzut ekranu przedstawiający listę wstępnie skonfigurowanych Azure NetApp Files."::: 

2. Wybierz skonfigurowane konto plików NetApp, aby wyświetlić jego ustawienia. Na przykład wybierz pozycję **contoso-anf2**. 

3. Wybierz pozycję **Pule pojemności** , aby zweryfikować skonfigurowaną pulę. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Zrzut ekranu przedstawiający opcje wyświetlania pul pojemności i woluminów ze skonfigurowanym kontem NetApp Files.":::

    Zostanie otwarta strona pule pojemności z wyświetlonym poziomem wydajności i usług. W tym przykładzie Pula magazynów jest skonfigurowana jako 4 TiB z poziomem usługi Premium.

4. Wybierz **woluminy** , aby wyświetlić woluminy utworzone w ramach puli pojemności. (Zobacz poprzedni zrzut ekranu).

5. Wybierz wolumin, aby wyświetlić jego konfigurację.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Zrzut ekranu przedstawiający woluminy utworzone w puli pojemności.":::

    Zostanie otwarte okno pokazujące szczegóły konfiguracji woluminu.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Zrzut ekranu przedstawiający szczegóły konfiguracji woluminu.":::

    Można zobaczyć, że wolumin anfvolume o rozmiarze 200 GiB, został utworzony w puli pojemności anfpool1 i wyeksportowany jako udział plików NFS za pośrednictwem 10.22.3.4:/ANFVOLUME. Jeden prywatny adres IP z usługi Azure Virtual Network (VNet) został utworzony dla Azure NetApp Files i ścieżki systemu plików NFS do zainstalowania na maszynie wirtualnej. Aby uzyskać informacje na temat Azure NetApp Files wydajności woluminu względem rozmiaru ("przydział"), zobacz [zagadnienia dotyczące wydajności w przypadku Azure NetApp Files](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Sprawdź wstępnie skonfigurowane mapowanie udziału maszyny wirtualnej rozwiązania Azure VMware

Przed zaprezentowanie dostępności Azure NetApp Files udziału w maszynie wirtualnej rozwiązania VMware platformy Azure należy zrozumieć mapowanie udziałów SMB i NFS. Tylko po skonfigurowaniu woluminów SMB lub NFS, można je zainstalować zgodnie z opisem w tym miejscu.

- Udział SMB: Utwórz połączenie Active Directory przed wdrożeniem woluminu SMB. Określone kontrolery domeny muszą być dostępne przez delegowaną podsieć Azure NetApp Files w celu pomyślnego nawiązania połączenia. Po skonfigurowaniu Active Directory w ramach konta Azure NetApp Files będzie on wyświetlany jako element do wyboru podczas tworzenia woluminów SMB.

- Udział NFS: Azure NetApp Files przyczynia się do tworzenia woluminów przy użyciu systemu plików NFS lub Dual Protocol (NFS i SMB). Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. System plików NFS można zainstalować na serwerze z systemem Linux przy użyciu wierszy poleceń lub wpisów/etc/fstab.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Przypadki użycia Azure NetApp Files z rozwiązaniem VMware platformy Azure

Poniżej przedstawiono zaledwie kilka atrakcyjnych Azure NetApp Files przypadków użycia. 
- Zarządzanie profilami horyzontu
- Zarządzanie profilami Citrix
- Zarządzanie profilami Usługi pulpitu zdalnego
- Udziały plików w rozwiązaniu VMware platformy Azure

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej [na temat limitów zasobów dla Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits).
- Zapoznaj się z [zaleceniami dotyczącymi planowania Azure NetApp Files sieci](../azure-netapp-files/azure-netapp-files-network-topologies.md).
- Dowiedz się więcej o [replikacji między regionami Azure NetApp Files woluminów](../azure-netapp-files/cross-region-replication-introduction.md). 
- Zobacz [często zadawane pytania dotyczące Azure NetApp Files](../azure-netapp-files/azure-netapp-files-faqs.md).
