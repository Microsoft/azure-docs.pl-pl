---
title: Użyj Azure Portal, aby skonfigurować klucze zarządzane przez klienta
titleSuffix: Cognitive Services
description: Informacje dotyczące konfigurowania kluczy zarządzanych przez klienta przy użyciu Azure Portal w programie Azure Key Vault. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81455261"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Skonfiguruj klucze zarządzane przez klienta za pomocą Azure Key Vault przy użyciu Azure Portal

Aby przechowywać klucze zarządzane przez klienta, należy użyć Azure Key Vault. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Zasób Cognitive Services i Magazyn kluczy muszą znajdować się w tym samym regionie i w tej samej dzierżawie Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

W tym artykule przedstawiono sposób konfigurowania Azure Key Vault za pomocą kluczy zarządzanych przez klienta przy użyciu [Azure Portal](https://portal.azure.com/). Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu Azure Portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurowanie usługi Azure Key Vault

Przy użyciu kluczy zarządzanych przez klienta wymagane jest ustawienie dwóch właściwości w magazynie kluczy, **usuwanie nietrwałe** i **nie przeczyszczanie**. Te właściwości nie są domyślnie włączone, ale można je włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure dla nowego lub istniejącego magazynu kluczy.

> [!IMPORTANT]
> Jeśli nie masz włączonych właściwości **usuwania nietrwałego** i **nie przeczyszczasz** i usuniesz klucz, nie będzie można odzyskać danych z zasobu usługi poznawczej.

Aby dowiedzieć się, jak włączyć te właściwości w istniejącym magazynie kluczy, zobacz sekcję zatytułowaną **Włączanie usuwania nietrwałego** i **Włączanie ochrony przed przeczyszczeniem** w jednym z następujących artykułów:

- [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Tylko klucze RSA o rozmiarze 2048 są obsługiwane przez szyfrowanie usługi Azure Storage. Aby uzyskać więcej informacji na temat kluczy, zobacz **Key Vault klucze** w temacie [informacje Azure Key Vault klucze, wpisy tajne i certyfikaty](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta

Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do zasobu Cognitive Services.
1. W bloku **Ustawienia** dla zasobu Cognitive Services kliknij pozycję **szyfrowanie**. Wybierz opcję **klucze zarządzane przez klienta** , jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu przedstawiający sposób wybierania kluczy zarządzanych przez klienta](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Określ klucz

Po włączeniu kluczy zarządzanych przez klienta będziesz mieć możliwość określenia klucza do skojarzenia z zasobem Cognitive Services.

### <a name="specify-a-key-as-a-uri"></a>Określ klucz jako identyfikator URI

Aby określić klucz jako identyfikator URI, wykonaj następujące kroki:

1. Aby zlokalizować identyfikator URI klucza w Azure Portal, przejdź do magazynu kluczy, a następnie wybierz ustawienie **klucze** . Wybierz odpowiedni klucz, a następnie kliknij klucz, aby wyświetlić jego wersje. Wybierz wersję klucza, aby wyświetlić ustawienia dla tej wersji.
1. Skopiuj wartość pola **Identyfikator klucza** , która zapewnia identyfikator URI.

    ![Zrzut ekranu przedstawiający identyfikator URI klucza magazynu kluczy](../media/cognitive-services-encryption/key-uri-portal.png)

1. W ustawieniach **szyfrowania** dla konta magazynu wybierz opcję **Wprowadź identyfikator URI klucza** .
1. Wklej identyfikator URI, który został skopiowany do pola **klucza URI** .

   ![Zrzut ekranu przedstawiający sposób wprowadzania identyfikatora URI klucza](../media/cognitive-services-encryption/ssecmk2.png)

1. Określ subskrypcję zawierającą magazyn kluczy.
1. Zapisz zmiany.

### <a name="specify-a-key-from-a-key-vault"></a>Określ klucz z magazynu kluczy

Aby określić klucz z magazynu kluczy, najpierw upewnij się, że masz Magazyn kluczy zawierający klucz. Aby określić klucz z magazynu kluczy, wykonaj następujące kroki:

1. Wybierz opcję **Wybierz z Key Vault** .
1. Wybierz magazyn kluczy zawierający klucz, którego chcesz użyć.
1. Wybierz klucz z magazynu kluczy.

   ![Zrzut ekranu przedstawiający opcję klucz zarządzany przez klienta](../media/cognitive-services-encryption/ssecmk3.png)

1. Zapisz zmiany.

## <a name="update-the-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować zasób Cognitive Services, aby korzystał z nowej wersji. Wykonaj następujące kroki:

1. Przejdź do zasobu Cognitive Services i Wyświetl ustawienia **szyfrowania** .
1. Wprowadź identyfikator URI dla nowej wersji klucza. Alternatywnie można wybrać Magazyn kluczy i ponownie klucz, aby zaktualizować wersję.
1. Zapisz zmiany.

## <a name="use-a-different-key"></a>Użyj innego klucza

Aby zmienić klucz używany do szyfrowania, wykonaj następujące kroki:

1. Przejdź do zasobu Cognitive Services i Wyświetl ustawienia **szyfrowania** .
1. Wprowadź identyfikator URI dla nowego klucza. Alternatywnie można wybrać Magazyn kluczy i wybrać nowy klucz.
1. Zapisz zmiany.

## <a name="disable-customer-managed-keys"></a>Wyłącz klucze zarządzane przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta zasób Cognitive Services jest szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby wyłączyć klucze zarządzane przez klienta, wykonaj następujące kroki:

1. Przejdź do zasobu Cognitive Services i Wyświetl ustawienia **szyfrowania** .
1. Usuń zaznaczenie pola wyboru obok ustawienia **Użyj własnego klucza** .

## <a name="next-steps"></a>Następne kroki

* [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Formularz żądania klucza zarządzanego przez klienta Cognitive Services](https://aka.ms/cogsvc-cmk)
* [Szyfrowanie usług w czasie spoczynku](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker szyfrowanie danych magazynowanych](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Language Understanding szyfrowanie usługi danych w spoczynku](../LUIS/luis-encryption-of-data-at-rest.md)
* [Content Moderator szyfrowanie danych magazynowanych](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Szyfrowanie danych w usłudze translator](../translator/translator-encryption-of-data-at-rest.md)
* [Personalizacja szyfrowania danych magazynowanych](../personalizer/personalizer-encryption-of-data-at-rest.md)
