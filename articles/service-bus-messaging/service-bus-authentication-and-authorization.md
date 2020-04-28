---
title: Azure Service Bus uwierzytelniania i autoryzacji | Microsoft Docs
description: Uwierzytelniaj aplikacje w celu Service Bus z uwierzytelnianiem sygnatury dostępu współdzielonego (SAS).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70013244"
---
# <a name="service-bus-authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja w usłudze Service Bus

Aplikacje uzyskują dostęp do zasobów Azure Service Bus przy użyciu uwierzytelniania za pomocą tokenu sygnatury dostępu współdzielonego (SAS). Za pomocą sygnatury dostępu współdzielonego aplikacje składają Token Service Bus, który został podpisany przy użyciu klucza symetrycznego znanego zarówno do wystawcy tokenów, jak i do Service Bus (w związku z czym ten klucz jest bezpośrednio skojarzony z regułą udzielania określonych praw dostępu, takich jak uprawnienie do odbierania/nasłuchiwania lub wysyłania wiadomości). Reguły SAS są konfigurowane w przestrzeni nazw lub bezpośrednio na jednostkach, takich jak Kolejka lub temat, co pozwala na precyzyjne sterowanie dostępem.

Tokeny sygnatury dostępu współdzielonego mogą być generowane bezpośrednio przez klienta Service Bus lub mogą być wygenerowane przez pewien punkt końcowy wystawiający token pośredni, z którym klient działa. Na przykład system może wymagać, aby klient wywoływał punkt końcowy usługi sieci Web chronionej autoryzacją Active Directory, aby potwierdzić jego tożsamość i prawa dostępu do systemu, a następnie usługa sieci Web zwróci odpowiedni Token Service Bus. Token sygnatury dostępu współdzielonego można łatwo wygenerować przy użyciu dostawcy tokenów Service Bus zawartych w zestawie Azure SDK. 

> [!IMPORTANT]
> W przypadku korzystania z programu Azure Active Directory Access Control (znanego również jako Access Control Service lub ACS) z Service Bus należy zauważyć, że obsługa tej metody jest teraz ograniczona i należy przeprowadzić migrację aplikacji w celu użycia sygnatury dostępu współdzielonego. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) i [ten artykuł](service-bus-migrate-acs-sas.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Integracja Azure Active Directory (Azure AD) dla zasobów Service Bus zapewnia kontrolę dostępu opartą na rolach (RBAC) na potrzeby precyzyjnej kontroli dostępu klienta do zasobów. Za pomocą kontroli dostępu opartej na rolach (RBAC) można przyznać uprawnienia podmiotowi zabezpieczeń, które mogą być użytkownikami, grupami lub podmiotem usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniany przez usługę Azure AD w celu zwrócenia tokenu OAuth 2,0. Token może służyć do autoryzowania żądania dostępu do zasobu Service Bus (Kolejka, temat itp.).

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu usługi Azure AD, zobacz następujące artykuły:

- [Uwierzytelnianie przy użyciu tożsamości zarządzanych](service-bus-managed-service-identity.md)
- [Uwierzytelnianie przy użyciu aplikacji](authenticate-application.md)

> [!IMPORTANT]
> Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2,0 zwróconego przez usługę Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia w odniesieniu do sygnatur dostępu współdzielonego (SAS). W przypadku usługi Azure AD nie ma potrzeby przechowywania tokenów w kodzie i ryzyka potencjalnych luk w zabezpieczeniach. Zalecamy korzystanie z usługi Azure AD z aplikacjami Azure Service Bus, jeśli jest to możliwe. 


## <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego
[Uwierzytelnianie za pomocą sygnatury dostępu współdzielonego](service-bus-sas.md) umożliwia użytkownikom dostęp do Service Bus zasobów przy użyciu określonych praw. Uwierzytelnianie za pomocą sygnatury dostępu współdzielonego w Service Bus obejmuje konfigurację klucza kryptograficznego ze skojarzonymi prawami w ramach zasobu Service Bus. Klienci mogą następnie uzyskać dostęp do tego zasobu przez przedstawienie tokenu sygnatury dostępu współdzielonego, który składa się z identyfikatora URI zasobu, do którego jest uzyskiwany dostęp, i podpisanego kluczem.

Klucze dla sygnatury dostępu współdzielonego można skonfigurować w Service Bus przestrzeni nazw. Klucz ma zastosowanie do wszystkich jednostek obsługi komunikatów w tej przestrzeni nazw. Możesz również skonfigurować klucze na Service Bus kolejkach i tematach. Sygnatura dostępu współdzielonego jest również obsługiwana na [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Aby użyć sygnatury dostępu współdzielonego, można skonfigurować obiekt [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) dla przestrzeni nazw, kolejki lub tematu. Ta reguła składa się z następujących elementów:

* *NazwaKlucza*: identyfikuje regułę.
* *PrimaryKey*: klucz kryptograficzny używany do podpisywania/weryfikowania tokenów SAS.
* *SecondaryKey*: klucz kryptograficzny używany do podpisywania/weryfikowania tokenów SAS.
* *Prawa*: reprezentuje kolekcję praw **nasłuchiwania**, **wysyłania**lub **zarządzania** .

Reguły autoryzacji skonfigurowane na poziomie przestrzeni nazw mogą przyznawać dostęp do wszystkich jednostek w przestrzeni nazw klientom z tokenami podpisanymi przy użyciu odpowiedniego klucza. Można skonfigurować do 12 takich reguł autoryzacji na Service Bus przestrzeni nazw, kolejki lub tematu. Domyślnie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) z wszelkimi prawami jest konfigurowana dla każdej przestrzeni nazw, gdy jest ona najpierw inicjowana.

Aby uzyskać dostęp do jednostki, klient wymaga tokenu sygnatury dostępu współdzielonego wygenerowanego przy użyciu określonego [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token sygnatury dostępu współdzielonego jest generowany przy użyciu HMAC-SHA256 ciągu zasobu, który składa się z identyfikatora URI zasobu, do którego odnosi się dostęp, oraz wygaśnięcia klucza kryptograficznego skojarzonego z regułą autoryzacji.

Obsługa uwierzytelniania SAS dla Service Bus jest zawarta w zestawie SDK platformy Azure w wersji 2,0 lub nowszej. Sygnatura dostępu współdzielonego obejmuje obsługę [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Wszystkie interfejsy API, które akceptują parametry połączenia jako parametr obejmują obsługę ciągów połączeń sygnatury dostępu współdzielonego.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat SYGNATURy [dostępu współdzielonego, Kontynuuj odczytywanie Service Busgo uwierzytelniania za pomocą sygnatur dostęp](service-bus-sas.md)
- Jak [przeprowadzić migrację z usługi Azure Active Directory Access Control (ACS) do autoryzacji sygnatury dostępu współdzielonego](service-bus-migrate-acs-sas.md).
- [Zmiany w przestrzeniach nazw włączonych przez usługę ACS](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Aby uzyskać odpowiednie informacje na temat uwierzytelniania i autoryzacji Azure Relay, zobacz [Azure Relay uwierzytelnianie i autoryzacja](../service-bus-relay/relay-authentication-and-authorization.md). 

