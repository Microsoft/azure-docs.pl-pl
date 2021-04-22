---
title: Azure NetApp Files z Azure VMware Solution
description: Użyj Azure NetApp Files z maszynami Azure VMware Solution, aby migrować i synchronizować dane między serwerami lokalnymi, maszynami Azure VMware Solution i infrastrukturami chmury.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 09b9ba2ff6b95e12558b1ac49e401ce6dede4942
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870989"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files z Azure VMware Solution

Ten artykuł zawiera instrukcje integracji usług Azure NetApp Files z Azure VMware Solution opartymi na obciążeniach. System operacyjny gościa będzie działać wewnątrz maszyn wirtualnych, które będą Azure NetApp Files woluminów. 

## <a name="azure-netapp-files-overview"></a>Azure NetApp Files omówienie

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) to usługa platformy Azure do migracji i uruchamiania najbardziej wymagających obciążeń plików przedsiębiorstwa w chmurze: baz danych, oprogramowania SAP i aplikacji obliczeniowych o wysokiej wydajności bez zmian w kodzie.

### <a name="features"></a>Funkcje
(Usługi, Azure NetApp Files używane).

- **Połączenia usługi Active Directory:** Azure NetApp Files obsługuje [Active Directory Domain Services i Azure Active Directory Domain Services](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use).

- **Share Protocol:** Azure NetApp Files protokoły bloku komunikatów serwera (SMB) i sieciowego systemu plików (NFS). Ta obsługa oznacza, że woluminy mogą być instalowane na kliencie systemu Linux i mapowane na kliencie systemu Windows.

- **Azure VMware Solution:** Azure NetApp Files udziały można tworzyć z maszyn wirtualnych utworzonych w Azure VMware Solution środowisku.

