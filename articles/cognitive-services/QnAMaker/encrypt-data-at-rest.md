---
title: QnA Maker szyfrowanie danych magazynowanych
titleSuffix: Azure Cognitive Services
description: Firma Microsoft oferuje klucze szyfrowania zarządzane przez firmę Microsoft, a także umożliwia zarządzanie subskrypcjami Cognitive Services przy użyciu własnych kluczy nazywanych kluczami zarządzanymi przez klienta (CMK). W tym artykule opisano szyfrowanie danych przechowywane dla QnA Maker i sposób włączania usługi CMK oraz zarządzania nią.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 19dc0f3a676d5373b28e4b7055050477c426f847
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524530"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker szyfrowanie danych magazynowanych

QnA Maker automatycznie szyfruje dane, gdy zostaną utrwalone w chmurze, pomagając w spełnieniu celów związanych z bezpieczeństwem i zgodnością organizacji.

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

Domyślnie subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Istnieje również możliwość zarządzania subskrypcją przy użyciu własnych kluczy o nazwie klucze zarządzane przez klienta (CMK). CMK zapewnia większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych. Jeśli CMK jest skonfigurowany dla Twojej subskrypcji, zapewniane jest podwójne szyfrowanie, które oferuje drugą warstwę ochrony, a jednocześnie pozwala kontrolować klucz szyfrowania za pomocą Azure Key Vault.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

QnA Maker używa obsługi CMK z usługi Azure Search. Skonfiguruj [CMK w Azure Search przy użyciu Azure Key Vault](../../search/search-security-manage-encryption-keys.md). To wystąpienie platformy Azure powinno być skojarzone z usługą QnA Maker, aby można było włączyć CMK IT.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

QnA Maker używa [obsługi CMK z usługi Azure Search](../../search/search-security-manage-encryption-keys.md)i automatycznie kojarzy podaną CMK do szyfrowania danych przechowywanych w indeksie usługi Azure Search.

---

> [!IMPORTANT]
> Zasób usługi Azure Search musi zostać utworzony po styczniu 2019 i nie może znajdować się w warstwie Bezpłatna (współdzielona). W Azure Portal nie jest obsługiwane Konfigurowanie kluczy zarządzanych przez klienta.

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta

Usługa QnA Maker używa programu CMK z usługi Azure Search. Wykonaj następujące kroki, aby włączyć CMKs:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

1. Utwórz nowe wystąpienie Azure Search i Włącz wymagania wstępne określone w [wymaganiach wstępnych dotyczących klucza zarządzanego przez klienta dla usługi Azure wyszukiwanie poznawcze](../../search/search-security-manage-encryption-keys.md#prerequisites).

   ![Wyświetl ustawienia szyfrowania 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Podczas tworzenia zasobu QnA Maker jest on automatycznie kojarzony z wystąpieniem Azure Search. To wystąpienie nie może być używane z CMK. Aby użyć CMK, musisz skojarzyć nowo utworzone wystąpienie Azure Search, które zostało utworzone w kroku 1. W tym celu należy zaktualizować `AzureSearchAdminKey` i `AzureSearchName` w ramach zasobu QNA Maker.

   ![Wyświetl ustawienia szyfrowania 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Następnie utwórz nowe ustawienie aplikacji:
   * **Nazwa**: Ustaw na `CustomerManagedEncryptionKeyUrl`
   * **Wartość**: Użyj wartości, która została pobrana w kroku 1 podczas tworzenia wystąpienia Azure Search.

   ![Wyświetl ustawienia szyfrowania 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Po zakończeniu uruchom ponownie środowisko uruchomieniowe. Teraz usługa QnA Maker jest włączona.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

1.  Przejdź do karty **szyfrowanie** usługi Managed QNA Maker (wersja zapoznawcza).
2.  Wybierz opcję **klucze zarządzane przez klienta** . Podaj szczegóły [kluczy zarządzanych przez klienta](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) i kliknij pozycję **Zapisz**.

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="Ustawienie CMK zarządzane (wersja zapoznawcza) QnA Maker" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  Po pomyślnym zapisaniu CMK będzie używany do szyfrowania danych przechowywanych w indeksie Azure Search.

> [!IMPORTANT]
> Zalecane jest ustawienie CMK w nowej usłudze Azure Wyszukiwanie poznawcze przed utworzeniem jakichkolwiek baz wiedzy. Jeśli ustawisz CMK w usłudze QnA Maker z istniejącymi bazami wiedzy, możesz utracić dostęp do nich. Przeczytaj więcej na temat [pracy z zaszyfrowaną zawartością](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content) w usłudze Azure poznawcze wyszukiwanie.

> [!NOTE]
> Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij [formularz żądania klucza Cognitive Services Customer-Managed](https://aka.ms/cogsvc-cmk).

---

## <a name="regional-availability"></a>Dostępność regionalna

Klucze zarządzane przez klienta są dostępne we wszystkich regionach Azure Search.

## <a name="encryption-of-data-in-transit"></a>Szyfrowanie danych podczas przesyłania

Portal QnA Maker jest uruchamiany w przeglądarce użytkownika. Każda akcja wyzwala bezpośrednie wywołanie odpowiedniego interfejsu API usługi poznawczej. W związku z tym QnA Maker jest zgodny do przesyłania danych.
Jednak ponieważ usługa Portal QnA Maker jest hostowana w regionie zachodnie stany USA, nadal nie jest idealnym rozwiązaniem dla klientów innych niż USA. 

## <a name="next-steps"></a>Następne kroki

* [Szyfrowanie w Azure Search przy użyciu CMKs w Azure Key Vault](../../search/search-security-manage-encryption-keys.md)
* [Szyfrowanie danych w spoczynku](../../security/fundamentals/encryption-atrest.md)
* [Dowiedz się więcej o Azure Key Vault](../../key-vault/general/overview.md)