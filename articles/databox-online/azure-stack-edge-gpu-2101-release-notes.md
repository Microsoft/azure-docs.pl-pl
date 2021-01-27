---
title: Informacje o wersji Azure Stack EDGE Pro 2101
description: W tym artykule opisano krytyczne problemy i rozwiązania dla Azure Stack brzeg Pro z uruchomioną wersją 2101.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/19/2021
ms.author: alkohli
ms.openlocfilehash: 8158868a39bf8a1fe03a620f37e4dcb1c9adc14e
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805178"
---
# <a name="azure-stack-edge-2101-release-notes"></a>Azure Stack Edge 2101 — informacje o wersji

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących wydania 2101 na urządzeniach brzegowych Azure Stack. Te informacje o wersji dotyczą Azure Stack brzegowych procesorów GPU, Azure Stack EDGE Pro R i Azure Stack Edge. Funkcje i problemy, które odpowiadają określonemu modelowi, są wywoływane wszędzie tam, gdzie ma to zastosowanie.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem urządzenia uważnie zapoznaj się z informacjami zawartymi w informacjach o wersji.

Ten artykuł ma zastosowanie do **Azure Stack** w wersji 2101, która jest mapowana na numer wersji oprogramowania **2.2.1473.2521**.

## <a name="whats-new"></a>Co nowego

Poniższe nowe funkcje są dostępne w wersji Azure Stack Edge 2101. 