Azure NetApp Files jest dostępna w wielu regionach świadczenia usługi Azure i obsługuje replikację między regionami. Aby uzyskać informacje na Azure NetApp Files konfiguracji, zobacz [Hierarchia magazynu Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Architektura referencyjna

Na poniższym diagramie przedstawiono połączenie za pośrednictwem Azure ExpressRoute z Azure VMware Solution chmurą prywatną. Środowisko Azure VMware Solution uzyskuje dostęp do udziału Azure NetApp Files zainstalowanego na Azure VMware Solution wirtualnych.

![Diagram przedstawiający architekturę usługi NetApp Files Azure VMware Solution architektury.](media/net-app-files/net-app-files-topology.png)

Ten artykuł zawiera instrukcje dotyczące testowania i testowania woluminu Azure NetApp Files jako udziału plików dla Azure VMware Solution wirtualnych. W tym scenariuszu umyliśmy protokół NFS. Azure NetApp Files i Azure VMware Solution są tworzone w tym samym regionie świadczenia usługi Azure.

## <a name="prerequisites"></a>Wymagania wstępne 

> [!div class="checklist"]
> * Subskrypcja platformy Azure z Azure NetApp Files włączona
> * Podsieć dla Azure NetApp Files
> * Maszyna wirtualna z systemem Linux na Azure VMware Solution
> * Maszyny wirtualne z systemem Windows na Azure VMware Solution

## <a name="regions-supported"></a>Obsługiwane regiony

Listę obsługiwanych regionów można znaleźć na stronie [Produkty platformy Azure według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)

## <a name="verify-pre-configured-azure-netapp-files"></a>Weryfikowanie wstępnie skonfigurowanych Azure NetApp Files 

Postępuj zgodnie z instrukcjami krok po kroku w następujących artykułach, aby utworzyć i zainstalować woluminy Azure NetApp Files na Azure VMware Solution wirtualnych.

- [Tworzenie konta usługi NetApp](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Konfigurowanie puli pojemności](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Tworzenie woluminu SMB dla usługi Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Tworzenie woluminu NFS dla usługi Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Delegowanie podsieci do usługi Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

Poniższe kroki obejmują weryfikację wstępnie skonfigurowanych ustawień Azure NetApp Files na platformie Azure na Azure NetApp Files Premium.

1. W obszarze Azure Portal **STORAGE** wybierz pozycję **Azure NetApp Files**. Zostanie wyświetlona lista Azure NetApp Files konfiguracji. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Zrzut ekranu przedstawiający listę wstępnie skonfigurowanych Azure NetApp Files."::: 

2. Wybierz skonfigurowane konto usługi NetApp Files, aby wyświetlić jego ustawienia. Na przykład wybierz pozycję **Contoso-anf2.** 

3. Wybierz **pozycję Pule pojemności,** aby zweryfikować skonfigurowaną pulę. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Zrzut ekranu przedstawiający opcje wyświetlania pul pojemności i woluminów skonfigurowanego konta usługi NetApp Files.":::

    Zostanie otwarta strona Pule pojemności z wyświetloną pojemnością i poziomem usługi. W tym przykładzie pula magazynów jest skonfigurowana jako 4 TiB z poziomem usługi Premium.

4. Wybierz **pozycję Woluminy,** aby wyświetlić woluminy utworzone w puli pojemności. (Zobacz poprzedni zrzut ekranu).

5. Wybierz wolumin, aby wyświetlić jego konfigurację.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Zrzut ekranu przedstawiający woluminy utworzone w puli pojemności.":::

    Zostanie otwarte okno ze szczegółami konfiguracji woluminu.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Zrzut ekranu przedstawiający szczegóły konfiguracji woluminu.":::

    Widać, że anfvolume ma rozmiar 200 GiB i znajduje się w puli pojemności anfpool1. Jest on eksportowany jako udział plików NFS za pośrednictwem adresu 10.22.3.4:/ANFVOLUME. Jeden prywatny adres IP z usługi Azure Virtual Network (VNet) został utworzony na Azure NetApp Files i ścieżkę systemu plików NFS do instalacji na maszynie wirtualnej.

    Aby dowiedzieć się więcej Azure NetApp Files wydajności woluminu według rozmiaru lub limitu przydziału, zobacz [Zagadnienia](../azure-netapp-files/azure-netapp-files-performance-considerations.md)dotyczące wydajności Azure NetApp Files . 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Weryfikowanie wstępnie skonfigurowanego mapowania Azure VMware Solution wirtualnej

Aby udostępnić udział Azure NetApp Files do maszyny wirtualnej Azure VMware Solution wirtualnej, należy zrozumieć mapowanie udziałów SMB i NFS. Tylko po skonfigurowaniu woluminów SMB lub NFS można zainstalować je zgodnie z dokumentacją tutaj.

- Udział SMB: utwórz połączenie usługi Active Directory przed wdrożeniem woluminu SMB. Określone kontrolery domeny muszą być dostępne dla delegowanego podsieci usługi Azure NetApp Files w celu pomyślnego nawiązaniu połączenia. Po skonfigurowaniu usługi Active Directory w ramach Azure NetApp Files będzie ona wyświetlana jako element do wyboru podczas tworzenia woluminów SMB.

- Udział NFS: Azure NetApp Files do tworzenia woluminów przy użyciu systemu plików NFS lub podwójnego protokołu (NFS i SMB). Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. System plików NFS można zainstalowane na serwerze z systemem Linux przy użyciu wiersza polecenia lub /etc/fstab wpisów.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Przypadki użycia Azure NetApp Files z Azure VMware Solution

Poniżej przedstawiono kilka atrakcyjnych przykładów Azure NetApp Files przypadkach użycia. 
- Zarządzanie profilami horyzontów
- Zarządzanie profilami Citrix
- Usługi pulpitu zdalnego zarządzania profilami
- Udziały plików na Azure VMware Solution

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zintegrować Azure NetApp Files obciążeniami Azure VMware Solution, możesz dowiedzieć się więcej na temat:

- [Ograniczenia zasobów dla Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Wytyczne dotyczące planowania sieci usługi Azure NetApp Files](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Replikacja woluminów Azure NetApp Files między regionami](../azure-netapp-files/cross-region-replication-introduction.md) 
- [Często zadawane pytania dotyczące usługi Azure NetApp Files](../azure-netapp-files/azure-netapp-files-faqs.md)
