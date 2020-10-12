---
title: Zarządzane tożsamości w usłudze Azure Signal Service
description: Dowiedz się, jak zarządzane tożsamości działają w usłudze Azure Signal i jak używać tożsamości zarządzanej w scenariuszach bezserwerowych.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: abe7503e7eb73d533ae901af21de001960173fb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85559414"
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

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Dodawanie tożsamości przypisanej do systemu w portalu":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Używanie tożsamości zarządzanej w scenariuszach bezserwerowych

Usługa Azure Signal Service to w pełni zarządzana usługa, dlatego nie można używać tożsamości zarządzanej do ręcznego uzyskiwania tokenów. Zamiast tego usługa Azure sygnalizująca korzysta z tożsamości zarządzanej ustawionej w celu uzyskania tokenu dostępu. Następnie Usługa ustawia token dostępu do `Authorization` nagłówka w żądaniu nadrzędnym w scenariuszach bezserwerowych.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Włącz uwierzytelnianie tożsamości zarządzanej w ustawieniach nadrzędnych

1. Dodawanie tożsamości przypisanej do systemu lub tożsamości przypisanej do użytkownika.

2. Skonfiguruj ustawienia nadrzędne i Użyj **ManagedIdentity** jako ustawień **uwierzytelniania** . Aby dowiedzieć się, jak utworzyć ustawienia nadrzędne z uwierzytelnianiem, zobacz [Ustawienia nadrzędne](concept-upstream.md).

3. W ustawieniach uwierzytelniania tożsamości zarządzanej dla **zasobu**można określić zasób docelowy. Zasób stanie się `aud` roszczeń w uzyskanym tokenie dostępu, który może być używany jako część weryfikacji w punktach końcowych w strumieniu. Może to być jeden z następujących zasobów:
    - Pusty
    - Identyfikator aplikacji (klienta) jednostki usługi
    - Identyfikator URI identyfikatora aplikacji nazwy głównej usługi
    - [Identyfikator zasobu usługi platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > W przypadku sprawdzenia poprawności tokenu dostępu przez użytkownika w usłudze możesz wybrać dowolny z formatów zasobów. Upewnij się, że wartość **zasobu** w ustawieniach **uwierzytelniania** i weryfikacja jest spójna. W przypadku korzystania z kontroli dostępu opartej na rolach (RBAC) dla płaszczyzny danych należy użyć zasobu wymaganego przez dostawcę usług.

### <a name="validate-access-tokens"></a>Weryfikowanie tokenów dostępu

Token w `Authorization` nagłówku jest [tokenem dostępu platformy tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

Aby sprawdzić poprawność tokenów dostępu, aplikacja powinna również sprawdzić poprawność odbiorców i tokenów podpisywania. Należy sprawdzić poprawność tych wartości w dokumencie odnajdywania OpenID Connect. Na przykład zapoznaj się z [wersją dokumentu niezależną od dzierżawy](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Oprogramowanie pośredniczące Azure Active Directory (Azure AD) ma wbudowane funkcje do sprawdzania poprawności tokenów dostępu. Możesz przejrzeć nasze [przykłady](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) , aby znaleźć je w wybranym języku.

Udostępniamy biblioteki i przykłady kodu, które pokazują, jak obsługiwać sprawdzanie poprawności tokenu. Istnieje również kilka bibliotek partnerskich Open Source dostępnych dla weryfikacji tokenu sieci Web JSON (JWT). Istnieje co najmniej jedna opcja dla niemal każdej platformy i języka w tym miejscu. Aby uzyskać więcej informacji na temat bibliotek uwierzytelniania i przykładów kodu usługi Azure AD, zobacz [biblioteki uwierzytelniania platformy tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries).

## <a name="next-steps"></a>Następne kroki

- [Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service](signalr-concept-serverless-development-config.md)
