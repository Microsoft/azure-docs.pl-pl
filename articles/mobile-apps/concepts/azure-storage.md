---
title: Magazyn w chmurze umożliwiający tworzenie wysoce bezpiecznych, trwałych i skalowalnych aplikacji w usłudze Azure Storage
description: Dowiedz się więcej o usługach, które przechowują dane aplikacji mobilnej z dużą strukturą i niestrukturalną strukturą w chmurze.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 6f8ed02659f1720435df3ff98817233a5a46dbcb
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450990"
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

## <a name="azure-files"></a>Azure Files
Za pomocą [Azure Files](https://azure.microsoft.com/services/storage/files/)można skonfigurować udziały plików o wysokiej dostępności, do których można uzyskać dostęp przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Wiele maszyn wirtualnych może współużytkować te same pliki z dostępem do odczytu i zapisu. Pliki można również odczytywać przy użyciu interfejsu REST lub bibliotek klienta usługi Storage. Możesz uzyskać dostęp do plików z dowolnego miejsca na świecie przy użyciu adresu URL, który wskazuje plik i zawiera token sygnatury dostępu współdzielonego (SAS). Można generować tokeny SAS. Umożliwiają one określony dostęp do prywatnego zasobu przez określony czas.

Udziały plików platformy Azure mogą być używane w następujących celach:
- **Zastąp lub Uzupełnij lokalne serwery plików:** Popularne systemy operacyjne, takie jak Windows, macOS i Linux, mogą bezpośrednio instalować udziały plików platformy Azure wszędzie tam, gdzie znajdują się na świecie. Udziały plików platformy Azure można również replikować za pomocą usługi Azure File Sync w systemach Windows Server, lokalnie lub w chmurze, w celu zapewnienia wydajnego i rozproszonego buforowania danych w miejscu ich używania.
- **Aplikacje podnoszenia i przesunięcia:** Migruj aplikacje do chmury, które oczekują udziału plików na przechowywanie plików aplikacji lub danych użytkownika.
- **Uproszczenie programowania w chmurze:** Azure Files można również używać na wiele sposobów w celu uproszczenia nowych projektów programistycznych w chmurze. Przykład:
    - **Ustawienia aplikacji udostępnionej:** Typowym wzorcem aplikacji rozproszonych jest posiadanie plików konfiguracji w scentralizowanej lokalizacji, w której można uzyskać do nich dostęp z wielu wystąpień aplikacji. Wystąpienia aplikacji mogą ładować swoją konfigurację za pomocą interfejsu API REST plików. Użytkownicy mogą uzyskiwać do nich dostęp w razie konieczności, instalując udział SMB lokalnie.
    - **Udział diagnostyczny:** Udział plików platformy Azure jest wygodnym miejscem do zapisywania dzienników, metryk i zrzutów awaryjnych aplikacji w chmurze. Dzienniki mogą być zapisywane przez wystąpienia aplikacji za pośrednictwem interfejsu API REST plików. Deweloperzy mogą uzyskać do nich dostęp, instalując udział plików na ich maszynach lokalnych. Ta funkcja zapewnia dużą elastyczność. Deweloperzy mogą wdrożyć programowanie w chmurze bez konieczności porzucenia istniejących narzędzi, które znają.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com)
- [Dokumentacja usługi Azure Files](/azure/storage/files/storage-files-introduction)
- [Przewodniki Szybki start](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue Storage
[Azure queue storage](https://azure.microsoft.com/services/storage/queues/) to usługa służąca do przechowywania dużej liczby komunikatów. Użytkownik uzyskuje dostęp do komunikatów z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń przy użyciu protokołu HTTP lub HTTPS. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com)
- [Dokumentacja usługi Azure Queue Storage](/azure/storage/queues/)
- [Przewodniki Szybki start](/azure/storage/queues/storage-quickstart-queues-portal)
- [Przykłady](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
