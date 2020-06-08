---
title: Magazyn w chmurze umożliwiający tworzenie wysoce bezpiecznych, trwałych i skalowalnych aplikacji w usłudze Azure Storage
description: Dowiedz się więcej o usługach, które przechowują dane aplikacji mobilnej z dużą strukturą i niestrukturalną strukturą w chmurze.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: e45726cfc54963c546274e0777dd0a10d89da72d
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84483308"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Magazyn w chmurze dla wysoce bezpiecznych, trwałych i skalowalnych aplikacji z usługą Azure Storage
Usługa [Azure Storage](https://azure.microsoft.com/services/storage/) to rozwiązanie magazynu w chmurze firmy Microsoft dla nowoczesnych aplikacji, które oferuje wysoce skalowalny magazyn obiektów dla obiektów danych, usługę systemu plików dla chmury, magazyn komunikatów dla niezawodnej obsługi komunikatów i Magazyn NoSQL. Usługa Azure Storage to:
- **Trwałe i wysoce dostępne:** Nadmiarowość gwarantuje, że dane są bezpieczne w przypadku przejściowych awarii sprzętu. Dla dodatkowej ochrony przed lokalnymi katastrofami lub klęskami żywiołowymi można również zdecydować się na replikowanie danych między centrami danych lub regionami geograficznymi. Dane replikowane w ten sposób pozostają wysoce dostępne w przypadku awarii.
- **Zabezpiecz:** Wszystkie dane zapisywane w usłudze Azure Storage są szyfrowane przez usługę. Usługa Azure Storage zapewnia precyzyjną kontrolę tego, kto ma dostęp do danych.
- **Skalowalność:** Usługi zostały zaprojektowane tak, aby były wysoce skalowalne, aby sprostać wymaganiom magazynu i wydajności danych współczesnych aplikacji.
- **Zarządzane:** Platforma Azure obsługuje konserwację sprzętu, aktualizacje i problemy krytyczne.
- **Dostępne:** Dane są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Firma Microsoft udostępnia biblioteki klienckie w różnych językach, takich jak .NET, Java, Node. js, Python, PHP, Ruby i go oraz interfejs API REST. Obsługa skryptów jest obsługiwana w Azure PowerShell lub interfejs wiersza polecenia platformy Azure. Azure Portal i Eksplorator usługi Azure Storage oferują łatwe rozwiązania wizualne umożliwiające pracę z danymi.

Użyj następujących usług, aby włączyć magazyn w chmurze w aplikacjach mobilnych.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Usługa Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) oferuje rozwiązanie magazynu obiektów dla chmury. Magazyn obiektów BLOB jest zoptymalizowany pod kątem przechowywania dużych ilości danych bez struktury, które nie są zgodne z konkretnym modelem danych lub definicją, takimi jak tekst lub dane binarne. Obsługuje on różne języki używane przez biblioteki klienta. Magazyn obiektów BLOB jest przeznaczony dla:
- Bezpośrednie obsługiwanie obrazów lub dokumentów w przeglądarce.
- Przechowywanie plików do dostępu rozproszonego.
- Przesyłanie strumieniowe wideo i audio.
- Zapisz w plikach dziennika.
- Przechowywanie danych do tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania.
- Przechowuj dane do analizy za pomocą usługi lokalnej lub hostowanej przez platformę Azure.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com)
- [Dokumentacja usługi Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction)
- [Przewodniki Szybki start](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Samples](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) to usługa, która przechowuje strukturalne dane NoSQL w chmurze i udostępnia klucz lub magazyn atrybutów z projektem bez schematu. Usługa Azure Table Storage służy do przechowywania dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który akceptuje uwierzytelnione wywołania z chmury platformy Azure i poza nią. Tabele platformy Azure doskonale nadają się do przechowywania strukturalnych danych nierelacyjnych. Magazyn tabel jest zazwyczaj używany do:
- Przechowywanie terabajtów danych ze strukturą, które mogą obsługiwać aplikacje w skali sieci Web.
- Przechowuj zestawy danych, które nie wymagają złożonych sprzężeń, kluczy obcych ani procedur składowanych i mogą być nieznormalizowane, aby uzyskać szybki dostęp.
- Szybko Badaj dane przy użyciu indeksu klastrowanego.
- Dostęp do danych przy użyciu protokołu OData i zapytań LINQ z Windows Communication Foundation (WCF) Data Services biblioteki .NET.

Magazyn tabel służy do przechowywania i wykonywania zapytań dotyczących dużych zestawów strukturalnych, nierelacyjnych danych. Tabele są skalowane w miarę wzrostu zapotrzebowania.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com)
- [Dokumentacja usługi Azure Table Storage](/azure/storage/tables/table-storage-overview)
- [Samples](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Przewodniki Szybki start](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure queue storage](https://azure.microsoft.com/services/storage/queues/) to usługa służąca do przechowywania dużej liczby komunikatów. Użytkownik uzyskuje dostęp do komunikatów z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń przy użyciu protokołu HTTP lub HTTPS. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com)
- [Dokumentacja usługi Azure Queue Storage](/azure/storage/queues/)
- [Przewodniki Szybki start](/azure/storage/queues/storage-quickstart-queues-portal)
- [Przykłady](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
