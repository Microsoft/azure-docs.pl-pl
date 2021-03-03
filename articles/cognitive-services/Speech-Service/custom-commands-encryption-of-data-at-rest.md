---
title: Usługa poleceń niestandardowych szyfrowanie danych w spoczynku
titleSuffix: Azure Cognitive Services
description: Niestandardowe polecenia szyfrowania danych magazynowanych.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: 89d7a6f8beb004f57a00dfe75e4cc387c8591b1e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716588"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>Szyfrowanie danych magazynowanych przy użyciu poleceń niestandardowych

Polecenia niestandardowe automatycznie szyfrują dane, gdy są utrwalane w chmurze. Szyfrowanie usługi poleceń niestandardowych chroni dane i pomaga spełnić wymagania dotyczące zabezpieczeń i zgodności organizacji.

> [!NOTE]
> Usługa poleceń niestandardowych nie włącza automatycznie szyfrowania dla zasobów LUIS skojarzonych z aplikacją. W razie potrzeby należy włączyć szyfrowanie dla zasobu LUIS z tego [miejsca](../luis/encrypt-data-at-rest.md).

## <a name="about-cognitive-services-encryption"></a>Informacje o szyfrowaniu Cognitive Services
Dane są szyfrowane i odszyfrowywane przy użyciu zgodności ze standardem [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bitowego szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) . Szyfrowanie i odszyfrowywanie są przezroczyste, co oznacza, że szyfrowanie i dostęp są zarządzane przez Ciebie. Dane są domyślnie bezpieczne i nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania.

## <a name="about-encryption-key-management"></a>Informacje o zarządzaniu kluczami szyfrowania

W przypadku korzystania z poleceń niestandardowych usługa Speech Service będzie przechowywać następujące dane w chmurze:
* Plik JSON konfiguracji za pomocą aplikacji poleceń niestandardowych
* LUIS tworzenie i klucz predykcyjny

Domyślnie subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Można też zarządzać subskrypcją przy użyciu własnych kluczy szyfrowania. Klucze zarządzane przez klienta (CMK), znane także jako własne klucze (BYOK), zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych.


> [!IMPORTANT]
> Klucze zarządzane przez klienta są dostępne tylko dla zasobów utworzonych po 27 czerwca 2020. Aby korzystać z CMK z usługą Speech Services, musisz utworzyć nowy zasób mowy. Po utworzeniu zasobu możesz użyć Azure Key Vault, aby skonfigurować swoją tożsamość zarządzaną.

Aby zażądać możliwości korzystania z kluczy zarządzanych przez klienta, Wypełnij i prześlij Customer-Managed formularz żądania klucza. Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z CMK za pomocą usługi Speech Services należy utworzyć nowy zasób mowy na podstawie Azure Portal.
   > [!NOTE]
   > **Klucze zarządzane przez klienta (CMK) są obsługiwane tylko dla poleceń niestandardowych.**
   >
   >  **Custom Speech i niestandardowy głos nadal obsługują tylko własny magazyn (BYOS).**  [Dowiedz się więcej](speech-encryption-of-data-at-rest.md)
   >
   > Jeśli używasz danego zasobu mowy do uzyskiwania dostępu do tej usługi, musisz spełnić wymagania dotyczące zgodności, jawnie konfigurując BYOS.


## <a name="customer-managed-keys-with-azure-key-vault"></a>Klucze zarządzane przez klienta za pomocą usługi Azure Key Vault

Aby przechowywać klucze zarządzane przez klienta, należy użyć Azure Key Vault. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Zasób mowy i Magazyn kluczy muszą znajdować się w tym samym regionie i w tej samej dzierżawie usługi Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/general/overview.md).

