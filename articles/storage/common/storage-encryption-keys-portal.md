---
title: Konfigurowanie kluczy zarządzanych przez klienta za pomocą portalu Azure
titleSuffix: Azure Storage
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta za pomocą usługi Azure Key Vault dla usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 192e58b101b824ca0cc0c732e02647838be6dc35
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456485"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault przy użyciu witryny Azure portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule pokazano, jak skonfigurować usługę Azure Key Vault za pomocą kluczy zarządzanych przez klienta za pomocą [witryny Azure portal](https://portal.azure.com/). Aby dowiedzieć się, jak utworzyć magazyn kluczy za pomocą witryny Azure portal, zobacz [Szybki start: Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu witryny Azure portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurowanie usługi Azure Key Vault

Korzystanie z kluczy zarządzanych przez klienta za pomocą szyfrowania usługi Azure Storage wymaga, aby dwie właściwości były ustawiane w magazynie kluczy, **Usuwanie nietrwałe** i **Nie czyścić**. Te właściwości nie są domyślnie włączone, ale można włączyć za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure w nowym lub istniejącym magazynie kluczy.

Aby dowiedzieć się, jak włączyć te właściwości w istniejącym magazynie kluczy, zobacz sekcje **"Włączanie usuwania nietrwale** i **włączanie ochrony przed przeczyszczaniem"** w jednym z następujących artykułów:

- [Jak używać soft-delete z programem PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Jak używać soft-delete z CLI](../../key-vault/general/soft-delete-cli.md).

Tylko 2048-bitowe klucze RSA i RSA-HSM są obsługiwane za pomocą szyfrowania usługi Azure Storage. Aby uzyskać więcej informacji o kluczach, zobacz **Klucze magazynu kluczy,** wpisy tajne i certyfikaty usługi Azure Key Vault — informacje o [kluczach, wpisach tajnych i certyfikatach](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)usługi Azure Key Vault .

## <a name="enable-customer-managed-keys"></a>Włączanie kluczy zarządzanych przez klienta

Aby włączyć klucze zarządzane przez klienta w witrynie Azure portal, wykonaj następujące kroki:

1. Przejdź do swojego konta magazynu.
1. W bloku **Ustawienia** dla konta magazynu kliknij pozycję **Szyfrowanie**. Wybierz opcję **Klucze zarządzane przez klienta,** jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu z portalu z opcją szyfrowania](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Określanie klucza

Po włączeniu kluczy zarządzanych przez klienta, będziesz mieć możliwość określenia klucza do skojarzenia z kontem magazynu.

### <a name="specify-a-key-as-a-uri"></a>Określanie klucza jako identyfikatora URI

Aby określić klucz jako identyfikator URI, wykonaj następujące kroki:

1. Aby zlokalizować identyfikator URI klucza w witrynie Azure portal, przejdź do magazynu kluczy i wybierz ustawienie **Klucze.** Wybierz żądany klawisz, a następnie kliknij klawisz, aby wyświetlić jego wersje. Wybierz wersję klucza, aby wyświetlić ustawienia dla tej wersji.
1. Skopiuj wartość pola **Identyfikator klucza,** które zapewnia identyfikator URI.

    ![Zrzut ekranu przedstawiający identyfikator URI klucza przechowalni kluczy](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. W **ustawieniach szyfrowania** konta magazynu wybierz opcję **Wprowadź klucz URI.**
1. Wklej identyfikator URI skopiowany do pola **Identyfikator URI kluczy.**

   ![Zrzut ekranu przedstawiający sposób wprowadzania klucza URI](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Określ subskrypcję zawierającą magazyn kluczy.
1. Zapisz zmiany.

### <a name="specify-a-key-from-a-key-vault"></a>Określanie klucza z magazynu kluczy

Aby określić klucz z magazynu kluczy, najpierw upewnij się, że masz magazyn kluczy zawierający klucz. Aby określić klucz z magazynu kluczy, wykonaj następujące czynności:

1. Wybierz opcję **Wybierz z przechowalni kluczy.**
1. Wybierz przechowalnię kluczy zawierającą klucz, którego chcesz użyć.
1. Wybierz klucz z magazynu kluczy.

   ![Zrzut ekranu przedstawiający opcję klucza zarządzanego przez klienta](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Zapisz zmiany.

## <a name="update-the-key-version"></a>Aktualizowanie wersji klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować konto magazynu, aby użyć nowej wersji. Wykonaj następujące kroki:

1. Przejdź do konta magazynu i wyświetl ustawienia **szyfrowania.**
1. Wprowadź identyfikator URI dla nowej wersji klucza. Alternatywnie można ponownie wybrać magazyn kluczy i klucz, aby zaktualizować wersję.
1. Zapisz zmiany.

## <a name="use-a-different-key"></a>Używanie innego klucza

Aby zmienić klucz używany do szyfrowania usługi Azure Storage, wykonaj następujące kroki:

1. Przejdź do konta magazynu i wyświetl ustawienia **szyfrowania.**
1. Wprowadź identyfikator URI dla nowego klucza. Alternatywnie można wybrać przechowalnię kluczy i wybrać nowy klucz.
1. Zapisz zmiany.

## <a name="disable-customer-managed-keys"></a>Wyłączanie kluczy zarządzanych przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta konto magazynu jest ponownie szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby wyłączyć klucze zarządzane przez klienta, wykonaj następujące kroki:

1. Przejdź do konta magazynu i wyświetl ustawienia **szyfrowania.**
1. Usuń zaznaczenie pola wyboru obok ustawienia **Użyj własnego klucza.**

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](storage-service-encryption.md)
- [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
