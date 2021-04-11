---
title: Diagnozuj opuszczone powiadomienia w usłudze Azure Notification Hubs
description: Dowiedz się, jak zdiagnozować typowe problemy z porzuconymi powiadomieniami w usłudze Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 787cf922fcee0ee613fc0874a490830da9adf38a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455029"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Diagnozuj opuszczone powiadomienia w usłudze Azure Notification Hubs

Często zadawane pytania dotyczące usługi Azure Notification Hubs to sposób rozwiązywania problemów, gdy powiadomienia z aplikacji nie są wyświetlane na urządzeniach klienckich. Klienci chcą wiedzieć, gdzie i dlaczego powiadomienia zostały porzucone, i jak rozwiązać problem. W tym artykule opisano, dlaczego powiadomienia mogą zostać porzucone lub nie odebrane przez urządzenia. Wyjaśniono również, jak ustalić główną przyczynę.

Ważne jest, aby najpierw zrozumieć, jak Notification Hubs wypychania powiadomień do urządzenia.

![Architektura Notification Hubs][0]

W typowym przepływie powiadomień wysyła komunikat z *zaplecza aplikacji* do Notification Hubs. Notification Hubs przetwarza wszystkie rejestracje. Uwzględnia on skonfigurowane Tagi i wyrażenia tagów, aby określić cele. Cele są rejestracjami, które muszą odebrać Powiadomienie wypychane. Te rejestracje mogą obejmować dowolne z naszych obsługiwanych platform: Android, Baidu (urządzenia z systemem Android w Chinach), system pożaru (Amazon) iOS, Windows i Windows Phone.

Mając ustanowione cele, Notification Hubs wypychanie powiadomień do *usługi powiadomień wypychanych* dla platformy urządzeń. Przykładami mogą być usługi Apple Push Notification Service (APNs) dla systemów iOS i macOS oraz Firebase Cloud Messaging (FCM) dla urządzeń z systemem Android. Notification Hubs wypychania powiadomień w wielu partiach rejestracji. Jest on uwierzytelniany przy użyciu odpowiedniej usługi powiadomień wypychanych na podstawie poświadczeń ustawionych w Azure Portal, w obszarze **Konfigurowanie centrum powiadomień**. Następnie usługa powiadomień wypychanych przekazuje powiadomienia do odpowiednich *urządzeń klienckich*.

Końcowy etap dostarczania powiadomień jest między usługą powiadomień wypychanych platformy a urządzeniem. Dostarczanie powiadomień może zakończyć się niepowodzeniem na dowolnym z czterech etapów procesu powiadomień wypychanych (klienta, zaplecza aplikacji, Notification Hubs i usługi powiadomień wypychanych platformy). Aby uzyskać więcej informacji na temat architektury Notification Hubs, zobacz [Notification Hubs Omówienie].

Podczas wstępnej fazy testowania/przemieszczania nie można dostarczyć powiadomień. Opuszczone powiadomienia na tym etapie mogą wskazywać na problem z konfiguracją. Jeśli błąd dostarczania powiadomień w środowisku produkcyjnym, niektóre lub wszystkie powiadomienia mogą zostać porzucone. W tym przypadku jest wskazany bardziej szczegółowy problem z wzorcem aplikacji lub komunikatów.

Następna sekcja analizuje scenariusze, w których można porzucić powiadomienia, od typowych do rzadkich.

## <a name="notification-hubs-misconfiguration"></a>NieNotification Hubsa konfiguracja

Aby wysyłać powiadomienia do odpowiedniej usługi powiadomień wypychanych, Notification Hubs musi uwierzytelnić się w kontekście aplikacji. Musisz utworzyć konto dewelopera przy użyciu usługi powiadamiania platformy docelowej (Microsoft, Apple, Google itp.). Następnie musisz zarejestrować swoją aplikację w systemie operacyjnym, w którym uzyskujesz token lub klucz używany do pracy z docelowym PNS.

