---
title: Azure Notification Hubs często zadawane pytania (FAQ) | Microsoft Docs
description: Często zadawane pytania dotyczące projektowania i implementowania rozwiązań na platformie Azure Notification Hubs.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
keywords: Powiadomienie wypychane, powiadomienia wypychane, powiadomienia wypychane systemu iOS, powiadomienia wypychane systemu Android, wypychanie systemu iOS, wypychanie systemu Android
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 11/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 9d476b1db645ed1f91b62fcf11464f7077a8fb3c
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491430"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Powiadomienia wypychane za pomocą usługi Azure Notification Hubs: często zadawane pytania

## <a name="general"></a>Ogólne

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Co to jest struktura zasobów Notification Hubs?

Usługa Azure Notification Hubs ma dwa poziomy zasobów: centra i przestrzenie nazw. Centrum to pojedynczy zasób wypychania, który może zawierać Międzyplatformowe informacje wypychane jednej aplikacji. Przestrzeń nazw jest kolekcją centrów w jednym regionie. Zalecane mapowanie pasuje do jednej przestrzeni nazw z jedną aplikacją. W ramach przestrzeni nazw można korzystać z centrum produkcyjnego, które współpracuje z aplikacją produkcyjną, centrum testowania, które współpracuje z aplikacją testową i tak dalej.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Jaki jest model cen dla Notification Hubs?

Najnowsze szczegóły cennika można znaleźć na stronie [cennika Notification Hubs] . Notification Hubs jest rozliczana na poziomie przestrzeni nazw. (Aby zapoznać się z definicją przestrzeni nazw, zobacz "co to jest struktura zasobów Notification Hubs?"). Notification Hubs oferuje trzy warstwy:

* **Bezpłatna** : Ta warstwa jest dobrym punktem wyjścia do eksplorowania funkcji wypychania. Nie jest to zalecane w przypadku aplikacji produkcyjnych. Otrzymujesz 500 urządzeń i 1 000 000 wypchnięcia na przestrzeń nazw miesięcznie, bez gwarancji umowy dotyczącej poziomu usług (SLA).
* **Podstawowa** : Ta warstwa (lub warstwa standardowa) jest zalecana w przypadku mniejszych aplikacji produkcyjnych. Odbierasz 200 000 urządzeń i 10 000 000 wypchnięcia na przestrzeń nazw miesięcznie jako linia bazowa.
* **Standardowa** : Ta warstwa jest zalecana dla średnich i dużych aplikacji produkcyjnych. Odbierasz 10 000 000 urządzeń i 10 000 000 wypchnięcia na przestrzeń nazw miesięcznie jako linia bazowa. Obejmuje rozbudowaną telemetrię (dodatkowe dane dotyczące podanego stanu wypychania).

Funkcje warstwy standardowej:

* **Rozbudowana Telemetria** : możesz użyć Notification Hubs poszczególnych komunikatów telemetrycznych do śledzenia żądań wypychania i system powiadomień platformy informacji zwrotnych na potrzeby debugowania.
* **Wielodostępność** : można korzystać z poświadczeń system powiadomień platformy na poziomie przestrzeni nazw. Ta opcja umożliwia łatwe dzielenie dzierżawców w centrach w obrębie tej samej przestrzeni nazw.
* **Zaplanowane wypychanie** : możesz zaplanować wysyłanie powiadomień w dowolnym czasie.
* **Operacje zbiorcze** : Włącza funkcje eksportu/importu rejestracji zgodnie z opisem w dokumencie [eksport/import rejestracji] .

### <a name="what-is-the-notification-hubs-sla"></a>Co to jest umowa SLA Notification Hubs?

W przypadku warstw Podstawowa i Standardowa Notification Hubs prawidłowo skonfigurowane aplikacje mogą wysyłać powiadomienia wypychane lub wykonywać operacje zarządzania rejestracją przez co najmniej 99,9% czasu. Aby dowiedzieć się więcej na temat umowy SLA, przejdź do strony [Notification Hubs umów SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) .

> [!NOTE]
> Ponieważ powiadomienia wypychane są zależne od systemów powiadomień platformy innych firm, takich jak usługa wypychania powiadomień firmy Apple (APNs) i Google Firebase Cloud Messaging (FCM), nie ma gwarancji SLA przy dostarczaniu tych komunikatów. Gdy program Notification Hubs wyśle partie do systemów powiadomień platformy (gwarantowanych przez umowę SLA), jest odpowiedzialny za systemy powiadomień platformy o dostarczenie wypchnięcia (bez gwarancji SLA).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Jak mogę uaktualnić lub obniżyć poziom mojego centrum lub przestrzeni nazw do innej warstwy?

