---
title: Azure Relay uwierzytelniania i autoryzacji | Microsoft Docs
description: Ten artykuł zawiera omówienie uwierzytelniania sygnatury dostępu współdzielonego (SAS) w usłudze Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4b0a5c7a092155a006419eedd170a63abed42bb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87033381"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay uwierzytelnianie i autoryzacja

Aplikacje mogą uwierzytelniać się w celu Azure Relay przy użyciu uwierzytelniania sygnatury dostępu współdzielonego (SAS). Uwierzytelnianie SAS umożliwia aplikacjom uwierzytelnianie w usłudze Azure Relay przy użyciu klucza dostępu skonfigurowanego w przestrzeni nazw usługi Relay. Następnie można użyć tego klucza do wygenerowania tokenu sygnatury dostępu współdzielonego, którego klienci mogą używać do uwierzytelniania w usłudze przekazywania.

## <a name="shared-access-signature-authentication"></a>Uwierzytelnianie sygnatury dostępu współdzielonego

[Uwierzytelnianie za pomocą sygnatury dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md) umożliwia użytkownikowi dostęp do Azure Relay zasobów przy użyciu określonych praw. Uwierzytelnianie za pomocą sygnatury dostępu współdzielonego obejmuje konfigurację klucza kryptograficznego ze skojarzonymi prawami do zasobu. Klienci mogą następnie uzyskać dostęp do tego zasobu przez przedstawienie tokenu sygnatury dostępu współdzielonego, który składa się z identyfikatora URI zasobu, do którego jest uzyskiwany dostęp, i podpisanego kluczem.

Klucze dla sygnatury dostępu współdzielonego można skonfigurować w przestrzeni nazw usługi Relay. W przeciwieństwie do Service Bus Messaging [połączenia hybrydowe Relay](relay-hybrid-connections-protocol.md) obsługuje nadawcy nieautoryzowanych lub anonimowych. Można włączyć dostęp anonimowy dla jednostki podczas tworzenia, jak pokazano na poniższym zrzucie ekranu z portalu:

![Okno dialogowe zatytułowane "Utwórz połączenie hybrydowe" ma pole tekstowe "name" i pole wyboru z etykietą "wymaga uwierzytelniania klienta", które jest zaznaczone.][0]

Aby użyć sygnatury dostępu współdzielonego, można skonfigurować obiekt [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) w przestrzeni nazw przekaźnika, która składa się z następujących elementów:

* *NazwaKlucza* , która identyfikuje regułę.
* *PrimaryKey* to klucz kryptograficzny używany do podpisywania/weryfikowania tokenów SAS.
* *SecondaryKey* to klucz kryptograficzny używany do podpisywania/weryfikowania tokenów SAS.
* *Prawa* reprezentujące kolekcję przyznanych praw nasłuchujących, wysyłania lub zarządzania.

Reguły autoryzacji skonfigurowane na poziomie przestrzeni nazw mogą udzielić dostępu do wszystkich połączeń przekaźnika w przestrzeni nazw dla klientów z tokenami podpisanymi przy użyciu odpowiedniego klucza. Do 12 takich reguł autoryzacji można skonfigurować w przestrzeni nazw przekaźnika. Domyślnie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) z wszelkimi prawami jest konfigurowana dla każdej przestrzeni nazw, gdy jest ona najpierw inicjowana.

Aby uzyskać dostęp do jednostki, klient wymaga tokenu sygnatury dostępu współdzielonego wygenerowanego przy użyciu określonego [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token sygnatury dostępu współdzielonego jest generowany przy użyciu HMAC-SHA256 ciągu zasobu, który składa się z identyfikatora URI zasobu, do którego odnosi się dostęp, oraz wygaśnięcia klucza kryptograficznego skojarzonego z regułą autoryzacji.

Obsługa uwierzytelniania SAS dla Azure Relay jest zawarta w zestawie SDK platformy Azure w wersji 2,0 lub nowszej. Sygnatura dostępu współdzielonego obejmuje obsługę [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Wszystkie interfejsy API, które akceptują parametry połączenia jako parametr obejmują obsługę ciągów połączeń sygnatury dostępu współdzielonego.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat SYGNATURy [dostępu współdzielonego, Kontynuuj odczytywanie Service Busgo uwierzytelniania za pomocą sygnatur dostęp](../service-bus-messaging/service-bus-sas.md)
- Szczegółowe informacje na temat możliwości Połączenia hybrydowe można znaleźć w [przewodniku po protokole Połączenia hybrydowe Azure Relay](relay-hybrid-connections-protocol.md) .
- Aby uzyskać odpowiednie informacje dotyczące Service Bus uwierzytelniania i autoryzacji komunikatów, zobacz [Service Bus uwierzytelnianie i autoryzacja](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png