---
title: Klucze zarządzane przez klienta do szyfrowania kont
titleSuffix: Azure Storage
description: Możesz użyć własnego klucza szyfrowania do ochrony danych na koncie magazynu. Gdy określisz klucz zarządzany przez klienta, ten klucz będzie używany w celu ochrony i kontroli dostępu do klucza szyfrującego dane. Klucze zarządzane przez klienta zapewniają większą elastyczność zarządzania kontrolą dostępu.
services: storage
author: tamram
ms.service: storage
ms.date: 09/15/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: b7a6584b1566d1fa2e1e250938212a01d845e113
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578227"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Klucze zarządzane przez klienta dla szyfrowania usługi Azure Storage

Możesz użyć własnego klucza szyfrowania do ochrony danych na koncie magazynu. Gdy określisz klucz zarządzany przez klienta, ten klucz będzie używany w celu ochrony i kontroli dostępu do klucza szyfrującego dane. Klucze zarządzane przez klienta zapewniają większą elastyczność zarządzania kontrolą dostępu.

Aby przechowywać klucze zarządzane przez klienta, należy użyć Azure Key Vault lub Azure Key Vault zarządzanego modelu zabezpieczeń sprzętu (HSM) (wersja zapoznawcza). Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub zarządzanym module HSM lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Konto magazynu i Magazyn kluczy lub zarządzany moduł HSM muszą znajdować się w tym samym regionie i w tej samej dzierżawie Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach.

Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/general/overview.md).

> [!NOTE]
> Azure Key Vault i Azure Key Vault zarządzanym modułem HSM obsługują te same interfejsy API i interfejsów zarządzania na potrzeby konfiguracji.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-customer-managed-keys"></a>Informacje o kluczach zarządzanych przez klienta

Na poniższym diagramie pokazano, w jaki sposób usługa Azure Storage używa Azure Active Directory i magazynu kluczy lub zarządzanego modułu HSM do wykonywania żądań przy użyciu klucza zarządzanego przez klienta:

