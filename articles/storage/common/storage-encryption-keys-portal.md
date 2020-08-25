---
title: Użyj Azure Portal, aby skonfigurować klucze zarządzane przez klienta
titleSuffix: Azure Storage
description: Dowiedz się, jak za pomocą Azure Portal skonfigurować klucze zarządzane przez klienta za pomocą Azure Key Vault na potrzeby szyfrowania usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/31/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a506f59d3f2d331e4c7680565f3c110b9cd12956
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799177"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Skonfiguruj klucze zarządzane przez klienta za pomocą usługi Azure Key Vault przy użyciu witryny Azure Portal

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

1. Przejdź do konta magazynu.
1. W bloku **Ustawienia** dla konta magazynu kliknij pozycję **szyfrowanie**. Wybierz opcję **klucze zarządzane przez klienta** , jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu portalu przedstawiający opcję szyfrowania](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Określ klucz

Po włączeniu kluczy zarządzanych przez klienta będziesz mieć możliwość określenia klucza do skojarzenia z kontem magazynu. Możesz również wskazać, czy usługa Azure Storage ma automatycznie aktualizować klucz zarządzany przez klienta do najnowszej wersji, czy też aktualizować wersję klucza ręcznie.

### <a name="specify-a-key-from-a-key-vault"></a>Określ klucz z magazynu kluczy

Po wybraniu klucza zarządzanego przez klienta z magazynu kluczy zostanie włączona automatyczna aktualizacja wersji klucza. Aby ręcznie zarządzać wersją klucza, określ zamiast niego identyfikator URI klucza i Dołącz wersję klucza. Aby uzyskać więcej informacji, zobacz [Określanie klucza jako identyfikatora URI](#specify-a-key-as-a-uri).

Aby określić klucz z magazynu kluczy, wykonaj następujące kroki:

1. Wybierz opcję **Wybierz z Key Vault** .
1. Wybierz pozycję **Wybierz magazyn kluczy i klucz**.
1. Wybierz magazyn kluczy zawierający klucz, którego chcesz użyć.
1. Wybierz klucz z magazynu kluczy.

   ![Zrzut ekranu przedstawiający sposób wybierania magazynu kluczy i klucza](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Zapisz zmiany.

### <a name="specify-a-key-as-a-uri"></a>Określ klucz jako identyfikator URI

Usługa Azure Storage może automatycznie zaktualizować klucz zarządzany przez klienta używany do szyfrowania, aby użyć najnowszej wersji klucza. Gdy klucz zarządzany przez klienta zostanie obrócony w Azure Key Vault, usługa Azure Storage automatycznie zacznie używać najnowszej wersji klucza do szyfrowania.

> [!NOTE]
> Aby obrócić klucz, Utwórz nową wersję klucza w Azure Key Vault. Usługa Azure Storage nie obsługuje rotacji klucza w Azure Key Vault, więc musisz ręcznie obrócić klucz lub utworzyć funkcję, aby obrócić ją zgodnie z harmonogramem.

W przypadku określenia identyfikatora URI klucza należy pominąć wersję klucza z identyfikatora URI, aby włączyć automatyczną aktualizację do najnowszej wersji. Jeśli w identyfikatorze URI klucza zostanie dołączona wersja klucza, automatyczne aktualizowanie nie jest włączone i musisz samodzielnie zarządzać wersją klucza. Aby uzyskać więcej informacji o aktualizowaniu wersji klucza, zobacz [Ręczne aktualizowanie wersji klucza](#manually-update-the-key-version).

Aby określić klucz jako identyfikator URI, wykonaj następujące kroki:

1. Aby zlokalizować identyfikator URI klucza w Azure Portal, przejdź do magazynu kluczy, a następnie wybierz ustawienie **klucze** . Wybierz odpowiedni klucz, a następnie kliknij klucz, aby wyświetlić jego wersje. Wybierz wersję klucza, aby wyświetlić ustawienia dla tej wersji.
1. Skopiuj wartość pola **Identyfikator klucza** , która zapewnia identyfikator URI.

    ![Zrzut ekranu przedstawiający identyfikator URI klucza magazynu kluczy](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. W ustawieniach **klucza szyfrowania** dla konta magazynu wybierz opcję **Wprowadź identyfikator URI klucza** .
1. Wklej identyfikator URI, który został skopiowany do pola **klucza URI** . Pomiń wersję klucza z identyfikatora URI, aby włączyć automatyczną aktualizację wersji klucza.

   ![Zrzut ekranu przedstawiający sposób wprowadzania identyfikatora URI klucza](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Określ subskrypcję zawierającą magazyn kluczy.
1. Zapisz zmiany.

Po określeniu klucza Azure Portal wskazuje, czy automatyczna aktualizacja wersji klucza jest włączona i wyświetla aktualnie używaną wersję klucza do szyfrowania.

:::image type="content" source="media/storage-encryption-keys-portal/portal-auto-rotation-enabled.png" alt-text="Zrzut ekranu przedstawiający automatyczną aktualizację wersji klucza":::

## <a name="manually-update-the-key-version"></a>Ręcznie zaktualizuj wersję klucza

Domyślnie podczas tworzenia nowej wersji klucza zarządzanego przez klienta w programie Key Vault usługa Azure Storage automatycznie używa nowej wersji do szyfrowania z kluczami zarządzanymi przez klienta, zgodnie z opisem w poprzednich sekcjach. Jeśli zdecydujesz się na samodzielne zarządzanie wersją klucza, należy zaktualizować wersję klucza skojarzoną z kontem magazynu za każdym razem, gdy tworzysz nową wersję klucza.

Aby zaktualizować konto magazynu tak, aby korzystało z nowej wersji klucza, wykonaj następujące kroki:

1. Przejdź do konta magazynu i Wyświetl ustawienia **szyfrowania** .
1. Wprowadź identyfikator URI nowej wersji klucza. Alternatywnie można wybrać Magazyn kluczy i ponownie klucz, aby zaktualizować wersję.
1. Zapisz zmiany.

## <a name="switch-to-a-different-key"></a>Przełącz do innego klucza

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
