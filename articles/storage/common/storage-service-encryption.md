---
title: Szyfrowanie w usłudze Azure Storage dla danych magazynowanych
description: Usługa Azure Storage chroni dane, automatycznie szyfrując je przed ich utrwaleniem do chmury. Możesz polegać na kluczach zarządzanych przez firmę Microsoft, aby szyfrować dane na koncie magazynu, lub możesz zarządzać szyfrowaniem przy użyciu własnych kluczy.
services: storage
author: tamram
ms.service: storage
ms.date: 03/23/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 0688e14b77d885132d6c3fbaa44bed117cc7cf9d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641128"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Szyfrowanie w usłudze Azure Storage dla danych magazynowanych

Usługa Azure Storage używa szyfrowania po stronie serwera (SSE), aby automatycznie szyfrować dane po utrwaleniu ich w chmurze. Szyfrowanie usługi Azure Storage chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji.

## <a name="about-azure-storage-encryption"></a>Informacje o szyfrowaniu usługi Azure Storage

Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Szyfrowanie usługi Azure Storage jest podobne do szyfrowania funkcją BitLocker w systemie Windows.

Szyfrowanie usługi Azure Storage jest włączone dla wszystkich kont magazynu, w tym kont magazynu Menedżer zasobów i klasycznych. Nie można wyłączyć szyfrowania usługi Azure Storage. Ponieważ dane są zabezpieczone domyślnie, nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania usługi Azure Storage.

Dane na koncie magazynu są szyfrowane niezależnie od warstwy wydajności (standardowa lub Premium), warstwy dostępu (gorąca lub chłodna) lub modelu wdrażania (Azure Resource Manager lub klasycznego). Wszystkie obiekty blob w warstwie archiwum również są szyfrowane. Wszystkie opcje nadmiarowości usługi Azure Storage obsługują szyfrowanie, a wszystkie dane w regionach podstawowym i pomocniczym są szyfrowane po włączeniu replikacji geograficznej. Wszystkie zasoby usługi Azure Storage są szyfrowane, w tym obiektów blob, dysków, plików, kolejek i tabel. Wszystkie metadane obiektu są również szyfrowane. Nie ma dodatkowych opłat za szyfrowanie usługi Azure Storage.

Każdy blokowy obiekt BLOB, dołączany obiekt BLOB lub stronicowy obiekt BLOB, który został zapisany w usłudze Azure Storage po 20 października 2017, jest szyfrowany. Obiekty blob utworzone przed tą datą nadal są szyfrowane przez proces w tle. Aby wymusić szyfrowanie obiektu BLOB, który został utworzony przed 20 października 2017, można ponownie napisać obiekt BLOB. Aby dowiedzieć się, jak sprawdzić stan szyfrowania obiektu BLOB, zobacz [Sprawdzanie stanu szyfrowania obiektu BLOB](../blobs/storage-blob-encryption-status.md).

Aby uzyskać więcej informacji na temat modułów kryptograficznych związanych z szyfrowaniem usługi Azure Storage, zobacz [interfejs API kryptografii: Kolejna generacja](/windows/desktop/seccng/cng-portal).

Informacje o szyfrowaniu i zarządzaniu kluczami dla usługi Azure Managed disks można znaleźć w temacie [szyfrowanie po stronie serwera dla usługi Azure Managed disks](../../virtual-machines/disk-encryption.md).

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Dane na nowym koncie magazynu są domyślnie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Możesz w dalszym ciągu korzystać z kluczy zarządzanych przez firmę Microsoft do szyfrowania danych lub możesz zarządzać szyfrowaniem przy użyciu własnych kluczy. W przypadku wybrania opcji zarządzania szyfrowaniem przy użyciu własnych kluczy dostępne są dwie opcje. Można użyć dowolnego typu zarządzania kluczami lub obu:

