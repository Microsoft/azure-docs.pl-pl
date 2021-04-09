---
title: Łączenie zdalne z węzłem klastra usługi Azure Service Fabric
description: Dowiedz się, jak zdalnie połączyć się z wystąpieniem zestawu skalowania (Service Fabric węźle klastra).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: 98d573af4fc2026134e75d4caf24a09e57e52c87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012548"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Połączenie zdalne z wystąpieniem zestawu skalowania maszyn wirtualnych lub węzłem klastra
W klastrze Service Fabric działającym na platformie Azure każdy zdefiniowany typ węzła klastra [konfiguruje maszynę wirtualną w osobnym skalowaniu](service-fabric-cluster-nodetypes.md).  Można połączyć się zdalnie z określonymi wystąpieniami zestawu skalowania (węzły klastra).  W przeciwieństwie do maszyn wirtualnych z pojedynczym wystąpieniem, wystąpienia zestawu skalowania nie mają własnych wirtualnych adresów IP. Może to być trudne, gdy szukasz adresu IP i portu, których można użyć do zdalnego łączenia się z konkretnym wystąpieniem.

Aby znaleźć adres IP i port, których można użyć do zdalnego łączenia się z konkretnym wystąpieniem, wykonaj następujące czynności.

1. Pobierz reguły NAT dla ruchu przychodzącego dla Remote Desktop Protocol (RDP).

    Zazwyczaj każdy typ węzła zdefiniowany w klastrze ma swój własny wirtualny adres IP i dedykowany moduł równoważenia obciążenia. Domyślnie moduł równoważenia obciążenia dla typu węzła ma nazwę o następującym formacie: *lb-{Cluster-Name}-{Node-Type}*; na przykład *lb-moja klaster-fronton*. 
    
    Na stronie modułu równoważenia obciążenia w Azure Portal wybierz pozycję **Ustawienia**  >  **reguły NAT dla ruchu przychodzącego**: 

    ![Zrzut ekranu przedstawiający stronę usługi równoważenia obciążenia w Azure Portal. W menu po lewej stronie w obszarze Ustawienia wybrana jest opcja reguły NAT dla ruchu przychodzącego.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Na poniższym zrzucie ekranu przedstawiono reguły NAT dla ruchu przychodzącego dla typu węzła o nazwie fronton: 

    ![Zrzut ekranu przedstawiający reguły NAT dla ruchu przychodzącego dla modułu równoważenia obciążenia. Dla każdej reguły są wyświetlane nazwy, wersja IP, miejsce docelowe, cel i usługa.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Dla każdego węzła adres IP jest wyświetlany w kolumnie Target **, kolumna** **docelowa** zawiera wystąpienie zestawu skalowania, a kolumna **usługi** zawiera numer portu. W przypadku połączenia zdalnego porty są przypisywane do każdego węzła w kolejności rosnącej, począwszy od portu 3389.

    Reguły NAT dla ruchu przychodzącego można również znaleźć w `Microsoft.Network/loadBalancers` sekcji szablonu Menedżer zasobów klastra.
    
2. Aby potwierdzić mapowanie portów przychodzących do docelowego dla węzła, możesz kliknąć jego regułę i przyjrzeć się wartości **portu docelowego** . Poniższy zrzut ekranu przedstawia regułę ruchu przychodzącego NAT dla węzła **frontonu (wystąpienie 1)** w poprzednim kroku. Należy zauważyć, że mimo że numer portu (przychodzący) to 3390, port docelowy jest mapowany na port 3389, port dla usługi RDP na serwerze docelowym.  

    ![Mapowanie portów docelowych](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Domyślnie w przypadku klastrów systemu Windows Port docelowy jest portem 3389, który jest mapowany na usługę RDP w węźle docelowym. W przypadku klastrów systemu Linux port docelowy to port 22, który jest mapowany na usługę Secure Shell (SSH).

3. Połącz się zdalnie z określonym węzłem (wystąpienie zestawu skalowania). Można użyć nazwy użytkownika i hasła, które zostały ustawione podczas tworzenia klastra lub innych skonfigurowanych poświadczeń. 

    Poniższy zrzut ekranu przedstawia używanie Podłączanie pulpitu zdalnego do nawiązywania połączenia z węzłem **frontonu (wystąpienie 1)** w klastrze systemu Windows:
    
    ![Podłączanie pulpitu zdalnego](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    W węzłach systemu Linux można nawiązać połączenie przy użyciu protokołu SSH (Poniższy przykład używa tego samego adresu IP i portu dla zwięzłości):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Aby zapoznać się z kolejnymi krokami, przeczytaj następujące artykuły:
* Zobacz [Omówienie funkcji "wdróż gdziekolwiek" i porównanie z klastrami zarządzanymi przez platformę Azure](service-fabric-deploy-anywhere.md).
* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* [Aktualizowanie wartości zakresu portów protokołu RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) na maszynach wirtualnych klastra po wdrożeniu
* [Zmień nazwę użytkownika i hasło administratora](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) dla maszyn wirtualnych klastra

