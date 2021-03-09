---
title: Używanie usługi Azure Blob Storage do konwersji modelu
description: Opisuje typowe kroki konfigurowania magazynu obiektów blob i używania go na potrzeby konwersji modeli.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: d8a6fd458cdcf79cdeb693b25acf72d4ec48def7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507521"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Używanie usługi Azure Blob Storage do konwersji modelu

Usługa [konwersji modeli](model-conversion.md) wymaga dostępu do usługi Azure Blob Storage, aby umożliwić pobieranie danych wejściowych i przechowywanie danych wyjściowych. W tym artykule opisano sposób wykonywania najczęstszych kroków.

## <a name="prepare-azure-storage-accounts"></a>Przygotowywanie kont usługi Azure Storage

- Tworzenie konta magazynu (StorageV2)
- Tworzenie wejściowego kontenera obiektów BLOB na koncie magazynu (na przykład o nazwie "arrinput")
- Tworzenie wyjściowego kontenera obiektów BLOB na koncie magazynu (na przykład o nazwie "arroutput")

> [!TIP]
> Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania konta magazynu, zobacz [Szybki Start: konwertowanie modelu do renderowania](../../quickstarts/convert-model.md)

Tworzenie konta magazynu i kontenerów obiektów BLOB można wykonać przy użyciu jednego z następujących narzędzi:

- [Witryna Azure Portal](https://portal.azure.com)
- [AZ Command line](/cli/azure/install-azure-cli)
- [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
- Zestawy SDK (C#, Python...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Upewnij się, że zdalne renderowanie na platformie Azure może uzyskać dostęp do konta magazynu

Usługa Azure Remote Rending musi pobrać dane modelu z konta magazynu i zapisać z powrotem dane.

Dostęp do usługi Azure Remote rendering można udzielić na koncie magazynu na dwa sposoby:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Połącz swoje konto usługi Azure Storage z kontem renderowania zdalnego na platformie Azure

Wykonaj czynności podane w sekcji [Tworzenie konta](../create-an-account.md#link-storage-accounts) .

### <a name="retrieve-sas-for-the-storage-containers"></a>Pobieranie sygnatury dostępu współdzielonego dla kontenerów magazynu

Przechowywane sygnatury dostępu są używane do udzielania dostępu do odczytu dla danych wejściowych i dostępu do zapisu dla danych wyjściowych. Zalecamy generowanie nowych identyfikatorów URI przy każdej konwersji modelu. Ponieważ identyfikatory URI wygasają po pewnym czasie, utrwalenie ich przez dłuższy czas może spowodować nieoczekiwane uszkodzenie aplikacji.

Szczegóły dotyczące sygnatury dostępu współdzielonego można znaleźć w [dokumentacji sygnatury dostępu współdzielonego](../../../storage/common/storage-sas-overview.md).

Identyfikator URI sygnatury dostępu współdzielonego można wygenerować przy użyciu jednego z:

- AZ PowerShell module
  - Zobacz [przykładowe skrypty programu PowerShell](../../samples/powershell-example-scripts.md)
- [AZ Command line](/cli/azure/install-azure-cli)
- [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
  - Kliknij prawym przyciskiem myszy kontener "Uzyskaj sygnaturę dostępu współdzielonego" (odczyt, dostęp do listy dla kontenera danych wejściowych, dostęp do zapisu dla kontenera wyjściowego)
- Zestawy SDK (C#, Python...)

Przykład użycia sygnatur dostępu współdzielonego w konwersji zasobów przedstawiono w Conversion.ps1 [przykładowych skryptów programu PowerShell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Przekaż model wejściowy

Aby rozpocząć konwertowanie modelu, należy go przekazać przy użyciu jednej z następujących opcji:

- [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) — wygodny interfejs użytkownika do przekazywania/pobierania plików i zarządzania nimi w usłudze Azure Blob Storage
- [Wiersz polecenia platformy Azure](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Moduł programu Azure PowerShell](/powershell/azure/install-az-ps)
  - Zobacz [przykładowe skrypty programu PowerShell](../../samples/powershell-example-scripts.md)
- [Korzystanie z zestawu SDK magazynu (Python, C#...)](../../../storage/index.yml)
- [Korzystanie z interfejsów API REST usługi Azure Storage](/rest/api/storageservices/blob-service-rest-api)

Przykład przekazywania danych do konwersji można znaleźć w Conversion.ps1 [przykładowych skryptów programu PowerShell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Pobierz identyfikator URI sygnatury dostępu współdzielonego dla przekonwertowanego modelu

Ten krok jest podobny do [pobierania sygnatury dostępu współdzielonego dla kontenerów magazynu](#retrieve-sas-for-the-storage-containers). Jednak ten czas należy pobrać identyfikator URI sygnatury dostępu współdzielonego dla pliku modelu, który został zapisany w kontenerze danych wyjściowych.

Na przykład, aby pobrać identyfikator URI SYGNATURy dostępu współdzielonego za pośrednictwem [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), kliknij prawym przyciskiem myszy plik modelu i wybierz polecenie "Pobierz sygnaturę dostęp współdzielony".

Sygnatura dostępu współdzielonego (SAS) do załadowania modeli jest wymagana, jeśli konto magazynu nie zostało połączone z kontem renderowania zdalnego na platformie Azure. Możesz dowiedzieć się, jak połączyć konto w programie [Tworzenie konta](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie konwersji modelu](configure-model-conversion.md)
- [Interfejs API REST konwersji modelu](conversion-rest-api.md)
