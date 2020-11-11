---
title: Model tożsamości
titleSuffix: An Azure Communication Services concept
description: Informacje o tożsamościach i tokenach dostępu
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 336e708334778e107331f5f393476c4b3dbb98a3
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507487"
---
# <a name="identity-model"></a>Model tożsamości

Usługi komunikacyjne Azure to usługa Identity niezależny od. Ten projekt ma wiele zalet:
- Ponowne używanie istniejących tożsamości z systemu zarządzania tożsamościami
- Elastyczność scenariuszy integracji
- Utrzymuje tożsamość prywatną w usłudze Azure Communications Services

Zamiast duplikowania istniejących informacji w systemie zachowasz relację mapowania, która jest specyficzna dla Twojej sprawy biznesowej. Na przykład mapowanie tożsamości 1:1, 1: N, N:1, N:M. Identyfikatory zewnętrzne (na przykład numery telefonów, użytkowników, urządzeń, aplikacji i GUID) nie mogą być używane jako tożsamość komunikacji Azure. Tokeny dostępu wygenerowane dla tożsamości usługi komunikacyjnej platformy Azure są używane do uzyskiwania dostępu do elementów podstawowych, takich jak rozmowa lub wywoływanie. 

## <a name="identity"></a>Tożsamość

Tożsamości są tworzone przy użyciu biblioteki administracyjnej usługi Azure Communication Service. Tożsamość służy jako identyfikator w konwersacjach i służy do tworzenia tokenów dostępu. Ta sama tożsamość może uczestniczyć w wielu jednoczesnych sesjach na wielu urządzeniach. Tożsamość może mieć jednocześnie wiele tokenów dostępu. Usunięcie tożsamości, zasobu lub subskrypcji spowoduje unieważnienie wszystkich tokenów dostępu i usunięcie wszystkich danych przechowywanych dla tej tożsamości. Usunięta tożsamość nie może wydać nowych tokenów dostępu ani uzyskać dostępu do danych wcześniej przechowywanych (na przykład komunikatów czatu). 

Opłata nie jest naliczana według liczby posiadanych tożsamości, ale przez użycie elementów podstawowych. Liczba tożsamości nie musi ograniczać się do mapowania tożsamości aplikacji na tożsamości usług Azure Communications Services. Ze względu na to, że mapowanie jest odpowiedzialne za ochronę prywatności. Jeśli użytkownik aplikacji ma zostać usunięty z systemu, należy usunąć wszystkie tożsamości, które zostały skojarzone z tym użytkownikiem.

Usługi komunikacyjne platformy Azure nie udostępniają specjalnych tożsamości użytkownikom anonimowym. Nie przechowuje mapowania między użytkownikami i tożsamościami, ale nie ma informacji o tym, czy tożsamość jest anonimowa. Koncepcję można zaprojektować zgodnie z wymaganiami. Nasze zalecenie polega na utworzeniu nowej tożsamości dla anonimowego użytkownika aplikacji. W przypadku posiadania prawidłowego tokenu dostępu każdy może uzyskać dostęp do nieusuniętej zawartości tożsamości. Na przykład wiadomości czatu wysyłane przez użytkownika. Dostęp jest ograniczony tylko do zakresów, które są częścią tokenu dostępu. Więcej szczegółów na temat zakresów znajdują się w sekcji *token dostępu*.

### <a name="mapping-of-identities"></a>Mapowanie tożsamości

Usługi komunikacyjne platformy Azure nie są replikowane w ramach funkcji IMS. Nie pozwala klientom na korzystanie z tożsamości specyficznych dla klienta. Na przykład numer telefonu lub adres e-mail. Zamiast tego udostępnia unikatowe identyfikatory, które można przypisać do tożsamości aplikacji. Usługi komunikacyjne platformy Azure nie przechowują żadnych informacji, co może ujawnić rzeczywistą tożsamość Twoich użytkowników.

Zamiast duplikowania zaleca się projektowanie, jak użytkownicy z domeny tożsamości zostaną zamapowane na tożsamości usługi Azure Communications Service. Można przestrzegać dowolnego rodzaju wzorca 1:1, 1: N, N:1 lub M:N. Można zdecydować, czy pojedynczy użytkownik jest mapowany na jedną tożsamość, czy na wiele tożsamości. Po utworzeniu nowej tożsamości zaleca się przechowywanie mapowania tej tożsamości dla użytkowników lub użytkowników aplikacji. Ponieważ tożsamości wymagają tokenów dostępu w celu użycia elementów pierwotnych, tożsamość musi być znana użytkownikowi lub użytkownikom aplikacji.

