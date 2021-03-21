---
title: Przenoszenie własnego klucza (klucze zarządzane przez klienta)
description: Możesz użyć klucza zarządzanego przez klienta (czyli wprowadzić własny klucz) z Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4564e28f76aebe7f708c2b6f68903fe67bcefe26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698862"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Przenoszenie własnych kluczy (kluczy zarządzanych przez klienta) za pomocą Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bring Your Own Key (BYOK) to Azure Wide Initiative, która ułatwia klientom przenoszenie obciążeń do chmury. Klucze zarządzane przez klienta umożliwiają klientom przestrzeganie regulacji zgodności branżowej i zwiększają izolację dzierżawy usługi. Umożliwienie klientom kontrolowania kluczy szyfrowania jest sposobem minimalizowania niepotrzebnego dostępu i kontroli oraz zapewnienia zaufania do usług firmy Microsoft.

## <a name="keys-and-key-management"></a>Klucze i zarządzanie kluczami

W przypadku korzystania z interfejsu API Media Services 2020-05-01 można użyć własnego klucza z Media Services. Tworzony jest domyślny klucz konta dla wszystkich kont, które są szyfrowane za pomocą klucza systemowego należącego do Media Services. W przypadku korzystania z własnego klucza klucz konta jest szyfrowany przy użyciu klucza. Klucze zawartości są szyfrowane za pomocą klucza konta. Adresy URL JobInputHttp i klucze weryfikacji tokenu symetrycznego są również szyfrowane.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Klucz zarządzany przez klienta zastępuje klucz zarządzany przez system":::

Media Services używa zarządzanej tożsamości konta Media Services do odczytywania klucza z Key Vault należącego do użytkownika. Media Services wymaga, aby Key Vault znajduje się w tym samym regionie co konto, i ma włączoną ochronę nietrwałego usuwania i przeczyszczania.

Klucz może mieć wartość 2048, 3072 lub 4096 klucz RSA i obsługiwane są zarówno klucze HSM, jak i programowe.

> [!NOTE]
> Klucze EC nie są obsługiwane.

Możesz określić nazwę klucza i wersję klucza lub tylko nazwę klucza. Jeśli używasz tylko nazwy klucza, Media Services będzie używać najnowszej wersji klucza. Nowe wersje kluczy klientów są wykrywane automatycznie, a klucz konta jest ponownie szyfrowany.

> [!WARNING]
> Media Services monitoruje dostęp do klucza klienta. Jeśli klucz klienta stanie się niedostępny (na przykład klucz został usunięty lub Key Vault został usunięty lub zostanie usunięty przydział dostępu), Media Services nastąpi przejście konta do stanu niedostępności klucza klienta (w efekcie wyłączenie konta). Konto można jednak usunąć w tym stanie. Jedyne obsługiwane operacje to account GET, LIST i DELETE; wszystkie inne żądania (kodowanie, przesyłanie strumieniowe itp.) będą kończyć się niepowodzeniem, dopóki nie zostanie przywrócony dostęp do klucza konta.

## <a name="double-encryption"></a>Podwójne szyfrowanie

Media Services automatycznie obsługuje podwójne szyfrowanie. W przypadku danych przechowywanych w pierwszej warstwie szyfrowania jest używany klucz zarządzany przez klienta lub klucz zarządzany przez firmę Microsoft w zależności od `AccountEncryption` Ustawienia na koncie.  Druga warstwa szyfrowania danych magazynowanych jest udostępniana automatycznie przy użyciu oddzielnego klucza zarządzanego firmy Microsoft. Aby dowiedzieć się więcej na temat podwójnego szyfrowania, zobacz [Azure podwójne szyfrowanie](../../security/fundamentals/double-encryption.md).

> [!NOTE]
> Podwójne szyfrowanie jest włączane automatycznie na koncie Media Services. Należy jednak skonfigurować klucz zarządzany przez klienta i dwukrotnie szyfrowanie na koncie magazynu. Zobacz, [szyfrowanie Storege](../../storage/common/storage-service-encryption.md).

## <a name="tutorials"></a>Samouczki

- [Użyj Azure Portal, aby użyć kluczy zarządzanych przez klienta lub BYOK z Media Services](tutorial-byok-portal.md)
- [Użyj kluczy zarządzanych przez klienta lub BYOK za pomocą interfejsu API REST Media Services](tutorial-byok-postman.md).

## <a name="next-steps"></a>Następne kroki

[Ochrona zawartości przy użyciu szyfrowania dynamicznego Media Services](content-protection-overview.md)