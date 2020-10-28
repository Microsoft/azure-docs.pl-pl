---
title: Wybierz rozwiązanie platformy Azure na potrzeby okresowego transferu danych | Microsoft Docs
description: Dowiedz się, jak wybrać rozwiązanie platformy Azure na potrzeby transferu danych podczas okresowego przesyłania danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: a0efc6f6f6d4ae6355fbb42fbc7e13ad7c078cf3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792892"
---
# <a name="solutions-for-periodic-data-transfer"></a>Rozwiązania do okresowego transferu danych
 
Ten artykuł zawiera omówienie rozwiązań transferu danych w przypadku okresowego przesyłania danych. Okresowe przesyłanie danych za pośrednictwem sieci można podzielić na kategorie jako cykliczne w regularnych odstępach czasu lub ciągłe przenoszenie danych. W tym scenariuszu opisano również zalecane opcje transferu danych oraz odpowiednie kluczowe klasy macierzy.

Aby zapoznać się z omówieniem wszystkich dostępnych opcji transferu danych, przejdź do pozycji [Wybierz rozwiązanie do transferu danych Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Zalecane opcje

Zalecane opcje okresowego transferu danych dzielą się na dwie kategorie w zależności od tego, czy transfer jest cykliczny, czy ciągły.

- **Narzędzia skryptowe/programistyczne** — w przypadku transferu danych, który jest przeprowadzany w regularnych odstępach czasu, należy używać narzędzi skryptów i programistycznych, takich jak AzCopy i interfejsy API REST usługi Azure Storage. Te narzędzia są przeznaczone dla informatyków i deweloperów.

    - **AzCopy** — za pomocą tego narzędzia wiersza polecenia można łatwo kopiować dane do i z obiektów blob platformy Azure, plików i magazynu tabel z optymalną wydajnością. AzCopy obsługuje współbieżność i równoległość oraz możliwość wznowienia operacji kopiowania po przerwaniu.
    - **Interfejsy API REST usługi Azure Storage/zestawy SDK** — podczas kompilowania aplikacji można opracowywać aplikację pod kątem interfejsów API REST usługi Azure Storage i korzystać z zestawów SDK platformy Azure oferowanych w wielu językach. Interfejsy API REST mogą również korzystać z biblioteki przenoszenia danych usługi Azure Storage przeznaczonej specjalnie do kopiowania danych do i z platformy Azure.

- **Stałe narzędzia** do pozyskiwania danych — w przypadku ciągłego, trwającego pozyskiwania danych można wybrać jedno z urządzenie Data Box urządzeń transferu w trybie online lub Azure Data Factory. Te narzędzia są konfigurowane przez specjalistów IT i mogą w sposób przezroczysty automatyzować transfer danych.

    - **Azure Data Factory** — Data Factory należy użyć do skalowania operacji transferu oraz, jeśli istnieje potrzeba organizowania i monitorowania klasy korporacyjnej. Użyj Azure Data Factory, aby skonfigurować potok w chmurze, który regularnie przesyła pliki między kilka usług platformy Azure, lokalnie lub kombinację tych dwóch. Azure Data Factory pozwala organizować przepływy pracy oparte na danych, które pozyskiwanie danych z różnych magazynów danych i automatyzowanie przenoszenia danych i przekształcania danych.
    - **Azure Data Box rodzina do transferów online** — Data Box Edge i Data Box Gateway są urządzeniami sieciowymi online, które umożliwiają przenoszenie danych do i z platformy Azure. Data Box Edge korzysta z obliczeń brzegowych z włączoną funkcją sztucznej analizy (AI), aby wstępnie przetworzyć dane przed przekazaniem. Data Box Gateway to wirtualna wersja urządzenia z takimi samymi możliwościami transferu danych.


## <a name="comparison-of-key-capabilities"></a>Porównanie kluczowych możliwości

Poniższa tabela zawiera podsumowanie różnic między kluczowymi funkcjami.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Transfer danych przez skrypt/program programistyczny

| Możliwość                  | AzCopy                                 | Interfejsy API REST usługi Azure Storage       |
|-----------------------------|----------------------------------------|-------------------------------|
| Współczynnik postaci                 | Narzędzie wiersza polecenia firmy Microsoft       | Klienci opracowują magazyn <br> Interfejsy API REST przy użyciu bibliotek klienckich platformy Azure |
| Początkowa konfiguracja jednorazowa     | Minimalny                                | Umiarkowany, zmienny nakład pracy programistycznej    |
| Format danych                 | Obiekty blob platformy Azure, Azure Files, tabele platformy Azure | Obiekty blob platformy Azure, Azure Files, tabele platformy Azure   |
| Wydajność                 | Już zoptymalizowane                      | Optymalizuj podczas opracowywania                  |
| Cennik                     | Bezpłatna, naliczane są opłaty za dane wychodzące      | Bezpłatna, naliczane są opłaty za dane wychodzące        |

### <a name="continuous-data-ingestion-over-network"></a>Ciągłe pozyskiwanie danych za pośrednictwem sieci

| Cechy                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Współczynnik postaci                                   | Urządzenie wirtualne             | Urządzenie fizyczne          | Usługa w Azure Portal, Agent lokalny                                                            |
| Sprzęt                                      | Funkcja hypervisor            | Dostarczone przez firmę Microsoft    | Nie dotyczy                                                            |
| Wstępny nakład pracy konfiguracji                          | Niska (<30 minut)            | Umiarkowane (~ kilka godzin) | Duże (~ dni)                                                 |
| Format danych                                   | Obiekty blob platformy Azure, Azure Files   | Obiekty blob platformy Azure, Azure Files | [Obsługuje ponad 70 łączników danych dla magazynów danych i formatów](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| Przetwarzanie wstępne danych                           | Nie                         | Tak, za pomocą obliczeń brzegowych    | Tak                                                           |
| Lokalna pamięć podręczna<br>(do przechowywania danych lokalnych)    | Tak                        | Tak                      | Nie                                                            |
| Transfer z innych chmur                    | Nie                         | Nie                       | Tak                                                           |
| Cennik                                       | [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Cennik](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Następne kroki

- [Transfer danych za pomocą AzCopy](./storage-use-azcopy-v10.md?toc=%252fazure%252fstorage%252ftables%252ftoc.json).
- [Więcej informacji na temat transferu danych za pomocą interfejsów API REST magazynu](/dotnet/api/overview/azure/storage).
- Zapoznaj się z tematem:
    - [Transferuj dane przy użyciu Data Box Gateway](../../databox-online/data-box-gateway-deploy-add-shares.md).
    - [Przekształć dane przy użyciu Data Box Edge przed wysłaniem do platformy Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Dowiedz się, jak transferować dane za pomocą Azure Data Factory](../../data-factory/tutorial-bulk-copy-portal.md).