Jeśli używasz relacyjnej bazy danych do przechowywania użytkowników, implementacja może się różnić w zależności od scenariusza mapowania. W scenariuszach z mapowaniem 1:1 lub N:1 można dodać do tabeli kolumnę *CommunicationServicesId* w celu przechowywania tożsamości usług Azure Communications Services. W scenariuszach z relacją 1: N lub N:M warto rozważyć utworzenie oddzielnej tabeli w relacyjnej bazie danych.

## <a name="access-token"></a>Token dostępu

Token dostępu jest tokenem JWT, który może służyć do uzyskiwania dostępu do elementów podstawowych usługi Azure Communications Service. Wystawiony token dostępu ma ochronę integralności, nie można zmienić jego oświadczeń po wydaniu. Oznacza to, że ręczna zmiana właściwości, takich jak tożsamość, wygaśnięcie lub zakresy, spowoduje, że token dostępu jest nieprawidłowy. Użycie elementów podstawowych z niezweryfikowanymi tokenami spowoduje odmowę dostępu do elementu podstawowego. 

Właściwości tokenu dostępu to: *tożsamość, wygaśnięcie* i *zakresy*. Token dostępu jest zawsze ważny przez 24 godziny. Po upływie tego czasu token dostępu jest unieważniony i nie można go użyć w celu uzyskania dostępu do żadnego elementu podstawowego. Tożsamość musi mieć sposób, aby zażądać nowego tokenu dostępu z usługi po stronie serwera. *Zakres* parametru definiuje niepusty zestaw elementów podstawowych, który może być używany. Usługi komunikacyjne platformy Azure obsługują następujące zakresy tokenów dostępu:

|Nazwa|Opis|
|---|---|
|Czat|  Umożliwia uczestnictwo w rozmowie|
|VoIP|  Przyznaje możliwość wywoływania tożsamości i numerów telefonów|


Jeśli chcesz odwołać token dostępu przed jego wygaśnięciem, możesz użyć biblioteki administracyjnej usługi Azure Communications Service. Odwołanie tokenu nie jest natychmiastowe i może potrwać do 15 minut. Usunięcie tożsamości, zasobu lub subskrypcji spowoduje unieważnienie wszystkich tokenów dostępu. Jeśli chcesz usunąć dostęp użytkownika do określonych funkcji, Odwołaj wszystkie tokeny dostępu. Następnie wydaj nowy token dostępu z bardziej ograniczonym zestawem zakresów.
Rotacja kluczy dostępu usługi Azure Communication Service spowoduje odwołanie wszystkich aktywnych tokenów dostępu utworzonych przy użyciu poprzedniego klucza dostępu. Wszystkie tożsamości utracą dostęp do usługi komunikacyjnej platformy Azure i są wymagane do wystawienia nowych tokenów dostępu. 

Zalecamy wystawianie tokenów dostępu w usłudze po stronie serwera, a nie w aplikacji klienta. Przyczyną tego jest fakt, że wydawanie wymaga klucza dostępu lub tożsamości zarządzanej. Nie jest to zalecane ze względów bezpieczeństwa, aby udostępnić klucze dostępu aplikacji klienta. Aplikacja kliencka powinna używać zaufanego punktu końcowego usługi, który może uwierzytelniać klientów, i wystawiać token dostępu w ich imieniu. Więcej szczegółowych informacji o architekturze można znaleźć [tutaj](./client-and-server-architecture.md).

W przypadku buforowania tokenów dostępu do magazynu zapasowego zalecamy korzystanie z szyfrowania. Token dostępu jest poufnymi danymi i może być używany do złośliwego działania, jeśli nie jest chroniony. W przypadku posiadania tokenu dostępu można zainicjować zestaw SDK i uzyskać dostęp do interfejsu API. Dostępny interfejs API jest ograniczony tylko w oparciu o zakresy, które ma token dostępu. Zalecamy wydawanie tokenów dostępu tylko z zakresami, które są wymagane.