Musisz dodać poświadczenia platformy do Azure Portal. Jeśli żadne powiadomienia nie docierają do urządzenia, pierwszym krokiem jest upewnienie się, że w Notification Hubs są skonfigurowane poprawne poświadczenia. Poświadczenia muszą być zgodne z aplikacją utworzoną w ramach konta dewelopera określonego dla platformy.

Instrukcje krok po kroku dotyczące wykonywania tego procesu znajdują się w temacie [Rozpoczynanie pracy z usługą Azure Notification Hubs].

Poniżej przedstawiono kilka typowych niespotykanych konfiguracji do sprawdzenia:

### <a name="notification-hub-name-location"></a>Lokalizacja nazwy centrum powiadomień

Upewnij się, że nazwa centrum powiadomień (bez literówek) jest taka sama w każdej z następujących lokalizacji:

* Miejsce rejestracji z poziomu klienta
* Miejsce wysyłania powiadomień z zaplecza
* Miejsce, w którym skonfigurowano poświadczenia usługi powiadomień wypychanych

Upewnij się, że używasz prawidłowych ciągów konfiguracji sygnatury dostępu współdzielonego na kliencie i zapleczu aplikacji. Ogólnie rzecz biorąc, należy użyć **DefaultListenSharedAccessSignature** na kliencie i **DefaultFullSharedAccessSignature** na zapleczu aplikacji. To przyznaje uprawnienia do wysyłania powiadomień do Notification Hubs.

### <a name="apn-configuration"></a>Konfiguracja APN

Należy zachować dwa różne centra: jeden do produkcji i drugi do testowania. Certyfikat używany w środowisku piaskownicy należy przekazać do oddzielnego centrum niż certyfikat/koncentrator, który będzie używany w produkcji. Nie próbuj przekazywać różnych typów certyfikatów do tego samego centrum. Spowoduje to niepowodzenie powiadomień.

Jeśli przypadkowo przekazano różne typy certyfikatów do tego samego centrum, należy usunąć centrum i zacząć od nowa centrum. Jeśli z jakiegoś powodu nie można usunąć centrum, należy usunąć wszystkie istniejące rejestracje z centrum.

### <a name="fcm-configuration"></a>Konfiguracja FCM

1. Upewnij się, że *klucz serwera* uzyskany z Firebase jest zgodny z kluczem serwera zarejestrowanego w Azure Portal.

   ![Klucz serwera Firebase][3]

2. Upewnij się, że na kliencie został skonfigurowany **Identyfikator projektu** . Wartość **identyfikatora projektu** można uzyskać z pulpitu nawigacyjnego Firebase.

   ![Identyfikator projektu Firebase][1]

## <a name="application-issues"></a>Problemy z aplikacją

### <a name="tags-and-tag-expressions"></a>Tagi i wyrażenia tagów

Jeśli używasz tagów lub wyrażeń tagów do segmentacji odbiorców, istnieje możliwość, że po wysłaniu powiadomienia nie zostanie znaleziony żaden element docelowy. Ten błąd jest oparty na określonych znacznikach lub wyrażeniach tagów w wywołaniu wysyłania.

Przejrzyj rejestracje, aby upewnić się, że Tagi są zgodne podczas wysyłania powiadomienia. Następnie sprawdź, czy odebrano powiadomienie tylko od klientów, którzy mają te rejestracje.

Załóżmy na przykład, że wszystkie rejestracje z Notification Hubs korzystają ze znacznika "Polityka". Jeśli następnie wyślesz powiadomienie ze znacznikiem "Sport", powiadomienie nie zostanie wysłane do żadnego urządzenia. Złożone przypadki mogą zawierać wyrażenia tagów, w których zarejestrowano przy użyciu "tag A" *lub* "tag B", ale wskazywane jest "tag && tagu b". Sekcja porady samodiagnostyki w dalszej części artykułu przedstawia sposób przeglądania rejestracji i ich tagów.

