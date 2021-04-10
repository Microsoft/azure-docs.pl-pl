---
title: Zarządzane tożsamości w usłudze Azure Signal Service
description: Dowiedz się, jak zarządzane tożsamości działają w usłudze Azure Signal i jak używać tożsamości zarządzanej w scenariuszach bezserwerowych.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: dee15977318eda7bcd0b1950286bb33f621221dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731588"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Zarządzane tożsamości dla usługi Azure Signal Service

W tym artykule pokazano, jak utworzyć zarządzaną tożsamość usługi Azure Signal Service oraz jak korzystać z niej w scenariuszach bezserwerowych.

> [!Important] 
> Usługa Azure Signal Service może obsługiwać tylko jedną tożsamość zarządzaną. Oznacza to, że można dodać tożsamości przypisanej do systemu lub tożsamości przypisanej do użytkownika. 

## <a name="add-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu

Aby skonfigurować tożsamość zarządzaną w Azure Portal, należy najpierw utworzyć wystąpienie usługi Azure sygnalizujące, a następnie włączyć tę funkcję.

1. Utwórz wystąpienie usługi Azure Signal Service w portalu, jak zwykle. Przejdź do niego w portalu.

2. Wybierz pozycję **tożsamość**.

4. Na karcie **przypisana przez system** Przełącz pozycję **stan** na wartość **włączone**. Wybierz pozycję **Zapisz**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Dodawanie tożsamości przypisanej do systemu w portalu":::

## <a name="add-a-user-assigned-identity"></a>Dodawanie tożsamości przypisanej do użytkownika

Utworzenie wystąpienia usługi Azure sygnalizującego z tożsamością przypisaną przez użytkownika wymaga utworzenia tożsamości, a następnie dodania jej identyfikatora zasobu do usługi.

