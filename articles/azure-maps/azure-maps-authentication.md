---
title: Uwierzytelnianie za pomocą map Microsoft Azure
titleSuffix: Azure Maps
description: 'Poznaj dwa sposoby uwierzytelniania żądań w Azure Maps: uwierzytelnianie klucza wspólnego i uwierzytelnianie Azure Active Directory (Azure AD).'
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9c973859c8b7a3d04693946f50377837c3538b85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094092"
---
# <a name="authentication-with-azure-maps"></a>Uwierzytelnianie w usłudze Azure Maps

Azure Maps obsługuje dwa sposoby uwierzytelniania żądań: uwierzytelnianie klucza wspólnego i uwierzytelnianie [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) . W tym artykule opisano metody uwierzytelniania, które ułatwiają wdrożenie usług Azure Maps.

> [!NOTE]
> Aby ulepszyć bezpieczną komunikację z usługą Azure Maps, firma Microsoft obsługuje teraz protokół Transport Layer Security (TLS) 1,2 i wycofywanie pomocy technicznej dla protokołów TLS 1,0 i 1,1. Jeśli obecnie używasz protokołu TLS 1. x, Oceń gotowość protokołu TLS 1,2 i Opracuj plan migracji z testowaniem opisanym w temacie [Rozwiązywanie problemów z protokołem tls 1,0](/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Uwierzytelnianie klucza wspólnego

 Klucze podstawowe i pomocnicze są generowane po utworzeniu konta Azure Maps. Podczas wywoływania Azure Maps z uwierzytelnianiem za pomocą klucza wspólnego zaleca się użycie klucza podstawowego jako klucza subskrypcji. Uwierzytelnianie klucza wspólnego przekazuje klucz wygenerowany przez konto Azure Maps do usługi Azure Maps. Dla każdego żądania Azure Maps usług Dodaj *klucz subskrypcji* jako parametr do adresu URL. Klucz pomocniczy może być używany w scenariuszach, takich jak stopniowe zmiany klawiszy.  

Aby uzyskać informacje na temat wyświetlania kluczy w Azure Portal, zobacz [Zarządzanie uwierzytelnianiem](./how-to-manage-authentication.md#view-authentication-details).

> [!TIP]
> Ze względów bezpieczeństwa zaleca się obracanie między kluczami podstawowymi i pomocniczymi. Aby obrócić klucze, zaktualizuj aplikację tak, aby korzystała z klucza pomocniczego, wdróż, a następnie naciśnij przycisk Cykl/Odśwież obok klucza podstawowego, aby wygenerować nowy klucz podstawowy. Stary klucz podstawowy zostanie wyłączony. Aby uzyskać więcej informacji na temat rotacji kluczy, zobacz [konfigurowanie Azure Key Vault przy użyciu rotacji kluczy i inspekcji](../key-vault/secrets/tutorial-rotation-dual.md)

## <a name="azure-ad-authentication"></a>Uwierzytelnianie w usłudze Azure AD

Subskrypcje platformy Azure są dostarczane z dzierżawą usługi Azure AD w celu umożliwienia precyzyjnej kontroli dostępu. Azure Maps oferuje uwierzytelnianie dla usług Azure Maps za pomocą usługi Azure AD. Usługa Azure AD zapewnia uwierzytelnianie oparte na tożsamościach użytkowników i aplikacji zarejestrowanych w dzierżawie usługi Azure AD.

Azure Maps akceptuje tokeny dostępu **OAuth 2,0** dla dzierżawców usługi Azure AD skojarzonych z subskrypcją platformy Azure, która zawiera konto Azure Maps. Azure Maps również akceptuje tokeny dla:

* Użytkownicy usługi Azure AD
* Aplikacje partnerskie korzystające z uprawnień delegowanych przez użytkowników
* Tożsamości zarządzane dla zasobów platformy Azure

Azure Maps generuje *unikatowy identyfikator (identyfikator klienta)* dla każdego konta Azure Maps. Możesz zażądać tokenów z usługi Azure AD w przypadku łączenia tego identyfikatora klienta z dodatkowymi parametrami.

Aby uzyskać więcej informacji na temat sposobu konfigurowania usługi Azure AD i tokenów żądań dla Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

Aby uzyskać ogólne informacje o uwierzytelnianiu w usłudze Azure AD, zobacz [co to jest uwierzytelnianie?](../active-directory/develop/authentication-vs-authorization.md).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Zarządzane tożsamości dla zasobów platformy Azure i Azure Maps

[Zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) zapewniają usługi platformy Azure z automatycznie zarządzanym podmiotem zabezpieczeń opartym na aplikacji, który może być uwierzytelniany w usłudze Azure AD. Za pomocą kontroli dostępu opartej na rolach (Azure RBAC), zarządzanym podmiotem zabezpieczeń tożsamości można autoryzować do uzyskiwania dostępu do usług Azure Maps. Przykłady zarządzanych tożsamości obejmują: Azure App Service, Azure Functions i Virtual Machines platformy Azure. Aby uzyskać listę zarządzanych tożsamości, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="configuring-application-azure-ad-authentication"></a>Konfigurowanie uwierzytelniania aplikacji Azure AD

Aplikacje będą uwierzytelniane w dzierżawie usługi Azure AD przy użyciu co najmniej jednego obsługiwanego scenariusza dostarczonego przez usługę Azure AD. Każdy scenariusz aplikacji usługi Azure AD reprezentuje różne wymagania w zależności od potrzeb firmy. Niektóre aplikacje mogą wymagać środowiska logowania użytkownika, a inne aplikacje mogą wymagać logowania do aplikacji. Aby uzyskać więcej informacji, zobacz [przepływy uwierzytelniania i scenariusze aplikacji](../active-directory/develop/authentication-flows-app-scenarios.md).

Po odebraniu tokenu dostępu przez aplikację zestaw SDK i/lub aplikacja wysyła żądanie HTTPS z następującym zestawem wymaganych nagłówków HTTP, a także z innymi nagłówkami HTTP interfejsu API REST:

| Nazwa nagłówka    | Wartość               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| Autoryzacja  | Bearer eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id` jest identyfikatorem GUID opartym na koncie Azure Maps, który pojawia się na stronie uwierzytelnianie Azure Maps.

Oto przykład żądania Azure Maps trasy, które używa tokenu okaziciela OAuth usługi Azure AD:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Informacje o wyświetlaniu identyfikatora klienta znajdują się w temacie [Wyświetlanie szczegółów uwierzytelniania](./how-to-manage-authentication.md#view-authentication-details).

## <a name="authorization-with-role-based-access-control"></a>Autoryzacja za pomocą kontroli dostępu opartej na rolach

Azure Maps obsługuje dostęp do wszystkich typów podmiotów dla [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md) , w tym: indywidualnych użytkowników, grup, aplikacji platformy Azure AD i tożsamości zarządzanych przez platformę Azure. Typy główne mają przyznany zestaw uprawnień, nazywany także definicją roli. Definicja roli zapewnia uprawnienia do akcji interfejsu API REST. Stosowanie dostępu do co najmniej jednego konta Azure Maps jest znane jako zakres. W przypadku stosowania podmiotu zabezpieczeń, definicji roli i zakresu zostanie utworzone przypisanie roli. 

W następnych sekcjach omówiono koncepcje i składniki Azure Maps integrację z usługą Azure RBAC. W ramach procesu konfigurowania konta Azure Maps katalog usługi Azure AD jest skojarzony z subskrypcją platformy Azure, w której znajduje się konto Azure Maps. 

Podczas konfigurowania usługi Azure RBAC należy wybrać podmiot zabezpieczeń i zastosować go do przypisania roli. Aby dowiedzieć się, jak dodać przypisania ról na Azure Portal, zobacz [Przypisywanie ról platformy Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="picking-a-role-definition"></a>Wybieranie definicji roli

W celu obsługi scenariuszy aplikacji istnieją następujące typy definicji ról.

| Definicja roli platformy Azure       | Opis                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure Maps czytnika danych      | Zapewnia dostęp do niezmiennego Azure Maps interfejsów API REST.                                                       |
| Współautor danych Azure Maps | Zapewnia dostęp do modyfikowalnych interfejsów API REST Azure Maps. Zmienność jest definiowana przez akcje: Write i DELETE. |
| Definicja roli niestandardowej      | Utwórz przygotowaną rolę, aby włączyć elastyczny dostęp ograniczony do Azure Maps interfejsów API REST.                      |

Niektóre usługi Azure Maps mogą wymagać podwyższonego poziomu uprawnień do wykonywania akcji zapisu lub usuwania na Azure Maps interfejsach API REST. Rola współautora danych Azure Maps jest wymagana dla usług, które zapewniają akcje zapisu lub usuwania. W poniższej tabeli opisano, których usług Azure Maps współautor danych dotyczy użycie akcji zapisu lub usuwania w danej usłudze. Jeśli w usłudze są używane tylko akcje odczytu, można użyć czytnika danych Azure Maps zamiast Azure Maps współautor danych.

| Usługa Azure Maps | Azure Maps definicję roli  |
| :----------------- | :-------------------------- |
| Dane               | Współautor danych Azure Maps |
| Kreator            | Współautor danych Azure Maps |
| Przestrzenne            | Współautor danych Azure Maps |

Informacje o wyświetlaniu ustawień kontroli dostępu platformy Azure można znaleźć w temacie [How to configure Azure RBAC for Azure Maps](./how-to-manage-authentication.md).

#### <a name="custom-role-definitions"></a>Niestandardowe definicje ról

Jednym z aspektów zabezpieczeń aplikacji jest zastosowanie zasady najniższych uprawnień. Ta zasada oznacza, że podmiot zabezpieczeń powinien mieć tylko dozwolony dostęp, który jest wymagany i nie ma dodatkowego dostępu. Tworzenie niestandardowych definicji ról może obsługiwać przypadki użycia, które wymagają dalszej szczegółowości kontroli dostępu. Aby utworzyć niestandardową definicję roli, można wybrać określone akcje danych, które mają zostać dołączone lub wykluczone dla definicji.

Definicji roli niestandardowej można następnie użyć w przypisaniu roli dla dowolnego podmiotu zabezpieczeń. Aby dowiedzieć się więcej na temat definicji ról niestandardowych platformy Azure, zobacz [role niestandardowe platformy Azure](../role-based-access-control/custom-roles.md).

Poniżej przedstawiono kilka przykładowych scenariuszy, w których role niestandardowe mogą poprawić zabezpieczenia aplikacji.

| Scenariusz                                                                                                                                                                                                                 | Niestandardowe akcje danych roli                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Publiczna lub interaktywna Strona sieci Web logowania z kafelkami mapy podstawowej i inne interfejsy API REST.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Aplikacja, która wymaga jedynie odwrotnego geokodowania i nie ma innych interfejsów API REST.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Rola podmiotu zabezpieczeń, która żąda odczytywania danych mapy opartych na programie Azure Maps Creator i interfejsów API REST kafelka mapy podstawowej.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Rola podmiotu zabezpieczeń, która wymaga odczytywania, zapisywania i usuwania danych mapy opartych na programie Creator. Może to być zdefiniowane jako rola edytora danych mapy, ale nie zezwala na dostęp do innych interfejsów API REST, takich jak kafelki mapy podstawowej. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Zrozumienie zakresu

Podczas tworzenia przypisania roli jest on definiowany w hierarchii zasobów platformy Azure. W górnej części hierarchii jest [Grupa zarządzania](../governance/management-groups/overview.md) , a najniższy to zasób platformy Azure, taki jak konto Azure Maps.
Przypisanie przypisania roli do grupy zasobów może umożliwić dostęp do wielu kont Azure Maps lub zasobów w grupie.

> [!TIP]
> Ogólnym zaleceniem firmy Microsoft jest przypisanie dostępu do zakresu konta Azure Maps, ponieważ uniemożliwia on **niezamierzony dostęp do innych kont Azure Maps** istniejących w tej samej subskrypcji platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze Azure RBAC, zobacz
> [!div class="nextstepaction"]
> [Kontrola dostępu na podstawie ról na platformie Azure](../role-based-access-control/overview.md)

Aby dowiedzieć się więcej o uwierzytelnianiu aplikacji za pomocą usługi Azure AD i Azure Maps, zobacz
> [!div class="nextstepaction"]
> [Zarządzanie uwierzytelnianiem w Azure Maps](./how-to-manage-authentication.md)

Aby dowiedzieć się więcej o uwierzytelnianiu kontrolka mapy Azure Maps za pomocą usługi Azure AD, zobacz
> [!div class="nextstepaction"]
> [Użyj kontrolka mapy Azure Maps](./how-to-use-map-control.md)