### <a name="template-issues"></a>Problemy związane z szablonami

W przypadku używania szablonów należy postępować zgodnie z wytycznymi opisanymi w [szablonach].

### <a name="invalid-registrations"></a>Nieprawidłowe rejestracje

Jeśli centrum powiadomień zostało prawidłowo skonfigurowane i Tagi lub wyrażenia tagów zostały prawidłowo użyte, znaleziono prawidłowe elementy docelowe. Powiadomienia powinny być wysyłane do tych celów. Notification Hubs następnie wygeneruje kilka partii przetwarzania równolegle. Każda partia wysyła komunikaty do zestawu rejestracji.

> [!NOTE]
> Ponieważ Notification Hubs przetwarza partii równolegle, kolejność dostarczania powiadomień nie jest gwarantowana.

Notification Hubs jest zoptymalizowany pod kątem "co najwyżej jednego" modelu dostarczania komunikatów. Podjęto próbę przeprowadzenia deduplikacji, aby żadne powiadomienia nie były dostarczane do urządzenia więcej niż raz. Rejestracje są sprawdzane w celu zapewnienia, że tylko jeden komunikat jest wysyłany na identyfikator urządzenia przed wysłaniem go do usługi powiadomień wypychanych.

Każda partia jest wysyłana do usługi powiadomień wypychanych, która z kolei akceptuje i weryfikuje rejestracje. W trakcie tego procesu usługa powiadomień wypychanych może wykryć błąd z co najmniej jedną rejestracją w partii. Usługa powiadomień wypychanych zwraca błąd do Notification Hubs i proces zostaje zatrzymany. Usługa powiadomień wypychanych opuszcza tę partię całkowicie. Jest to szczególnie prawdziwe w przypadku usługi APNs, która używa protokołu strumienia TCP.

W takim przypadku Rejestracja błędów zostanie usunięta z bazy danych. Następnie ponów próbę dostarczenia powiadomienia dla pozostałych urządzeń w tej partii.

Aby uzyskać więcej informacji o błędach dotyczących nieudanej próby dostarczenia na rejestrację, można użyć Notification Hubs interfejsów API REST [na potrzeby telemetrii komunikatów: Pobierz dane telemetryczne komunikatów powiadomień](/rest/api/notificationhubs/get-notification-message-telemetry) i [PNS opinię](/previous-versions/azure/reference/mt705560(v=azure.100)). Aby uzyskać przykładowy kod, zobacz [przykład wysyłania w dalszej części](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/).

## <a name="push-notification-service-issues"></a>Problemy z usługą powiadomień wypychanych

Gdy usługa powiadomień wypychanych otrzyma powiadomienie, udostępnia powiadomienie na urządzeniu. W tym momencie Notification Hubs nie ma kontroli nad dostarczeniem powiadomienia na urządzeniu.

Ponieważ usługi powiadomień platformy są niezawodne, powiadomienia mogą dotrzeć do urządzeń w ciągu kilku sekund. Jeśli usługa powiadomień wypychanych ma ograniczenia, Notification Hubs stosuje strategię wycofywania wykładniczego. Jeśli usługa powiadomień wypychanych pozostaje nieosiągalna przez 30 minut, istnieją zasady, które mają na celu wygaśnięcie i porzucenie komunikatów.

Jeśli usługa powiadomień wypychanych próbuje dostarczyć powiadomienie, ale urządzenie jest w trybie offline, powiadomienie jest przechowywane przez usługę powiadomień wypychanych. Jest on przechowywany przez ograniczony czas. Powiadomienie jest dostarczane do urządzenia, gdy urządzenie będzie dostępne.