1. Utwórz zasób tożsamości zarządzanej przez użytkownika zgodnie z [tymi instrukcjami](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Utwórz wystąpienie usługi Azure Signal Service w portalu, jak zwykle. Przejdź do niego w portalu.

3. Wybierz pozycję **tożsamość**.

4. Na karcie **przypisane przez użytkownika** wybierz pozycję **Dodaj**.

5. Wyszukaj utworzoną wcześniej tożsamość i wybierz ją. Wybierz pozycję **Dodaj**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Dodawanie tożsamości przypisanej do użytkownika w portalu":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Używanie tożsamości zarządzanej w scenariuszach bezserwerowych

Usługa Azure Signal Service to w pełni zarządzana usługa, dlatego nie można używać tożsamości zarządzanej do ręcznego uzyskiwania tokenów. Zamiast tego usługa Azure sygnalizująca korzysta z tożsamości zarządzanej ustawionej w celu uzyskania tokenu dostępu. Następnie Usługa ustawia token dostępu do `Authorization` nagłówka w żądaniu nadrzędnym w scenariuszach bezserwerowych.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Włącz uwierzytelnianie tożsamości zarządzanej w ustawieniach nadrzędnych

1. Dodawanie tożsamości przypisanej do systemu lub tożsamości przypisanej do użytkownika.

2. Dodaj jedno ustawienie nadrzędne i kliknij dowolną gwiazdkę, aby przejść do szczegółowej strony, jak pokazano poniżej.
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="ustawienie przed MSI":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="Ustawienia MSI":::

3. W ustawieniach uwierzytelniania tożsamości zarządzanej dla **zasobu** można określić zasób docelowy. Zasób stanie się `aud` roszczeń w uzyskanym tokenie dostępu, który może być używany jako część weryfikacji w punktach końcowych w strumieniu. Może to być jeden z następujących zasobów:
    - Pusty
    - Identyfikator aplikacji (klienta) jednostki usługi
    - Identyfikator URI identyfikatora aplikacji nazwy głównej usługi
    - [Identyfikator zasobu usługi platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > W przypadku sprawdzenia poprawności tokenu dostępu przez użytkownika w usłudze możesz wybrać dowolny z formatów zasobów. Upewnij się, że wartość **zasobu** w ustawieniach **uwierzytelniania** i weryfikacja jest spójna. Jeśli używasz kontroli dostępu opartej na rolach (Azure RBAC) na potrzeby płaszczyzny danych, musisz użyć zasobu, którego żąda dostawca usług.

### <a name="validate-access-tokens"></a>Weryfikowanie tokenów dostępu

Token w `Authorization` nagłówku jest [tokenem dostępu platformy tożsamości firmy Microsoft](../active-directory/develop/access-tokens.md#validating-tokens).

Aby sprawdzić poprawność tokenów dostępu, aplikacja powinna również sprawdzić poprawność odbiorców i tokenów podpisywania. Należy sprawdzić poprawność tych wartości w dokumencie odnajdywania OpenID Connect. Na przykład zapoznaj się z [wersją dokumentu niezależną od dzierżawy](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Oprogramowanie pośredniczące Azure Active Directory (Azure AD) ma wbudowane funkcje do sprawdzania poprawności tokenów dostępu. Możesz przejrzeć nasze [przykłady](../active-directory/develop/sample-v2-code.md) , aby znaleźć je w wybranym języku.

Udostępniamy biblioteki i przykłady kodu, które pokazują, jak obsługiwać sprawdzanie poprawności tokenu. Istnieje również kilka bibliotek partnerskich Open Source dostępnych dla weryfikacji tokenu sieci Web JSON (JWT). Istnieje co najmniej jedna opcja dla niemal każdej platformy i języka w tym miejscu. Aby uzyskać więcej informacji na temat bibliotek uwierzytelniania i przykładów kodu usługi Azure AD, zobacz [biblioteki uwierzytelniania platformy tożsamości firmy Microsoft](../active-directory/develop/reference-v2-libraries.md).

#### <a name="authentication-in-function-app"></a>Uwierzytelnianie w aplikacja funkcji

Ustawienie walidacji tokenów dostępu w aplikacja funkcji jest łatwe i wydajne bez działania kodu.

1. Na stronie **uwierzytelnianie/autoryzacja** Przełącz opcję **uwierzytelnianie App Service** na wartość **włączone**.

2. Wybierz pozycję **Zaloguj się za pomocą Azure Active Directory** w **akcji, która ma zostać podjęta, gdy żądanie nie zostanie uwierzytelnione**.

3. W dostawcy uwierzytelniania kliknij pozycję w **Azure Active Directory**

4. Na nowej stronie. Wybierz pozycję **Express** i **Utwórz nową aplikację usługi AD** , a następnie kliknij przycisk **OK** :::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="Funkcja AAD"::: .

5. Przejdź do usługi sygnalizującej [i postępuj zgodnie z instrukcjami,](howto-use-managed-identity.md#add-a-system-assigned-identity) aby dodać tożsamość przypisaną do systemu lub tożsamość przypisaną przez użytkownika.

6. Aby przejść do **ustawień nadrzędnych** w usłudze sygnalizującego, wybierz opcję **Użyj tożsamości zarządzanej** i **Wybierz z istniejących aplikacji**. Wybierz utworzoną wcześniej aplikację.

Po tych ustawieniach aplikacja funkcji będzie odrzucać żądania bez tokenu dostępu w nagłówku.

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Użyj tożsamości zarządzanej do Key Vault odwołania

Usługa sygnalizująca może uzyskać dostęp do Key Vault, aby uzyskać klucz tajny przy użyciu tożsamości zarządzanej.

1. Dodaj tożsamość przypisaną do systemu lub tożsamość przypisaną przez użytkownika dla usługi Azure Signal Service.

2. Przyznaj uprawnienia do odczytu dla tożsamości zarządzanej w zasadach dostępu w Key Vault. Zobacz [przypisywanie zasad dostępu Key Vault przy użyciu Azure Portal](../key-vault/general/assign-access-policy-portal.md)

Obecnie ta funkcja może być używana w następujących scenariuszach:

- [Wpis tajny odwołania w wzorcu nadrzędnego adresu URL](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>Następne kroki

- [Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service](signalr-concept-serverless-development-config.md)