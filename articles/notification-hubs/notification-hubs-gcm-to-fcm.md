---
title: Azure Notification Hubs i migracja usługi Google Firebase Cloud Messaging (FCM)
description: Opis sposobu, w jaki usługa Azure Notification Hubs umożliwia GCM migracji do usługi FCM firmy Google.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80127017"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Migracja do usługi Azure Notification Hubs i Google Firebase Cloud Messaging

## <a name="current-state"></a>Bieżący stan

Gdy firma Google ogłosiła migrację z Google Cloud Messaging (GCM) do Firebase Cloud Messaging (FCM), usługi wypychania, takie jak nasza, musiały dostosować sposób wysyłania powiadomień do urządzeń z systemem Android w celu uwzględnienia zmian.

Zaktualizowaliśmy nasze zaplecze usługi, a następnie opublikowane aktualizacje naszego interfejsu API i zestawów SDK zgodnie z wymaganiami. W naszej implementacji podjęto decyzję o utrzymaniu zgodności z istniejącymi schematami powiadomień GCM, aby zminimalizować wpływ klientów. Oznacza to, że obecnie wysyłamy powiadomienia do urządzeń z systemem Android przy użyciu usługi FCM w trybie starszej wersji FCM. Ostatecznie chcemy dodać prawdziwą obsługę FCM, w tym nowe funkcje i format ładunku. Jest to średniookresowa zmiana, a bieżąca migracja jest ukierunkowana na utrzymanie zgodności z istniejącymi aplikacjami i zestawami SDK. Możesz użyć zestawów SDK GCM lub FCM w aplikacji (wraz z naszym zestawem SDK) i upewnić się, że powiadomienie jest prawidłowo wysyłane.

Niektórzy klienci niedawno otrzymali wiadomość e-mail z usługi Google Warning o aplikacjach przy użyciu punktu końcowego GCM na potrzeby powiadomień. Jest to ostrzeżenie, a nic nie zostało naruszone — powiadomienia systemu Android aplikacji są nadal wysyłane do firmy Google w celu ich przetworzenia i nadal będą przetwarzane przez firmę Google. Niektórzy klienci, którzy określili punkt końcowy GCM w swojej konfiguracji usługi, nadal korzystają z przestarzałego punktu końcowego. Ta luka została już zidentyfikowana i działała nad rozwiązaniem problemu podczas wysyłania wiadomości e-mail przez firmę Google.

Zastąpiono przestarzały punkt końcowy i poprawka została wdrożona.

## <a name="going-forward"></a>Przechodzenie do przodu

Często zadawane pytania dotyczące usługi Google FCM — nie trzeba nic robić. W [FCM często zadawane pytania](https://developers.google.com/cloud-messaging/faq)firma Google stwierdziła, że tokeny SDK klienta i GCM będą nadal działały w nieskończoność. Nie będzie jednak można kierować do najnowszej wersji Usługi Google Play w aplikacji systemu Android, chyba że zostanie przeprowadzona migracja do FCM ".

Jeśli aplikacja używa biblioteki GCM, przejdź dalej i postępuj zgodnie z instrukcjami firmy Google, aby przeprowadzić uaktualnienie do biblioteki FCM w aplikacji. Nasz zestaw SDK jest zgodny z programem, dlatego nie trzeba aktualizować niczego w aplikacji po naszej stronie (o ile jest to aktualne przy użyciu naszej wersji zestawu SDK).

## <a name="questions-and-answers"></a>Pytania i odpowiedzi

Poniżej znajdują się odpowiedzi na często zadawane pytania, które zostały wystawione przez klientów:

**P:** Co muszę zrobić, aby była zgodna z datą końcową (aktualna Data odcięcia w firmie Google może być 29 i może ulec zmianie)?

Odp **.:** Wartość. Zachowamy zgodność z istniejącym schematem powiadomień GCM. Klucz GCM będzie nadal działał tak samo jak w przypadku wszystkich GCM zestawów SDK i bibliotek używanych przez aplikację.

Jeśli/po podjęciu decyzji o przeprowadzeniu uaktualnienia do zestawów SDK i bibliotek FCM, aby móc korzystać z nowych funkcji, klucz GCM będzie nadal działał. Jeśli chcesz, możesz przełączyć się na użycie klucza FCM, ale upewnij się, że dodajesz Firebase do istniejącego projektu GCM podczas tworzenia nowego projektu Firebase. Zapewnia to gwarancję zgodności z poprzednimi wersjami klientów korzystających ze starszych wersji aplikacji, które nadal korzystają z zestawów SDK i bibliotek GCM.

Jeśli tworzysz nowy projekt FCM i nie dołączysz do istniejącego projektu GCM, po zaktualizowaniu Notification Hubs przy użyciu nowego klucza tajnego FCM utracisz możliwość wypychania powiadomień do Twoich bieżących instalacji aplikacji, ponieważ nowy klucz FCM nie ma linku do starego projektu GCM.

**P:** Dlaczego otrzymuję tę wiadomość e-mail dotyczącą używanych starych punktów końcowych GCM? Co mam zrobić?

Odp **.:** Wartość. Przeprowadzono migrację do nowych punktów końcowych i zostanie ona zakończona wkrótce, więc nie trzeba zmieniać. Nic nie zostało przerwane, nasz nieodebrany punkt końcowy po prostu spowodował komunikaty ostrzegawcze z usługi Google.

**P:** Jak przejść do nowych zestawów SDK i bibliotek FCM bez przerywania istniejących użytkowników?

Odp.: Uaktualnij w dowolnym momencie. Firma Google nie ogłosiła jeszcze żadnych przestarzałych istniejących zestawów SDK i bibliotek GCM. Aby nie przerywać powiadomień wypychanych do istniejących użytkowników, należy się upewnić, Kiedy utworzysz nowy projekt Firebase, który jest kojarzony z istniejącym projektem GCM. Dzięki temu nowe wpisy tajne Firebase będą działać dla użytkowników korzystających ze starszych wersji aplikacji z zestawami SDK i bibliotekami GCM, a także nowymi użytkownikami aplikacji przy użyciu FCM SDK i bibliotek.

**P:** Kiedy mogę używać nowych funkcji i schematów FCM dla moich powiadomień?

Odp **.:** Po opublikowaniu aktualizacji naszych interfejsów API i zestawów SDK można korzystać z usługi w najbliższych miesiącach.