Każda aplikacja przechowuje tylko jedno ostatnie powiadomienie. W przypadku wysyłania wielu powiadomień, gdy urządzenie jest w trybie offline, każde nowe powiadomienie spowoduje odrzucenie ostatniego z nich. Tylko najnowsze powiadomienia są nazywane *łączeniami* w usłudze APNs i *zwijane* w FCM. (FCM używa klucza zwijanego). Gdy urządzenie pozostanie w trybie offline przez dłuższy czas, powiadomienia, które były przechowywane na urządzeniu, są odrzucane. Aby uzyskać więcej informacji, zobacz [Omówienie usługi APNs] i [Informacje o komunikatach FCM].

Za pomocą Notification Hubs można przekazać klucz łączenia za pośrednictwem nagłówka HTTP przy użyciu ogólnego interfejsu API SendNotification. Na przykład dla zestawu .NET SDK można użyć `SendNotificationAsync` . Interfejs API SendNotification pobiera również nagłówki HTTP, które są przekazywane w postaci, do odpowiedniej usługi powiadomień wypychanych.

## <a name="self-diagnosis-tips"></a>Porady dotyczące samodiagnostyki

Poniżej przedstawiono ścieżki umożliwiające zdiagnozowanie głównej przyczyny porzucenia powiadomień w Notification Hubs.

### <a name="verify-credentials"></a>Weryfikowanie poświadczeń

#### <a name="push-notification-service-developer-portal"></a>Portal dla deweloperów usługi powiadomień wypychanych

