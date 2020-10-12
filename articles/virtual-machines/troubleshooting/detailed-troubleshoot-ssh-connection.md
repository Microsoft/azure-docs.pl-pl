---
title: Szczegółowe Rozwiązywanie problemów z protokołem SSH dla maszyny wirtualnej platformy Azure | Microsoft Docs
description: Bardziej szczegółowe procedury rozwiązywania problemów z protokołem SSH dotyczące problemów z nawiązywaniem połączenia z maszyną wirtualną platformy Azure
keywords: odmówiono połączenia SSH, błąd SSH, usługa Azure SSH, Niepowodzenie połączenia SSH
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 0b4890181721d08b741d327adb74bd097be5c9f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87069170"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Szczegółowe procedury rozwiązywania problemów dotyczących połączenia SSH z maszyną wirtualną z systemem Linux na platformie Azure
Istnieje wiele możliwych powodów, dla których klient SSH może nie mieć dostępu do usługi SSH na maszynie wirtualnej. Jeśli po wykonaniu bardziej [ogólnych kroków związanych z rozwiązywaniem problemów](troubleshoot-ssh-connection.md)z PROTOKOŁem SSH należy jeszcze bardziej rozwiązać problem z połączeniem. W tym artykule szczegółowo opisano kroki rozwiązywania problemów w celu ustalenia, gdzie połączenie SSH kończy się niepowodzeniem i jak rozwiązać ten problem.

## <a name="take-preliminary-steps"></a>Wykonaj kroki wstępne
Na poniższym diagramie przedstawiono składniki, które są objęte pomocą.

