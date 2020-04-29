---
title: Możliwości renderowania
description: Standardowe możliwości Azure Batch są używane do uruchamiania renderowania obciążeń i aplikacji. Partia zadań zawiera określone funkcje do obsługi obciążeń renderowania.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 3efe1dfa69de5ce41aed2152baa88b313fd928f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115758"
---
# <a name="azure-batch-rendering-capabilities"></a>Możliwości renderowania Azure Batch

Standardowe możliwości Azure Batch są używane do uruchamiania obciążeń i aplikacji renderowania. Program Batch zawiera również określone funkcje do obsługi obciążeń renderowania.

Aby zapoznać się z omówieniem pojęć związanych z przetwarzaniem wsadowym, w tym pulami, zadaniami i zadaniami, zobacz [ten artykuł](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Pule wsadowe

### <a name="rendering-application-installation"></a>Renderowanie instalacji aplikacji

Obraz maszyny wirtualnej renderowania w portalu Azure Marketplace można określić w konfiguracji puli, jeśli trzeba będzie używać tylko wstępnie zainstalowanych aplikacji.

Istnieje obraz systemu Windows 2016 i obraz CentOS.  W [portalu Azure Marketplace](https://azuremarketplace.microsoft.com)obrazy maszyn wirtualnych można znaleźć, wyszukując frazę "renderowanie wsadowe".

Aby zapoznać się z przykładową konfiguracją puli, zobacz [samouczek renderowania interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  Azure Portal i Batch Explorer udostępniają narzędzia graficznego interfejsu użytkownika, które umożliwiają wybranie renderowania obrazu maszyny wirtualnej podczas tworzenia puli.  W przypadku korzystania z interfejsu API usługi Batch określ następujące wartości właściwości [elementu imagereference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) podczas tworzenia puli:

| Wydawca | Oferta | SKU | Wersja |
|---------|---------|---------|--------|
| partia | Renderowanie — centos73 | dawania | najnowsza |
| partia | Renderowanie — windows2016 | dawania | najnowsza |

Inne opcje są dostępne, jeśli na maszynach wirtualnych puli są wymagane dodatkowe aplikacje:

* Obraz niestandardowy z galerii obrazów udostępnionych:
  * Przy użyciu tej opcji możesz skonfigurować swoją maszynę wirtualną z odpowiednimi aplikacjami w określonych wersjach, których potrzebujesz. Aby uzyskać więcej informacji, zobacz [Tworzenie puli za pomocą galerii obrazów udostępnionych](batch-sig-images.md). Grupy Autodesk i chaos zmieniły odpowiednio Arnold i V-Ray, aby można było sprawdzić poprawność usługi licencjonowania Azure Batch. Upewnij się, że masz wersje tych aplikacji z tą obsługą, w przeciwnym razie Licencjonowanie za użycie nie będzie działało. Bieżące wersje Maya lub 3ds Max nie wymagają serwera licencji podczas uruchamiania bezobsługowego (w trybie wsadowym/wierszu polecenia). Skontaktuj się z pomocą techniczną platformy Azure, jeśli nie masz pewności, jak kontynuować tę opcję.
* [Pakiety aplikacji](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Pakowanie plików aplikacji przy użyciu co najmniej jednego pliku ZIP, przekazywanie przez Azure Portal i określanie pakietu w konfiguracji puli. Podczas tworzenia puli maszyn wirtualnych pliki ZIP są pobierane i wyodrębnione pliki.
* Pliki zasobów:
  * Pliki aplikacji są przekazywane do usługi Azure Blob Storage, a w [zadaniu puli](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)należy określić odwołania do plików. Podczas tworzenia puli maszyn wirtualnych pliki zasobów są pobierane na każdą maszynę wirtualną.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Płatność za korzystanie z usługi licencjonowania dla wstępnie zainstalowanych aplikacji

W konfiguracji puli należy określić aplikacje, które będą używane i mieć opłatę licencyjną.

* Określ `applicationLicenses` właściwość podczas [tworzenia puli](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  Następujące wartości można określić w tablicy ciągów-"VRay", "Arnold", "3dsMax", "Maya".
* Po określeniu jednej lub kilku aplikacji koszt tych aplikacji zostanie dodany do kosztu maszyn wirtualnych.  Ceny aplikacji są wymienione na [stronie cennika Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Jeśli zamiast tego nawiążesz połączenie z serwerem licencji, aby korzystać z aplikacji do renderowania, `applicationLicenses` nie określaj właściwości.

Możesz użyć Azure Portal lub Batch Explorer do wybrania aplikacji i pokazywania cen aplikacji.

Jeśli podjęto próbę użycia aplikacji, ale aplikacja nie została określona we `applicationLicenses` właściwości konfiguracji puli lub nie dociera do serwera licencji, wykonanie aplikacji kończy się niepowodzeniem z błędem licencjonowania i kodem zakończenia innym niż zero.

### <a name="environment-variables-for-pre-installed-applications"></a>Zmienne środowiskowe dla wstępnie zainstalowanych aplikacji

Aby można było utworzyć wiersz polecenia dla zadań renderowania, należy określić lokalizację instalacji plików wykonywalnych aplikacji do renderowania.  Systemowe zmienne środowiskowe zostały utworzone w obrazach maszyn wirtualnych portalu Azure Marketplace, których można użyć zamiast określania rzeczywistych ścieżek.  Te zmienne środowiskowe są uzupełnieniem [standardowych zmiennych środowiskowych usługi Batch](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) utworzonych dla każdego zadania.

|Aplikacja|Plik wykonywalny aplikacji|Zmienna środowiskowa|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio. exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio. exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|Render. exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|Render. exe|MAYA_2018_EXEC|
|Grupa chaos V-Ray Standalone|VRay. exe|VRAY_3.60.4_EXEC|
Wiersz polecenia Arnold 2017|zastrzelc. exe|ARNOLD_2017_EXEC|
|Wiersz polecenia Arnold 2018|zastrzelc. exe|ARNOLD_2018_EXEC|
|Programem Blender|Blender. exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Rodziny maszyn wirtualnych platformy Azure

Podobnie jak w przypadku innych obciążeń, renderowanie wymagań systemowych aplikacji jest różne i wymagania dotyczące wydajności różnią się w zależności od zadań i projektów.  Na platformie Azure dostępne są duże różne rodziny maszyn wirtualnych, w zależności od wymagań — najniższy koszt, Najlepsza cena/wydajność, Najlepsza wydajność i tak dalej.
Niektóre aplikacje do renderowania, takie jak Arnold, są oparte na procesorach CPU; inne, takie jak cykle V-Ray i Blender, mogą korzystać z procesorów CPU i/lub GPU.
Aby uzyskać opis dostępnych rodzin maszyn wirtualnych i rozmiarów maszyn wirtualnych, [Zobacz typy i rozmiary maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>Maszyny wirtualne o niskim priorytecie

Podobnie jak w przypadku innych obciążeń, maszyny wirtualne o niskim priorytecie mogą być wykorzystywane w pulach usługi Batch do renderowania.  Maszyny wirtualne o niskim priorytecie działają tak samo jak regularne dedykowane maszyny wirtualne, ale wykorzystują nadwyżkową pojemność platformy Azure i są dostępne dla dużego rabatu.  Użycie maszyn wirtualnych o niskim priorytecie polega na tym, że te maszyny wirtualne mogą nie być dostępne do przydzielenia lub mogą zostać przeniesione w dowolnym momencie, w zależności od dostępnej pojemności. Z tego powodu maszyny wirtualne o niskim priorytecie nie są odpowiednie dla wszystkich zadań renderowania. Na przykład, jeśli obrazy potrwają wiele godzin w celu renderowania, prawdopodobnie wyrenderowanie tych obrazów zostało przerwane i uruchomione ze względu na to, że maszyny wirtualne są zaakceptowane.

Aby uzyskać więcej informacji na temat właściwości maszyn wirtualnych o niskim priorytecie i różnych sposobów konfigurowania ich przy użyciu usługi Batch, zobacz [Korzystanie z maszyn wirtualnych o niskim priorytecie w usłudze Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Zadania i zadania

Zadania i zadania nie wymagają obsługi specyficznej dla renderowania.  Głównym elementem konfiguracji jest wiersz poleceń zadania, który musi odwoływać się do wymaganej aplikacji.
Gdy są używane obrazy maszyn wirtualnych w portalu Azure Marketplace, najlepszym rozwiązaniem jest użycie zmiennych środowiskowych w celu określenia ścieżki i pliku wykonywalnego aplikacji.

## <a name="next-steps"></a>Następne kroki

Przykłady renderowania wsadowego Wypróbuj dwa samouczki:

* [Renderowanie przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderowanie przy użyciu programu Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
