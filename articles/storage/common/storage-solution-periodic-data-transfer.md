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
ms.openlocfilehash: a15ebd43861e2116ddbb2d9055b289645962e203
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96573922"
---
# <a name="solutions-for-periodic-data-transfer"></a>Rozwiązania do okresowego transferu danych
 
Ten artykuł zawiera omówienie rozwiązań transferu danych w przypadku okresowego przesyłania danych. Okresowe przesyłanie danych za pośrednictwem sieci można podzielić na kategorie jako cykliczne w regularnych odstępach czasu lub ciągłe przenoszenie danych. W tym scenariuszu opisano również zalecane opcje transferu danych oraz odpowiednie kluczowe klasy macierzy.

Aby zapoznać się z omówieniem wszystkich dostępnych opcji transferu danych, przejdź do pozycji [Wybierz rozwiązanie do transferu danych Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Zalecane opcje

Zalecane opcje dla okresowego transferu danych dzielą się na dwie kategorie, w zależności od tego, czy transfer jest cykliczny, czy ciągły.

- **Narzędzia skryptowe/programistyczne** — w przypadku transferu danych, który jest przeprowadzany w regularnych odstępach czasu, należy używać narzędzi skryptów i programistycznych, takich jak AzCopy i interfejsy API REST usługi Azure Storage. Te narzędzia są przeznaczone dla informatyków i deweloperów.

    - **AzCopy** — za pomocą tego narzędzia wiersza polecenia można łatwo kopiować dane do i z obiektów blob platformy Azure, plików i magazynu tabel z optymalną wydajnością. Narzędzie AzCopy obsługuje współbieżność i równoległość oraz umożliwia wznawianie operacji kopiowania, gdy zostaną przerwane.
    - **Interfejsy API REST usługi Azure Storage/zestawy SDK** — podczas kompilowania aplikacji można opracowywać aplikację pod kątem interfejsów API REST usługi Azure Storage i korzystać z zestawów SDK platformy Azure oferowanych w wielu językach. Interfejsy API REST mogą również korzystać z biblioteki przenoszenia danych usługi Azure Storage przeznaczonej specjalnie do kopiowania danych do i z platformy Azure.

- **Stałe narzędzia** do pozyskiwania danych — w przypadku ciągłego, trwającego pozyskiwania danych można wybrać jedno z urządzenie Data Box urządzeń transferu w trybie online lub Azure Data Factory. Te narzędzia są konfigurowane przez informatyków i umożliwiają automatyzację przesyłania danych w sposób niewidoczny dla użytkownika.

    - **Azure Data Factory** — Data Factory należy użyć do skalowania operacji transferu oraz, jeśli istnieje potrzeba organizowania i monitorowania klasy korporacyjnej. Użyj usługi Azure Data Factory, aby skonfigurować potok w chmurze umożliwiający regularne przesyłanie plików pomiędzy kilkoma usługami platformy Azure, w środowisku lokalnym lub gdy konieczne jest połączenie tych dwóch wymagań. Usługa Azure Data Factory umożliwia orkiestrację opartych na danych przepływów pracy, które pozyskują dane z wielu różnych magazynów danych i automatyzują przenoszenie danych i ich przekształcanie.
    - **Azure Data Box rodzina do transferów online** — Data Box Edge i Data Box Gateway są urządzeniami sieciowymi online, które umożliwiają przenoszenie danych do i z platformy Azure. Urządzenie Data Box Edge wykorzystuje funkcję obliczeniową Edge wykorzystującą sztuczną inteligencję do wstępnego przetwarzania danych przed ich przekazaniem. Data Box Gateway jest wirtualną wersją urządzenia z takimi samymi możliwościami transferu danych.


## <a name="comparison-of-key-capabilities"></a>Porównanie kluczowych możliwości

W poniższej tabeli podsumowano różnice w zakresie kluczowych funkcji.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Transfer danych przez skrypt/program programistyczny

| Możliwość                  | AzCopy                                 | Interfejsy API REST usługi Azure Storage       |
|-----------------------------|----------------------------------------|-------------------------------|
| Współczynnik postaci                 | Narzędzie wiersza polecenia firmy Microsoft       | Klienci opracowują magazyn <br> Interfejsy API REST przy użyciu bibliotek klienckich platformy Azure |
| Początkowa konfiguracja jednorazowa     | Minimalny                                | Umiarkowany, zmienny nakład pracy programistycznej    |
| Format danych                 | Obiekty blob platformy Azure, Azure Files, tabele platformy Azure | Obiekty blob platformy Azure, Azure Files, tabele platformy Azure   |
| Wydajność                 | Już zoptymalizowane                      | Optymalizuj podczas opracowywania                  |
| Ceny                     | Bezpłatna, naliczane są opłaty za dane wychodzące      | Bezpłatna, naliczane są opłaty za dane wychodzące        |

### <a name="continuous-data-ingestion-over-network"></a>Ciągłe pozyskiwanie danych za pośrednictwem sieci

| Cecha                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Współczynnik postaci                                   | Urządzenie wirtualne             | Urządzenie fizyczne          | Usługa w Azure Portal, Agent lokalny                                                            |
| Sprzęt                                      | Funkcja hypervisor            | Dostarczone przez firmę Microsoft    | NA                                                            |
| Wstępny nakład pracy konfiguracji                          | Niska (<30 minut)            | Umiarkowane (~ kilka godzin) | Duże (~ dni)                                                 |
| Format danych                                   | Obiekty blob platformy Azure, Azure Files   | Obiekty blob platformy Azure, Azure Files | [Obsługuje ponad 70 łączników danych dla magazynów danych i formatów](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| Przetwarzanie wstępne danych                           | Nie                         | Tak, za pomocą obliczeń brzegowych    | Tak                                                           |
| Lokalna pamięć podręczna<br>(do przechowywania danych lokalnych)    | Tak                        | Tak                      | Nie                                                            |
| Transfer z innych chmur                    | Nie                         | Nie                       | Tak                                                           |
| Ceny                                       | [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Cennik](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Następne kroki

- [Transfer danych za pomocą AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Więcej informacji na temat transferu danych za pomocą interfejsów API REST magazynu](/dotnet/api/overview/azure/storage).
- Zapoznaj się z tematem:
    - [Transferuj dane przy użyciu Data Box Gateway](../../databox-gateway/data-box-gateway-deploy-add-shares.md).
    - [Przekształć dane przy użyciu Data Box Edge przed wysłaniem do platformy Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Dowiedz się, jak transferować dane za pomocą Azure Data Factory](../../data-factory/tutorial-bulk-copy-portal.md).