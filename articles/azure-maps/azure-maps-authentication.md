---
title: Metody uwierzytelniania | Mapy Microsoft Azure
description: Ten artykuł zawiera informacje na temat Azure Active Directory (Azure AD) i uwierzytelniania klucza współużytkowanego. Oba są używane na potrzeby usług Microsoft Azure Maps. Dowiedz się, jak uzyskać klucz subskrypcji Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335700"
---
# <a name="authentication-with-azure-maps"></a>Uwierzytelnianie w usłudze Azure Maps

Azure Maps obsługuje dwa sposoby uwierzytelniania żądań: uwierzytelnianie klucza wspólnego i uwierzytelnianie Azure Active Directory. W tym artykule opisano te metody uwierzytelniania, które ułatwiają wdrożenie usług Azure Maps.

> [!NOTE]
> Aby ulepszyć bezpieczną komunikację z usługą Azure Maps, firma Microsoft obsługuje teraz protokół Transport Layer Security (TLS) 1,2 i wycofywanie pomocy technicznej dla protokołów TLS 1,0 i 1,1. Aby uniknąć przerw w działaniu usługi, **zaktualizuj serwery i aplikacje do korzystania z protokołu TLS 1,2 przed 2 kwietnia 2020**.  Jeśli obecnie używasz protokołu TLS 1. x, Oceń gotowość protokołu TLS 1,2 i Opracuj plan migracji z testowaniem opisanym w temacie [Rozwiązywanie problemów z protokołem tls 1,0](https://docs.microsoft.com/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Uwierzytelnianie klucza wspólnego

 Klucze podstawowe i pomocnicze są generowane po utworzeniu konta Azure Maps. Zaleca się użycie klucza podstawowego jako klucza subskrypcji podczas wywoływania Azure Maps przy użyciu uwierzytelniania za pomocą klucza współużytkowanego. Uwierzytelnianie klucza wspólnego przekazuje klucz wygenerowany przez konto Azure Maps do usługi Azure Maps. Dla każdego żądania Azure Maps usług Dodaj *klucz subskrypcji* jako parametr do adresu URL. Klucz pomocniczy może być używany w scenariuszach, takich jak stopniowe zmiany klawiszy.  

Aby uzyskać informacje na temat wyświetlania kluczy w Azure Portal, zobacz [Zarządzanie uwierzytelnianiem](https://aka.ms/amauthdetails).

> [!Tip]
> Zaleca się regularne ponowne generowanie kluczy. Podano dwa klucze, dzięki czemu można zachować połączenia z jednym kluczem przy ponownym generowaniu innych. Po ponownym wygenerowaniu kluczy należy zaktualizować wszystkie aplikacje, które uzyskują dostęp do Twojego konta, przy użyciu nowych kluczy.

## <a name="authentication-with-azure-active-directory-preview"></a>Uwierzytelnianie w usłudze Azure Active Directory (wersja zapoznawcza)

Azure Maps teraz oferuje żądania uwierzytelniania dla usług Azure Maps przy użyciu [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Usługa Azure AD zapewnia uwierzytelnianie oparte na tożsamościach, w tym [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). RBAC służy do udzielania dostępu do Azure Maps zasobów na poziomie użytkownika, na poziomie grupy lub na poziomie aplikacji. W następnych sekcjach omówiono koncepcje i składniki Azure Maps integracji z usługą Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Uwierzytelnianie przy użyciu tokenów dostępu OAuth

Azure Maps akceptuje tokeny dostępu **OAuth 2,0** dla dzierżawców usługi Azure AD skojarzonych z subskrypcją platformy Azure, która zawiera konto Azure Maps. Azure Maps również akceptuje tokeny dla:

* Użytkownicy usługi Azure AD
* Aplikacje partnerskie korzystające z uprawnień delegowanych przez użytkowników
* Tożsamości zarządzane dla zasobów platformy Azure

Azure Maps generuje *unikatowy identyfikator (identyfikator klienta)* dla każdego konta Azure Maps. Możesz zażądać tokenów z usługi Azure AD w przypadku łączenia tego identyfikatora klienta z dodatkowymi parametrami. Aby zażądać tokenu, określ wartości w poniższej tabeli w oparciu o środowisko platformy Azure.

| Środowisko platformy Azure   | Punkt końcowy tokenu usługi Azure AD |
| --------------------|-------------------------|
| Azure — publiczna        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Aby uzyskać więcej informacji na temat sposobu konfigurowania usługi Azure AD i tokenów żądań dla Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Aby uzyskać ogólne informacje na temat żądania tokenów z usługi Azure AD, zobacz [co to jest uwierzytelnianie?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Żądaj zasobów usługi Azure map przy użyciu tokenów OAuth

Gdy usługa Azure AD otrzyma token, Azure Maps wysyła żądanie z następującym zestawem wymaganych nagłówków żądań:

| Nagłówek żądania    |    Wartość    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Autoryzacja     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id`jest identyfikatorem GUID opartym na koncie Azure Maps, który pojawia się na stronie uwierzytelnianie Azure Maps.

Oto przykład żądania Azure Maps trasy, które używa tokenu OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Informacje o wyświetlaniu identyfikatora klienta znajdują się w temacie [Wyświetlanie szczegółów uwierzytelniania](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Kontrola dostępu za pomocą RBAC

W usłudze Azure AD Użyj RBAC, aby kontrolować dostęp do zabezpieczonych zasobów. Skonfiguruj konto Azure Maps i zarejestruj dzierżawę Azure Maps usługi Azure AD. Azure Maps obsługuje kontrolę dostępu do odczytu dla poszczególnych użytkowników, grup, aplikacji, zasobów platformy Azure i usług platformy Azure za pośrednictwem zarządzanych tożsamości dla zasobów platformy Azure. Na stronie portalu Azure Maps można skonfigurować RBAC dla wybranych ról.

![Czytnik danych Azure Maps (wersja zapoznawcza)](./media/azure-maps-authentication/concept.png)

Aby uzyskać informacje na temat wyświetlania ustawień RBAC, zobacz [How to configure RBAC for Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Zarządzane tożsamości dla zasobów platformy Azure i Azure Maps

[Zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) zapewniają usługi platformy Azure z automatyczną tożsamością zarządzaną, która może być autoryzowana do uzyskiwania dostępu do usług Azure Maps. Przykłady zarządzanych tożsamości: Azure App Service, Azure Functions i Virtual Machines platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o uwierzytelnianiu aplikacji za pomocą usługi Azure AD i Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Aby dowiedzieć się więcej na temat uwierzytelniania kontrolka mapy Azure Maps i usługi Azure AD, zobacz [Korzystanie z kontrolka mapy Azure Maps](https://aka.ms/amaadmc).
