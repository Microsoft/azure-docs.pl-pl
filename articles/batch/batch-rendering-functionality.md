---
title: Możliwości renderowania
description: Standardowe możliwości Azure Batch są używane do uruchamiania renderowania obciążeń i aplikacji. Partia zadań zawiera określone funkcje do obsługi obciążeń renderowania.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: a2e2cfb71999bd5ab83591448342d4bac1dabdd5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496341"
---
# <a name="azure-batch-rendering-capabilities"></a>Możliwości renderowania Azure Batch

Standardowe możliwości Azure Batch są używane do uruchamiania obciążeń i aplikacji renderowania. Program Batch zawiera również określone funkcje do obsługi obciążeń renderowania.

Aby zapoznać się z omówieniem pojęć związanych z przetwarzaniem wsadowym, w tym pulami, zadaniami i zadaniami, zobacz [ten artykuł](./batch-service-workflow-features.md).

## <a name="batch-pools-using-custom-vm-images-and-standard-application-licensing"></a>Pule usługi Batch używające niestandardowych obrazów maszyn wirtualnych i standardowej licencji aplikacji

Podobnie jak w przypadku innych obciążeń i typów aplikacji, można utworzyć niestandardowy obraz maszyny wirtualnej z wymaganymi aplikacjami do renderowania i wtyczkami. Niestandardowy obraz maszyny wirtualnej jest umieszczany w [galerii obrazów udostępnionych](../virtual-machines/shared-image-galleries.md) i [może służyć do tworzenia pul wsadowych](batch-sig-images.md).

Ciągi wiersza polecenia zadania muszą odwoływać się do aplikacji i ścieżek używanych podczas tworzenia niestandardowego obrazu maszyny wirtualnej.

Większość aplikacji renderowania będzie wymagać licencji uzyskanych z serwera licencji. W przypadku istniejącego lokalnego serwera licencji, zarówno puli, jak i serwera licencji, muszą znajdować się w tej samej [sieci wirtualnej](../virtual-network/virtual-networks-overview.md). Istnieje również możliwość uruchomienia serwera licencji na maszynie wirtualnej platformy Azure z pulą usługi Batch i MASZYNą wirtualną serwera licencji znajdującą się w tej samej sieci wirtualnej.

## <a name="batch-pools-using-rendering-vm-images"></a>Pule usługi Batch używające renderowania obrazów maszyn wirtualnych

