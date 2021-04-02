---
title: Wybierz rozwiązanie platformy Azure na potrzeby transferu danych | Microsoft Docs
description: Dowiedz się, jak wybrać rozwiązanie platformy Azure na potrzeby transferu danych w oparciu o rozmiary danych i dostępną przepustowość sieci w danym środowisku.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 11ea9c759bdb4bb2b837028407ce6e83f6e25a8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92784052"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Wybieranie rozwiązania platformy Azure do transferu danych

Ten artykuł zawiera omówienie niektórych typowych rozwiązań do transferu danych na platformie Azure. Artykuł zawiera również instrukcje dotyczące zalecanych opcji w zależności od przepustowości sieci w danym środowisku oraz wielkości danych, które mają być transferowane.

## <a name="types-of-data-movement"></a>Typy przenoszenia danych

Transfer danych może być w trybie offline lub za pośrednictwem połączenia sieciowego. Wybierz rozwiązanie w zależności od:

- **Rozmiar danych** — rozmiar danych przeznaczonych do przeniesienia,
- **Częstotliwość transferu** — jednorazowe lub okresowe wprowadzanie danych i
- **Sieć** — przepustowość dostępna na potrzeby transferu danych w danym środowisku.

Przenoszenie danych może mieć następujące typy:

- **Transfer w trybie offline przy użyciu urządzeń z możliwością przewoźnicy** — umożliwia korzystanie z fizycznych urządzeń do przesyłania w trybie offline jednorazowych transferów danych. Firma Microsoft wysyła dysk lub bezpieczne wyspecjalizowane urządzenie. Alternatywnie możesz kupić i dostarczyć własne dyski. Kopiujesz dane do urządzenia, a następnie wysyłasz je do platformy Azure, w której dane są przekazywane.  Dostępne opcje w tym przypadku to Data Box Disk, urządzenie Data Box, Data Box Heavy i importowania/eksportowania (używaj własnych dysków).

- **Transfer sieciowy** — przenosisz dane na platformę Azure za pośrednictwem połączenia sieciowego. Można to zrobić na wiele sposobów.

    - **Interfejs graficzny** — Jeśli okresowo przesyłasz zaledwie kilka plików i nie musisz zautomatyzować transferu danych, możesz wybrać narzędzie interfejsu graficznego, takie jak Eksplorator usługi Azure Storage lub narzędzie eksploracji oparte na sieci Web w programie Azure Portal.
    - **Skryptowy lub przenoszony przez skrypty** — możesz używać zoptymalizowanych narzędzi programistycznych udostępnianych przez nas lub bezpośrednio wywoływać interfejsy API REST/zestawy SDK. Dostępne narzędzia skryptowe to AzCopy, Azure PowerShell i interfejs wiersza polecenia platformy Azure. Dla interfejsu programistycznego Użyj jednego z zestawów SDK dla platformy .NET, Java, Python, Node/JS, C++, go, PHP lub Ruby.
    - **Urządzenia lokalne** — udostępniamy urządzenia fizyczne lub wirtualne, które znajdują się w centrum danych, a następnie optymalizują transfer przy użyciu sieci. Te urządzenia zapewniają również lokalną pamięć podręczną często używanych plików. Urządzenie fizyczne jest Azure Stack krawędzią, a urządzenie wirtualne jest Data Box Gateway. Obie działają trwale w Twoim środowisku lokalnym i łączą się z platformą Azure za pośrednictwem sieci.
    - **Potok danych zarządzanych** — można skonfigurować potok w chmurze do regularnego transferu plików między kilkoma usługami platformy Azure, lokalnymi lub kombinacją dwóch. Użyj Azure Data Factory, aby skonfigurować potoki danych i zarządzać nimi, oraz przenosić i przekształcać dane w celu analizy.

W poniższej wizualizacji przedstawiono wskazówki pozwalające wybrać różne narzędzia transferu danych platformy Azure w zależności od przepustowości sieci dostępnej do transferu, rozmiaru danych przeznaczonego do transferu oraz częstotliwości transferu.

![Narzędzia transferu danych platformy Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Górne limity urządzeń transferu w trybie offline — Data Box Disk, urządzenie Data Box i Data Box Heavy można rozszerzyć, umieszczając wiele zamówień typu urządzenia.*

## <a name="selecting-a-data-transfer-solution"></a>Wybieranie rozwiązania do transferu danych

Odpowiedz na następujące pytania, aby pomóc wybrać rozwiązanie do transferu danych:

- Czy dostępna przepustowość sieci jest ograniczona lub nieistniejąca i chcesz przenieść duże zestawy danych?
  
    Jeśli tak, zobacz: [scenariusz 1: transfer dużych zestawów danych z niewielką przepustowością sieci](storage-solution-large-dataset-low-network.md).
- Czy chcesz transferować duże zestawy danych przez sieć i masz umiarkowaną przepustowość sieci?

    Jeśli tak, zobacz: [Scenariusz 2: transfer dużych zestawów danych z umiarkowaną do dużej przepustowości sieci](storage-solution-large-dataset-moderate-high-network.md).
- Czy chcesz czasami przenieść kilka plików za pośrednictwem sieci?

    Jeśli tak, zobacz [Scenariusz 3: transfer małych zestawów danych z ograniczoną do umiarkowanej przepustowości sieci](storage-solution-small-dataset-low-moderate-network.md).
- Czy szukasz w regularnych odstępach czasu na transfer danych z punktu widzenia?

    Jeśli tak, użyj opcji skryptowych/programistycznych opisanych w [scenariuszu 4: okresowe transfery danych](storage-solution-periodic-data-transfer.md).
- Czy szukasz ciągłego transferu danych w toku?

    Jeśli tak, użyj opcji w [scenariuszu 4: okresowe transfery danych](storage-solution-periodic-data-transfer.md).

## <a name="data-transfer-feature-in-azure-portal"></a>Funkcja transferu danych w Azure Portal

Możesz również przejść do konta usługi Azure Storage w Azure Portal i wybrać funkcję **transferu danych** . Podaj przepustowość sieci w środowisku, rozmiar danych, które chcesz przenieść, oraz częstotliwość transferu danych. Zobaczysz optymalne rozwiązania dotyczące transferu danych odpowiadające danym, które zostały podane przez Ciebie. 

## <a name="next-steps"></a>Następne kroki

- [Zapoznaj się z wprowadzeniem do Eksplorator usługi Azure Storage](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Zapoznaj się z omówieniem AzCopy](./storage-use-azcopy-v10.md).
- [Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą programu PowerShell](../blobs/storage-quickstart-blobs-powershell.md)
- [Szybki Start: Tworzenie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure](../blobs/storage-quickstart-blobs-cli.md)
- Dowiedz się więcej:

    - [Azure Data Box, Azure Data Box Disk i Azure Data Box Heavy do transferów w trybie offline](../../databox/index.yml).
    - [Azure Data Box Gateway i Azure Stack Edge do transferów online](../../databox-online/index.yml).
- [Dowiedz się, co to jest Azure Data Factory](../../data-factory/copy-activity-overview.md).
- Transferowanie danych za pomocą interfejsów API REST

    - [W programie .NET](/dotnet/api/overview/azure/storage)
    - [W języku Java](/java/api/overview/azure/storage)