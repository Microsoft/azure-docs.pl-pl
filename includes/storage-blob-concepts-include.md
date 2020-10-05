---
title: plik dołączania
description: plik dołączania
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 7c3438631dca921989309bb8701e113cb5ce3ff2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89570273"
---
Azure Blob Storage to rozwiązanie do magazynowania obiektów w chmurze firmy Microsoft. Magazyn obiektów blob jest zoptymalizowany pod kątem przechowywania olbrzymich ilości danych bez struktury. Dane bez struktury to dane, które nie są zgodne z konkretnym modelem danych lub definicją, takimi jak dane tekstowe lub binarne.

## <a name="about-blob-storage"></a>Informacje o usłudze Blob Storage

Przeznaczenie usługi Blob Storage:

* Obsługiwanie obrazów i dokumentów bezpośrednio w przeglądarce.
* Przechowywanie plików do dostępu rozproszonego.
* Przesyłanie strumieniowe audio i wideo.
* Zapisywanie plików dziennika.
* Zapisywanie danych w celu tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania.
* Przechowywanie danych w celu analizy w usłudze lokalnej lub hostowanej na platformie Azure.

Użytkownicy lub aplikacje klienckie mogą uzyskiwać dostęp do obiektów w magazynie obiektów blob za pośrednictwem protokołu HTTP/HTTPS z dowolnego miejsca na świecie. Obiekty w magazynie obiektów blob są dostępne za pośrednictwem [interfejsu API REST usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/storage) lub biblioteki klienta usługi Azure Storage. Biblioteki klienckie są dostępne dla różnych języków, w tym:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node)
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Przejdź](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Informacje o usłudze Azure Data Lake Storage Gen2

Magazyn obiektów blob obsługuje usługę Azure Data Lake Storage Gen2 — rozwiązanie firmy Microsoft do analizy danych big data dla przedsiębiorstw w chmurze. Azure Data Lake Storage Gen2 oferuje hierarchiczny system plików oraz zalety magazynu obiektów blob, w tym:

* Niski koszt magazynu warstwowego
* Wysoka dostępność
* Silna spójność
* Możliwości odzyskiwania po awarii

Aby uzyskać więcej informacji na temat Data Lake Storage Gen2, zobacz [wprowadzenie do Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
