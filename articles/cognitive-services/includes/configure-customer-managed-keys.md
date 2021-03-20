---
title: Użyj Azure Portal, aby skonfigurować klucze zarządzane przez klienta
titleSuffix: Cognitive Services
description: Informacje dotyczące konfigurowania kluczy zarządzanych przez klienta przy użyciu Azure Portal w programie Azure Key Vault. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: c79846b0a5b675c34e4e7919e9ecd9d591bfefe5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96356362"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>Klucze zarządzane przez klienta za pomocą usługi Azure Key Vault

Aby przechowywać klucze zarządzane przez klienta, należy użyć Azure Key Vault. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Zasób Cognitive Services i Magazyn kluczy muszą znajdować się w tym samym regionie i w tej samej dzierżawie Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/general/overview.md).

Po utworzeniu nowego zasobu Cognitive Services jest on zawsze szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft. W momencie utworzenia zasobu nie można włączyć kluczy zarządzanych przez klienta. Klucze zarządzane przez klienta są przechowywane w Azure Key Vault, a Magazyn kluczy musi być zainicjowany przy użyciu zasad dostępu, które przyznają kluczowe uprawnienia do zarządzanej tożsamości skojarzonej z zasobem Cognitive Services. Tożsamość zarządzana jest dostępna tylko po utworzeniu zasobu przy użyciu warstwy cenowej wymaganej przez CMK.

Włączenie kluczy zarządzanych przez klienta spowoduje również włączenie [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md)przypisanej do systemu, funkcji usługi Azure AD. Gdy jest włączona tożsamość zarządzana przypisana przez system, ten zasób zostanie zarejestrowany w Azure Active Directory. Po zarejestrowaniu tożsamość zarządzana będzie mieć dostęp do Key Vault wybranej podczas instalacji klucza zarządzanego przez klienta. 

> [!IMPORTANT]
> Jeśli wyłączysz tożsamości zarządzane przypisane przez system, dostęp do magazynu kluczy zostanie usunięty, a wszystkie dane zaszyfrowane za pomocą kluczy klienta nie będą już dostępne. Wszystkie funkcje zależne od tych danych przestaną działać.

> [!IMPORTANT]
> Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. W przypadku konfigurowania kluczy zarządzanych przez klienta w Azure Portal, zarządzana tożsamość zostanie automatycznie przypisana w obszarze okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub zasób z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z zasobem nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz **transfer subskrypcji między katalogami usługi Azure AD** w [często zadawanych pytaniach i znanych problemach z tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="configure-azure-key-vault"></a>Konfigurowanie usługi Azure Key Vault

Przy użyciu kluczy zarządzanych przez klienta wymagane jest ustawienie dwóch właściwości w magazynie kluczy, **usuwanie nietrwałe** i **nie przeczyszczanie**. Te właściwości nie są domyślnie włączone, ale można je włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure dla nowego lub istniejącego magazynu kluczy.

> [!IMPORTANT]
> Jeśli nie masz włączonych właściwości **usuwania nietrwałego** i **nie przeczyszczasz** i usuniesz klucz, nie będzie można odzyskać danych z zasobu usługi poznawczej.

Aby dowiedzieć się, jak włączyć te właściwości w istniejącym magazynie kluczy, zobacz sekcję zatytułowaną **Włączanie usuwania nietrwałego** i **Włączanie ochrony przed przeczyszczeniem** w jednym z następujących artykułów:

- [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../../key-vault/general/key-vault-recovery.md).
- [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../../key-vault/general/key-vault-recovery.md).

Tylko klucze RSA o rozmiarze 2048 są obsługiwane przez szyfrowanie usługi Azure Storage. Aby uzyskać więcej informacji na temat kluczy, zobacz **Key Vault klucze** w temacie [informacje Azure Key Vault klucze, wpisy tajne i certyfikaty](../../key-vault/general/about-keys-secrets-certificates.md).

## <a name="enable-customer-managed-keys-for-your-resource"></a>Włącz klucze zarządzane przez klienta dla zasobu

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

## <a name="rotate-customer-managed-keys"></a>Obróć klucze zarządzane przez klienta

Klucz zarządzany przez klienta można obrócić w Azure Key Vault zgodnie z zasadami zgodności. Gdy klucz jest obrócony, należy zaktualizować zasób Cognitive Services, aby używał nowego identyfikatora URI klucza. Aby dowiedzieć się, jak zaktualizować zasób w celu używania nowej wersji klucza w Azure Portal, zobacz [Aktualizacja wersji klucza](#update-the-key-version).

Obracanie klucza nie wyzwala ponownego szyfrowania danych w zasobie. Od użytkownika nie są wymagane żadne dalsze działania.

## <a name="revoke-access-to-customer-managed-keys"></a>Odwołaj dostęp do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) lub [interfejs wiersza polecenia Azure Key Vault](/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich danych w zasobie Cognitive Services, ponieważ klucz szyfrowania jest niedostępny przez Cognitive Services.

## <a name="disable-customer-managed-keys"></a>Wyłącz klucze zarządzane przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta zasób Cognitive Services jest szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby wyłączyć klucze zarządzane przez klienta, wykonaj następujące kroki:

1. Przejdź do zasobu Cognitive Services i Wyświetl ustawienia **szyfrowania** .
1. Usuń zaznaczenie pola wyboru obok ustawienia **Użyj własnego klucza** .