- Możesz określić *klucz zarządzany przez klienta* , który będzie używany do szyfrowania i odszyfrowywania danych w magazynie obiektów blob i w Azure Files. <sup>1, 2</sup> klucze zarządzane przez klienta muszą być przechowywane w Azure Key Vault lub Azure Key Vault zarządzanym sprzętowym modelem zabezpieczeń (HSM) (wersja zapoznawcza). Aby uzyskać więcej informacji o kluczach zarządzanych przez klienta, zobacz [Korzystanie z kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage](./customer-managed-keys-overview.md).
- *Klucz dostarczony przez klienta* można określić w operacjach magazynu obiektów BLOB. Klient wykonujący żądanie odczytu lub zapisu w usłudze BLOB Storage może dołączyć klucz szyfrowania żądania, aby uzyskać szczegółową kontrolę nad sposobem szyfrowania i odszyfrowywania danych obiektów BLOB. Więcej informacji o kluczach dostarczonych przez klienta znajduje się [w temacie zapewnianie klucza szyfrowania w żądaniu usługi BLOB Storage](../blobs/encryption-customer-provided-keys.md).

Poniższa tabela zawiera porównanie opcji zarządzania kluczami dla szyfrowania usługi Azure Storage.

| Parametr zarządzania kluczami | Klucze zarządzane przez firmę Microsoft | Klucze zarządzane przez klienta | Klucze dostarczone przez klienta |
|--|--|--|--|
| Operacje szyfrowania/odszyfrowywania | Azure | Azure | Azure |
| Obsługiwane usługi Azure Storage | Wszystko | BLOB Storage, Azure Files<sup>1, 2</sup> | Blob Storage |
| Magazyn kluczy | Magazyn kluczy firmy Microsoft | Moduł HSM Azure Key Vault lub Key Vault | Własny magazyn kluczy klienta |
| Odpowiedzialność za kluczowe rotacje | Microsoft | Customer | Customer |
| Klucz — formant | Microsoft | Customer | Customer |

<sup>1</sup> Aby uzyskać informacje na temat tworzenia konta obsługującego Używanie kluczy zarządzanych przez klienta z usługą queue storage, zobacz [Tworzenie konta, które obsługuje klucze zarządzane przez klienta dla kolejek](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Aby uzyskać informacje na temat tworzenia konta, które obsługuje używanie kluczy zarządzanych przez klienta w usłudze Table Storage, zobacz [Tworzenie konta, które obsługuje klucze zarządzane przez klienta dla tabel](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> Klucze zarządzane przez firmę Microsoft zostały odpowiednio obrócone na wymagania dotyczące zgodności. Jeśli masz określone wymagania dotyczące rotacji kluczy, firma Microsoft zaleca przechodzenie między kluczami zarządzanymi przez klienta, aby umożliwić samodzielne zarządzanie rotacją i przeprowadzanie inspekcji.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>Podwójna szyfrowanie danych przy użyciu szyfrowania infrastruktury

Klienci, którzy wymagają wysokiego poziomu pewności, że ich dane są bezpieczne, mogą również włączyć 256-bitowe szyfrowanie AES na poziomie infrastruktury usługi Azure Storage. Po włączeniu szyfrowania infrastruktury dane na koncie magazynu są szyfrowane dwa razy &mdash; na poziomie usługi i raz na poziomie infrastruktury &mdash; z dwoma różnymi algorytmami szyfrowania i dwoma różnymi kluczami. Podwójne szyfrowanie danych usługi Azure Storage chroni przed scenariuszem, w którym można złamać jeden z algorytmów szyfrowania lub kluczy. W tym scenariuszu dodatkowa warstwa szyfrowania nadal chroni dane.

Szyfrowanie na poziomie usługi obsługuje używanie kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta w programie Azure Key Vault. Szyfrowanie na poziomie infrastruktury korzysta z kluczy zarządzanych przez firmę Microsoft i zawsze korzysta z osobnego klucza.

Aby uzyskać więcej informacji na temat tworzenia konta magazynu, które umożliwia szyfrowanie infrastruktury, zobacz [Tworzenie konta magazynu z włączoną funkcją szyfrowania infrastruktury w celu podwójnego szyfrowania danych](infrastructure-encryption-enable.md).

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)
- [Klucze zarządzane przez klienta dla szyfrowania usługi Azure Storage](customer-managed-keys-overview.md)
- [Zakresy szyfrowania dla usługi BLOB Storage](../blobs/encryption-scope-overview.md)
- [Podaj klucz szyfrowania dla żądania do magazynu obiektów BLOB](../blobs/encryption-customer-provided-keys.md)
