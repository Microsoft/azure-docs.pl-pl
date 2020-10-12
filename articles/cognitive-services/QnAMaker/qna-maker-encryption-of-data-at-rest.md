---
title: QnA Maker szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Firma Microsoft oferuje klucze szyfrowania zarządzane przez firmę Microsoft, a także umożliwia zarządzanie subskrypcjami Cognitive Services przy użyciu własnych kluczy nazywanych kluczami zarządzanymi przez klienta (CMK). W tym artykule opisano szyfrowanie danych przechowywane dla QnA Maker i sposób włączania usługi CMK oraz zarządzania nią.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: e744423e00377ef763824f6e39865e6b3e8ee475
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073543"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker szyfrowanie danych magazynowanych

QnA Maker automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze, pomagając w spełnieniu celów związanych z bezpieczeństwem i zgodnością organizacji.

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Domyślnie subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Istnieje również możliwość zarządzania subskrypcją przy użyciu własnych kluczy o nazwie klucze zarządzane przez klienta (CMK). CMK zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych. Jeśli CMK jest skonfigurowany dla Twojej subskrypcji, zapewniane jest podwójne szyfrowanie, które oferuje drugą warstwę ochrony, a jednocześnie pozwala kontrolować klucz szyfrowania za pomocą Azure Key Vault.

QnA Maker używa obsługi CMK z usługi Azure Search. Należy utworzyć [CMK w Azure Search przy użyciu Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). To wystąpienie platformy Azure powinno być skojarzone z usługą QnA Maker, aby można było włączyć CMK IT.

> [!IMPORTANT]
> Zasób usługi Azure Search musi zostać utworzony po styczniu 2019 i nie może znajdować się w warstwie Bezpłatna (współdzielona). W Azure Portal nie jest obsługiwane Konfigurowanie kluczy zarządzanych przez klienta.

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta

Usługa QnA Maker używa programu CMK z usługi Azure Search. Wykonaj następujące kroki, aby włączyć CMKs:

1. Utwórz nowe wystąpienie Azure Search i Włącz wymagania wstępne określone w [wymaganiach wstępnych dotyczących klucza zarządzanego przez klienta dla usługi Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Wyświetl ustawienia szyfrowania 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Podczas tworzenia zasobu QnA Maker jest on automatycznie kojarzony z wystąpieniem Azure Search. Nie można go używać z CMK. Aby użyć CMK, musisz skojarzyć nowo utworzone wystąpienie Azure Search, które zostało utworzone w kroku 1. W tym celu należy zaktualizować `AzureSearchAdminKey` i `AzureSearchName` w ramach zasobu QNA Maker.

   ![Wyświetl ustawienia szyfrowania 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Następnie utwórz nowe ustawienie aplikacji:
   * **Nazwa**: Ustaw tę wartość na `CustomerManagedEncryptionKeyUrl`
   * **Wartość**: jest to wartość uzyskana w kroku 1 podczas tworzenia wystąpienia Azure Search.

   ![Wyświetl ustawienia szyfrowania 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Po zakończeniu uruchom ponownie środowisko uruchomieniowe. Teraz usługa QnA Maker jest włączona.

## <a name="regional-availability"></a>Dostępność regionalna

Klucze zarządzane przez klienta są dostępne we wszystkich regionach Azure Search.

## <a name="encryption-of-data-in-transit"></a>Szyfrowanie danych podczas przesyłania

Portal QnA Maker jest uruchamiany w przeglądarce użytkownika. Każde działanie wyzwala bezpośrednie wywołanie odpowiedniego interfejsu API usługi poznawczej. W związku z tym QnA Maker jest zgodny do przesyłania danych.
Jednak ponieważ usługa Portal QnA Maker jest hostowana w regionie zachodnie stany USA, nadal nie jest idealnym rozwiązaniem dla klientów innych niż USA. 

## <a name="next-steps"></a>Następne kroki

* [Szyfrowanie w Azure Search przy użyciu CMKs w Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Szyfrowanie danych w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)