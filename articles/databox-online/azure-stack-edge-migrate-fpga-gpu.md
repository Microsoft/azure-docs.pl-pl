---
title: Przewodnik dotyczący migracji dla Azure Stack EDGE Pro FPGA na urządzenie fizyczne GPU
description: Ten przewodnik zawiera instrukcje dotyczące migrowania obciążeń z Azure Stack urządzenia z Azure Stack Edge FPGA
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: alkohli
ms.openlocfilehash: 430e34a1ca631be00ef46170affd4b56c79894a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566408"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Migrowanie obciążeń z Azure Stack Edge FPGA z krawędzią Pro do Azure Stack

W tym artykule opisano sposób migrowania obciążeń i danych z Azure Stack urządzenia z Azure Stack Edge FPGA Proces migracji rozpoczyna się od porównania dwóch urządzeń, planu migracji i przeglądu zagadnień związanych z migracją. Procedura migracji zawiera szczegółowe kroki kończące się weryfikacją i oczyszczaniem urządzenia.

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

## <a name="about-migration"></a>Informacje o migracji

Migracja to proces przenoszenia obciążeń i danych aplikacji z jednej lokalizacji magazynu do innej. Oznacza to, że dokładną kopię bieżących danych organizacji z jednego urządzenia magazynującego do innego urządzenia magazynującego, najlepiej bez zakłócania ani wyłączania aktywnych aplikacji, a następnie przekierowania wszystkich działań wejścia/wyjścia (we/wy) do nowego urządzenia. 

Ten przewodnik migracji zawiera przewodnik krok po kroku dotyczący kroków wymaganych do migracji danych z urządzenia z systemem Azure Stack EDGE Pro FPGA do urządzenia z systemem Azure Stack Edge. Ten dokument jest przeznaczony dla specjalistów IT i pracowników merytorycznych odpowiedzialnych za obsługę i wdrażanie Azure Stack urządzeń brzegowych w centrum danych oraz zarządzanie nimi.

W tym artykule Azure Stack FPGA EDGE Pro jest określana jako urządzenie *źródłowe* , a Azure Stack urządzenia GPU z krawędzią Pro to urządzenie *docelowe* . 

## <a name="comparison-summary"></a>Podsumowanie porównania

Ta sekcja zawiera porównawcze podsumowanie możliwości między urządzeniami z systemem Azure Stack Edge z procesorem GPU a pakietem Azure Stack EDGE Pro FPGA. Sprzęt w urządzeniu źródłowym i docelowym jest w dużym stopniu identyczny; tylko karta akceleracja sprzętowa i pojemność magazynu mogą się różnić.<!--Please verify: These components MAY, but need not necessarily, differ?-->

