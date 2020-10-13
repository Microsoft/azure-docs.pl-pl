---
title: Informacje o wersji Azure Stack EDGE Pro Preview | Microsoft Docs
description: W tym artykule opisano krytyczne problemy i rozwiązania dla Azure Stack EDGE Pro z wersją ogólne dostępność.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/07/2020
ms.author: alkohli
ms.openlocfilehash: d166b0a4c4b69f03d7dba9d997d7d07fbd81ef41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893974"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>Informacje o wersji Azure Stack EDGE Pro z procesorem GPU

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących wersji zapoznawczej 2008 dla urządzeń z usługą Azure Stack EDGE Pro z procesorem GPU.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem urządzenia z Azure Stack Edge w programie należy uważnie zapoznać się z informacjami zawartymi w informacjach o wersji.

Ten artykuł ma zastosowanie do następującej wersji oprogramowania — **Azure Stack EDGE Pro 2008**. 

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>Co nowego

Następujące nowe funkcje zostały dodane w Azure Stack Edge 2008. W zależności od używanej wersji zapoznawczej programu mogą zostać wyświetlone podzbiór tych funkcji. 

- **Klasy magazynu** — w poprzedniej wersji można tylko statycznie zainicjować magazyn za pośrednictwem udziałów SMB lub NFS dla aplikacji stanowych wdrożonych w klastrze Kubernetes działającym na urządzeniu Azure Stack brzeg Pro. W tej wersji dodano klasy magazynu, które umożliwiają dynamiczne Inicjowanie obsługi magazynu. Aby uzyskać więcej informacji, zobacz [Kubernetes Storage Management na urządzeniu z procesorem GPU w Azure Stack Edge](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Pulpit nawigacyjny Kubernetes z serwerem metryk** — w tym wydaniu zostanie dodany pulpit nawigacyjny Kubernetes z dodatkiem serwera metryk. Możesz użyć pulpitu nawigacyjnego, aby zapoznać się z omówieniem aplikacji uruchomionych na urządzeniu Azure Stack EDGE Pro, wyświetlić stan zasobów klastra Kubernetes i zobaczyć wszystkie błędy, które wystąpiły na urządzeniu. Serwer metryk agreguje użycie procesora i pamięci przez zasoby Kubernetes na urządzeniu. Aby uzyskać więcej informacji, zobacz [Korzystanie z pulpitu nawigacyjnego Kubernetes w celu monitorowania urządzenia z procesorem GPU w programie Azure Stack Edge](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure ARC dla Azure Stack brzeg Pro** — Rozpoczynanie tej wersji, możesz wdrażać obciążenia aplikacji na urządzeniu z systemem Azure Stack EDGE Pro za pośrednictwem usługi Azure Arc. Azure Arc to narzędzie do zarządzania hybrydowego, które pozwala na wdrażanie aplikacji w klastrach Kubernetes. Aby uzyskać więcej informacji, zobacz [wdrażanie obciążeń za pośrednictwem usługi Azure Arc na urządzeniu Azure Stack brzeg Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Znane problemy 

Poniższa tabela zawiera podsumowanie znanych problemów dotyczących urządzeń z programem Azure Stack Edge.

| Nie. | Cecha | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
| **1.** |Azure Stack EDGE Pro + Azure SQL | Tworzenie bazy danych SQL wymaga dostępu administratora.   |Wykonaj następujące czynności zamiast kroków 1-2 w temacie [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) . <ul><li>W lokalnym interfejsie użytkownika urządzenia Włącz interfejs obliczeniowy. Wybierz pozycję **obliczenia > port # > Włącz dla obliczeń > Zastosuj.**</li><li>Pobierz `sqlcmd` na komputerze klienckim z https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Nawiąż połączenie z adresem IP interfejsu obliczeniowego (włączonym portem), dodając znak ", 1401" na końcu adresu.</li><li>Końcowe polecenie będzie wyglądać następująco: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Po wykonaniu tej czynności kroki 3-4 z bieżącej dokumentacji powinny być identyczne. </li></ul> |
| **2.** |Odśwież| Przyrostowe zmiany w obiektach Blob przywrócone za pośrednictwem **odświeżania** nie są obsługiwane |W przypadku punktów końcowych obiektów BLOB częściowe aktualizacje obiektów BLOB po odświeżeniu mogą spowodować, że aktualizacje nie są przekazywane do chmury. Na przykład sekwencja akcji, takich jak:<ul><li>Utwórz obiekt BLOB w chmurze. Lub Usuń wcześniej przekazany obiekt BLOB z urządzenia.</li><li>Odśwież obiekt BLOB z chmury do urządzenia, korzystając z funkcji odświeżania.</li><li>Zaktualizuj tylko część obiektu BLOB przy użyciu interfejsów API REST usługi Azure SDK.</li></ul>Te akcje mogą spowodować, że zaktualizowane sekcje obiektu BLOB nie zostaną zaktualizowane w chmurze. <br>**Obejście**: Użyj narzędzi, takich jak Robocopy, lub zwykłych kopii plików w Eksploratorze lub wierszu polecenia, aby zastąpić całe obiekty blob.|
|**3.**|Ograniczanie przepływności|W przypadku ograniczania przepustowości, jeśli nowe zapisy nie są dozwolone na urządzeniu, operacje zapisu wykonane przez klienta NFS kończą się niepowodzeniem z powodu błędu "odmowa uprawnień".| Błąd zostanie wyświetlony w następujący sposób:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: nie można utworzyć katalogu "test": odmowa uprawnień|
|**czwart.**|Pozyskiwanie Blob Storage|W przypadku korzystania z programu AzCopy w wersji 10 na potrzeby pozyskiwania usługi BLOB Storage należy uruchomić AzCopy z następującym argumentem: `Azcopy <other arguments> --cap-mbps 2000`| Jeśli te limity nie są podane dla AzCopy, może to potencjalnie wysłać do urządzenia dużą liczbę żądań i spowodować problemy związane z usługą.|
|**5000.**|Konta magazynu warstwowego|W przypadku korzystania z kont magazynu warstwowego obowiązują następujące kwestie:<ul><li> Obsługiwane są tylko blokowe obiekty blob. Stronicowe obiekty blob nie są obsługiwane.</li><li>Brak obsługi interfejsu API migawek lub kopiowania.</li><li> Pozyskiwanie obciążeń usługi Hadoop za pośrednictwem programu `distcp` nie jest obsługiwane, ponieważ wielokrotnie używa operacji kopiowania.</li></ul>||
|**ust.**|Połączenie z udziałem NFS|Jeśli wiele procesów jest kopiowanych do tego samego udziału, a `nolock` atrybut nie jest używany, podczas kopiowania mogą pojawić się błędy.|Aby `nolock` skopiować pliki do udziału NFS, ten atrybut musi być przekazaniem do polecenia instalacji. Przykład: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Klaster Kubernetes|W przypadku zastosowania aktualizacji na urządzeniu, na którym działa klaster Kubernetes, maszyny wirtualne Kubernetes zostaną ponownie uruchomione i ponownie uruchamiane. W tym przypadku tylko te, które są wdrożone z wybranymi replikami są automatycznie przywracane po aktualizacji.  |Jeśli utworzono poszczególne zbiory () poza kontrolerem replikacji bez określania zestawu replik, te zasobniki nie zostaną automatycznie przywrócone po aktualizacji urządzenia. Konieczne będzie przywrócenie tych zasobników.<br>Zestaw replik zastępuje te, które zostały usunięte z jakiegokolwiek powodu, takie jak awaria węzła lub nieprzerwane uaktualnienie węzła. Z tego powodu zalecamy użycie zestawu replik, nawet jeśli aplikacja wymaga tylko jednego elementu.|
|**0,8.**|Klaster Kubernetes|Kubernetes na Azure Stack EDGE Pro jest obsługiwana tylko z Helm v3 lub nowszym. Aby uzyskać więcej informacji, zapoznaj się z [często zadawanymi pytaniami: usuwanie obiektu do](https://v3.helm.sh/docs/faq/)wykonania.|
|**9.**|Azure Arc + Azure Stack EDGE Pro|Wdrożenia usługi Azure Arc nie są obsługiwane, jeśli serwer proxy sieci Web jest skonfigurowany na urządzeniu Azure Stack EDGE Pro.||
|**dziesięć.**|Kubernetes |Port 31000 jest zarezerwowany dla pulpitu nawigacyjnego Kubernetes. Podobnie w konfiguracji domyślnej adresy IP 172.28.0.1 i 172.28.0.10 są zarezerwowane odpowiednio dla usług Kubernetes Service i Core DNS.|Nie używaj zarezerwowanych adresów IP.|
|**11.**|Kubernetes |Kubernetes obecnie nie zezwala na wiele protokołów usługi równoważenia obciążenia. Na przykład usługa DNS, która będzie musiała nasłuchiwać zarówno TCP, jak i UDP. |Aby obejść to ograniczenie Kubernetes z MetalLB, można utworzyć dwie usługi (jeden dla TCP, jeden dla UDP) w tym samym selektorze pod. Te usługi używają tego samego klucza udostępniania i specyfikacji loadBalancerIP, aby współużytkować ten sam adres IP. Adresy IP mogą być również udostępniane, jeśli masz więcej usług niż dostępne. <br> Aby uzyskać więcej informacji, zobacz [udostępnianie adresów IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**dwunastomiesięcznych.**|Klaster Kubernetes|Istniejące moduły Azure IoT Edge Marketplace nie będą działać w klastrze Kubernetes jako platforma hostingu dla IoT Edge na Azure Stack urządzeniu brzegowym.|Przed wdrożeniem modułów na urządzeniu brzegowym Azure Stack należy zmodyfikować moduły. Aby uzyskać więcej informacji, zobacz Modyfikowanie modułów Azure IoT Edge z witryny Marketplace do uruchamiania na Azure Stack urządzeniu brzegowym.<!-- insert link-->|
|**trzynast.**|Kubernetes |Instalacje powiązań oparte na plikach nie są obsługiwane w przypadku Azure IoT Edge w usłudze Kubernetes na Azure Stack urządzeniu brzegowym.|IoT Edge używa warstwy translacji do translacji `ContainerCreate` opcji na konstrukcje Kubernetes. Tworzenie `Binds` map do katalogu hostpath lub tworzenie i w ten sposób instalacji powiązań opartych na plikach nie można powiązać ze ścieżkami w kontenerach IoT Edge.|
|**14,5.**|Kubernetes |Jeśli przeniesiesz własne certyfikaty do IoT Edge i dodasz je na urządzeniu brzegowym Azure Stack, nowe certyfikaty nie zostaną pobrane w ramach aktualizacji wykresów Helm.|Aby obejść ten problem, [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md). Uruchom ponownie `iotedged` i `edgehub` zasobniki.|
|**15000.**|Certyfikaty |W niektórych przypadkach Aktualizacja stanu certyfikatu w lokalnym interfejsie użytkownika może trwać kilka sekund. |Może to dotyczyć następujących scenariuszy w lokalnym interfejsie użytkownika.<ul><li>Kolumna **stan** na stronie **Certyfikaty** .</li><li>Kafelek **zabezpieczenia** na **stronie Wprowadzenie.**</li><li>Kafelek **konfiguracji** na stronie **Przegląd** .</li></ul>  |

## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do wdrożenia urządzenia z programem Azure Stack Edge z procesorem GPU](azure-stack-edge-gpu-deploy-prep.md)