![Diagram przedstawiający sposób działania kluczy zarządzanych przez klienta w usłudze Azure Storage](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

Poniższa lista zawiera opis kroków w diagramie:

1. Administrator Azure Key Vault przyznaje uprawnienia do kluczy szyfrowania do zarządzanej tożsamości skojarzonej z kontem magazynu.
2. Administrator usługi Azure Storage konfiguruje szyfrowanie za pomocą klucza zarządzanego przez klienta dla konta magazynu.
3. Usługa Azure Storage korzysta z zarządzanej tożsamości skojarzonej z kontem magazynu w celu uwierzytelniania dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.
4. Usługa Azure Storage zawija klucz szyfrowania konta przy użyciu klucza klienta w Azure Key Vault.
5. W przypadku operacji odczytu/zapisu usługa Azure Storage wysyła żądania do Azure Key Vault w celu odpakowania klucza szyfrowania konta w celu wykonania operacji szyfrowania i odszyfrowywania.

## <a name="customer-managed-keys-for-queues-and-tables"></a>Klucze zarządzane przez klienta dla kolejek i tabel

Dane przechowywane w kolejkach i w usłudze Table Storage nie są automatycznie chronione przez klucz zarządzany przez klienta, gdy dla konta magazynu są włączone klucze zarządzane przez klienta. Opcjonalnie można skonfigurować te usługi do uwzględnienia w tej ochronie w momencie utworzenia konta magazynu.

Aby uzyskać więcej informacji na temat tworzenia konta magazynu obsługującego klucze zarządzane przez klienta dla kolejek i tabel, zobacz [Tworzenie konta, które obsługuje klucze zarządzane przez klienta dla tabel i kolejek](account-encryption-key-create.md).

Dane w magazynie obiektów blob i Azure Files są zawsze chronione przez klucze zarządzane przez klienta, gdy dla konta magazynu są skonfigurowane klucze zarządzane przez klienta.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Włączanie kluczy zarządzanych przez klienta dla konta magazynu

Po skonfigurowaniu klucza zarządzanego przez klienta usługa Azure Storage zawija klucz szyfrowania danych głównych dla konta z kluczem zarządzanym przez klienta w skojarzonym magazynie kluczy lub zarządzanym module HSM. Włączenie kluczy zarządzanych przez klienta nie ma wpływu na wydajność i zaczyna obowiązywać natychmiast.

Po włączeniu lub wyłączeniu kluczy zarządzanych przez klienta lub zmodyfikowaniu klucza lub wersji klucza ochrona głównego klucza szyfrowania jest zmieniana, ale nie trzeba ponownie szyfrować danych na koncie usługi Azure Storage.

Klucze zarządzane przez klienta można włączyć tylko dla istniejących kont magazynu. Magazyn kluczy lub zarządzany moduł HSM musi być skonfigurowany w taki sposób, aby przyznać uprawnienia do zarządzanej tożsamości skojarzonej z kontem magazynu. Tożsamość zarządzana jest dostępna tylko po utworzeniu konta magazynu.

W dowolnym momencie można przełączać się między kluczami zarządzanymi przez klienta i kluczami zarządzanymi przez firmę Microsoft. Aby uzyskać więcej informacji na temat kluczy zarządzanych przez firmę Microsoft, zobacz [Informacje o zarządzaniu kluczami szyfrowania](storage-service-encryption.md#about-encryption-key-management).

Aby dowiedzieć się, jak skonfigurować szyfrowanie usługi Azure Storage za pomocą kluczy zarządzanych przez klienta w magazynie kluczy, zobacz [Konfigurowanie szyfrowania z kluczami zarządzanymi przez klienta, które są przechowywane w Azure Key Vault](customer-managed-keys-configure-key-vault.md). Aby skonfigurować klucze zarządzane przez klienta w zarządzanym module HSM, zobacz [Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta, przechowywanych w Azure Key Vault zarządzanym module HSM (wersja zapoznawcza)](customer-managed-keys-configure-key-vault-hsm.md).

> [!IMPORTANT]
> Klucze zarządzane przez klienta wykorzystują zarządzane tożsamości dla zasobów platformy Azure, funkcję usługi Azure AD. Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. Po skonfigurowaniu kluczy zarządzanych przez klienta w Azure Portal, zarządzana tożsamość zostanie automatycznie przypisana do konta magazynu w obszarze okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub konto magazynu z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z kontem magazynu nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz **transfer subskrypcji między katalogami usługi Azure AD** w [często zadawanych pytaniach i znanych problemach z tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

Szyfrowanie za pomocą usługi Azure Storage obsługuje klucze RSA i RSA-HSM o rozmiarach 2048, 3072 i 4096. Aby uzyskać więcej informacji na temat kluczy, zobacz **Key Vault klucze** w temacie [informacje Azure Key Vault klucze, wpisy tajne i certyfikaty](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

Przy użyciu magazynu kluczy lub zarządzanego modułu HSM są związane z nimi koszty. Aby uzyskać więcej informacji, zobacz [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="update-the-key-version"></a>Zaktualizuj wersję klucza

Podczas konfigurowania szyfrowania przy użyciu kluczy zarządzanych przez klienta dostępne są dwie opcje aktualizacji wersji klucza:

- **Automatycznie zaktualizuj wersję klucza:** Aby automatycznie zaktualizować klucz zarządzany przez klienta, gdy jest dostępna nowa wersja, Pomiń wersję klucza po włączeniu szyfrowania z kluczami zarządzanymi przez klienta dla konta magazynu. W przypadku pominięcia wersji klucza usługa Azure Storage sprawdza Magazyn kluczy lub moduł modułu HSM codziennie dla nowej wersji klucza zarządzanego przez klienta. Usługa Azure Storage automatycznie używa najnowszej wersji klucza.
- **Ręcznie zaktualizuj wersję klucza:** Aby użyć określonej wersji klucza do szyfrowania usługi Azure Storage, należy określić tę wersję klucza po włączeniu szyfrowania przy użyciu kluczy zarządzanych przez klienta dla konta magazynu. W przypadku określenia wersji klucza usługa Azure Storage korzysta z tej wersji do szyfrowania do momentu ręcznego zaktualizowania wersji klucza.

    Po określeniu określonej wersji klucza należy ręcznie zaktualizować konto magazynu, aby używało nowego identyfikatora URI wersji klucza podczas tworzenia nowej wersji. Aby dowiedzieć się, jak zaktualizować konto magazynu tak, aby używało nowej wersji klucza, zobacz [Konfigurowanie szyfrowania z kluczami zarządzanymi przez klienta w Azure Key Vault](customer-managed-keys-configure-key-vault.md) lub [Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta przechowywanych w Azure Key Vault zarządzanym module HSM (wersja zapoznawcza)](customer-managed-keys-configure-key-vault-hsm.md).

Aktualizacja wersji klucza dla klucza zarządzanego przez klienta nie wyzwala ponownego szyfrowania danych na koncie magazynu. Od użytkownika nie są wymagane żadne dalsze działania.

> [!NOTE]
> Aby obrócić klucz, Utwórz nową wersję klucza w magazynie kluczy lub zarządzanym module HSM zgodnie z zasadami zgodności. Możesz obrócić klucz ręcznie lub utworzyć funkcję, aby obrócić ją zgodnie z harmonogramem.

## <a name="revoke-access-to-customer-managed-keys"></a>Odwołaj dostęp do kluczy zarządzanych przez klienta

W dowolnym momencie można odwołać dostęp do konta magazynu do klucza zarządzanego przez klienta. Po odwołaniu dostępu do kluczy zarządzanych przez klienta lub po wyłączeniu lub usunięciu klucza klienci nie mogą wywoływać operacji, które odczytują lub zapisują do obiektu BLOB lub jego metadanych. Próby wywołania dowolnej z następujących operacji zakończą się niepowodzeniem z kodem błędu 403 (dostęp zabroniony) dla wszystkich użytkowników:

- [Wyświetl listę obiektów BLOB](/rest/api/storageservices/list-blobs), gdy zostanie wywołana z `include=metadata` parametrem identyfikatora URI żądania
- [Pobierz obiekt BLOB](/rest/api/storageservices/get-blob)
- [Pobieranie właściwości obiektu blob](/rest/api/storageservices/get-blob-properties)
- [Pobierz metadane obiektu BLOB](/rest/api/storageservices/get-blob-metadata)
- [Ustawianie metadanych obiektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Obiekt BLOB Snapshot](/rest/api/storageservices/snapshot-blob), gdy jest wywoływany z `x-ms-meta-name` nagłówkiem żądania
- [Kopiowanie obiektu blob](/rest/api/storageservices/copy-blob)
- [Kopiuj obiekt BLOB z adresu URL](/rest/api/storageservices/copy-blob-from-url)
- [Ustawianie warstwy obiektu blob](/rest/api/storageservices/set-blob-tier)
- [Umieść blok](/rest/api/storageservices/put-block)
- [Umieść blok z adresu URL](/rest/api/storageservices/put-block-from-url)
- [Dołącz blok](/rest/api/storageservices/append-block)
- [Dołącz blok z adresu URL](/rest/api/storageservices/append-block-from-url)
- [Wstawianie obiektu blob](/rest/api/storageservices/put-blob)
- [Umieść stronę](/rest/api/storageservices/put-page)
- [Umieść stronę na podstawie adresu URL](/rest/api/storageservices/put-page-from-url)
- [Obiekt BLOB kopiowania przyrostowego](/rest/api/storageservices/incremental-copy-blob)

Aby ponownie wywołać te operacje, Przywróć dostęp do klucza zarządzanego przez klienta.

Wszystkie operacje na danych, które nie są wymienione w tej sekcji, mogą zostać przeprowadzone po odwołaniu kluczy zarządzanych przez klienta lub wyłączeniu lub usunięciu klucza.

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć [programu PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) lub [interfejsu wiersza polecenia platformy Azure](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Klucze zarządzane przez klienta dla usługi Azure Managed disks

Klucze zarządzane przez klienta są również dostępne do zarządzania szyfrowaniem usługi Azure Managed Disks. Klucze zarządzane przez klienta działają inaczej niż w przypadku zasobów usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [szyfrowanie po stronie serwera dla usługi Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) dla systemu Windows lub [szyfrowanie po stronie serwera dla usługi Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) w systemie Linux.

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Skonfiguruj szyfrowanie za pomocą kluczy zarządzanych przez klienta, przechowywanych w Azure Key Vault](customer-managed-keys-configure-key-vault.md)
- [Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta, przechowywanych w Azure Key Vault zarządzanym module HSM (wersja zapoznawcza)](customer-managed-keys-configure-key-vault-hsm.md)
