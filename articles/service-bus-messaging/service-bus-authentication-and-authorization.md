---
title: Azure Service Bus uwierzytelniania i autoryzacji | Microsoft Docs
description: Uwierzytelniaj aplikacje w celu Service Bus z uwierzytelnianiem sygnatury dostępu współdzielonego (SAS).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a71cef6aad973f3c39ef61a8dbab313ebfca44ef
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517284"
---
# <a name="service-bus-authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja w usłudze Service Bus
Istnieją dwa sposoby uwierzytelniania i autoryzowania dostępu do zasobów Azure Service Bus: katalog aktywności platformy Azure (Azure AD) i sygnatury dostępu współdzielonego (SAS). Ten artykuł zawiera szczegółowe informacje dotyczące korzystania z tych dwóch typów mechanizmów zabezpieczeń. 

## <a name="azure-active-directory"></a>Usługa Azure Active Directory
Integracja z usługą Azure AD dla zasobów Service Bus zapewnia kontrolę dostępu opartą na rolach (Azure RBAC) na potrzeby precyzyjnej kontroli nad dostępem klientów do zasobów. Możesz użyć kontroli RBAC platformy Azure, aby przyznać uprawnienia podmiotowi zabezpieczeń, które może być użytkownikiem, grupą lub jednostką usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniany przez usługę Azure AD w celu zwrócenia tokenu OAuth 2,0. Token może służyć do autoryzowania żądania dostępu do zasobu Service Bus (Kolejka, temat itp.).

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu usługi Azure AD, zobacz następujące artykuły:

- [Uwierzytelnianie przy użyciu tożsamości zarządzanych](service-bus-managed-service-identity.md)
- [Uwierzytelnianie przy użyciu aplikacji](authenticate-application.md)

> [!NOTE]
> [Interfejs API REST Service Bus](/rest/api/servicebus/) obsługuje uwierzytelnianie OAuth za pomocą usługi Azure AD.

> [!IMPORTANT]
> Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2,0 zwróconego przez usługę Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia w odniesieniu do sygnatur dostępu współdzielonego (SAS). W przypadku usługi Azure AD nie ma potrzeby przechowywania tokenów w kodzie i ryzyka potencjalnych luk w zabezpieczeniach. Zalecamy korzystanie z usługi Azure AD z aplikacjami Azure Service Bus, jeśli jest to możliwe. 

## <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego
[Uwierzytelnianie za pomocą sygnatury dostępu współdzielonego](service-bus-sas.md) umożliwia użytkownikom dostęp do Service Bus zasobów przy użyciu określonych praw. Uwierzytelnianie za pomocą sygnatury dostępu współdzielonego w Service Bus obejmuje konfigurację klucza kryptograficznego ze skojarzonymi prawami w ramach zasobu Service Bus. Klienci mogą następnie uzyskać dostęp do tego zasobu przez przedstawienie tokenu sygnatury dostępu współdzielonego, który składa się z identyfikatora URI zasobu, do którego jest uzyskiwany dostęp, i podpisanego kluczem.

Klucze dla sygnatury dostępu współdzielonego można skonfigurować w Service Bus przestrzeni nazw. Klucz ma zastosowanie do wszystkich jednostek obsługi komunikatów w tej przestrzeni nazw. Możesz również skonfigurować klucze na Service Bus kolejkach i tematach. Sygnatura dostępu współdzielonego jest również obsługiwana na [Azure Relay](../azure-relay/relay-authentication-and-authorization.md).

Aby użyć sygnatury dostępu współdzielonego, można skonfigurować obiekt [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) dla przestrzeni nazw, kolejki lub tematu. Ta reguła składa się z następujących elementów:

* *NazwaKlucza*: identyfikuje regułę.
* *PrimaryKey*: klucz kryptograficzny używany do podpisywania/weryfikowania tokenów SAS.
* *SecondaryKey*: klucz kryptograficzny używany do podpisywania/weryfikowania tokenów SAS.
* *Prawa*: reprezentuje kolekcję praw **nasłuchiwania**, **wysyłania**lub **zarządzania** .

Reguły autoryzacji skonfigurowane na poziomie przestrzeni nazw mogą przyznawać dostęp do wszystkich jednostek w przestrzeni nazw klientom z tokenami podpisanymi przy użyciu odpowiedniego klucza. Można skonfigurować do 12 takich reguł autoryzacji na Service Bus przestrzeni nazw, kolejki lub tematu. Domyślnie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) z wszelkimi prawami jest konfigurowana dla każdej przestrzeni nazw, gdy jest ona najpierw inicjowana.

Aby uzyskać dostęp do jednostki, klient wymaga tokenu sygnatury dostępu współdzielonego wygenerowanego przy użyciu określonego [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token sygnatury dostępu współdzielonego jest generowany przy użyciu HMAC-SHA256 ciągu zasobu, który składa się z identyfikatora URI zasobu, do którego odnosi się dostęp, oraz wygaśnięcia klucza kryptograficznego skojarzonego z regułą autoryzacji.

Obsługa uwierzytelniania SAS dla Service Bus jest zawarta w zestawie SDK platformy Azure w wersji 2,0 lub nowszej. Sygnatura dostępu współdzielonego obejmuje obsługę [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Wszystkie interfejsy API, które akceptują parametry połączenia jako parametr obejmują obsługę ciągów połączeń sygnatury dostępu współdzielonego.

> [!IMPORTANT]
> W przypadku korzystania z programu Azure Active Directory Access Control (znanego również jako Access Control Service lub ACS) z Service Bus należy zauważyć, że obsługa tej metody jest teraz ograniczona i należy [przeprowadzić migrację aplikacji do korzystania z SAS](service-bus-migrate-acs-sas.md) lub uwierzytelniania OAuth 2,0 z usługą Azure AD (zalecane). Aby uzyskać więcej informacji o zaniechaniu usługi ACS, zobacz [ten wpis w blogu](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu usługi Azure AD, zobacz następujące artykuły:

- [Uwierzytelnianie za pomocą tożsamości zarządzanych](service-bus-managed-service-identity.md)
- [Uwierzytelnianie z poziomu aplikacji](authenticate-application.md)

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu sygnatury dostępu współdzielonego, zobacz następujące artykuły:

- [Uwierzytelnianie za pomocą SAS](service-bus-sas.md)