---
title: Podaj klucz szyfrowania dla żądania do magazynu obiektów BLOB
titleSuffix: Azure Storage
description: Klienci, którzy wysyłają żądania do usługi Azure Blob Storage, mają możliwość zapewnienia klucza szyfrowania dla każdego żądania. Dołączenie klucza szyfrowania żądania zapewnia szczegółową kontrolę nad ustawieniami szyfrowania operacji usługi BLOB Storage.
services: storage
author: tamram
ms.service: storage
ms.date: 12/14/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcc5c02c4a37e205622470260d3c620ad76d07d8
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694703"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>Podaj klucz szyfrowania dla żądania do magazynu obiektów BLOB

Klienci wysyłający żądania do usługi Azure Blob Storage mogą zapewnić klucz szyfrowania AES-256 dla każdego żądania. Dołączenie klucza szyfrowania żądania zapewnia szczegółową kontrolę nad ustawieniami szyfrowania operacji usługi BLOB Storage. Klucze dostarczone przez klienta mogą być przechowywane w Azure Key Vault lub w innym magazynie kluczy.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="encrypting-read-and-write-operations"></a>Szyfrowanie operacji odczytu i zapisu

Gdy aplikacja kliencka udostępnia klucz szyfrowania w żądaniu, usługa Azure Storage wykonuje szyfrowanie i odszyfrowywanie w niewidoczny sposób podczas odczytywania i zapisywania danych obiektów BLOB. Usługa Azure Storage zapisuje skrót SHA-256 klucza szyfrowania obok zawartości obiektu BLOB. Skrót jest używany do sprawdzania, czy wszystkie kolejne operacje dotyczące obiektu BLOB używają tego samego klucza szyfrowania.

Usługa Azure Storage nie przechowuje klucza szyfrowania, który jest wysyłany przez klienta wraz z żądaniem, ani nie zarządza nim. Klucz jest bezpiecznie odrzucany zaraz po zakończeniu procesu szyfrowania lub odszyfrowywania.

Gdy klient tworzy lub aktualizuje obiekt BLOB przy użyciu klucza dostarczonego przez klienta w żądaniu, kolejne żądania odczytu i zapisu dla tego obiektu BLOB muszą również dostarczyć klucz. Jeśli nie podano klucza dla żądania obiektu BLOB, który został już zaszyfrowany za pomocą klucza dostarczonego przez klienta, żądanie kończy się niepowodzeniem z kodem błędu 409 (konflikt).

Jeśli aplikacja kliencka wysyła klucz szyfrowania na żądanie, a konto magazynu jest również szyfrowane przy użyciu klucza zarządzanego przez firmę Microsoft lub klucza zarządzanego przez klienta, usługa Azure Storage korzysta z klucza podanego w żądaniu szyfrowania i odszyfrowywania.

Aby wysłać klucz szyfrowania w ramach żądania, klient musi nawiązać bezpieczne połączenie z usługą Azure Storage przy użyciu protokołu HTTPS.

Każda migawka obiektu BLOB może mieć własny klucz szyfrowania.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Nagłówki żądań określające klucze dostarczone przez klienta

W przypadku wywołań REST klienci mogą używać następujących nagłówków do bezpiecznego przekazywania informacji o kluczu szyfrowania na żądanie do magazynu obiektów blob:

|Nagłówek żądania | Opis |
|---------------|-------------|
|`x-ms-encryption-key` |Wymagane dla żądań zapisu i odczytu. Zakodowana algorytmem Base64 wartość klucza szyfrowania AES-256. |
|`x-ms-encryption-key-sha256`| Wymagane dla żądań zapisu i odczytu. SHA256 szyfrowany algorytmem Base64 klucza szyfrowania. |
|`x-ms-encryption-algorithm` | Wymagane w przypadku żądań zapisu, które są opcjonalne w przypadku żądań odczytu. Określa algorytm używany do szyfrowania danych przy użyciu podanego klucza.  Wartością tego nagłówka musi być `AES256` . |

Określanie kluczy szyfrowania w żądaniu jest opcjonalne. Jednak w przypadku określenia jednego z nagłówków wymienionych powyżej dla operacji zapisu należy określić wszystkie z nich.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operacje magazynu obiektów BLOB obsługujące klucze dostarczone przez klienta

Następujące operacje magazynu obiektów BLOB obsługują wysyłanie kluczy szyfrowania dostarczonych przez klienta na żądanie:

- [Wstawianie obiektu blob](/rest/api/storageservices/put-blob)
- [Umieść listę zablokowanych](/rest/api/storageservices/put-block-list)
- [Umieść blok](/rest/api/storageservices/put-block)
- [Umieść blok z adresu URL](/rest/api/storageservices/put-block-from-url)
- [Umieść stronę](/rest/api/storageservices/put-page)
- [Umieść stronę na podstawie adresu URL](/rest/api/storageservices/put-page-from-url)
- [Dołącz blok](/rest/api/storageservices/append-block)
- [Ustawianie właściwości obiektu BLOB](/rest/api/storageservices/set-blob-properties)
- [Ustawianie metadanych obiektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Pobierz obiekt BLOB](/rest/api/storageservices/get-blob)
- [Pobieranie właściwości obiektu blob](/rest/api/storageservices/get-blob-properties)
- [Pobierz metadane obiektu BLOB](/rest/api/storageservices/get-blob-metadata)
- [Wykonywanie migawki obiektu blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Obróć klucze dostarczone przez klienta

Aby obrócić klucz szyfrowania, który został użyty do zaszyfrowania obiektu BLOB, Pobierz obiekt BLOB, a następnie Przekaż go ponownie przy użyciu nowego klucza szyfrowania.

> [!IMPORTANT]
> Nie można użyć Azure Portal do odczytu lub zapisu do kontenera lub obiektu BLOB zaszyfrowanego przy użyciu klucza dostarczonego w żądaniu.
>
> Pamiętaj, aby chronić klucz szyfrowania udostępniany w żądaniu do magazynu obiektów BLOB w bezpiecznym magazynie kluczy, takim jak Azure Key Vault. Jeśli podjęto próbę wykonania operacji zapisu w kontenerze lub obiekcie blob bez klucza szyfrowania, operacja zakończy się niepowodzeniem i utracisz dostęp do obiektu.

## <a name="next-steps"></a>Następne kroki

- [Określ klucz dostarczony przez klienta w żądaniu do magazynu obiektów BLOB za pomocą platformy .NET](storage-blob-customer-provided-key.md)
- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../common/storage-service-encryption.md)
