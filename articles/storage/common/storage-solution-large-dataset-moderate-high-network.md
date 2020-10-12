---
title: Opcje usługi Azure Data Transfer dla dużych zestawów danych — umiarkowane dla dużej przepustowości sieci | Microsoft Docs
description: Dowiedz się, jak wybrać rozwiązanie platformy Azure na potrzeby transferu danych, gdy masz średnią przepustowość sieci w środowisku i planujesz transfer dużych zestawów danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 900221bd27fe4020a927b3155c91aa8e494ca890
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86203846"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Transfer dużych zestawów danych w przypadku umiarkowanej lub wysokiej przepustowości sieci
 
Ten artykuł zawiera omówienie rozwiązań transferu danych w przypadku umiarkowanej i dużej przepustowości sieci w środowisku i planowania transferu dużych zestawów danych. W tym scenariuszu opisano również zalecane opcje transferu danych oraz odpowiednie kluczowe klasy macierzy.

Aby zapoznać się z omówieniem wszystkich dostępnych opcji transferu danych, przejdź do pozycji [Wybierz rozwiązanie do transferu danych Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Opis scenariusza

Duże zbiory danych odnoszą się do rozmiarów, w kolejności od usług TBs do usługi książek telefonicznych. Średnia do wysoka przepustowość sieci to 100 MB/s do 10 GB/s.

## <a name="recommended-options"></a>Zalecane opcje

Opcje zalecane w tym scenariuszu zależą od tego, czy masz umiarkowaną przepustowość sieci czy dużą przepustowość sieci.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Umiarkowana przepustowość sieci (100 MB/s — 1 GB/s)

W przypadku umiarkowanej przepustowości sieci należy zaprojektować czas przesyłania danych przez sieć.

Skorzystaj z poniższej tabeli, aby oszacować czas i na podstawie tego, wybrać między transferem w trybie offline lub transferem sieciowym. W tabeli przedstawiono przewidywany czas transferu danych w sieci dla różnych dostępnych przepustowości sieci (przy założeniu użycia 90%).  

![Transfer sieciowy lub transfer w trybie offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Jeśli transfer sieciowy jest rzutowany, aby był zbyt wolny, należy użyć urządzenia fizycznego. Zalecanymi opcjami w tym przypadku są urządzenia transferu w trybie offline z rodziny Azure Data Box lub importowania/eksportowania platformy Azure przy użyciu własnych dysków.

    - **Azure Data Box rodzina do transferów w trybie offline** — używanie urządzeń z urządzeń urządzenie Data Box dostarczonych przez firmę Microsoft do przenoszenia dużych ilości danych na platformę Azure, gdy są one ograniczone przez czas, dostępność sieci lub koszty. Skopiuj dane lokalne przy użyciu narzędzi, takich jak Robocopy. W zależności od rozmiaru danych przeznaczonego do transferu można wybrać jedną z Data Box Disk, urządzenie Data Box lub Data Box Heavy.
    - **Azure Import/Export** — usługa Azure Import/Export umożliwia dostarczenie własnych stacji dysków w celu bezpiecznego zaimportowania dużych ilości danych do usługi Azure Blob storage i Azure Files. Ta usługa może również służyć do transferowania danych z usługi Azure Blob Storage do stacji dysków i dostarczania ich do lokacji lokalnych.

- Jeśli transfer sieciowy jest przewidywany jako rozsądny, można użyć dowolnego z poniższych narzędzi w celu zapewnienia [wysokiego obciążenia sieci](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Wysoka przepustowość sieci (1 GB/s — 100 GB/s)

Jeśli dostępna przepustowość sieci jest wysoka, użyj jednego z następujących narzędzi.

- **AzCopy** — za pomocą tego narzędzia wiersza polecenia można łatwo kopiować dane do i z obiektów blob platformy Azure, plików i magazynu tabel z optymalną wydajnością. AzCopy obsługuje współbieżność i równoległość oraz możliwość wznowienia operacji kopiowania po przerwaniu.
- **Interfejsy API REST usługi Azure Storage/zestawy SDK** — podczas kompilowania aplikacji można opracowywać aplikację pod kątem interfejsów API REST usługi Azure Storage i korzystać z zestawów SDK platformy Azure oferowanych w wielu językach.
- **Azure Data Box rodzina do transferów online** — Data Box Edge i Data Box Gateway są urządzeniami sieciowymi online, które umożliwiają przenoszenie danych do i z platformy Azure. Użyj Data Box Edge urządzenia fizycznego, gdy istnieje równoczesna potrzeba ciągłego pozyskiwania i wstępnego przetwarzania danych przed przekazaniem. Data Box Gateway to wirtualna wersja urządzenia z takimi samymi możliwościami transferu danych. W każdym przypadku transfer danych jest zarządzany przez urządzenie.
- **Azure Data Factory** — Data Factory należy użyć do skalowania operacji transferu oraz, jeśli istnieje potrzeba organizowania i monitorowania klasy korporacyjnej. Użyj Data Factory do regularnego transferu plików między kilkoma usługami platformy Azure, lokalnymi lub kombinacjami tych dwóch. za pomocą Data Factory można tworzyć i planować przepływy pracy oparte na danych (nazywane potokami), które pozyskiwanie danych z różnych magazynów danych i automatyzowanie przenoszenia danych i przekształcania danych.

## <a name="comparison-of-key-capabilities"></a>Porównanie kluczowych możliwości

W poniższej tabeli zestawiono różnice między kluczowymi funkcjami dla zalecanych opcji.

### <a name="moderate-network-bandwidth"></a>Średnia przepustowość sieci

W przypadku korzystania z transferu danych w trybie offline Skorzystaj z poniższej tabeli, aby poznać różnice między kluczowymi funkcjami.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Rozmiar danych**                    |    Do 35 usług TBs                 |    Do 80 usług TBs na urządzenie                       |    Do 800 TB na urządzenie               |    Zmienna                            |
|    **Typ danych**                    |    Obiekty blob platformy Azure                  |    Obiekty blob platformy Azure<br>Azure Files                    |    Obiekty blob platformy Azure<br>Azure Files            |    Obiekty blob platformy Azure<br>Azure Files          |
|    **Współczynnik postaci**                  |    5 dysków SSD na zamówienie             |    1 X 50-funtów urządzenie o rozmiarze stacjonarnym na zamówienie    |    1 X ~ 500-funty. duże urządzenie na zamówienie    |    Do 10 HDD/dysków SSD na zamówienie        |
|    **Czas wstępnej instalacji**               |    Niski <br>(15 minut)            |    Od niska do umiarkowanych <br> (<30 minut)               |    Umiarkowane<br>(1-2 godzin)               |    Umiarkowane, aby trudne<br>zmiennej |
|    **Wysyłanie danych do platformy Azure**           |    Tak                          |    Tak                                           |    Tak                                   |    Tak                                 |
|    **Eksportowanie danych z platformy Azure**           |    Nie                           |    Nie                                            |    Nie                                    |    Tak                                 |
|    **Szyfrowanie**                   |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    **Sprzęt**                     |     Dostarczone przez firmę Microsoft          |    Dostarczone przez firmę Microsoft                            |    Dostarczone przez firmę Microsoft                    |    Dostarczone przez klienta                   |
|    **Interfejs sieciowy**            |    USB 3.1/SATA                 |    PORT RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    **Integracja z partnerami**          |    Niektóre                         |    [Wysoki](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Wysoki](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Niektóre                                |
|    **Wysyłka**                     |    Zarządzane przez firmę Microsoft            |    Zarządzane przez firmę Microsoft                             |    Zarządzane przez firmę Microsoft                     |    Zarządzane przez klienta                    |
| **Użyj podczas przenoszenia danych**     |Granica handlowa|Granica handlowa|Granica handlowa|Między granicami geograficznymi, np. do UE|
|    **Cennik**                          |    [Cennik](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Cennik](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


W przypadku korzystania z transferu danych w trybie online Skorzystaj z tabeli w poniższej sekcji, aby uzyskać wysoką przepustowość sieci.

### <a name="high-network-bandwidth"></a>Wysoka przepustowość sieci

|                                     |    Narzędzia AzCopy, <br>Azure PowerShell <br>Interfejs wiersza polecenia platformy Azure             |    Interfejsy API REST usługi Azure Storage, zestawy SDK                   |    Data Box Gateway lub Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    **Typ danych**              |    Obiekty blob platformy Azure, Azure Files, tabele platformy Azure    |    Obiekty blob platformy Azure, Azure Files, tabele platformy Azure    |    Obiekty blob platformy Azure, Azure Files                           |   Obsługuje ponad 70 łączników danych dla magazynów danych i formatów    |
|    **Współczynnik postaci**            |    Narzędzia wiersza polecenia                        |    Interfejs programistyczny                    |    Firma Microsoft dostarcza wirtualne <br>lub urządzenie fizyczne     |    Usługa w Azure Portal                                            |
|    **Początkowa konfiguracja jednorazowa** |    Narzędzia               |    Umiarkowane                       |    Łatwe (<30 minut) do umiarkowanego (1-2 godzin)            |    Złożone                                                          |
|    **Przetwarzanie wstępne danych**          |    Nie                                        |    Nie                                        |    Tak (przy użyciu obliczeń brzegowych)                               |    Tak                                                                |
|    **Transfer z innych chmur**   |    Nie                                        |    Nie                                        |    Nie                                                    |    Tak                                                                |
|    **Typ użytkownika**                    |    Specjalista IT Pro lub dev                                       |    Deweloperskie                                       |    Informatyk                                                |    Informatyk                                                             |
|    **Cennik**                      |    Bezpłatna, naliczane są opłaty za dane wychodzące         |    Bezpłatna, naliczane są opłaty za dane wychodzące         |    [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Cennik](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak transferować dane przy użyciu metody Import/Export](/azure/storage/common/storage-import-export-data-to-blobs).
- Zapoznaj się z tematem

    - [Transferuj dane przy użyciu Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Transferuj dane przy użyciu urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Transfer danych za pomocą AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Zapoznaj się z tematem:
    - [Transferuj dane przy użyciu Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Przekształć dane przy użyciu Data Box Edge przed wysłaniem do platformy Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Dowiedz się, jak transferować dane za pomocą Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Transferowanie danych za pomocą interfejsów API REST

    - [W programie .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [W języku Java](https://docs.microsoft.com/java/api/overview/azure/storage)
