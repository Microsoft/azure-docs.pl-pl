---
title: Zarządzanie szyfrowaniem kont za pomocą kluczy zarządzanych przez klienta w usłudze Azure Key Vault
titleSuffix: Azure Storage
description: Możesz użyć własnego klucza szyfrowania, aby chronić dane na koncie magazynu. Po określeniu klucza zarządzanego przez klienta klucz ten jest używany do ochrony i kontrolowania dostępu do klucza, który szyfruje dane. Klucze zarządzane przez klienta oferują większą elastyczność zarządzania kontrolami dostępu.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456825"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Zarządzanie szyfrowaniem usługi Azure Storage za pomocą kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault

Możesz użyć własnego klucza szyfrowania, aby chronić dane na koncie magazynu. Po określeniu klucza zarządzanego przez klienta klucz ten jest używany do ochrony i kontrolowania dostępu do klucza, który szyfruje dane. Klucze zarządzane przez klienta oferują większą elastyczność zarządzania kontrolami dostępu.

Do przechowywania kluczy zarządzanych przez klienta należy używać usługi Azure Key Vault. Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy. Konto magazynu i magazyn kluczy muszą znajdować się w tym samym regionie i w tej samej dzierżawie usługi Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="about-customer-managed-keys"></a>Informacje o kluczach zarządzanych przez klienta

Na poniższym diagramie pokazano, jak usługa Azure Storage używa usługi Azure Active Directory i usługi Azure Key Vault do składania żądań przy użyciu klucza zarządzanego przez klienta:

![Diagram przedstawiający sposób działania kluczy zarządzanych przez klienta w usłudze Azure Storage](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

Na poniższej liście wyjaśniono ponumerowane kroki na diagramie:

1. Administrator usługi Azure Key Vault udziela uprawnień do kluczy szyfrowania do tożsamości zarządzanej skojarzonej z kontem magazynu.
2. Administrator usługi Azure Storage konfiguruje szyfrowanie za pomocą klucza zarządzanego przez klienta dla konta magazynu.
3. Usługa Azure Storage używa tożsamości zarządzanej skojarzonej z kontem magazynu w celu uwierzytelnienia dostępu do usługi Azure Key Vault za pośrednictwem usługi Azure Active Directory.
4. Usługa Azure Storage zawija klucz szyfrowania konta kluczem klienta w usłudze Azure Key Vault.
5. W przypadku operacji odczytu/zapisu usługa Azure Storage wysyła żądania do usługi Azure Key Vault w celu odrapania klucza szyfrowania konta w celu wykonania operacji szyfrowania i odszyfrowywania.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Tworzenie konta obsługującego klucze zarządzane przez klienta dla kolejek i tabel

Dane przechowywane w usługach Kolejka i Tabela nie są automatycznie chronione przez klucz zarządzany przez klienta, gdy klucze zarządzane przez klienta są włączone dla konta magazynu. Opcjonalnie można skonfigurować te usługi w momencie tworzenia konta magazynu, które ma zostać uwzględnione w tej ochronie.

Aby uzyskać więcej informacji na temat tworzenia konta magazynu obsługującego klucze zarządzane przez klienta dla kolejek i tabel, zobacz [Tworzenie konta obsługującego klucze zarządzane przez klienta dla tabel i kolejek](account-encryption-key-create.md).

Dane w usługach obiektów Blob i File są zawsze chronione przez klucze zarządzane przez klienta, gdy klucze zarządzane przez klienta są skonfigurowane dla konta magazynu.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Włączanie kluczy zarządzanych przez klienta dla konta magazynu

Klucze zarządzane przez klienta można włączyć tylko na istniejących kontach magazynu. Magazyn kluczy musi być aprowizowana za pomocą zasad dostępu, które przyznają uprawnienia klucza do tożsamości zarządzanej skojarzonej z kontem magazynu. Tożsamość zarządzana jest dostępna tylko po utworzeniu konta magazynu.

Podczas konfigurowania klucza zarządzanego przez klienta usługa Azure Storage zawija główny klucz szyfrowania danych dla konta kluczem zarządzanym przez klienta w skojarzonym magazynie kluczy. Włączenie kluczy zarządzanych przez klienta nie wpływa na wydajność i staje się skuteczne natychmiast.

Podczas modyfikowania klucza używanego do szyfrowania usługi Azure Storage przez włączenie lub wyłączenie kluczy zarządzanych przez klienta, aktualizowanie wersji klucza lub określanie innego klucza, a następnie szyfrowanie klucza głównego zmienia się, ale dane na koncie usługi Azure Storage nie muszą być ponownie szyfrowane.

Po włączeniu lub wyłączeniu kluczy zarządzanych przez klienta lub podczas modyfikowania klucza lub wersji klucza zmienia się ochrona głównego klucza szyfrowania, ale dane na koncie usługi Azure Storage nie muszą być ponownie szyfrowane.

Aby dowiedzieć się, jak używać kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault dla szyfrowania usługi Azure Storage, zobacz jeden z następujących artykułów:

- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Key Vault dla szyfrowania usługi Azure Storage z witryny Azure portal](storage-encryption-keys-portal.md)
- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Key Vault dla szyfrowania usługi Azure Storage z programu PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Key Vault dla szyfrowania usługi Azure Storage z interfejsu wiersza polecenia platformy Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Klucze zarządzane przez klienta opierają się na zarządzanych tożsamościach zasobów platformy Azure, funkcja usługi Azure AD. Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. Podczas konfigurowania kluczy zarządzanych przez klienta w witrynie Azure portal, tożsamość zarządzana jest automatycznie przypisywana do konta magazynu w ramach okładek. Jeśli następnie przeniesiesz konto subskrypcji, grupy zasobów lub magazynu z jednego katalogu usługi Azure AD do innego, tożsamość zarządzana skojarzona z kontem magazynu nie zostanie przeniesiona do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz **Przenoszenie subskrypcji między katalogami usługi Azure AD** w [często zadawanych pytaniach i znanych problemach z tożsamościami zarządzanymi dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Przechowywanie kluczy zarządzanych przez klienta w usłudze Azure Key Vault

Aby włączyć klucze zarządzane przez klienta na koncie magazynu, należy użyć usługi Azure Key Vault do przechowywania kluczy. Należy włączyć właściwości **Usuwanie nietrwałe** i **Nie przeczyszczaj** w magazynie kluczy.

Tylko 2048-bitowe klucze RSA i RSA-HSM są obsługiwane za pomocą szyfrowania usługi Azure Storage. Aby uzyskać więcej informacji o kluczach, zobacz **Klucze magazynu kluczy,** wpisy tajne i certyfikaty usługi Azure Key Vault — informacje o [kluczach, wpisach tajnych i certyfikatach](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)usługi Azure Key Vault .

## <a name="rotate-customer-managed-keys"></a>Obracanie kluczy zarządzanych przez klienta

Klucz zarządzany przez klienta można obrócić w usłudze Azure Key Vault zgodnie z zasadami zgodności. Po obróceniu klucza należy zaktualizować konto magazynu, aby użyć nowego identyfikatora URI wersji klucza. Aby dowiedzieć się, jak zaktualizować konto magazynu, aby użyć nowej wersji klucza w witrynie Azure Portal, zobacz sekcję **"Aktualizowanie wersji klucza** w [konfigurowaniu kluczy zarządzanych przez klienta dla usługi Azure Storage za pomocą portalu Azure.](storage-encryption-keys-portal.md)

Obracanie klucza nie powoduje ponownego szyfrowania danych na koncie magazynu. Nie ma żadnych dalszych działań wymaganych od użytkownika.

## <a name="revoke-access-to-customer-managed-keys"></a>Cofanie dostępu do kluczy zarządzanych przez klienta

W każdej chwili można odwołać dostęp konta magazynu do klucza zarządzanego przez klienta. Po odwołaniu dostępu do kluczy zarządzanych przez klienta lub po wyłączeniu lub usunięciu klucza klienci nie mogą wywoływać operacji odczytu lub zapisu w obiekcie blob lub jego metadanych. Próby wywołania dowolnej z następujących operacji zakończy się niepowodzeniem z kodem błędu 403 (Zabronione) dla wszystkich użytkowników:

- [Lista obiektów blob](/rest/api/storageservices/list-blobs), `include=metadata` po wywołaniu z parametrem identyfikatora URI żądania
- [Pobierz obiekt blob](/rest/api/storageservices/get-blob)
- [Pobieranie właściwości obiektu blob](/rest/api/storageservices/get-blob-properties)
- [Pobierz metadane obiektów blob](/rest/api/storageservices/get-blob-metadata)
- [Ustawianie metadanych obiektów blob](/rest/api/storageservices/set-blob-metadata)
- [Obiekt Blob migawki](/rest/api/storageservices/snapshot-blob), wywoływany z nagłówkiem `x-ms-meta-name` żądania
- [Kopiowanie obiektu blob](/rest/api/storageservices/copy-blob)
- [Kopiowanie obiektu blob z adresu URL](/rest/api/storageservices/copy-blob-from-url)
- [Ustawianie warstwy obiektu blob](/rest/api/storageservices/set-blob-tier)
- [Umieść blok](/rest/api/storageservices/put-block)
- [Umieść blok z adresu URL](/rest/api/storageservices/put-block-from-url)
- [Dołącz blok](/rest/api/storageservices/append-block)
- [Dołącz blok z adresu URL](/rest/api/storageservices/append-block-from-url)
- [Wstawianie obiektu blob](/rest/api/storageservices/put-blob)
- [Umieść stronę](/rest/api/storageservices/put-page)
- [Umieść adres URL strony z](/rest/api/storageservices/put-page-from-url)
- [Obiekt blob kopiowania przyrostowego](/rest/api/storageservices/incremental-copy-blob)

Aby ponownie wywołać te operacje, przywróć dostęp do klucza zarządzanego przez klienta.

Wszystkie operacje danych, które nie są wymienione w tej sekcji mogą być kontynuowane po odwołaniu kluczy zarządzanych przez klienta lub wyłączenia lub usunięcia klucza.

Aby odwołać dostęp do kluczy zarządzanych przez klienta, użyj programu [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) lub [interfejsu wiersza polecenia platformy Azure](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Klucze zarządzane przez klienta dla dysków zarządzanych platformy Azure

Klucze zarządzane przez klienta są również dostępne do zarządzania szyfrowaniem dysków zarządzanych platformy Azure. Klucze zarządzane przez klienta zachowują się inaczej w przypadku dysków zarządzanych niż zasobów usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [szyfrowanie po stronie serwera dysków zarządzanych platformy Azure](../../virtual-machines/windows/disk-encryption.md) dla systemu Windows lub [szyfrowania po stronie serwera dysków zarządzanych platformy Azure](../../virtual-machines/linux/disk-encryption.md) dla systemu Linux.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Key Vault dla szyfrowania usługi Azure Storage z witryny Azure portal](storage-encryption-keys-portal.md)
- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Key Vault dla szyfrowania usługi Azure Storage z programu PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Key Vault dla szyfrowania usługi Azure Storage z interfejsu wiersza polecenia platformy Azure](storage-encryption-keys-cli.md)
- [Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](storage-service-encryption.md)