> [!IMPORTANT]
> Renderowanie obrazów maszyn wirtualnych i Licencjonowanie za użycie jest [przestarzałe i zostanie wycofane z 29 lutego 2024](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/). Aby użyć usługi Batch do renderowania, [należy użyć niestandardowego obrazu maszyny wirtualnej i standardowej licencji aplikacji.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

### <a name="rendering-application-installation"></a>Renderowanie instalacji aplikacji

Obraz maszyny wirtualnej renderowania w portalu Azure Marketplace można określić w konfiguracji puli, jeśli trzeba będzie używać tylko wstępnie zainstalowanych aplikacji.

Istnieje obraz systemu Windows i obraz CentOS.  W [portalu Azure Marketplace](https://azuremarketplace.microsoft.com)obrazy maszyn wirtualnych można znaleźć, wyszukując frazę "renderowanie wsadowe".

Aby zapoznać się z przykładową konfiguracją puli, zobacz [samouczek renderowania interfejsu wiersza polecenia platformy Azure](./tutorial-rendering-cli.md).  Azure Portal i Batch Explorer udostępniają narzędzia graficznego interfejsu użytkownika, które umożliwiają wybranie renderowania obrazu maszyny wirtualnej podczas tworzenia puli.  W przypadku korzystania z interfejsu API usługi Batch określ następujące wartości właściwości [elementu imagereference](/rest/api/batchservice/pool/add#imagereference) podczas tworzenia puli:

| Publisher | Oferta | SKU | Wersja |
|---------|---------|---------|--------|
| partia | Renderowanie — centos73 | dawania | latest |
| partia | Renderowanie — windows2016 | dawania | latest |

Inne opcje są dostępne, jeśli na maszynach wirtualnych puli są wymagane dodatkowe aplikacje:

* Obraz niestandardowy z galerii obrazów udostępnionych:
  * Przy użyciu tej opcji możesz skonfigurować swoją maszynę wirtualną z odpowiednimi aplikacjami w określonych wersjach, których potrzebujesz. Aby uzyskać więcej informacji, zobacz [Tworzenie puli za pomocą galerii obrazów udostępnionych](batch-sig-images.md). Grupy Autodesk i chaos zmieniły odpowiednio Arnold i V-Ray, aby można było sprawdzić poprawność usługi licencjonowania Azure Batch. Upewnij się, że masz wersje tych aplikacji z tą obsługą, w przeciwnym razie Licencjonowanie za użycie nie będzie działało. Bieżące wersje Maya lub 3ds Max nie wymagają serwera licencji podczas uruchamiania bezobsługowego (w trybie wsadowym/wierszu polecenia). Skontaktuj się z pomocą techniczną platformy Azure, jeśli nie masz pewności, jak kontynuować tę opcję.
* [Pakiety aplikacji](./batch-application-packages.md):
  * Pakowanie plików aplikacji przy użyciu co najmniej jednego pliku ZIP, przekazywanie przez Azure Portal i określanie pakietu w konfiguracji puli. Podczas tworzenia puli maszyn wirtualnych pliki ZIP są pobierane i wyodrębnione pliki.
* Pliki zasobów:
  * Pliki aplikacji są przekazywane do usługi Azure Blob Storage, a w [zadaniu puli](/rest/api/batchservice/pool/add#starttask)należy określić odwołania do plików. Podczas tworzenia puli maszyn wirtualnych pliki zasobów są pobierane na każdą maszynę wirtualną.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Płatność za korzystanie z usługi licencjonowania dla wstępnie zainstalowanych aplikacji

W konfiguracji puli należy określić aplikacje, które będą używane i mieć opłatę licencyjną.

* Określ `applicationLicenses` Właściwość podczas [tworzenia puli](/rest/api/batchservice/pool/add#request-body).  Następujące wartości można określić w tablicy ciągów-"VRay", "Arnold", "3dsMax", "Maya".
* Po określeniu jednej lub kilku aplikacji koszt tych aplikacji zostanie dodany do kosztu maszyn wirtualnych.  Ceny aplikacji są wymienione na [stronie cennika Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Jeśli zamiast tego nawiążesz połączenie z serwerem licencji, aby korzystać z aplikacji do renderowania, nie określaj `applicationLicenses` właściwości.

Możesz użyć Azure Portal lub Batch Explorer do wybrania aplikacji i pokazywania cen aplikacji.

Jeśli podjęto próbę użycia aplikacji, ale aplikacja nie została określona we `applicationLicenses` Właściwości konfiguracji puli lub nie dociera do serwera licencji, wykonanie aplikacji kończy się niepowodzeniem z błędem licencjonowania i kodem zakończenia innym niż zero.

### <a name="environment-variables-for-pre-installed-applications"></a>Zmienne środowiskowe dla wstępnie zainstalowanych aplikacji

Aby można było utworzyć wiersz polecenia dla zadań renderowania, należy określić lokalizację instalacji plików wykonywalnych aplikacji do renderowania.  Systemowe zmienne środowiskowe zostały utworzone w obrazach maszyn wirtualnych portalu Azure Marketplace, których można użyć zamiast określania rzeczywistych ścieżek.  Te zmienne środowiskowe są uzupełnieniem [standardowych zmiennych środowiskowych usługi Batch](./batch-compute-node-environment-variables.md) utworzonych dla każdego zadania.

|Aplikacja|Plik wykonywalny aplikacji|Zmienna środowiskowa|
|---------|---------|---------|
|Autodesk 3ds Max 2021|3dsmaxcmdio.exe|3DSMAX_2021_EXEC|
|Autodesk Maya 2020|render.exe|MAYA_2020_EXEC|
|Grupa chaos V-Ray Standalone|vray.exe|VRAY_4 VRAY_4.10.03_EXEC|
|Wiersz polecenia Arnold 2020|kick.exe|ARNOLD_2020_EXEC|
|Programem Blender|blender.exe|BLENDER_2018_EXEC|

## <a name="azure-vm-families"></a>Rodziny maszyn wirtualnych platformy Azure

Podobnie jak w przypadku innych obciążeń, renderowanie wymagań systemowych aplikacji jest różne i wymagania dotyczące wydajności różnią się w zależności od zadań i projektów.  Na platformie Azure dostępne są duże różne rodziny maszyn wirtualnych, w zależności od wymagań — najniższy koszt, Najlepsza cena/wydajność, Najlepsza wydajność i tak dalej.
Niektóre aplikacje do renderowania, takie jak Arnold, są oparte na procesorach CPU; inne, takie jak cykle V-Ray i Blender, mogą korzystać z procesorów CPU i/lub GPU.
Aby uzyskać opis dostępnych rodzin maszyn wirtualnych i rozmiarów maszyn wirtualnych, [Zobacz typy i rozmiary maszyn wirtualnych](../virtual-machines/sizes.md).

## <a name="low-priority-vms"></a>Maszyny wirtualne o niskim priorytecie

Podobnie jak w przypadku innych obciążeń, maszyny wirtualne o niskim priorytecie mogą być wykorzystywane w pulach usługi Batch do renderowania.  Maszyny wirtualne o niskim priorytecie działają tak samo jak regularne dedykowane maszyny wirtualne, ale wykorzystują nadwyżkową pojemność platformy Azure i są dostępne dla dużego rabatu.  Użycie maszyn wirtualnych o niskim priorytecie polega na tym, że te maszyny wirtualne mogą nie być dostępne do przydzielenia lub mogą zostać przeniesione w dowolnym momencie, w zależności od dostępnej pojemności. Z tego powodu maszyny wirtualne o niskim priorytecie nie są odpowiednie dla wszystkich zadań renderowania. Na przykład, jeśli obrazy potrwają wiele godzin w celu renderowania, prawdopodobnie wyrenderowanie tych obrazów zostało przerwane i uruchomione ze względu na to, że maszyny wirtualne są zaakceptowane.

Aby uzyskać więcej informacji na temat właściwości maszyn wirtualnych o niskim priorytecie i różnych sposobów konfigurowania ich przy użyciu usługi Batch, zobacz [Korzystanie z maszyn wirtualnych o niskim priorytecie w usłudze Batch](./batch-low-pri-vms.md).

## <a name="jobs-and-tasks"></a>Zadania

Zadania i zadania nie wymagają obsługi specyficznej dla renderowania.  Głównym elementem konfiguracji jest wiersz poleceń zadania, który musi odwoływać się do wymaganej aplikacji.
Gdy są używane obrazy maszyn wirtualnych w portalu Azure Marketplace, najlepszym rozwiązaniem jest użycie zmiennych środowiskowych w celu określenia ścieżki i pliku wykonywalnego aplikacji.

## <a name="next-steps"></a>Następne kroki

Przykłady renderowania wsadowego Wypróbuj dwa samouczki:

* [Renderowanie przy użyciu interfejsu wiersza polecenia platformy Azure](./tutorial-rendering-cli.md)
* [Renderowanie przy użyciu programu Batch Explorer](./tutorial-rendering-batchexplorer-blender.md)