---
title: Azure Stack Edge — informacje o wersji pakietu Pro GA | Microsoft Docs
description: W tym artykule opisano krytyczne problemy i rozwiązania dla Azure Stack EDGE Pro z wersją ogólne dostępność.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 20340a07160a0b4501069e0f4d45599c5bff33f2
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444047"
---
# <a name="azure-stack-edge-pro-with-gpu-general-availability-ga-release-notes"></a>Azure Stack EDGE Pro z informacjami o wersji ogólnego udostępnienia procesora GPU

[!INCLUDE [applies-to-Pro-GPU-sku](../../includes/azure-stack-edge-applies-to-gpu-sku.md)]

Poniższe informacje o wersji dotyczą krytycznych problemów z problemami i rozwiązanymi problemami dotyczącymi ogólnej dostępności (wersja ogólna) dla urządzeń z Azure Stack EDGE Pro z procesorem GPU.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem urządzenia z Azure Stack Edge w programie należy uważnie zapoznać się z informacjami zawartymi w informacjach o wersji.

Ten artykuł ma zastosowanie do wersji **Azure Stack EDGE Pro 2010** , która jest mapowana na numer wersji oprogramowania **2.1.1377.2170**.

## <a name="whats-new"></a>Co nowego

Poniższe nowe funkcje są dostępne w wersji Azure Stack Edge 2010. 

