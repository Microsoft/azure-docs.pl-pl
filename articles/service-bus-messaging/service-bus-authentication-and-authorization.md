---
title: Azure Service Bus uwierzytelniania i autoryzacji | Microsoft Docs
description: Uwierzytelnianie aplikacji w Service Bus za pomocą uwierzytelniania sygnatury dostępu współdzielonego (SAS).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ccb526abd99be50e33c8adb918186944b7af3bd6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516658"
---
# <a name="service-bus-authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja w usłudze Service Bus
Istnieją dwa sposoby uwierzytelniania i autoryzacji dostępu do zasobów Azure Service Bus: Azure Activity Directory (Azure AD) i Sygnatury dostępu współdzielonego (SAS). Ten artykuł zawiera szczegółowe informacje na temat korzystania z tych dwóch typów mechanizmów zabezpieczeń. 

## <a name="azure-active-directory"></a>Azure Active Directory
Integracja usługi Azure AD dla Service Bus zapewnia kontrolę dostępu opartą na rolach (RBAC) na platformie Azure w celu szczegółowej kontroli dostępu klienta do zasobów. Za pomocą kontroli dostępu na poziomie ról platformy Azure można przyznać uprawnienia do podmiotu zabezpieczeń, który może być użytkownikiem, grupą lub jednostką usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniany przez usługę Azure AD w celu zwrócenia tokenu OAuth 2.0. Token może służyć do autoryzowania żądania w celu uzyskania dostępu do Service Bus zasobów (kolejki, tematu itd.).

Aby uzyskać więcej informacji na temat uwierzytelniania za pomocą usługi Azure AD, zobacz następujące artykuły:

- [Uwierzytelnianie przy użyciu tożsamości zarządzanych](service-bus-managed-service-identity.md)
- [Uwierzytelnianie przy użyciu aplikacji](authenticate-application.md)

> [!NOTE]
> [Service Bus API REST obsługuje](/rest/api/servicebus/) uwierzytelnianie OAuth za pomocą usługi Azure AD.

> [!IMPORTANT]
> Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2.0 zwracanego przez usługę Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia za pomocą sygnatur dostępu współdzielonego (SAS). W usłudze Azure AD nie ma potrzeby przechowywania tokenów w kodzie ani ryzyka potencjalnych luk w zabezpieczeniach. Zalecamy używanie usługi Azure AD z aplikacjami Azure Service Bus, gdy jest to możliwe. 

## <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego
[Uwierzytelnianie sygnatury](service-bus-sas.md) dostępu współdzielonego umożliwia przyznanie użytkownikowi dostępu do Service Bus zasobów z określonymi prawami. Uwierzytelnianie sygnatury Service Bus polega na konfiguracji klucza kryptograficznego ze skojarzonymi prawami w zasobie Service Bus zasobów. Klienci mogą następnie uzyskać dostęp do tego zasobu, prezentując token SAS, który składa się z uzyskiwanych danych URI zasobów i wygaśnięcia podpisanego przy użyciu skonfigurowanego klucza.

Klucze sygnatury dostępu współdzielonego można skonfigurować w Service Bus nazw. Klucz ma zastosowanie do wszystkich jednostek obsługi komunikatów w tej przestrzeni nazw. Klucze można również skonfigurować w kolejkach Service Bus tematach. Sygnatura dostępu współdzielonego jest również [obsługiwana Azure Relay](../azure-relay/relay-authentication-and-authorization.md).

Aby użyć sygnatury dostępu współdzielonego, można skonfigurować regułę autoryzacji dostępu współdzielonego dla przestrzeni nazw, kolejki lub tematu. Ta reguła składa się z następujących elementów:

* *KeyName*: identyfikuje regułę.
* *PrimaryKey:* klucz kryptograficzny używany do podpisywania/weryfikowania tokenów SAS.
* *SecondaryKey:* klucz kryptograficzny używany do podpisywania/weryfikowania tokenów SAS.
* *Prawa*: reprezentuje kolekcję **udzielonych praw Nasłuchiwać,** **Wyślij** **lub** Zarządzaj.

Reguły autoryzacji skonfigurowane na poziomie przestrzeni nazw mogą udzielać dostępu do wszystkich jednostek w przestrzeni nazw dla klientów z tokenami podpisanymi przy użyciu odpowiedniego klucza. Można skonfigurować maksymalnie 12 takich reguł autoryzacji w Service Bus przestrzeni nazw, kolejki lub tematu. Domyślnie reguła autoryzacji dostępu współdzielona ze wszystkimi prawami jest konfigurowana dla każdej przestrzeni nazw po jej pierwszej aprowizeniu.

Aby uzyskać dostęp do jednostki, klient wymaga tokenu SAS wygenerowanego przy użyciu określonej reguły autoryzacji dostępu współdzielonego. Token sygnatury dostępu współdzielonego jest generowany przy użyciu algorytmu HMAC-SHA256 ciągu zasobu, który składa się z wartości URI zasobu, do której jest deklarowany dostęp, oraz wygaśnięcia z kluczem kryptograficznym skojarzonym z regułą autoryzacji.

Obsługa uwierzytelniania sas dla Service Bus jest zawarta w zestawie Azure .NET SDK w wersji 2.0 i nowszych. Sygnatura dostępu współdzielonego obejmuje obsługę reguły autoryzacji dostępu współdzielonego. Wszystkie interfejsy API, które akceptują parametry połączenia jako parametr, obejmują obsługę parametrów połączenia sygnatury dostępu współdzielonego.

> [!IMPORTANT]
> Jeśli używasz usługi Azure Active Directory Access Control (znanej także jako Access Control Service lub ACS) z usługą Service Bus, pamiętaj, że obsługa [](service-bus-migrate-acs-sas.md) tej metody jest teraz ograniczona i należy przeprowadzić migrację aplikacji w celu użycia sygnatury dostępu współdzielonego lub uwierzytelniania OAuth 2.0 w usłudze Azure AD (zalecane). Aby uzyskać więcej informacji na temat cofania pracy z ACS, zobacz [ten wpis w blogu](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uwierzytelniania za pomocą usługi Azure AD, zobacz następujące artykuły:

- [Uwierzytelnianie za pomocą tożsamości zarządzanych](service-bus-managed-service-identity.md)
- [Uwierzytelnianie z aplikacji](authenticate-application.md)

Aby uzyskać więcej informacji na temat uwierzytelniania za pomocą sygnatury dostępu współdzielonego, zobacz następujące artykuły:

- [Uwierzytelnianie przy użyciu sygnatury dostępu współdzielonego](service-bus-sas.md)