![Diagram przedstawiający składniki usługi SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Poniższe kroki ułatwiają wyizolowanie źródła awarii i ustalenie rozwiązań lub obejścia tego problemu.

1. Sprawdź stan maszyny wirtualnej w portalu.
   W [Azure Portal](https://portal.azure.com)wybierz opcję Nazwa **maszyny wirtualnej maszyn wirtualnych**  >  *VM name*.

   W okienku stanu dla maszyny wirtualnej powinny być wyświetlane **działania**. Przewiń w dół, aby wyświetlić ostatnie działania dotyczące zasobów obliczeniowych, magazynu i sieci.

2. Wybierz pozycję **Ustawienia** , aby przejrzeć punkty końcowe, adresy IP, sieciowe grupy zabezpieczeń i inne ustawienia.

   Maszyna wirtualna powinna mieć punkt końcowy zdefiniowany dla ruchu SSH, który można wyświetlić w **punktach końcowych** lub **[sieciowej grupie zabezpieczeń](../../virtual-network/security-overview.md)**. Punkty końcowe na maszynach wirtualnych, które zostały utworzone przy użyciu Menedżer zasobów są przechowywane w sieciowej grupie zabezpieczeń. Upewnij się, że reguły zostały zastosowane do sieciowej grupy zabezpieczeń i są przywoływane w podsieci.

Aby sprawdzić łączność sieciową, sprawdź skonfigurowane punkty końcowe i sprawdź, czy można nawiązać połączenie z maszyną wirtualną za pośrednictwem innego protokołu, takiego jak HTTP lub inna usługa.

Po wykonaniu tych kroków spróbuj ponownie nawiązać połączenie SSH.

## <a name="find-the-source-of-the-issue"></a>Znajdź źródło problemu
Klient SSH na komputerze może nie połączyć się z usługą SSH na maszynie wirtualnej platformy Azure z powodu problemów lub błędów konfiguracji w następujących obszarach:

* [Komputer kliencki SSH](#source-1-ssh-client-computer)
* [Urządzenie brzegowe organizacji](#source-2-organization-edge-device)
* [Punkt końcowy usługi w chmurze i lista kontroli dostępu (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Sieciowe grupy zabezpieczeń](#source-4-network-security-groups)
* [Maszyna wirtualna platformy Azure oparta na systemie Linux](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Źródło 1: komputer kliencki SSH
Aby wyeliminować komputer jako źródło błędu, sprawdź, czy może nawiązać połączenia SSH z innym lokalnym komputerem z systemem Linux.

![Diagram przedstawiający składniki komputera klienckiego SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Jeśli połączenie nie powiedzie się, sprawdź, czy na komputerze występują następujące problemy:

* Ustawienie zapory lokalnej, która blokuje ruch przychodzący lub wychodzących SSH (TCP 22)
* Zainstalowane lokalnie oprogramowanie proxy klienta, które uniemożliwia połączenia SSH
* Lokalne zainstalowanie oprogramowania do monitorowania sieci, które uniemożliwia połączenia SSH
* Inne typy oprogramowania zabezpieczającego, które monitorują ruch lub zezwalają/nie zezwalają na określone typy ruchu

W przypadku zastosowania jednego z tych warunków należy tymczasowo wyłączyć oprogramowanie i wypróbować połączenie SSH z komputerem lokalnym, aby dowiedzieć się, dlaczego połączenie jest blokowane na komputerze. Następnie skontaktuj się z administratorem sieci, aby skorygować ustawienia oprogramowania w celu zezwolenia na połączenia SSH.

Jeśli używasz uwierzytelniania przy użyciu certyfikatu, sprawdź, czy masz uprawnienia do folderu. SSH w katalogu macierzystym:

* Chmod 700 ~/.SSH
* Chmod 644 ~/.SSH/ \* . pub
* Chmod 600 ~/.ssh/id_rsa (lub innych plików, w których przechowywane są Twoje klucze prywatne)
* Chmod 644 ~/.ssh/known_hosts (zawiera hosty, z którymi nawiązano połączenie za pośrednictwem protokołu SSH)

## <a name="source-2-organization-edge-device"></a>Źródło 2: Urządzenie brzegowe organizacji
Aby wyeliminować urządzenie w organizacji jako źródło błędu, sprawdź, czy komputer połączony bezpośrednio z Internetem może nawiązać połączenia SSH z maszyną wirtualną platformy Azure. Jeśli uzyskujesz dostęp do maszyny wirtualnej za pośrednictwem sieci VPN typu lokacja-lokacja lub połączenia usługi Azure ExpressRoute, przejdź do [źródła 4: sieciowe grupy zabezpieczeń](#nsg).

![Diagram przedstawiający Urządzenie brzegowe organizacji](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Jeśli nie masz komputera połączonego bezpośrednio z Internetem, Utwórz nową maszynę wirtualną platformy Azure w ramach własnej grupy zasobów lub usługi w chmurze, a następnie użyj nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure](../linux/quick-create-cli.md). Po zakończeniu testowania Usuń grupę zasobów lub maszynę wirtualną oraz usługę w chmurze.

Jeśli można utworzyć połączenie SSH z komputerem, który jest bezpośrednio połączony z Internetem, należy sprawdzić urządzenie Edge w organizacji dla:

* Zapora wewnętrzna, która blokuje ruch SSH za pośrednictwem Internetu
* Serwer proxy, który uniemożliwia połączenia SSH
* Wykrywanie intruza lub oprogramowanie do monitorowania sieci działające na urządzeniach w sieci brzegowej, które uniemożliwiają połączenia SSH

Skontaktuj się z administratorem sieci, aby skorygować ustawienia urządzeń brzegowych organizacji w celu zezwolenia na ruch SSH za pośrednictwem Internetu.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Źródło 3: punkt końcowy usługi w chmurze i lista ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> To źródło ma zastosowanie tylko do maszyn wirtualnych, które zostały utworzone przy użyciu klasycznego modelu wdrażania. W przypadku maszyn wirtualnych, które zostały utworzone przy użyciu Menedżer zasobów, przejdź do [źródła 4: sieciowe grupy zabezpieczeń](#nsg).

Aby wyeliminować punkt końcowy usługi w chmurze i listę ACL jako źródło błędu, sprawdź, czy inna maszyna wirtualna platformy Azure w tej samej sieci wirtualnej może nawiązać połączenie przy użyciu protokołu SSH.

![Diagram, który wyróżnia punkt końcowy usługi w chmurze i listę ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Jeśli nie masz innej maszyny wirtualnej w tej samej sieci wirtualnej, możesz łatwo ją utworzyć. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu interfejsu wiersza polecenia](../linux/quick-create-cli.md). Usuń dodatkową maszynę wirtualną po zakończeniu testowania.

Jeśli możesz utworzyć połączenie SSH z MASZYNą wirtualną w tej samej sieci wirtualnej, sprawdź następujące obszary:

* **Konfiguracja punktu końcowego dla ruchu SSH na docelowej maszynie wirtualnej.** Prywatny port TCP punktu końcowego powinien być zgodny z portem TCP, na którym nasłuchuje usługa SSH na maszynie wirtualnej. (Domyślny numer portu to 22). Sprawdź numer portu TCP SSH w Azure Portal, wybierając pozycję **maszyny wirtualne**  >  *VM name*  >  **Ustawienia**nazw maszyn wirtualnych  >  **Endpoints**.
* **Lista ACL dla punktu końcowego ruchu SSH na docelowej maszynie wirtualnej.** Lista ACL pozwala określić dozwolony lub zabroniony ruch przychodzący z Internetu, na podstawie jego źródłowego adresu IP. Nieprawidłowo skonfigurowane listy ACL mogą zapobiegać przychodzącemu ruchowi SSH do punktu końcowego. Sprawdź listy kontroli dostępu, aby upewnić się, że ruch przychodzący z publicznych adresów IP serwera proxy lub innego serwera granicznego jest dozwolony. Aby uzyskać więcej informacji, zobacz [Informacje o listach kontroli dostępu do sieci (ACL)](/previous-versions/azure/virtual-network/virtual-networks-acl).

Aby wyeliminować punkt końcowy jako źródło problemu, Usuń bieżący punkt końcowy, Utwórz inny punkt końcowy i określ nazwę SSH (port TCP 22 dla numeru portu publicznego i prywatnego). Aby uzyskać więcej informacji, zobacz [Konfigurowanie punktów końcowych na maszynie wirtualnej na platformie Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Źródło 4: sieciowe grupy zabezpieczeń
Sieciowe grupy zabezpieczeń umożliwiają bardziej szczegółową kontrolę nad dozwolonym ruchem przychodzącym i wychodzącym. Można tworzyć reguły obejmujące podsieci i usługi w chmurze w sieci wirtualnej platformy Azure. Sprawdź reguły sieciowej grupy zabezpieczeń, aby upewnić się, że ruch SSH do i z Internetu jest dozwolony.
Aby uzyskać więcej informacji, zobacz [Informacje o sieciowych grupach zabezpieczeń](../../virtual-network/security-overview.md).

Możesz również użyć weryfikacji adresu IP, aby sprawdzić poprawność konfiguracji sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Omówienie monitorowania sieci platformy Azure](../../network-watcher/network-watcher-monitoring-overview.md). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Źródło 5: maszyna wirtualna platformy Azure oparta na systemie Linux
Ostatnim źródłem możliwych problemów jest sama maszyna wirtualna platformy Azure.

![Diagram, który podświetla maszynę wirtualną platformy Azure opartą na systemie Linux](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Jeśli jeszcze tego nie zrobiono, postępuj zgodnie z instrukcjami [resetowania maszyn wirtualnych opartych](./reset-password.md)na systemie Linux.

Spróbuj ponownie nawiązać połączenie z komputera. Jeśli nadal nie powiedzie się, poniżej przedstawiono niektóre z możliwych problemów:

* Usługa SSH nie jest uruchomiona na docelowej maszynie wirtualnej.
* Usługa SSH nie nasłuchuje na porcie TCP 22. Aby przetestować, zainstaluj klienta Telnet na komputerze lokalnym i uruchom polecenie "Telnet *cloudServiceName*. cloudapp.NET 22". Ten krok określa, czy maszyna wirtualna zezwala na ruch przychodzący i wychodzący do punktu końcowego SSH.
* Lokalna Zapora na docelowej maszynie wirtualnej ma reguły uniemożliwiające ruch w ruchu SSH lub wychodzącym.
* Wykrywanie intruza lub oprogramowanie do monitorowania sieci działające na maszynie wirtualnej platformy Azure uniemożliwia połączenia SSH.

## <a name="additional-resources"></a>Zasoby dodatkowe
Aby uzyskać więcej informacji na temat rozwiązywania problemów z dostępem do aplikacji, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure](./troubleshoot-app-connection.md)