|    Możliwość  | Azure Stack krawędź procesora GPU Pro (urządzenie docelowe)  | Azure Stack EDGE Pro FPGA (urządzenie źródłowe)|
|----------------|-----------------------|------------------------|
| Sprzęt       | Przyspieszenie sprzętowe: 1 lub 2 procesory GPU T4 <br> Specyfikacje obliczeniowe, pamięć, interfejs sieciowy, jednostka zasilacza i przewód zasilający są takie same jak w przypadku urządzenia z FPGA.  | Przyspieszenie sprzętowe: Intel Arria 10 FPGA <br> Specyfikacje obliczeniowe, pamięć, interfejs sieciowy, jednostka zasilacza i przewód zasilający są takie same jak w przypadku urządzenia z procesorem GPU.          |
| Przydatny magazyn | 4,19 TB <br> Po zapewnieniu odporności na parzystość i użycia wewnętrznego | 12,5 TB <br> Po zarezerwowania miejsca do użytku wewnętrznego |
| Zabezpieczenia       | Certyfikaty |                                                     |
| Pakiety robocze      | IoT Edge obciążeń <br> Obciążenia maszyn wirtualnych <br> Obciążenia platformy Kubernetes| IoT Edge obciążeń |
| Ceny        | [Cennik](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Cennik](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Plan migracji

Aby utworzyć plan migracji, należy wziąć pod uwagę następujące informacje:

- Opracowywanie harmonogramu migracji. 
- Podczas migrowania danych może wystąpić Przestój. Zaleca się zaplanowanie migracji w czasie trwania okna obsługi przestoju, ponieważ proces jest zakłócany. Konfiguracje zostaną skonfigurowane i przywrócone w tym przestoju zgodnie z opisem w dalszej części tego dokumentu.
- Zapoznaj się z całkowitą długością przestoju i przekaż ją wszystkim uczestnikom projektu.
- Określ dane lokalne, które należy zmigrować z urządzenia źródłowego. Ze względów bezpieczeństwa upewnij się, że wszystkie dane w istniejącym magazynie mają najnowszą kopię zapasową. 


## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji 

Przed przejściem do migracji należy wziąć pod uwagę następujące informacje: 

- Nie można uaktywnić urządzenia GPU z Azure Stack Edge przy użyciu zasobu Azure Stack EDGE Pro FPGA. Należy utworzyć nowy zasób dla urządzenia GPU z systemem Azure Stack Edge, zgodnie z opisem w temacie [Tworzenie kolejności procesora GPU w programie Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
- Modele Machine Learning wdrożone na urządzeniu źródłowym, które używały FPGA należy zmienić dla urządzenia docelowego z procesorem GPU. Aby uzyskać pomoc dotyczącą modeli, możesz skontaktować się z pomoc techniczna firmy Microsoft. Modele niestandardowe wdrożone na urządzeniu źródłowym, które nie korzystały z FPGA (używany tylko procesor CPU), powinny być wykonywane na urządzeniu docelowym (przy użyciu procesora CPU).
- Moduły IoT Edge wdrożone na urządzeniu źródłowym mogą wymagać zmian, aby można było pomyślnie wdrożyć moduły na urządzeniu docelowym. 
- Urządzenie źródłowe obsługuje protokoły NFS 3,0 i 4,1. Urządzenie docelowe obsługuje tylko protokół NFS 3,0.
- Urządzenie źródłowe obsługuje protokoły SMB i NFS. Urządzenie docelowe obsługuje magazyn za pośrednictwem protokołu REST przy użyciu kont magazynu oprócz protokołów SMB i NFS dla udziałów.
- Dostęp do udziału na urządzeniu źródłowym jest za pośrednictwem adresu IP, podczas gdy dostęp do udziału na urządzeniu docelowym odbywa się za pośrednictwem nazwy urządzenia.

## <a name="migration-steps-at-a-glance"></a>Szybkie etapy migracji

Ta tabela zawiera podsumowanie ogólnego przepływu migracji, opisujące kroki wymagane do migracji i lokalizację, w której należy wykonać te kroki.

| W tej fazie | Wykonaj ten krok| Na tym urządzeniu |
|---------------|-------------|----------------|
| Przygotuj urządzenie źródłowe *       | 1. Rejestruj dane konfiguracji <br>2. Utwórz kopię zapasową danych udziału <br>3. Przygotuj IoT Edge obciążeń| Urządzenie źródłowe  |
| Przygotuj urządzenie docelowe *       |1. Utwórz nowe zamówienie <br>2. Skonfiguruj i aktywuj| Urządzenie docelowe  |
| Migrowanie danych       | 1. Migrowanie danych z udziałów <br>2. Wdróż ponownie IoT Edge obciążeniami| Urządzenie docelowe  |
| Weryfikacja danych            |Sprawdź zmigrowane dane |Urządzenie docelowe  |
| Wyczyść, Wróć              |Wymazywanie danych i zwracanie| Urządzenie źródłowe  |

**Urządzenie źródłowe i docelowe można przygotować równolegle.*

## <a name="prepare-source-device"></a>Przygotuj urządzenie źródłowe

Przygotowanie obejmuje identyfikację udziałów w chmurze, udziałów lokalnych oraz IoT Edge modułów wdrożonych na urządzeniu. 

### <a name="1-record-configuration-data"></a>1. Rejestruj dane konfiguracji

Wykonaj te kroki na urządzeniu źródłowym za pośrednictwem lokalnego interfejsu użytkownika.

Zapisz dane konfiguracji na urządzeniu *źródłowym* . [Lista kontrolna wdrożenia](azure-stack-edge-gpu-deploy-checklist.md) ułatwia rejestrowanie konfiguracji urządzeń. Podczas migracji te informacje konfiguracyjne zostaną użyte do skonfigurowania nowego urządzenia docelowego. 

### <a name="2-back-up-share-data"></a>2. Utwórz kopię zapasową danych udziału

Dane urządzenia mogą mieć jeden z następujących typów:

- Dane w udziałach w chmurze Edge
- Dane w udziałach lokalnych

#### <a name="data-in-edge-cloud-shares"></a>Dane w udziałach w chmurze Edge

W chmurze są udostępniane dane warstwy z urządzenia do platformy Azure. Wykonaj te kroki na urządzeniu *źródłowym* za pośrednictwem Azure Portal. 

- Utwórz listę wszystkich udziałów w chmurze i użytkowników, którzy znajdują się na urządzeniu źródłowym.
- Utwórz listę wszystkich harmonogramów przepustowości, których dysponujesz. Te harmonogramy przepustowości zostaną ponownie utworzone na urządzeniu docelowym.
- W zależności od dostępnej przepustowości sieci Skonfiguruj harmonogramy przepustowości na urządzeniu, aby zmaksymalizować dane warstwowo do chmury. Pozwala to zminimalizować dane lokalne na urządzeniu.
- Upewnij się, że udziały są w pełni warstwową chmurą. Obsługę warstw można potwierdzić, sprawdzając stan udziału w Azure Portal.  

#### <a name="data-in-edge-local-shares"></a>Dane w udziałach lokalnych Edge

Dane w udziałach lokalnych programu Edge pozostają na urządzeniu. Wykonaj te kroki na urządzeniu *źródłowym* za pośrednictwem Azure Portal. 

- Utwórz listę udziałów lokalnych Edge na urządzeniu.
- Ponieważ przejdziesz do jednorazowej migracji danych, Utwórz kopię danych udziału lokalnego na innym serwerze lokalnym. Aby skopiować dane, można użyć narzędzi do kopiowania, takich jak `robocopy` (SMB) lub `rsync` (NFS). Opcjonalnie można już wdrożyć rozwiązanie do ochrony danych innej firmy, aby utworzyć kopię zapasową danych w udziałach lokalnych. Następujące rozwiązania innych firm są obsługiwane w przypadku urządzeń z systemem Azure Stack Edge FPGA

    | Oprogramowanie innych producentów           | Odwołanie do rozwiązania                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Cohesity.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą CommVault.          |
    | Firmę                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Veeam. |


### <a name="3-prepare-iot-edge-workloads"></a>3. Przygotuj IoT Edge obciążeń

- Jeśli wdrożono moduły IoT Edge i korzystasz z przyspieszenia FPGA, może być konieczne zmodyfikowanie modułów, zanim zostaną one uruchomione na urządzeniu GPU. Postępuj zgodnie z instrukcjami w temacie [Modify IoT Edge modules](azure-stack-edge-gpu-modify-fpga-modules-gpu.md). 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Przygotowywanie urządzenia docelowego

### <a name="1-create-new-order"></a>1. Utwórz nowe zamówienie

Należy utworzyć nowe zamówienie (i nowy zasób) dla urządzenia *docelowego* . Urządzenie docelowe musi zostać aktywowane względem zasobu procesora GPU, a nie do zasobu FPGA.

Aby złożyć zamówienie, [Utwórz nowy zasób Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) w Azure Portal.


### <a name="2-set-up-activate"></a>2. Skonfiguruj, Aktywuj

Należy skonfigurować i aktywować urządzenie *docelowe* dla nowego utworzonego wcześniej zasobu. 

Wykonaj następujące kroki, aby skonfigurować urządzenie *docelowe* za pośrednictwem Azure Portal:

1. Zbierz informacje wymagane na liście [kontrolnej wdrożenia](azure-stack-edge-gpu-deploy-checklist.md). Możesz użyć zapisanych informacji z konfiguracji urządzenia źródłowego. 
1. [Rozpakowywanie](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), montowanie w [stojaku](azure-stack-edge-gpu-deploy-install.md#rack-the-device) i Podłączanie [kabli do urządzenia](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Nawiąż połączenie z lokalnym interfejsem użytkownika urządzenia](azure-stack-edge-gpu-deploy-connect.md).
1. Skonfiguruj sieć przy użyciu innego zestawu adresów IP (jeśli jest używany statyczny adres IPv4) niż te, które były używane dla starego urządzenia. Zobacz jak [skonfigurować ustawienia sieciowe](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Przypisz tę samą nazwę urządzenia co stare urządzenie i podaj domenę DNS. Zobacz jak [skonfigurować ustawienie urządzenia](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Skonfiguruj certyfikaty na nowym urządzeniu. Zobacz jak [skonfigurować certyfikaty](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Pobierz klucz aktywacji z Azure Portal i aktywuj nowe urządzenie. Zapoznaj się z tematem jak [aktywować urządzenie](azure-stack-edge-gpu-deploy-activate.md).

Teraz można przystąpić do przywracania danych udziału i wdrażania obciążeń, które były uruchomione na starym urządzeniu.

## <a name="migrate-data"></a>Migrowanie danych

Teraz dane z urządzenia źródłowego zostaną skopiowane do udziałów w chmurze brzegowych i udziałów lokalnych na urządzeniu *docelowym* .

### <a name="1-from-edge-cloud-shares"></a>1. z udziałów w chmurze Edge

Wykonaj następujące kroki, aby zsynchronizować dane na temat udziałów w chmurze Edge na urządzeniu docelowym:

1. [Dodaj udziały](azure-stack-edge-j-series-manage-shares.md#add-a-share) odpowiadające nazwom udziałów utworzonym na urządzeniu źródłowym. Podczas tworzenia udziałów upewnij się, że **Opcja Wybierz kontener obiektów BLOB** jest ustawiona na **Użyj istniejącej**, a następnie wybierz kontener, który był używany z poprzednim urządzeniem.
1. [Dodaj użytkowników](azure-stack-edge-j-series-manage-users.md#add-a-user) , którzy mieli dostęp do poprzedniego urządzenia.
1. [Odśwież dane udziału](azure-stack-edge-j-series-manage-shares.md#refresh-shares) z platformy Azure. Odświeżenie udziału spowoduje pobranie wszystkich danych w chmurze z istniejącego kontenera do udziałów.
1. Utwórz ponownie harmonogramy przepustowości, które mają być skojarzone z udziałami. Szczegółowe instrukcje można znaleźć w temacie [Dodawanie harmonogramu przepustowości](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule) .


### <a name="2-from-edge-local-shares"></a>2. z udziałów lokalnych Edge

Być może wdrożono rozwiązanie do tworzenia kopii zapasowych innych firm, aby chronić lokalne dane udziałów dla obciążeń IoT. Teraz musisz przywrócić te dane.

Po całkowitym skonfigurowaniu urządzenia zastępczego włącz je na potrzeby lokalnego magazynu. 

Wykonaj następujące kroki, aby odzyskać dane z udziałów lokalnych:

1. [Skonfiguruj obliczenia na urządzeniu](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Dodaj wszystkie udziały lokalne na urządzeniu docelowym. Szczegółowe instrukcje można znaleźć w temacie [Dodawanie udziału lokalnego](azure-stack-edge-gpu-manage-shares.md#add-a-local-share).
1. Uzyskanie dostępu do udziałów SMB na urządzeniu źródłowym spowoduje użycie adresów IP na urządzeniu docelowym. Zobacz [nawiązywanie połączenia z udziałem SMB na Azure Stack Edge GPU](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-smb-share). Aby nawiązać połączenie z udziałami NFS na urządzeniu docelowym, należy użyć nowych adresów IP skojarzonych z urządzeniem. Zobacz [nawiązywanie połączenia z udziałem NFS w witrynie Azure Stack Edge — procesor GPU](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-nfs-share). 

    Jeśli dane udziału zostały skopiowane na serwer pośredni za pośrednictwem protokołu SMB lub NFS, można skopiować dane z serwera pośredniego do udziałów na urządzeniu docelowym. Jeśli urządzenie źródłowe i docelowe są w *trybie online*, można także skopiować dane bezpośrednio z urządzenia źródłowego.

    Jeśli używasz oprogramowania innych firm do tworzenia kopii zapasowych danych w udziałach lokalnych, musisz uruchomić procedurę odzyskiwania, która jest dostępna w ramach wybranego rozwiązania do ochrony danych. Zobacz odwołania w poniższej tabeli.

    | Oprogramowanie innych producentów           | Odwołanie do rozwiązania                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Cohesity.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą CommVault. |
    | Firmę                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z firmą Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Aby uzyskać szczegółowe informacje, skontaktuj się z Veeam. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. Wdróż ponownie IoT Edge obciążeniami

Po przygotowaniu modułów IoT Edge należy wdrożyć IoT Edge obciążenia na urządzeniu docelowym. Jeśli występują błędy podczas wdrażania modułów IoT Edge, zobacz:

- [Typowe problemy i rozwiązania dla Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md). 
- [Błędy środowiska uruchomieniowego IoT Edge](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors).

## <a name="verify-data"></a>Weryfikacja danych

Po migracji Sprawdź, czy wszystkie dane zostały zmigrowane i czy obciążenia zostały wdrożone na urządzeniu docelowym.

## <a name="erase-data-return"></a>Wymazywanie danych, zwracanie

Po zakończeniu migracji danych Wymaż dane lokalne i zwróć urządzenie źródłowe. Postępuj zgodnie z instrukcjami w temacie [przywracanie urządzenia z Azure Stack Edge](azure-stack-edge-return-device.md).


## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak wdrażać obciążenia IoT Edge na urządzeniach z systemem Azure Stack EDGE Pro GPU](azure-stack-edge-gpu-deploy-compute-module-simple.md)