- **Klasy magazynu** — w tej wersji dostępne są klasy magazynu umożliwiające dynamiczne udostępnianie magazynu. Aby uzyskać więcej informacji, zobacz [Kubernetes Storage Management na urządzeniu z procesorem GPU w Azure Stack Edge](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Pulpit nawigacyjny Kubernetes z serwerem metryk** — w tym wydaniu zostanie dodany pulpit nawigacyjny Kubernetes z dodatkiem serwera metryk. Możesz użyć pulpitu nawigacyjnego, aby zapoznać się z omówieniem aplikacji uruchomionych na urządzeniu Azure Stack EDGE Pro, wyświetlić stan zasobów klastra Kubernetes i zobaczyć wszystkie błędy, które wystąpiły na urządzeniu. Serwer metryk agreguje użycie procesora i pamięci przez zasoby Kubernetes na urządzeniu. Aby uzyskać więcej informacji, zobacz [Korzystanie z pulpitu nawigacyjnego Kubernetes w celu monitorowania urządzenia z procesorem GPU w programie Azure Stack Edge](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Usługa Azure ARC z włączonym Kubernetes na Azure Stack EDGE Pro** — Rozpoczynanie tej wersji. obciążenia aplikacji można wdrożyć na urządzeniu Azure Stack EDGE Pro za pośrednictwem usługi Azure Arc Kubernetes. Azure Arc to narzędzie do zarządzania hybrydowego, które pozwala na wdrażanie aplikacji w klastrach Kubernetes. Aby uzyskać więcej informacji, zobacz [wdrażanie obciążeń za pośrednictwem usługi Azure Arc na urządzeniu Azure Stack brzeg Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Znane problemy 

Poniższa tabela zawiera podsumowanie znanych problemów dotyczących urządzeń z programem Azure Stack Edge.

| Nie. | Cecha | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
|**1.**|Funkcje w wersji zapoznawczej |W tej wersji na poziomie GA następujące funkcje: local Azure Resource Manager, Virtual Machines, Kubernetes, Azure Arc Enabled Kubernetes, wieloprocesowa usługa (MPS) dla procesora GPU — są dostępne w wersji zapoznawczej dla urządzenia z Azure Stack brzeg Pro.  |Te funkcje będą ogólnie dostępne w nowszej wersji. |
| **2.** |Azure Stack EDGE Pro + Azure SQL | Tworzenie bazy danych SQL wymaga dostępu administratora.   |Wykonaj następujące czynności zamiast kroków 1-2 w temacie [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>W lokalnym interfejsie użytkownika urządzenia Włącz interfejs obliczeniowy. Wybierz pozycję **obliczenia > port # > Włącz dla obliczeń > Zastosuj.**</li><li>Pobierz `sqlcmd` na komputerze klienckim z https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Nawiąż połączenie z adresem IP interfejsu obliczeniowego (włączonym portem), dodając znak ", 1401" na końcu adresu.</li><li>Końcowe polecenie będzie wyglądać następująco: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Po wykonaniu tej czynności kroki 3-4 z bieżącej dokumentacji powinny być identyczne. </li></ul> |
| **3.** |Odśwież| Przyrostowe zmiany w obiektach Blob przywrócone za pośrednictwem **odświeżania** nie są obsługiwane |W przypadku punktów końcowych obiektów BLOB częściowe aktualizacje obiektów BLOB po odświeżeniu mogą spowodować, że aktualizacje nie są przekazywane do chmury. Na przykład sekwencja akcji, takich jak:<ul><li>Utwórz obiekt BLOB w chmurze. Lub Usuń wcześniej przekazany obiekt BLOB z urządzenia.</li><li>Odśwież obiekt BLOB z chmury do urządzenia, korzystając z funkcji odświeżania.</li><li>Zaktualizuj tylko część obiektu BLOB przy użyciu interfejsów API REST usługi Azure SDK.</li></ul>Te akcje mogą spowodować, że zaktualizowane sekcje obiektu BLOB nie zostaną zaktualizowane w chmurze. <br>**Obejście**: Użyj narzędzi, takich jak Robocopy, lub zwykłych kopii plików w Eksploratorze lub wierszu polecenia, aby zastąpić całe obiekty blob.|
|**4.**|Ograniczanie przepływności|W przypadku ograniczania przepustowości, jeśli nowe zapisy nie są dozwolone na urządzeniu, operacje zapisu wykonane przez klienta NFS kończą się niepowodzeniem z powodu błędu "odmowa uprawnień".| Błąd zostanie wyświetlony w następujący sposób:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: nie można utworzyć katalogu "test": odmowa uprawnień|
|**5000.**|Pozyskiwanie Blob Storage|W przypadku korzystania z programu AzCopy w wersji 10 na potrzeby pozyskiwania usługi BLOB Storage należy uruchomić AzCopy z następującym argumentem: `Azcopy <other arguments> --cap-mbps 2000`| Jeśli te limity nie są podane dla AzCopy, może to potencjalnie wysłać do urządzenia dużą liczbę żądań i spowodować problemy związane z usługą.|
|**ust.**|Konta magazynu warstwowego|W przypadku korzystania z kont magazynu warstwowego obowiązują następujące kwestie:<ul><li> Obsługiwane są tylko blokowe obiekty blob. Stronicowe obiekty blob nie są obsługiwane.</li><li>Brak obsługi interfejsu API migawek lub kopiowania.</li><li> Pozyskiwanie obciążeń usługi Hadoop za pośrednictwem programu `distcp` nie jest obsługiwane, ponieważ wielokrotnie używa operacji kopiowania.</li></ul>||
|**7.**|Połączenie z udziałem NFS|Jeśli wiele procesów jest kopiowanych do tego samego udziału, a `nolock` atrybut nie jest używany, podczas kopiowania mogą pojawić się błędy.|Aby `nolock` skopiować pliki do udziału NFS, ten atrybut musi być przekazaniem do polecenia instalacji. Na przykład: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**0,8.**|Klaster Kubernetes|W przypadku zastosowania aktualizacji na urządzeniu, na którym działa klaster Kubernetes, maszyny wirtualne Kubernetes zostaną ponownie uruchomione i ponownie uruchamiane. W tym przypadku tylko te, które są wdrożone z wybranymi replikami są automatycznie przywracane po aktualizacji.  |Jeśli utworzono poszczególne zbiory () poza kontrolerem replikacji bez określania zestawu replik, te zasobniki nie zostaną automatycznie przywrócone po aktualizacji urządzenia. Konieczne będzie przywrócenie tych zasobników.<br>Zestaw replik zastępuje te, które zostały usunięte z jakiegokolwiek powodu, takie jak awaria węzła lub nieprzerwane uaktualnienie węzła. Z tego powodu zalecamy użycie zestawu replik, nawet jeśli aplikacja wymaga tylko jednego elementu.|
|**9.**|Klaster Kubernetes|Kubernetes na Azure Stack EDGE Pro jest obsługiwana tylko z Helm v3 lub nowszym. Aby uzyskać więcej informacji, zapoznaj się z [często zadawanymi pytaniami: usuwanie obiektu do](https://v3.helm.sh/docs/faq/)wykonania.|
|**dziesięć.**|Platforma Kubernetes z włączoną usługą Azure Arc |W przypadku wersji GA usługa Azure ARC z włączonym Kubernetes jest aktualizowana z wersji 0.1.18 na 0.2.9. Ponieważ aktualizacja Kubernetes z funkcją Azure Arc nie jest obsługiwana na Azure Stack urządzeniu brzegowym, należy ponownie wdrożyć usługę Azure ARC z obsługą Kubernetes.|Wykonaj następujące kroki:<ol><li>[Zastosuj oprogramowanie urządzenia i aktualizacje Kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Nawiąż połączenie z [interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Usuń istniejącego agenta usługi Azure Arc. Typ: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Wdróż [usługę Azure Arc na nowym zasobie](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Nie używaj istniejącego zasobu usługi Azure Arc.</li></ol>|
|**11.**|Platforma Kubernetes z włączoną usługą Azure Arc|Wdrożenia usługi Azure Arc nie są obsługiwane, jeśli serwer proxy sieci Web jest skonfigurowany na urządzeniu Azure Stack EDGE Pro.||
|**dwunastomiesięcznych.**|Kubernetes |Port 31000 jest zarezerwowany dla pulpitu nawigacyjnego Kubernetes. Podobnie w konfiguracji domyślnej adresy IP 172.28.0.1 i 172.28.0.10 są zarezerwowane odpowiednio dla usług Kubernetes Service i Core DNS.|Nie używaj zarezerwowanych adresów IP.|
|**trzynast.**|Kubernetes |Kubernetes obecnie nie zezwala na wiele protokołów usługi równoważenia obciążenia. Na przykład usługa DNS, która będzie musiała nasłuchiwać zarówno TCP, jak i UDP. |Aby obejść to ograniczenie Kubernetes z MetalLB, można utworzyć dwie usługi (jeden dla TCP, jeden dla UDP) w tym samym selektorze pod. Te usługi używają tego samego klucza udostępniania i specyfikacji loadBalancerIP, aby współużytkować ten sam adres IP. Adresy IP mogą być również udostępniane, jeśli masz więcej usług niż dostępne. <br> Aby uzyskać więcej informacji, zobacz [udostępnianie adresów IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**14,5.**|Klaster Kubernetes|Istniejące moduły Azure IoT Edge Marketplace mogą wymagać modyfikacji do uruchomienia na IoT Edge na Azure Stack urządzeniu brzegowym.|Aby uzyskać więcej informacji, zobacz Modyfikowanie modułów Azure IoT Edge z witryny Marketplace do uruchamiania na Azure Stack urządzeniu brzegowym.<!-- insert link-->|
|**15000.**|Kubernetes |Instalacje powiązań oparte na plikach nie są obsługiwane w przypadku Azure IoT Edge w usłudze Kubernetes na Azure Stack urządzeniu brzegowym.|IoT Edge używa warstwy translacji do translacji `ContainerCreate` opcji na konstrukcje Kubernetes. Tworzenie `Binds` map w `hostpath` katalogu i w ten sposób nie można powiązać instalacji powiązań opartych na plikach z ścieżkami w kontenerach IoT Edge. Jeśli to możliwe, zamapuj katalog nadrzędny.|
|**16.**|Kubernetes |Jeśli przeniesiesz własne certyfikaty do IoT Edge i dodasz je na urządzeniu brzegowym Azure Stack po skonfigurowaniu obliczeń na urządzeniu, nowe certyfikaty nie zostaną pobrane.|Aby obejść ten problem, należy przekazać certyfikaty przed skonfigurowaniem obliczeń na urządzeniu. Jeśli obliczenia są już skonfigurowane, Połącz się z [interfejsem programu PowerShell urządzenia i uruchom IoT Edge polecenia](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Uruchom ponownie `iotedged` i `edgehub` zasobniki.|
|**7.**|Certyfikaty |W niektórych przypadkach Aktualizacja stanu certyfikatu w lokalnym interfejsie użytkownika może trwać kilka sekund. |Może to dotyczyć następujących scenariuszy w lokalnym interfejsie użytkownika.<ul><li>Kolumna **stan** na stronie **Certyfikaty** .</li><li>Kafelek **zabezpieczenia** na **stronie Wprowadzenie.**</li><li>Kafelek **konfiguracji** na stronie **Przegląd** .</li></ul>  |
|**7.**|IoT Edge |Moduły wdrożone za pomocą IoT Edge nie mogą używać sieci hosta. | |
|**postanowienia.**|Obliczenia i Kubernetes |Obliczenia/Kubernetes nie obsługują serwera proxy sieci Web NTLM. ||
|**19.**|Obliczenia i serwer proxy sieci Web + aktualizacja |W przypadku skonfigurowania obliczeń przy użyciu serwera proxy sieci Web, aktualizacja obliczeń może zakończyć się niepowodzeniem. |Zalecamy wyłączenie obliczeń przed aktualizacją. |

<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->




## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do wdrożenia urządzenia z programem Azure Stack Edge z procesorem GPU](azure-stack-edge-gpu-deploy-prep.md)