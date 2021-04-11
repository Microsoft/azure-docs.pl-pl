---
title: Model tożsamości
titleSuffix: An Azure Communication Services concept
description: Informacje o tożsamościach i tokenach dostępu
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5e53f9e89dd9718111da4f8016bc3e5a7f791627
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496579"
---
# <a name="identity-model"></a>Model tożsamości

Usługi komunikacyjne Azure to usługa niezależny od Identity. Ten projekt oferuje wiele korzyści:

- Ponownie używa istniejących tożsamości z systemu zarządzania tożsamościami
- Zapewnia elastyczność scenariuszy integracji
- Utrzymuje tożsamość prywatną w usłudze Azure Communications Services

Zamiast duplikowania informacji w systemie zachowasz relację mapowania wymaganą przez przypadek biznesowy. Na przykład można mapować tożsamości 1:1, 1: N, N:1, N:M. Identyfikatory zewnętrzne, takie jak numery telefonów, użytkownicy, urządzenia, aplikacje i identyfikatory GUID nie mogą być używane do obsługi tożsamości w usłudze Azure Communications Services. Tokeny dostępu wygenerowane dla tożsamości usługi Azure Communications Services są używane w celu uzyskania dostępu do elementów podstawowych, takich jak rozmowa lub wywoływanie.

## <a name="identity"></a>Tożsamość

Tożsamości można tworzyć przy użyciu biblioteki tożsamości usługi Azure Communication Services. Tożsamość służy jako identyfikator w konwersacji. Służy do tworzenia tokenów dostępu. Ta sama tożsamość może uczestniczyć w wielu jednoczesnych sesjach na wielu urządzeniach. Tożsamość może mieć jednocześnie wiele tokenów dostępu.

Usunięcie tożsamości, zasobu lub subskrypcji unieważnia wszystkie tokeny dostępu. Ta akcja spowoduje również usunięcie wszystkich danych przechowywanych dla tożsamości. Usunięta tożsamość nie może tworzyć nowych tokenów dostępu ani uzyskiwać dostępu do wcześniej przechowywanych danych (na przykład komunikatów czatu).

Nie jest naliczana opłata za liczbę posiadanych tożsamości. Zamiast tego opłaty są naliczone za użycie elementów podstawowych. Liczba tożsamości nie musi ograniczać sposobu mapowania tożsamości aplikacji do tożsamości usług Azure Communications Services.

Dzięki swobody mapowania jest odpowiedzialna za prywatność. Jeśli użytkownik chce zostać usunięty z systemu, należy usunąć wszystkie tożsamości, które są skojarzone z tym użytkownikiem.

Usługi komunikacyjne platformy Azure nie udostępniają specjalnych tożsamości użytkownikom anonimowym. Nie zachowuje mapowania między użytkownikami i tożsamościami i nie może określić, czy tożsamość jest anonimowa. Koncepcję tożsamości można zaprojektować zgodnie z potrzebami. Nasze zalecenie polega na utworzeniu nowej tożsamości dla każdego użytkownika anonimowego w każdej aplikacji.