Przejdź do obszaru **[Azure portal]**  >  **nazw Notification Hubs** Azure Portal lub **Notification Hubs**. Wybierz zasób, który chcesz zaktualizować, i przejdź do **warstwy cenowej**. Należy pamiętać o następujących wymaganiach:

* Zaktualizowana warstwa cenowa ma zastosowanie do *wszystkich* centrów w przestrzeni nazw, z którymi pracujesz.
* Jeśli liczba urządzeń przekracza limit warstwy, do której należysz, należy usunąć urządzenia przed obniżeniem poziomu.

## <a name="design-and-development"></a>Projektowanie i programowanie

### <a name="which-server-side-platforms-do-you-support"></a>Które platformy po stronie serwera są obsługiwane?

Zestawy SDK serwera są dostępne dla platform .NET, Java, Node.js, PHP i Python. Interfejsy API Notification Hubs są oparte na interfejsach REST, dzięki czemu możesz współpracować bezpośrednio z interfejsami API REST, jeśli używasz różnych platform lub nie potrzebujesz dodatkowej zależności. Aby uzyskać więcej informacji, przejdź do strony [Notification Hubs interfejsów API REST] .

### <a name="which-client-platforms-do-you-support"></a>Które platformy klienckie są obsługiwane?

Powiadomienia wypychane są obsługiwane [dla systemów iOS](ios-sdk-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Android China (za pośrednictwem Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin [iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) i [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)oraz [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Aby uzyskać więcej informacji, zapoznaj się ze stroną [samouczków Wprowadzenie Notification Hubs](ios-sdk-get-started.md) .

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Czy są obsługiwane wiadomości tekstowe, wiadomości e-mail lub powiadomienia w sieci Web?

Notification Hubs wysyła powiadomienia do urządzeń, na których działają aplikacje mobilne. Nie udostępnia ona wiadomości e-mail ani wiadomości SMS. Notification Hubs również nie zapewnia usługi dostarczania powiadomień wypychanych w przeglądarce. Klienci mogą zaimplementować tę funkcję przy użyciu programu sygnalizującego na obsługiwanych platformach po stronie serwera. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Ile urządzeń można obsługiwać w przypadku wysyłania powiadomień wypychanych za pośrednictwem Notification Hubs?

Zapoznaj się ze stroną [cennika Notification Hubs] , aby uzyskać szczegółowe informacje o liczbie obsługiwanych urządzeń.

Jeśli wymagana jest obsługa ponad 10 000 000 zarejestrowanych urządzeń, należy podzielić urządzenia na partycje w wielu obszarach nazw.

### <a name="how-many-push-notifications-can-i-send-out"></a>Ile powiadomień wypychanych mogę wysyłać?

W zależności od wybranej warstwy platforma Azure Notification Hubs automatycznie skaluje się w zależności od liczby powiadomień przepływających przez system.

> [!NOTE]
> Całkowity koszt użycia może wzrosnąć w zależności od liczby wysłanych powiadomień wypychanych. Upewnij się, że masz świadomość ograniczeń warstwy przedstawionych na stronie z [cennikiem Notification Hubs] .

Nasi klienci używają Notification Hubs do wysyłania milionów powiadomień wypychanych codziennie. Nie trzeba wykonywać żadnych specjalnych czynności, aby skalować zasięg powiadomień wypychanych, o ile korzystasz z usługi Azure Notification Hubs.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Jak długo trwa wysyłanie powiadomień wypychanych w celu uzyskania dostępu do urządzenia?

W scenariuszu normalnego użycia, w którym obciążenie przychodzące jest spójne, a nawet usługa Azure Notification Hubs może przetworzyć co najmniej *1 000 000 Powiadomienie wypychane*. Ta stawka może się różnić w zależności od liczby tagów, charakteru wysyłanych elementów przychodzących i innych zewnętrznych czynników.

Podczas szacowanego czasu dostarczania usługa oblicza elementy docelowe na platformę i kieruje komunikaty do usługi powiadomień wypychanych (PNS) na podstawie zarejestrowanych tagów lub wyrażeń tagów. Jest odpowiedzialny za PNS do wysyłania powiadomień do urządzenia.

PNS nie gwarantuje żadnej umowy SLA dotyczącej dostarczania powiadomień. Większość powiadomień wypychanych jest jednak dostarczana do urządzeń docelowych w ciągu kilku minut (zazwyczaj w ciągu 10 minut) od momentu, w którym są wysyłane do Notification Hubs. Kilka powiadomień może zająć więcej czasu.

> [!NOTE]
> Na platformie Azure Notification Hubs wprowadzono zasady usuwania powiadomień wypychanych, które nie są dostarczane do PNS w ciągu 30 minut. To opóźnienie może wystąpić z kilku powodów, ale najczęściej ponieważ PNS ogranicza aplikację.

### <a name="is-there-any-latency-guarantee"></a>Czy istnieje gwarancja opóźnienia?

Ze względu na charakter powiadomień wypychanych (są one dostarczane przez zewnętrzny, specyficzny dla platformy PNS), nie ma gwarancji opóźnienia. Zazwyczaj większość powiadomień wypychanych jest dostarczanych w ciągu kilku minut.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Co należy wziąć pod uwagę podczas projektowania rozwiązania z przestrzeniami nazw i centrami powiadomień?

#### <a name="mobile-appenvironment"></a>Aplikacja mobilna/środowisko

* Użyj jednego centrum powiadomień dla każdej aplikacji mobilnej na środowisko.
* W scenariuszu obejmującym wiele dzierżawców każda dzierżawa powinna mieć osobne centrum.
* Nie udostępniaj tego samego centrum powiadomień dla środowisk produkcyjnych i testowych. To rozwiązanie może powodować problemy podczas wysyłania powiadomień. (Firma Apple oferuje piaskownicę i punkty końcowe wypychania produkcji, z których każdy ma osobne poświadczenia).
* Domyślnie możesz wysyłać powiadomienia testowe do zarejestrowanych urządzeń za pomocą Azure Portal lub składnika zintegrowanego platformy Azure w programie Visual Studio. Próg jest ustawiany na 10 urządzeń, które są wybierane losowo z puli rejestracji.

> [!NOTE]
> Jeśli Twoje centrum zostało pierwotnie skonfigurowane przy użyciu certyfikatu piaskownicy firmy Apple, a następnie została ponownie skonfigurowana do korzystania z certyfikatu produkcyjnego firmy Apple, oryginalne tokeny urządzenia są nieprawidłowe. Nieprawidłowe tokeny powodują, że wypychanie zakończy się niepowodzeniem. Rozdziel środowiska produkcyjne i testowe oraz Używaj różnych centrów dla różnych środowisk.

#### <a name="pns-credentials"></a>Poświadczenia PNS

Gdy aplikacja mobilna jest zarejestrowana w portalu dla deweloperów platformy (na przykład Apple lub Google), wysyłany jest identyfikator aplikacji i tokeny zabezpieczające. Zaplecze aplikacji udostępnia te tokeny PNS platformy, aby można było wysyłać powiadomienia wypychane do urządzeń. Tokeny zabezpieczające mogą mieć postać certyfikatów (na przykład Apple iOS lub Windows Phone) lub kluczy zabezpieczeń (np. Google Android lub Windows). Muszą być skonfigurowane w centrach powiadomień. Konfiguracja zazwyczaj odbywa się na poziomie centrum powiadomień, ale można ją również wykonać na poziomie przestrzeni nazw w scenariuszu obejmującym wiele dzierżawców.

#### <a name="namespaces"></a>Przestrzenie nazw

Przestrzenie nazw mogą być używane do grupowania wdrożenia. Mogą one również służyć do reprezentowania wszystkich centrów powiadomień dla wszystkich dzierżawców tej samej aplikacji w scenariuszu obejmującym wiele dzierżawców.

#### <a name="geo-distribution"></a>Dystrybucja geograficzna

Dystrybucja geograficzna nie zawsze ma krytyczne znaczenie w scenariuszach powiadomień wypychanych. Różne PNSes (na przykład APNs lub FCM), które dostarczają powiadomienia wypychane do urządzeń nie są równomiernie dystrybuowane.

Jeśli masz aplikację, która jest używana globalnie, możesz utworzyć centra w różnych obszarach nazw za pomocą usługi Notification Hubs w różnych regionach świadczenia usługi Azure na całym świecie.

> [!NOTE]
> Nie zalecamy tego uzgodnienia, ponieważ zwiększa koszt zarządzania, szczególnie w przypadku rejestracji. Należy to zrobić tylko wtedy, gdy istnieje jawna potrzeba.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Czy należy przeprowadzić rejestrację z zaplecza aplikacji lub bezpośrednio za pomocą urządzeń klienckich?

Rejestracje z zaplecza aplikacji są przydatne, gdy konieczne jest uwierzytelnienie klientów przed utworzeniem rejestracji. Są one również przydatne, gdy masz Tagi, które muszą być utworzone lub zmodyfikowane przez zaplecze aplikacji na podstawie logiki aplikacji. Aby uzyskać więcej informacji, przejdź do strony [wskazówki dotyczące rejestracji zaplecza] i [wskazówki dotyczące rejestracji zaplecza 2] .

### <a name="what-is-the-push-notification-delivery-security-model"></a>Co to jest model zabezpieczeń dostarczania powiadomień wypychanych?

Usługa Azure Notification Hubs korzysta z modelu zabezpieczeń opartego na [sygnaturach dostępu współdzielonego](../storage/common/storage-sas-overview.md). Możesz użyć tokenów sygnatury dostępu współdzielonego na poziomie głównego obszaru nazw lub na poziomie centrum powiadomień. Tokeny sygnatury dostępu współdzielonego można ustawić tak, aby były zgodne z różnymi regułami autoryzacji, na przykład w celu wysłania uprawnień do wiadomości lub nasłuchiwania uprawnień do powiadomień. Aby uzyskać więcej informacji, zobacz dokument [model zabezpieczeń Notification Hubs] .

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Jak należy obsługiwać poufne ładunki w powiadomieniach wypychanych?

Wszystkie powiadomienia są dostarczane do urządzeń docelowych za pomocą PNS platformy. Po wysłaniu powiadomienia do usługi Azure Notification Hubs są one przetwarzane i przekazywane do odpowiednich PNS.

Wszystkie połączenia, od nadawcy do usługi Azure Notification Hubs do PNS, używają protokołu HTTPS.

> [!NOTE]
> Usługa Azure Notification Hubs nie rejestruje ładunku komunikatów.

Aby wysyłać poufne ładunki, zalecamy użycie bezpiecznego wzorca wypychania. Nadawca dostarcza powiadomienie ping z identyfikatorem komunikatu do urządzenia bez poufnego ładunku. Gdy aplikacja na urządzeniu odbiera ładunek, aplikacja wywołuje bezpieczny interfejs API bezpośrednio, aby pobrać szczegóły komunikatu. Aby zapoznać się z przewodnikiem dotyczącym implementowania tego wzorca, przejdź do strony [Notification Hubs bezpiecznego wypychania samouczka] .

## <a name="operations"></a>Operacje

### <a name="what-support-is-provided-for-disaster-recovery"></a>Jaka pomoc techniczna jest świadczona w przypadku odzyskiwania po awarii?

Zapewniamy pokrycie odzyskiwania po awarii metadanych na naszym końcu (nazwy Notification Hubs, parametrów połączenia i innych informacji krytycznych). Gdy zostanie wyzwolony scenariusz odzyskiwania po awarii, dane rejestracji są *jedynym segmentem* infrastruktury Notification Hubs, która została utracona. Musisz zaimplementować rozwiązanie, aby ponownie wypełnić te dane do nowego centrum po odzyskaniu:

1. Utwórz pomocnicze centrum powiadomień w innym centrum danych. Zalecamy utworzenie jednej od początku, aby włączyć osłonę przed zdarzeniem odzyskiwania po awarii, które może mieć wpływ na możliwości zarządzania. Możesz również utworzyć jeden w czasie zdarzenia odzyskiwania po awarii.

2. Utrzymuj synchronizację pomocniczego centrum powiadomień z podstawowym centrum powiadomień przy użyciu jednej z następujących opcji:

   * Użyj zaplecza aplikacji, która jednocześnie tworzy i aktualizuje instalacje w obu centrach powiadomień. Instalacje pozwalają określić własny unikatowy identyfikator urządzenia, co sprawia, że jest to bardziej odpowiednie dla scenariusza replikacji. Aby uzyskać więcej informacji, zobacz ten [przykładowy kod](https://github.com/Azure/azure-notificationhubs-dotnet/tree/main/Samples/RedundantHubSample).
   * Użyj zaplecza aplikacji, który pobiera regularne zrzuty rejestracji z głównego centrum powiadomień jako kopii zapasowej. Następnie można wykonać operację wstawiania zbiorczego do pomocniczego centrum powiadomień.

Pomocnicze centrum powiadomień może zakończyć się z wygasłymi instalacjami/rejestracjami. Gdy wypchnięcie zostanie wysłane do wygasłego dojścia, Notification Hubs automatycznie czyści skojarzony rekord instalacji/rejestracji na podstawie odpowiedzi odebranej z serwera PNS. Aby wyczyścić wygasłe rekordy z pomocniczego centrum powiadomień, Dodaj logikę niestandardową, która przetwarza opinie od poszczególnych operacji wysyłania. Następnie wygaśnięcie instalacji/rejestracji w pomocniczym centrum powiadomień.

Jeśli nie masz zaplecza, gdy aplikacja zostanie uruchomiona na urządzeniach docelowych, przeprowadzimy nową rejestrację w pomocniczym centrum powiadomień. Ostatecznie pomocnicze centrum powiadomień będzie miało wszystkie aktywne zarejestrowane urządzenia.

W przypadku urządzeń z nieotwartymi aplikacjami nie będą odbierane powiadomienia.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Czy wszystkie dane są przechowywane w zaszyfrowanej formie?

Usługa Azure Notification Hubs szyfruje wszystkie dane klientów w stanie spoczynku z wyjątkiem tagów rejestracyjnych. Z tego powodu nie należy przechowywać danych osobistych ani poufnych przy użyciu tagów.

### <a name="is-there-audit-log-capability"></a>Czy istnieje funkcja dziennika inspekcji?

Tak. Wszystkie operacje związane z zarządzaniem Notification Hubsą aktualizują dziennik aktywności platformy Azure, który jest dostępny w [Azure Portal]. Dziennik aktywności platformy Azure zawiera szczegółowe informacje o operacjach wykonywanych na zasobach w Twoich subskrypcjach. Za pomocą dziennika aktywności można określić, kto i kiedy mają być wykonywane operacje zapisu (PUT, POST, DELETE) dla zasobów w subskrypcji. Można także zrozumieć stan operacji i innych istotnych właściwości. Ale. Dziennik aktywności nie zawiera operacji odczytu (GET).

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów

### <a name="what-troubleshooting-capabilities-are-available"></a>Jakie funkcje rozwiązywania problemów są dostępne?

Usługa Azure Notification Hubs udostępnia kilka funkcji rozwiązywania problemów, szczególnie w przypadku najbardziej typowego scenariusza porzucania powiadomień. Aby uzyskać szczegółowe informacje, zobacz dokument dotyczący [rozwiązywania problemów Notification Hubs] .

### <a name="what-telemetry-features-are-available"></a>Jakie funkcje telemetrii są dostępne?

Usługa Azure Notification Hubs umożliwia wyświetlanie danych telemetrycznych w [Azure Portal]. Szczegóły metryk są dostępne na stronie [metryki Notification Hubs] .

Możesz również programowo uzyskać dostęp do metryk. Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Pobierz metryki Azure monitor przy użyciu platformy .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). Ten przykład używa nazwy użytkownika i hasła. Aby użyć certyfikatu, przeciąż metodę FromServicePrincipal, aby dostarczyć certyfikat, jak pokazano w [tym przykładzie](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Pobieranie metryk i dzienników aktywności dla zasobu](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Przewodnik po interfejsie API REST usługi Azure Monitoring](../azure-monitor/platform/rest-api-walkthrough.md)

> [!NOTE]
> Pomyślne powiadomienia oznaczają, że powiadomienia wypychane zostały dostarczone do zewnętrznego PNS (na przykład APN dla systemów iOS i macOS lub FCM dla urządzeń z systemem Android). Jest odpowiedzialny za PNS do dostarczania powiadomień do urządzeń docelowych. Zazwyczaj PNS nie ujawnia metryk dostarczania podmiotom trzecim.  

[Witryna Azure Portal]: https://portal.azure.com
[Cennik Notification Hubs]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Interfejsy API REST Notification Hubs]: /previous-versions/azure/reference/dn530746(v=azure.100)
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Wskazówki dotyczące rejestracji zaplecza]: /previous-versions/azure/azure-services/dn743807(v=azure.100)
[Wskazówki dotyczące rejestracji zaplecza 2]: /previous-versions/azure/azure-services/dn530747(v=azure.100)
[Model zabezpieczeń Notification Hubs]: /previous-versions/azure/azure-services/dn495373(v=azure.100)
[Samouczek bezpiecznego wypychania Notification Hubs]: ./notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md
[Rozwiązywanie problemów Notification Hubs]: ./notification-hubs-push-notification-fixer.md
[Metryki Notification Hubs]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Eksportowanie/Importowanie rejestracji]: ./export-modify-registrations-bulk.md
[Witryna Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
