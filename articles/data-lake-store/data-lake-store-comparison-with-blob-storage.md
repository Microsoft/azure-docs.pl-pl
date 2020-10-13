---
title: Porównanie Azure Data Lake Storage Gen1 z usługą BLOB Storage
description: Poznaj różnice między Azure Data Lake Storage Gen1 i Blob Storage platformy Azure dotyczące niektórych kluczowych aspektów przetwarzania danych Big Data.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 170f20cbd3405ea6af8aef5650e4dd7ebeaeef7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89458216"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Porównanie Azure Data Lake Storage Gen1 i platformy Azure Blob Storage

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

W tabeli w tym artykule zestawiono różnice między Azure Data Lake Storage Gen1 i Blob Storage platformy Azure, a także kluczowe aspekty przetwarzania danych Big Data. Azure Blob Storage to ogólny cel, skalowalny magazyn obiektów przeznaczony dla szerokiej gamy scenariuszy magazynowych. Azure Data Lake Storage Gen1 to repozytorium skalowania funkcji Hyper-Skala, które jest zoptymalizowane pod kątem obciążeń związanych z analizą danych Big Data.

| Kategoria | Usługa Azure Data Lake Storage 1. generacji | Azure Blob Storage |
| -------- | ---------------------------- | ------------------ |
| Przeznaczenie |Zoptymalizowany magazyn dla obciążeń analizy danych Big Data |Magazyn obiektów ogólnego przeznaczenia dla wielu różnych scenariuszy magazynowania, w tym analizy danych Big Data |
| Przypadki użycia |Batch, Interactive, Stream Analytics i uczenie maszynowe, takie jak pliki dziennika, dane IoT, kliknij strumienie, duże zestawy danych |Dowolny typ danych tekstowych lub binarnych, takich jak zaplecze aplikacji, dane kopii zapasowej, magazyn multimediów do przesyłania strumieniowego i dane ogólne ogólnego przeznaczenia. Dodatkowo pełna obsługa obciążeń analitycznych; Batch, Interactive, Stream Analytics i uczenie maszynowe, takie jak pliki dziennika, dane IoT, kliknij strumienie, duże zestawy danych |
| Kluczowe pojęcia |Konto Data Lake Storage Gen1 zawiera foldery, które z kolei zawierają dane przechowywane jako pliki |Konto magazynu ma kontenery, które z kolei mają dane w postaci obiektów BLOB |
| Struktura |Hierarchiczny system plików |Magazyn obiektów z płaską przestrzenią nazw |
| Interfejs API |Interfejs API REST za pośrednictwem protokołu HTTPS |Interfejs API REST za pośrednictwem protokołu HTTP/HTTPS |
| Interfejs API po stronie serwera |[Interfejs API REST zgodny z WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Interfejs API REST usługi Azure Blob Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Klient systemu plików Hadoop |Tak |Tak |
| Operacje na danych — uwierzytelnianie |Na podstawie [tożsamości Azure Active Directory](../active-directory/develop/authentication-scenarios.md) |Na podstawie [kluczy dostępu](../storage/common/storage-account-keys-manage.md) współdzielonego i [kluczy sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operacje na danych — protokół uwierzytelniania |[OpenID Connect](https://openid.net/connect/). Wywołania muszą zawierać prawidłową wartość JWT (token sieci Web JSON) wydaną przez Azure Active Directory.|Kod uwierzytelniania wiadomości oparte na skrótach (HMAC). Wywołania muszą zawierać skrót SHA-256 zakodowany algorytmem Base64 w ramach żądania HTTP. |
| Operacje na danych — autoryzacja |Listy Access Control POSIX (ACL).  Listy ACL oparte na tożsamościach Azure Active Directory można ustawić na poziomie plików i folderów. |W przypadku autoryzacji na poziomie konta — Użyj [kluczy dostępu do konta](../storage/common/storage-account-keys-manage.md)<br>Dla konta, kontenera lub autoryzacji obiektów BLOB — Użyj [kluczy sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operacje na danych — Inspekcja |Udostępnione. Aby [uzyskać więcej informacji](data-lake-store-diagnostic-logs.md) , zobacz. |Dostępne |
| Szyfrowanie danych w spoczynku |<ul><li>Przezroczysty, po stronie serwera</li> <ul><li>Z kluczami zarządzanymi przez usługę</li><li>Z kluczami zarządzanymi przez klienta w usłudze Azure Keys</li></ul></ul> |<ul><li>Przezroczysty, po stronie serwera</li> <ul><li>Z kluczami zarządzanymi przez usługę</li><li>Z kluczami zarządzanymi przez klienta w magazynie kluczy platformy Azure (wersja zapoznawcza)</li></ul><li>Szyfrowania po stronie klienta</li></ul> |
| Operacje zarządzania (na przykład tworzenie konta) |[Kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md) na potrzeby zarządzania kontami |[Kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md) na potrzeby zarządzania kontami |
| Zestawy SDK dla deweloperów |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, go, Android, iOS |
| Wydajność obciążeń analitycznych |Zoptymalizowana wydajność dla obciążeń równoległych analizy. Wysoka przepływność i operacje we/wy. |Zoptymalizowana wydajność dla obciążeń równoległych analizy. |
| Limity rozmiaru |Brak limitów rozmiarów kont, rozmiarów plików ani liczby plików |Aby uzyskać określone limity, zobacz [elementy docelowe skalowalności dla standardowych kont magazynu](../storage/common/scalability-targets-standard-account.md) i [skalowalności oraz docelowych wydajności dla usługi BLOB Storage](../storage/blobs/scalability-targets.md). Większe limity konta dostępne przez skontaktowanie się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/) |
| Nadmiarowość geograficzna |Lokalnie nadmiarowy (wiele kopii danych w jednym regionie platformy Azure) |Lokalnie nadmiarowy (LRS), strefy nadmiarowe (ZRS), globalnie nadmiarowy (GRS), dostęp do odczytu globalnie nadmiarowy (RA-GRS). Aby uzyskać więcej informacji, zobacz [tutaj](../storage/common/storage-redundancy.md) . |
| Stan usługi |Ogólnie dostępne |Ogólnie dostępne |
| Dostępność regionalna |Zobacz [tutaj](https://azure.microsoft.com/regions/#services) |Dostępne we wszystkich regionach świadczenia usługi Azure |
| Cena |Zobacz [Cennik](https://azure.microsoft.com/pricing/details/data-lake-store/) |Zobacz [Cennik](https://azure.microsoft.com/pricing/details/storage/) |
