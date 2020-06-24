---
title: Zarządzane tożsamości w usłudze Azure Signal Service
description: Dowiedz się, jak zarządzane tożsamości działają w usłudze Azure Signal Service, jak skonfigurować program do korzystania z tożsamości zarządzanej w scenariuszach bezserwerowych.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: 8e122be74d623c7f1ea5576a5fe527dfa538d1d1
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988661"
---
# <a name="how-to-use-managed-identities-for-azure-signalr-service"></a>Jak używać tożsamości zarządzanych dla usługi Azure Signal Service

W tym temacie pokazano, jak utworzyć zarządzaną tożsamość usługi Azure Signal Service oraz jak korzystać z niej w scenariuszach bezserwerowych.

> [!Important] 
> Usługa Azure Signal Service może obsługiwać tylko jedną tożsamość zarządzaną. Oznacza to, że można dodać tożsamości przypisanej do systemu lub jedną tożsamość przypisaną przez użytkownika. 

## <a name="add-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, należy najpierw utworzyć usługę sygnalizującą jako normalną, a następnie włączyć tę funkcję.

1. Utwórz usługę sygnalizującą w portalu w miarę jak zwykle. Przejdź do niej w portalu.

2. Wybierz pozycję **tożsamość**.

4. W ramach karty **przypisanej do systemu** Przełącz pozycję **stan** na wartość **włączone**. Kliknij pozycję **Zapisz**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Dodawanie tożsamości przypisanej do systemu w portalu":::

## <a name="add-a-user-assigned-identity"></a>Dodawanie tożsamości przypisanej do użytkownika

Utworzenie usługi sygnalizującej z tożsamością przypisaną przez użytkownika wymaga utworzenia tożsamości, a następnie dodania jej identyfikatora zasobu do usługi.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Najpierw należy utworzyć zasób tożsamości przypisany przez użytkownika.

1. Utwórz zasób tożsamości zarządzanej przez użytkownika zgodnie z [tymi instrukcjami](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Utwórz usługę sygnalizującą w portalu w miarę jak zwykle. Przejdź do niej w portalu.

3. Wybierz pozycję **tożsamość**.

4. Na karcie **przypisane przez użytkownika** kliknij przycisk **Dodaj**.

5. Wyszukaj utworzoną wcześniej tożsamość i wybierz ją. Kliknij pozycję **Dodaj**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Dodawanie tożsamości użytkownika-assigened w portalu":::

## <a name="use-managed-identity-in-serverless-scenarios"></a>Używanie tożsamości zarządzanej w scenariuszach bezserwerowych

Usługa sygnalizująca to w pełni zarządzana usługa, więc nie można używać tożsamości zarządzanej do ręcznego uzyskiwania tokenów. Zamiast tego usługa sygnalizująca używa zarządzanej tożsamości ustawionej w taki sposób, aby uzyskać token dostępu i ustawić jako `Authorization` nagłówek w żądaniu nadrzędnym w scenariuszach bezserwerowych.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Włącz uwierzytelnianie tożsamości zarządzanej w ustawieniach nadrzędnych

1. Dodawanie tożsamości przypisanej do systemu lub tożsamości przypisanej do użytkownika

2. Skonfiguruj ustawienia nadrzędne i Użyj *ManagedIdentity* jako ustawień *uwierzytelniania* . Znajdź sposób tworzenia ustawień nadrzędnych z uwierzytelnianiem w [ustawieniach nadrzędnych](concept-upstream.md#create-upstream-settings).

3. W ustawieniach uwierzytelniania tożsamości zarządzanej można określić *zasób*docelowy. *Zasób* zostanie `aud` pozyskany w uzyskanym tokenie dostępu, który może być używany jako część weryfikacji w punktach końcowych w strumieniu. Może to być jeden z następujących *zasobów* :
    - Pusty
    - Identyfikator aplikacji (klienta) jednostki usługi
    - Identyfikator URI identyfikatora aplikacji nazwy głównej usługi
    - Identyfikatory zasobów usług platformy Azure, zobacz [Identyfikator zasobu usług platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > W przypadku zweryfikowania tokenu dostępu przez użytkownika w usłudze możesz wybrać *dowolny z nich* . Tak długo, aby upewnić się, że *zasób* w ustawieniach *uwierzytelniania* i sprawdzanie poprawności są spójne. W przypadku korzystania z kontroli RBAC płaszczyzny danych należy użyć *zasobu* , którego żąda dostawca usług.

### <a name="validate-access-token"></a>Weryfikowanie tokenu dostępu

Token w `Authorization` nagłówku jest [tokenem dostępu platformy tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

Aby sprawdzić poprawność tokenów dostępu, aplikacja powinna również sprawdzić poprawność odbiorców oraz tokeny podpisywania. Należy sprawdzić poprawność tych wartości w dokumencie odnajdywania OpenID Connect. Na przykład wersja dokumentu niezależna od dzierżawy znajduje się w lokalizacji [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

Oprogramowanie pośredniczące usługi Azure AD ma wbudowane funkcje do sprawdzania poprawności tokenów dostępu. możesz przeglądać nasze [przykłady](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) , aby znaleźć je w wybranym języku.

Udostępniamy biblioteki i przykłady kodu, które pokazują, jak obsługiwać sprawdzanie poprawności tokenu. Poniższe informacje są dostępne dla osób, które chcą zrozumieć proces podstawowy. Istnieje również kilka bibliotek Open-Source innych firm dostępnych do sprawdzania poprawności tokenu JWT — istnieje co najmniej jedna opcja dla niemal każdej platformy i języka. Aby uzyskać więcej informacji na temat bibliotek uwierzytelniania i przykładów kodu usługi Azure AD, zobacz [biblioteki uwierzytelniania w wersji 2.0](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries).

## <a name="next-steps"></a>Następne kroki

- [Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service](signalr-concept-serverless-development-config.md)
