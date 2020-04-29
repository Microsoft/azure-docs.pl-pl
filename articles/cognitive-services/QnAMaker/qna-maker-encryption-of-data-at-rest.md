---
title: QnA Maker szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: QnA Maker szyfrowanie danych magazynowanych.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372096"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker szyfrowanie danych magazynowanych

QnA Maker automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze, pomagając w spełnieniu celów związanych z bezpieczeństwem i zgodnością organizacji.

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Domyślnie Twoja subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Istnieje również możliwość zarządzania subskrypcją przy użyciu własnych kluczy. Klucze zarządzane przez klienta (CMK) zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych.

QnA Maker używa obsługi CMK z usługi Azure Search. Należy utworzyć [CMK w Azure Search przy użyciu Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). To wystąpienie platformy Azure powinno być skojarzone z usługą QnA Maker, aby można było włączyć CMK IT.

> [!IMPORTANT]
> Zasób usługi Azure Search musi zostać utworzony po styczniu 2019 i nie może znajdować się w warstwie Bezpłatna (współdzielona). W Azure Portal nie jest obsługiwane Konfigurowanie kluczy zarządzanych przez klienta.

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta

Usługa QnA Maker używa programu CMK z usługi Azure Search. Wykonaj następujące kroki, aby włączyć CMKs:

1. Utwórz nowe wystąpienie Azure Search i Włącz wymagania wstępne określone w [wymaganiach wstępnych dotyczących klucza zarządzanego przez klienta dla usługi Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Wyświetl ustawienia szyfrowania](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Podczas tworzenia zasobu QnA Maker jest on automatycznie kojarzony z wystąpieniem Azure Search. Nie można go używać z CMK. Aby użyć CMK, musisz skojarzyć nowo utworzone wystąpienie Azure Search, które zostało utworzone w kroku 1. W tym celu należy zaktualizować i `AzureSearchAdminKey` `AzureSearchName` w ramach zasobu QNA Maker.

   ![Wyświetl ustawienia szyfrowania](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Następnie utwórz nowe ustawienie aplikacji:
   * **Nazwa**: Ustaw tę wartość na`CustomerManagedEncryptionKeyUrl`
   * **Wartość**: jest to wartość uzyskana w kroku 1 podczas tworzenia wystąpienia Azure Search.

   ![Wyświetl ustawienia szyfrowania](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Po zakończeniu uruchom ponownie środowisko uruchomieniowe. Teraz usługa QnA Maker jest włączona.

## <a name="regional-availability"></a>Dostępność regionalna

Klucze zarządzane przez klienta są dostępne we wszystkich regionach Azure Search.

## <a name="next-steps"></a>Następne kroki

* [Szyfrowanie w Azure Search przy użyciu CMKs w Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Szyfrowanie danych w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Dowiedz się więcej o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