Gdy nowy zasób mowy jest tworzony i używany do udostępniania poleceń niestandardowych — dane są zawsze szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. W momencie utworzenia zasobu nie można włączyć kluczy zarządzanych przez klienta. Klucze zarządzane przez klienta są przechowywane w Azure Key Vault, a Magazyn kluczy musi być zainicjowany przy użyciu zasad dostępu, które przyznają kluczowe uprawnienia do zarządzanej tożsamości skojarzonej z zasobem Cognitive Services. Tożsamość zarządzana jest dostępna tylko po utworzeniu zasobu przy użyciu warstwy cenowej wymaganej przez CMK.

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

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>Włącz klucze zarządzane przez klienta dla zasobu mowy

Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do zasobu mowy.
1. W bloku **Ustawienia** dla zasobu mowy kliknij pozycję **szyfrowanie**. Wybierz opcję **klucze zarządzane przez klienta** , jak pokazano na poniższej ilustracji.

 ![Zrzut ekranu przedstawiający sposób wybierania kluczy zarządzanych przez klienta](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>Określ klucz

Po włączeniu kluczy zarządzanych przez klienta będziesz mieć możliwość określenia klucza do skojarzenia z zasobem Cognitive Services.

### <a name="specify-a-key-as-a-uri"></a>Określ klucz jako identyfikator URI

Aby określić klucz jako identyfikator URI, wykonaj następujące kroki:

1. Aby zlokalizować identyfikator URI klucza w Azure Portal, przejdź do magazynu kluczy, a następnie wybierz ustawienie **klucze** . Wybierz odpowiedni klucz, a następnie kliknij klucz, aby wyświetlić jego wersje. Wybierz wersję klucza, aby wyświetlić ustawienia dla tej wersji.
1. Skopiuj wartość pola **Identyfikator klucza** , która zapewnia identyfikator URI.

    ![Zrzut ekranu przedstawiający identyfikator URI klucza magazynu kluczy](../media/cognitive-services-encryption/key-uri-portal.png)

1. W ustawieniach **szyfrowania** dla usługi mowy wybierz opcję **Wprowadź identyfikator URI klucza** .
1. Wklej identyfikator URI, który został skopiowany do pola **klucza URI** .

1. Określ subskrypcję zawierającą magazyn kluczy.
1. Zapisz zmiany.

### <a name="specify-a-key-from-a-key-vault"></a>Określ klucz z magazynu kluczy

Aby określić klucz z magazynu kluczy, najpierw upewnij się, że masz Magazyn kluczy zawierający klucz. Aby określić klucz z magazynu kluczy, wykonaj następujące kroki:

1. Wybierz opcję **Wybierz z Key Vault** .
1. Wybierz magazyn kluczy zawierający klucz, którego chcesz użyć.
1. Wybierz klucz z magazynu kluczy.

   ![Zrzut ekranu przedstawiający opcję klucz zarządzany przez klienta](media/custom-commands/configure-cmk-fromKV.png)

1. Zapisz zmiany.

## <a name="update-the-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować zasób mowy, aby korzystał z nowej wersji. Wykonaj następujące kroki:

1. Przejdź do zasobu mowy i Wyświetl ustawienia **szyfrowania** .
1. Wprowadź identyfikator URI dla nowej wersji klucza. Alternatywnie można wybrać Magazyn kluczy i ponownie klucz, aby zaktualizować wersję.
1. Zapisz zmiany.

## <a name="use-a-different-key"></a>Użyj innego klucza

Aby zmienić klucz używany do szyfrowania, wykonaj następujące kroki:

1. Przejdź do zasobu mowy i Wyświetl ustawienia **szyfrowania** .
1. Wprowadź identyfikator URI dla nowego klucza. Alternatywnie można wybrać Magazyn kluczy i wybrać nowy klucz.
1. Zapisz zmiany.

## <a name="rotate-customer-managed-keys"></a>Obróć klucze zarządzane przez klienta

Klucz zarządzany przez klienta można obrócić w Azure Key Vault zgodnie z zasadami zgodności. Gdy klucz jest obrócony, należy zaktualizować zasób mowy, aby używał nowego identyfikatora URI klucza. Aby dowiedzieć się, jak zaktualizować zasób w celu używania nowej wersji klucza w Azure Portal, zobacz [Aktualizacja wersji klucza](#update-the-key-version).

Obracanie klucza nie wyzwala ponownego szyfrowania danych w zasobie. Od użytkownika nie są wymagane żadne dalsze działania.

## <a name="revoke-access-to-customer-managed-keys"></a>Odwołaj dostęp do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) lub [interfejs wiersza polecenia Azure Key Vault](/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich danych w zasobie Cognitive Services, ponieważ klucz szyfrowania jest niedostępny przez Cognitive Services.

## <a name="disable-customer-managed-keys"></a>Wyłącz klucze zarządzane przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta zasób mowy jest szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby wyłączyć klucze zarządzane przez klienta, wykonaj następujące kroki:

1. Przejdź do zasobu mowy i Wyświetl ustawienia **szyfrowania** .
1. Usuń zaznaczenie pola wyboru obok ustawienia **Użyj własnego klucza** .

## <a name="next-steps"></a>Następne kroki

* [Formularz żądania klucza Customer-Managed mowy](https://aka.ms/cogsvc-cmk)
* [Dowiedz się więcej o Azure Key Vault](../../key-vault/general/overview.md)
* [Co to są tożsamości zarządzane](../../active-directory/managed-identities-azure-resources/overview.md)