- **Ogólna dostępność urządzeń z systemem Azure Stack EDGE Pro r i Azure Stack Edge mini r urządzenia** — począwszy od tej wersji, dostępne są Azure Stack brzeg Pro r i Azure Stack Edge mini r urządzenia. Aby uzyskać więcej informacji, zobacz [co to jest Azure Stack Edge z krawędzią Pro r](azure-stack-edge-j-series-overview.md) i [co to jest Azure Stack Edge mini R](azure-stack-edge-k-series-overview.md).  
- **Zarządzanie chmurą Virtual Machines** — począwszy od tej wersji, można tworzyć maszyny wirtualne na urządzeniu i zarządzać nimi za pośrednictwem Azure Portal. Aby uzyskać więcej informacji, zobacz [wdrażanie maszyn wirtualnych za pośrednictwem Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
- **Integracja z Azure monitor** — teraz można używać Azure monitor do monitorowania kontenerów z aplikacji obliczeniowych uruchomionych na urządzeniu. Magazyn metryk Azure Monitor nie jest obsługiwany w tej wersji. Aby uzyskać więcej informacji, zobacz jak [włączyć Azure monitor na urządzeniu](azure-stack-edge-gpu-enable-azure-monitor.md).
- **Rejestr kontenerów krawędzi** — w tej wersji jest dostępny rejestr kontenerów brzegowych, który udostępnia repozytorium na urządzeniu brzegowym. Za pomocą tego rejestru można przechowywać obrazy kontenerów i zarządzać nimi. Aby uzyskać więcej informacji, zobacz [Włącz rejestr kontenerów brzegowych](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). 
- **Wirtualna sieć prywatna (VPN)** — umożliwia korzystanie z sieci VPN w celu zapewnienia innej warstwy szyfrowania danych przesyłanych między urządzeniami i chmurą. Sieć VPN jest dostępna tylko na Azure Stack EDGE Pro R i Azure Stack Edge mini R. Aby uzyskać więcej informacji, zobacz jak [skonfigurować sieć VPN na urządzeniu](azure-stack-edge-mini-r-configure-vpn-powershell.md). 
- **Obróć klucze szyfrowania-at-REST** — teraz można obrócić klucze szyfrowania w czasie spoczynku, które są używane do ochrony dysków na urządzeniu. Ta funkcja jest dostępna tylko w przypadku urządzeń z Azure Stack EDGE Pro R i Azure Stack Edge mini R. Aby uzyskać więcej informacji, zobacz [obracanie kluczy szyfrowania w czasie spoczynku](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#manage-access-to-device-data).
- **Aktywne rejestrowanie** — Rozpoczynanie tej wersji. możesz włączyć funkcję aktywnej kolekcji dzienników na urządzeniu w oparciu o wskaźniki kondycji systemu, aby ułatwić efektywne Rozwiązywanie problemów z urządzeniami. Aby uzyskać więcej informacji, zapoznaj się z tematem [proaktywne zbieranie dzienników na urządzeniu](azure-stack-edge-gpu-proactive-log-collection.md).


## <a name="known-issues-in-2101-release"></a>Znane problemy w wersji 2101

Poniższa tabela zawiera podsumowanie znanych problemów w wersji 2101.

| Nie. | Cechy | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
|**1.**|Funkcje w wersji zapoznawczej |W tej wersji w wersji zapoznawczej są dostępne następujące funkcje: lokalne Azure Resource Manager, maszyny wirtualne, zarządzanie chmurą maszyn wirtualnych, usługa Azure ARC z obsługą Kubernetes, Sieć VPN dla Azure Stack EDGE Pro R i Azure Stack Edge mini R, wiele procesów (MPS) dla Azure Stack EDGE Pro GPU  |Te funkcje będą ogólnie dostępne w nowszych wersjach. |
|**2.**|Pulpit nawigacyjny Kubernetes | Punkt końcowy *https* dla pulpitu nawigacyjnego Kubernetes z certyfikatem SSL nie jest obsługiwany. | |
|**3.**|Kubernetes |Rejestr kontenerów krawędzi nie działa, gdy jest włączony serwer proxy sieci Web.|Ta funkcja będzie dostępna w przyszłych wydaniach. |
|**4.**|Kubernetes |Rejestr kontenerów krawędzi nie działa z modułami IoT Edge.| |
|**5000.**|Kubernetes |Kubernetes nie obsługuje ":" w nazwach zmiennych środowiskowych, które są używane przez aplikacje platformy .NET. Jest to również wymagane w przypadku, gdy moduł IoT Edge Event Grid może działać na Azure Stack urządzeniu brzegowym i innych aplikacjach. Aby uzyskać więcej informacji, zobacz [dokumentację ASP.NET Core](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables).|Zamień ciąg ":" przez podwójne podkreślenie. Aby uzyskać więcej informacji, zobacz [Kubernetes Issue](https://github.com/kubernetes/kubernetes/issues/53201)|
|**ust.** |Azure Arc + klaster Kubernetes |Domyślnie po `yamls` usunięciu zasobu z repozytorium git odpowiednie zasoby nie są usuwane z klastra Kubernetes.  |Należy ustawić `--sync-garbage-collection`  w łuku OperatorParams, aby zezwolić na usunięcie zasobów po usunięciu z repozytorium git. Aby uzyskać więcej informacji, zobacz [Usuwanie konfiguracji](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters). |
|**7.**|NFS |Aplikacje korzystające z instalacji udziałów NFS na urządzeniu do zapisywania danych powinny używać zapisu wyłącznego. Dzięki temu zapis jest zapisywany na dysku.| |
|**0,8.**|Konfiguracja obliczeń |Konfiguracja obliczeń kończy się niepowodzeniem w konfiguracjach sieci, w których bramy lub przełączniki lub routery odpowiadają na żądania protokołu ARP (Address Resolution Protocol) dla systemów, które nie istnieją w sieci.| |
|**9.**|Obliczenia i Kubernetes |Jeśli Kubernetes jest najpierw skonfigurowany na urządzeniu, przejmuje wszystkie dostępne procesory GPU. W związku z tym nie jest możliwe tworzenie Azure Resource Manager maszyn wirtualnych przy użyciu procesorów GPU po skonfigurowaniu Kubernetes. |Jeśli urządzenie ma 2 procesory GPU, można utworzyć 1 maszynę wirtualną, która używa procesora GPU, a następnie skonfigurować Kubernetes. W takim przypadku Kubernetes będzie używać pozostałego dostępnego 1 procesora GPU. |


## <a name="known-issues-from-previous-releases"></a>Znane problemy z poprzednich wersji

Poniższa tabela zawiera podsumowanie znanych problemów przeprowadzonych w porównaniu z poprzednimi wersjami.

| Nie. | Cechy | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
| **1.** |Azure Stack EDGE Pro + Azure SQL | Tworzenie bazy danych SQL wymaga dostępu administratora.   |Wykonaj następujące czynności zamiast kroków 1-2 w temacie [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>W lokalnym interfejsie użytkownika urządzenia Włącz interfejs obliczeniowy. Wybierz pozycję **obliczenia > port # > Włącz dla obliczeń > Zastosuj.**</li><li>Pobierz `sqlcmd` na komputerze klienckim z https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Nawiąż połączenie z adresem IP interfejsu obliczeniowego (włączonym portem), dodając znak ", 1401" na końcu adresu.</li><li>Końcowe polecenie będzie wyglądać następująco: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Po wykonaniu tej czynności kroki 3-4 z bieżącej dokumentacji powinny być identyczne. </li></ul> |
| **2.** |Odśwież| Przyrostowe zmiany w obiektach Blob przywrócone za pośrednictwem **odświeżania** nie są obsługiwane |W przypadku punktów końcowych obiektów BLOB częściowe aktualizacje obiektów BLOB po odświeżeniu mogą spowodować, że aktualizacje nie są przekazywane do chmury. Na przykład sekwencja akcji, takich jak:<ul><li>Utwórz obiekt BLOB w chmurze. Lub Usuń wcześniej przekazany obiekt BLOB z urządzenia.</li><li>Odśwież obiekt BLOB z chmury do urządzenia, korzystając z funkcji odświeżania.</li><li>Zaktualizuj tylko część obiektu BLOB przy użyciu interfejsów API REST usługi Azure SDK.</li></ul>Te akcje mogą spowodować, że zaktualizowane sekcje obiektu BLOB nie zostaną zaktualizowane w chmurze. <br>**Obejście**: Użyj narzędzi, takich jak Robocopy, lub zwykłych kopii plików w Eksploratorze lub wierszu polecenia, aby zastąpić całe obiekty blob.|
|**3.**|Ograniczanie przepływności|W przypadku ograniczenia przepustowości Jeśli nowe zapisy na urządzeniu nie są dozwolone, zapis przez klienta NFS kończy się niepowodzeniem z powodu błędu "odmowa uprawnień".| Błąd zostanie wyświetlony w następujący sposób:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: nie można utworzyć katalogu "test": odmowa uprawnień|
|**4.**|Pozyskiwanie Blob Storage|W przypadku korzystania z programu AzCopy w wersji 10 na potrzeby pozyskiwania usługi BLOB Storage należy uruchomić AzCopy z następującym argumentem: `Azcopy <other arguments> --cap-mbps 2000`| Jeśli te limity nie są dostępne dla AzCopy, może potencjalnie wysłać do urządzenia dużą liczbę żądań, co spowodowało problemy z usługą.|
|**5000.**|Konta magazynu warstwowego|W przypadku korzystania z kont magazynu warstwowego obowiązują następujące kwestie:<ul><li> Obsługiwane są tylko blokowe obiekty blob. Stronicowe obiekty blob nie są obsługiwane.</li><li>Brak obsługi interfejsu API migawek lub kopiowania.</li><li> Pozyskiwanie obciążeń usługi Hadoop za pośrednictwem programu `distcp` nie jest obsługiwane, ponieważ wielokrotnie używa operacji kopiowania.</li></ul>||
|**ust.**|Połączenie z udziałem NFS|Jeśli wiele procesów jest kopiowanych do tego samego udziału, a `nolock` atrybut nie jest używany, podczas kopiowania mogą pojawić się błędy.|Aby `nolock` skopiować pliki do udziału NFS, ten atrybut musi być przekazaniem do polecenia instalacji. Na przykład: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Klaster Kubernetes|W przypadku zastosowania aktualizacji na urządzeniu, na którym działa klaster Kubernetes, maszyny wirtualne Kubernetes zostaną ponownie uruchomione i ponownie uruchamiane. W tym przypadku tylko te, które są wdrożone z wybranymi replikami są automatycznie przywracane po aktualizacji.  |Jeśli utworzono poszczególne zbiory () poza kontrolerem replikacji bez określania zestawu replik, te zasobniki nie zostaną przywrócone automatycznie po aktualizacji urządzenia. Konieczne będzie przywrócenie tych zasobników.<br>Zestaw replik zastępuje te, które zostały usunięte z jakiegokolwiek powodu, takie jak awaria węzła lub nieprzerwane uaktualnienie węzła. Z tego powodu zalecamy użycie zestawu replik, nawet jeśli aplikacja wymaga tylko jednego elementu.|
|**0,8.**|Klaster Kubernetes|Kubernetes na Azure Stack EDGE Pro jest obsługiwana tylko z Helm v3 lub nowszym. Aby uzyskać więcej informacji, zapoznaj się z [często zadawanymi pytaniami: usuwanie obiektu do](https://v3.helm.sh/docs/faq/)wykonania.|
|**9.**|Platforma Kubernetes z włączoną usługą Azure Arc |W przypadku wersji GA usługa Azure ARC z włączonym Kubernetes jest aktualizowana z wersji 0.1.18 na 0.2.9. Ponieważ aktualizacja Kubernetes z funkcją Azure Arc nie jest obsługiwana na Azure Stack urządzeniu brzegowym, należy ponownie wdrożyć usługę Azure ARC z obsługą Kubernetes.|Wykonaj następujące kroki:<ol><li>[Zastosuj oprogramowanie urządzenia i aktualizacje Kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Nawiąż połączenie z [interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Usuń istniejącego agenta usługi Azure Arc. Typ: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Wdróż [usługę Azure Arc na nowym zasobie](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Nie używaj istniejącego zasobu usługi Azure Arc.</li></ol>|
|**dziesięć.**|Platforma Kubernetes z włączoną usługą Azure Arc|Wdrożenia usługi Azure Arc nie są obsługiwane, jeśli serwer proxy sieci Web jest skonfigurowany na urządzeniu Azure Stack EDGE Pro.||
|**11.**|Kubernetes |Port 31000 jest zarezerwowany dla pulpitu nawigacyjnego Kubernetes. Port 31001 jest zarezerwowany dla rejestru kontenerów krawędzi. Podobnie w konfiguracji domyślnej adresy IP 172.28.0.1 i 172.28.0.10 są zarezerwowane odpowiednio dla usług Kubernetes Service i Core DNS.|Nie używaj zarezerwowanych adresów IP.|
|**dwunastomiesięcznych.**|Kubernetes |Kubernetes obecnie nie zezwala na wiele protokołów usługi równoważenia obciążenia. Na przykład usługa DNS, która będzie musiała nasłuchiwać zarówno TCP, jak i UDP. |Aby obejść to ograniczenie Kubernetes z MetalLB, można utworzyć dwie usługi (jeden dla TCP, jeden dla UDP) w tym samym selektorze pod. Te usługi używają tego samego klucza udostępniania i specyfikacji loadBalancerIP, aby współużytkować ten sam adres IP. Adresy IP mogą być również udostępniane, jeśli masz więcej usług niż dostępne. <br> Aby uzyskać więcej informacji, zobacz [udostępnianie adresów IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**trzynast.**|Klaster Kubernetes|Istniejące moduły Azure IoT Edge Marketplace mogą wymagać modyfikacji do uruchomienia na IoT Edge na Azure Stack urządzeniu brzegowym.|Aby uzyskać więcej informacji, zobacz Modyfikowanie modułów Azure IoT Edge z witryny Marketplace do uruchamiania na Azure Stack urządzeniu brzegowym.<!-- insert link-->|
|**14,5.**|Kubernetes |Instalacje powiązań oparte na plikach nie są obsługiwane w przypadku Azure IoT Edge w usłudze Kubernetes na Azure Stack urządzeniu brzegowym.|IoT Edge używa warstwy translacji do translacji `ContainerCreate` opcji na konstrukcje Kubernetes. Tworzenie `Binds` map w `hostpath` katalogu i w ten sposób nie można powiązać instalacji powiązań opartych na plikach z ścieżkami w kontenerach IoT Edge. Jeśli to możliwe, zamapuj katalog nadrzędny.|
|**15000.**|Kubernetes |Jeśli przeniesiesz własne certyfikaty do IoT Edge i dodasz je na urządzeniu brzegowym Azure Stack po skonfigurowaniu obliczeń na urządzeniu, nowe certyfikaty nie zostaną pobrane.|Aby obejść ten problem, należy przekazać certyfikaty przed skonfigurowaniem obliczeń na urządzeniu. Jeśli obliczenia są już skonfigurowane, Połącz się z [interfejsem programu PowerShell urządzenia i uruchom IoT Edge polecenia](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Uruchom ponownie `iotedged` i `edgehub` zasobniki.|
|**16.**|Certyfikaty |W niektórych przypadkach Aktualizacja stanu certyfikatu w lokalnym interfejsie użytkownika może trwać kilka sekund. |Może to dotyczyć następujących scenariuszy w lokalnym interfejsie użytkownika.<ul><li>Kolumna **stan** na stronie **Certyfikaty** .</li><li>Kafelek **zabezpieczenia** na **stronie Wprowadzenie.**</li><li>Kafelek **konfiguracji** na stronie **Przegląd** .</li></ul>  |
|**7.**|IoT Edge |Moduły wdrożone za pomocą IoT Edge nie mogą używać sieci hosta. | |
|**postanowienia.**|Obliczenia i Kubernetes |Obliczenia/Kubernetes nie obsługują serwera proxy sieci Web NTLM. ||
|**19.**|Obliczenia i serwer proxy sieci Web + aktualizacja |W przypadku skonfigurowania obliczeń przy użyciu serwera proxy sieci Web, aktualizacja obliczeń może zakończyć się niepowodzeniem. |Zalecamy wyłączenie obliczeń przed aktualizacją. |
|**20C.**|Kubernetes + Update |Starsze wersje oprogramowania, takie jak wersje 2008, mają problem z aktualizacją stanu wyścigu, który powoduje, że aktualizacja kończy się niepowodzeniem z ClusterConnectionException. |Korzystanie z nowszych kompilacji powinno pomóc uniknąć tego problemu. Jeśli nadal widzisz ten problem, obejście tego problemu polega na ponownym uaktualnieniu i zapewnieniu poprawnego działania.|


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie urządzenia](azure-stack-edge-gpu-install-update.md)