Sprawdź poświadczenia w odpowiednim portalu dla deweloperów usługi powiadomień wypychanych (APNs, FCM, usługa powiadomień systemu Windows itd.). Aby uzyskać więcej informacji, zobacz [Samouczek: wysyłanie powiadomień do platforma uniwersalna systemu Windows aplikacji przy użyciu usługi Azure Notification Hubs](./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

#### <a name="azure-portal"></a>Azure Portal

Aby przejrzeć i dopasować poświadczenia do tych, które zostały uzyskane z portalu dla deweloperów usługi powiadomień wypychanych, przejdź do karty **zasady dostępu** w Azure Portal.

![Zasady dostępu Azure Portal][4]

### <a name="verify-registrations"></a>Weryfikuj rejestracje

#### <a name="visual-studio"></a>Visual Studio

W programie Visual Studio możesz łączyć się z platformą Azure za pomocą Eksplorator serwera, aby wyświetlać wiele usług platformy Azure i zarządzać nimi, w tym Notification Hubs. Ten skrót jest szczególnie przydatny w środowisku deweloperskim/testowym.

![Eksplorator serwera programu Visual Studio][9]

![Eksplorator serwera](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

Można wyświetlać wszystkie rejestracje w centrum i zarządzać nimi. Rejestracje mogą być kategoryzowane według platform, natywnych lub szablonów rejestracji, tagów, identyfikatora usługi powiadomień wypychanych, identyfikatora rejestracji i daty wygaśnięcia. Możesz również edytować rejestrację na tej stronie. Jest to szczególnie przydatne w przypadku edytowania tagów.

Kliknij prawym przyciskiem myszy centrum powiadomień w **Eksplorator serwera** i wybierz polecenie **Diagnozuj**. 

![Visual Studio Eksplorator serwera: menu Diagnozuj](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

Zostanie wyświetlona następująca strona:

![Visual Studio: Strona diagnostyki](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

Przejdź do strony **rejestracji urządzeń** :

![Visual Studio: rejestracje urządzeń](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

Przy użyciu strony **wysyłania testowe** można wysłać testową wiadomość e-mail:

![Visual Studio: wysyłanie testowe](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Program Visual Studio umożliwia edytowanie rejestracji tylko podczas tworzenia i testowania oraz ograniczoną liczbę rejestracji. Jeśli trzeba edytować rejestracje zbiorczo, należy rozważyć użycie funkcji rejestracji eksportu i importu opisanej w artykule [jak: eksportować i modyfikować rejestracje zbiorczo](/previous-versions/azure/azure-services/dn790624(v=azure.100)).

#### <a name="service-bus-explorer"></a>Eksplorator usługi Service Bus

Wielu klientów używa [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) do wyświetlania centrów powiadomień i zarządzania nimi. Eksplorator Service Bus jest projektem Open Source. 

### <a name="verify-message-notifications"></a>Weryfikuj powiadomienia o wiadomościach

#### <a name="azure-portal"></a>Azure Portal

Aby wysłać testowe powiadomienie do klientów bez konieczności zapełniania i uruchamiania usługi, w obszarze **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **wysyłanie testowe**.

![Testowanie funkcji wysyłania na platformie Azure][7]

#### <a name="visual-studio"></a>Visual Studio

Możesz również wysyłać powiadomienia testowe z programu Visual Studio.

![Funkcja wysyłania testowego w programie Visual Studio][10]

Aby uzyskać więcej informacji na temat używania Notification Hubs z programem Visual Studio Eksplorator serwera, zobacz następujące artykuły:

* [Jak wyświetlić rejestracje urządzeń dla centrów powiadomień](/previous-versions/windows/apps/dn792122(v=win.10))
* [Głębokie szczegółowe: Visual Studio 2013 Update 2 RC i Azure SDK 2,3]
* [Ogłaszanie wersji Visual Studio 2013 Update 3 i zestawu Azure SDK 2,4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Debugowanie powiadomień zakończonych niepowodzeniem i przegląd wyników powiadomień

#### <a name="enabletestsend-property"></a>Właściwość EnableTestSend

Po wysłaniu powiadomienia za pośrednictwem Notification Hubs powiadomienia są początkowo umieszczane w kolejce. Notification Hubs określa prawidłowe elementy docelowe, a następnie wysyła powiadomienie do usługi powiadomień wypychanych. Jeśli korzystasz z interfejsu API REST lub dowolnego z zestawów SDK klienta, zwrot wywołania wysyłania oznacza tylko, że wiadomość jest umieszczona w kolejce przy użyciu Notification Hubs. Nie zapewnia on informacji o tym, co się stało, gdy Notification Hubs ostatecznie wysłał powiadomienie do usługi powiadomień wypychanych.

Jeśli Twoje powiadomienie nie dotarło do urządzenia klienckiego, może wystąpić błąd Notification Hubs podczas próby dostarczenia go do usługi powiadomień wypychanych. Na przykład rozmiar ładunku może przekroczyć maksymalną dozwoloną przez usługę powiadomień wypychanych lub poświadczenia skonfigurowane w Notification Hubs mogą być nieprawidłowe.

Aby uzyskać wgląd w błędy usługi powiadomień wypychanych, można użyć właściwości [EnableTestSend] . Ta właściwość jest włączana automatycznie podczas wysyłania wiadomości testowych z portalu lub klienta programu Visual Studio. Tej właściwości można użyć, aby wyświetlić szczegółowe informacje o debugowaniu, a także za pośrednictwem interfejsów API. Obecnie można używać go w zestawie SDK platformy .NET. Zostanie ona dodana do wszystkich zestawów SDK klienta ostatecznie.

Aby użyć `EnableTestSend` właściwości z wywołaniem REST, Dołącz parametr ciągu zapytania o nazwie *test* do końca wywołania wysyłania. Na przykład:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>Przykład zestawu SDK platformy .NET

Oto przykład użycia zestawu .NET SDK w celu wysłania natywnego powiadomienia wyskakującego okienka (wyskakujące okienko):

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

Po zakończeniu wykonywania — `result.State` po prostu Stany `Enqueued` . Wyniki nie zapewniają wglądu w to, co się stało z powiadomieniem wypychanym.

Następnie można użyć `EnableTestSend` właściwości Boolean. Użyj `EnableTestSend` właściwości podczas inicjowania, `NotificationHubClient` Aby uzyskać szczegółowy stan błędów usługi powiadomień wypychanych, które występują podczas wysyłania powiadomienia. Wysyłanie wywołania trwa dodatkowy czas, ponieważ najpierw musi Notification Hubs dostarczyć powiadomienia do usługi powiadomień wypychanych.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>Przykładowe dane wyjściowe

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

Ten komunikat oznacza, że poświadczenia skonfigurowane w Notification Hubs są nieprawidłowe lub wystąpił problem z rejestracjami w centrum. Usuń tę rejestrację i pozwól klientowi ponownie utworzyć rejestrację przed wysłaniem wiadomości.

> [!NOTE]
> Użycie `EnableTestSend` właściwości jest silnie ograniczone. Tej opcji należy używać tylko w środowisku deweloperskim/testowym i z ograniczonym zestawem rejestracji. Powiadomienia debugowania są wysyłane tylko do 10 urządzeń. Istnieje również limit przetwarzania wysyłanych danych debugowania przy 10 na minutę.

### <a name="review-telemetry"></a>Przejrzyj dane telemetryczne

#### <a name="azure-portal"></a>Azure Portal

W portalu możesz zapoznać się z krótkim omówieniem wszystkich działań w centrum powiadomień.

1. Na karcie **Omówienie** można zobaczyć Zagregowany widok rejestracji, powiadomień i błędów według platformy.

   ![Pulpit nawigacyjny przeglądu Notification Hubs][5]

2. Na karcie **monitor** można dodać wiele innych metryk specyficznych dla platformy w celu dokładniejszego przeszukiwania. Można wyszukać błędy, które są zwracane, gdy Notification Hubs próbuje wysłać powiadomienie do usługi powiadomień wypychanych.

   ![Azure Portal dziennik aktywności][6]

3. Zacznij od przejrzenia **komunikatów przychodzących**, **operacji rejestracji** i **powiadomień o powodzeniu**. Następnie przejdź do karty na platformę, aby przejrzeć błędy specyficzne dla usługi powiadomień wypychanych.

4. Jeśli ustawienia uwierzytelniania dla centrum powiadomień są nieprawidłowe, pojawi się komunikat **o błędzie uwierzytelniania PNS** . Dobrym wskaźnikiem jest sprawdzenie poświadczeń usługi powiadomień wypychanych.

#### <a name="programmatic-access"></a>Dostęp programowy

Aby uzyskać więcej informacji o dostępie programistycznym, zobacz [dostęp programistyczny](/previous-versions/azure/azure-services/dn458823(v=azure.100)).

> [!NOTE]
> Niektóre funkcje związane z telemetrią, takie jak eksportowanie i importowanie rejestracji oraz dostęp do telemetrii za pośrednictwem interfejsów API, są dostępne tylko w warstwie Standardowa usługi. Jeśli spróbujesz użyć tych funkcji z warstwy Bezpłatna lub podstawowa, zostanie wyświetlony komunikat o wyjątku w przypadku użycia zestawu SDK. Jeśli używasz funkcji bezpośrednio z interfejsów API REST, otrzymasz błąd HTTP 403 (dostęp zabroniony).
>
> Aby korzystać z funkcji związanych z telemetrią, należy najpierw upewnić się, że w Azure Portal jest używana standardowa warstwa usług.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Przegląd Notification Hubs]: notification-hubs-push-notification-overview.md
[Wprowadzenie do usługi Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Szablony]: /previous-versions/azure/azure-services/dn530748(v=azure.100)
[Omówienie usługi APNs]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Informacje o komunikatach FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: /previous-versions/azure/azure-services/dn790624(v=azure.100)
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: /previous-versions/windows/apps/dn792122(v=win.10)
[Głębokie szczegółowe: Visual Studio 2013 Update 2 RC i Azure SDK 2,3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Ogłaszanie wersji Visual Studio 2013 Update 3 i zestawu Azure SDK 2,4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: /dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend
[Programmatic telemetry access]: /previous-versions/azure/azure-services/dn458823(v=azure.100)
