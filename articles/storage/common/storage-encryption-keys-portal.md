---
title: Użyj Azure Portal, aby skonfigurować klucze zarządzane przez klienta
titleSuffix: Azure Storage
description: Dowiedz się, jak za pomocą Azure Portal skonfigurować klucze zarządzane przez klienta za pomocą Azure Key Vault na potrzeby szyfrowania usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 4af70a4e2a698bd280c8c41018bc5aaa1bfa27f8
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85512553"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Skonfiguruj klucze zarządzane przez klienta za pomocą Azure Key Vault przy użyciu Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule przedstawiono sposób konfigurowania Azure Key Vault za pomocą kluczy zarządzanych przez klienta przy użyciu [Azure Portal](https://portal.azure.com/). Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu Azure Portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurowanie usługi Azure Key Vault

Przy użyciu kluczy zarządzanych przez klienta z szyfrowaniem usługi Azure Storage wymagane są dwie właściwości w magazynie kluczy, **usuwanie nietrwałe** i **nie przeczyszczanie**. Te właściwości nie są domyślnie włączone, ale można je włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure dla nowego lub istniejącego magazynu kluczy.

Aby dowiedzieć się, jak włączyć te właściwości w istniejącym magazynie kluczy, zobacz sekcję zatytułowaną **Włączanie usuwania nietrwałego** i **Włączanie ochrony przed przeczyszczeniem** w jednym z następujących artykułów:

- [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../../key-vault/general/soft-delete-powershell.md).
- [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../../key-vault/general/soft-delete-cli.md).

Szyfrowanie za pomocą usługi Azure Storage obsługuje klucze RSA i RSA-HSM o rozmiarach 2048, 3072 i 4096. Aby uzyskać więcej informacji na temat kluczy, zobacz **Key Vault klucze** w temacie [informacje Azure Key Vault klucze, wpisy tajne i certyfikaty](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta

Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do swojego konta magazynu.
1. W bloku **Ustawienia** dla konta magazynu kliknij pozycję **szyfrowanie**. Wybierz opcję **klucze zarządzane przez klienta** , jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu portalu przedstawiający opcję szyfrowania](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Określ klucz

Po włączeniu kluczy zarządzanych przez klienta będziesz mieć możliwość określenia klucza do skojarzenia z kontem magazynu.

### <a name="specify-a-key-as-a-uri"></a>Określ klucz jako identyfikator URI

Aby określić klucz jako identyfikator URI, wykonaj następujące kroki:

1. Aby zlokalizować identyfikator URI klucza w Azure Portal, przejdź do magazynu kluczy, a następnie wybierz ustawienie **klucze** . Wybierz odpowiedni klucz, a następnie kliknij klucz, aby wyświetlić jego wersje. Wybierz wersję klucza, aby wyświetlić ustawienia dla tej wersji.
1. Skopiuj wartość pola **Identyfikator klucza** , która zapewnia identyfikator URI.

    ![Zrzut ekranu przedstawiający identyfikator URI klucza magazynu kluczy](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. W ustawieniach **szyfrowania** dla konta magazynu wybierz opcję **Wprowadź identyfikator URI klucza** .
1. Wklej identyfikator URI, który został skopiowany do pola **klucza URI** .

   ![Zrzut ekranu przedstawiający sposób wprowadzania identyfikatora URI klucza](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Określ subskrypcję zawierającą magazyn kluczy.
1. Zapisz zmiany.

### <a name="specify-a-key-from-a-key-vault"></a>Określ klucz z magazynu kluczy

Aby określić klucz z magazynu kluczy, najpierw upewnij się, że masz Magazyn kluczy zawierający klucz. Aby określić klucz z magazynu kluczy, wykonaj następujące kroki:

1. Wybierz opcję **Wybierz z Key Vault** .
1. Wybierz magazyn kluczy zawierający klucz, którego chcesz użyć.
1. Wybierz klucz z magazynu kluczy.

   ![Zrzut ekranu przedstawiający opcję klucz zarządzany przez klienta](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Zapisz zmiany.

## <a name="update-the-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować konto magazynu, aby używało nowej wersji. Wykonaj następujące kroki:

1. Przejdź do konta magazynu i Wyświetl ustawienia **szyfrowania** .
1. Wprowadź identyfikator URI dla nowej wersji klucza. Alternatywnie można wybrać Magazyn kluczy i ponownie klucz, aby zaktualizować wersję.
1. Zapisz zmiany.

## <a name="use-a-different-key"></a>Użyj innego klucza

Aby zmienić klucz używany do szyfrowania usługi Azure Storage, wykonaj następujące kroki:

1. Przejdź do konta magazynu i Wyświetl ustawienia **szyfrowania** .
1. Wprowadź identyfikator URI dla nowego klucza. Alternatywnie można wybrać Magazyn kluczy i wybrać nowy klucz.
1. Zapisz zmiany.

## <a name="disable-customer-managed-keys"></a>Wyłącz klucze zarządzane przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta konto magazynu będzie ponownie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby wyłączyć klucze zarządzane przez klienta, wykonaj następujące kroki:

1. Przejdź do konta magazynu i Wyświetl ustawienia **szyfrowania** .
1. Usuń zaznaczenie pola wyboru obok ustawienia **Użyj własnego klucza** .

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