Każdy, kto ma prawidłowy token dostępu, może uzyskać dostęp do bieżącej zawartości tożsamości. Na przykład użytkownicy mogą uzyskiwać dostęp do wysłanych komunikatów rozmowy. Dostęp jest ograniczony tylko do zakresów, które są częścią tokenu dostępu. Aby uzyskać więcej informacji, zobacz sekcję [tokeny dostępu](#access-tokens) w tym artykule.

### <a name="identity-mapping"></a>Mapowanie tożsamości

Usługi komunikacyjne Azure nie replikują funkcji systemu zarządzania tożsamościami platformy Azure. Nie pozwala klientom na korzystanie z tożsamości specyficznych dla klienta. Na przykład klienci nie mogą używać numeru telefonu ani adresu e-mail. Zamiast tego usługi komunikacyjne platformy Azure udostępniają unikatowe identyfikatory. Te unikatowe identyfikatory można przypisać do tożsamości aplikacji. Usługi komunikacyjne Azure nie przechowują żadnych informacji, które mogą ujawnić rzeczywistą tożsamość użytkowników.

Aby uniknąć duplikowania informacji w systemie, Zaplanuj sposób mapowania użytkowników z domeny tożsamości do tożsamości usług Azure Communications Services. Można przestrzegać dowolnego rodzaju wzorca. Można na przykład użyć 1:1, 1: N, N:1 lub M:N. Zdecyduj, czy pojedynczy użytkownik jest mapowany na jedną tożsamość, czy na wiele tożsamości.

Po utworzeniu nowej tożsamości należy przechowywać jej mapowanie do użytkownika lub użytkowników aplikacji. Ponieważ tożsamości wymagają tokenów dostępu do używania elementów podstawowych, tożsamość musi być znana użytkownikowi lub użytkownikom aplikacji.

Jeśli używasz relacyjnej bazy danych do przechowywania informacji o użytkowniku, możesz dostosować projekt na podstawie scenariusza mapowania. W scenariuszach, które mapują 1:1 lub N:1, można dodać `CommunicationServicesId` kolumnę do tabeli w celu przechowywania tożsamości usług Azure Communications Services. W scenariuszach korzystających z relacji 1: N lub N:M można rozważyć utworzenie oddzielnej tabeli w relacyjnej bazie danych.

## <a name="access-tokens"></a>Tokeny dostępu

Token dostępu to token sieci Web JSON (JWT), którego można użyć do uzyskania dostępu do elementów podstawowych usługi komunikacyjnej platformy Azure. Wystawiony token dostępu ma ochronę integralności. Oznacza to, że jego oświadczenia nie mogą być zmieniane po wydaniu. Dlatego ręczna zmiana właściwości, takich jak tożsamość, wygaśnięcie lub zakresy, spowoduje unieważnienie tokenu dostępu. Jeśli elementy podstawowe są używane z niezweryfikowanymi tokenami, dostęp do elementów podstawowych zostanie odrzucony.

Właściwości tokenu dostępu są następujące:
* Identity.
* Datę.
* Zakresy.

Token dostępu jest zawsze ważny przez 24 godziny. Po jego wygaśnięciu token dostępu jest unieważniony i nie można go użyć w celu uzyskania dostępu do żadnego elementu podstawowego.

Tożsamość musi umożliwiać zażądanie nowego tokenu dostępu z usługi po stronie serwera. Parametr *SCOPE* definiuje niepusty zestaw elementów podstawowych, które mogą być używane. Usługi komunikacyjne platformy Azure obsługują następujące zakresy tokenów dostępu.

|Nazwa|Opis|
|---|---|
|Czat|  Umożliwia uczestnictwo w rozmowie|
|VoIP|  Przyznaje możliwość wywoływania tożsamości i numerów telefonów|


Aby odwołać token dostępu przed upływem jego czasu wygaśnięcia, użyj biblioteki tożsamości usług Azure Communication Services. Odwołanie tokenu nie jest natychmiastowe. Propagowanie może potrwać do 15 minut. Usunięcie tożsamości, zasobu lub subskrypcji odwołuje wszystkie tokeny dostępu.

Jeśli chcesz usunąć dostęp użytkownika do określonych funkcji, Odwołaj wszystkie tokeny dostępu. Następnie wydaj nowy token dostępu, który ma bardziej ograniczony zestaw zakresów.

W usłudze Azure Communications Services rotacja kluczy dostępu odwołuje wszystkie aktywne tokeny dostępu, które zostały utworzone przy użyciu poprzedniego klucza dostępu. Wszystkie tożsamości tracą dostęp do usług Azure Communications Services i muszą wydać nowe tokeny dostępu.

Zalecamy wystawianie tokenów dostępu w usłudze po stronie serwera, a nie w aplikacji klienta. Powodem jest to, że wydawanie wymaga klucza dostępu lub tożsamości zarządzanej. Ze względów bezpieczeństwa nie zaleca się udostępniania kluczy dostępu za pomocą aplikacji klienta.

Aplikacja kliencka powinna używać zaufanego punktu końcowego usługi, który może uwierzytelniać klientów. Punkt końcowy powinien wystawiać tokeny dostępu w ich imieniu. Aby uzyskać więcej informacji, zobacz [Architektura klienta i serwera](./client-and-server-architecture.md).

W przypadku buforowania tokenów dostępu do magazynu zapasowego zalecamy korzystanie z szyfrowania. Token dostępu to poufne dane. Może służyć do złośliwego działania, jeśli nie jest chroniony. Osoba, która ma token dostępu, może uruchomić zestaw SDK i uzyskać dostęp do interfejsu API. Dostępny interfejs API jest ograniczony tylko w zależności od zakresów, których dotyczy token dostępu. Zalecamy wystawianie tokenów dostępu, które mają tylko wymagane zakresy.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do zarządzania tokenami, zobacz [Tworzenie tokenów dostępu i zarządzanie nimi](../quickstarts/access-tokens.md).
* Aby zapoznać się z wprowadzeniem do uwierzytelniania, zobacz [uwierzytelnianie w usłudze Azure Communications Services](./authentication.md).
* Aby uzyskać wprowadzenie do danych w miejscu zamieszkania i prywatności, zobacz [dostępność regionów i dane w miejscu zamieszkania](./privacy.md).