---
title: Wysoka dostępność dzięki Media Services wideo na żądanie
description: Ten artykuł zawiera omówienie usług platformy Azure, których można użyć w celu ułatwienia wysokiej dostępności aplikacji VOD.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: conceptual
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a87525248273db38e4e7bc8d1b59bbd9f99bb4c6
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106994"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>Wysoka dostępność dzięki Media Services i wideo na żądanie (VOD)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>Wysoka dostępność dla VOD

Istnieje Wzorzec projektowy o wysokiej dostępności o nazwie [geodes](/azure/architecture/patterns/geodes) w dokumentacji architektury platformy Azure. Opisano w nim, jak duplikaty zasobów są wdrażane w różnych regionach geograficznych w celu zapewnienia skalowalności i odporności.  Korzystając z usług platformy Azure, można utworzyć taką architekturę, która będzie obejmować wiele zagadnień związanych z projektowaniem wysokiej dostępności, takich jak nadmiarowość, monitorowanie kondycji, równoważenie obciążenia i tworzenie kopii zapasowych i odzyskiwanie danych.  Jedna taka architektura została opisana poniżej ze szczegółowymi informacjami dotyczącymi każdej usługi używanej w rozwiązaniu, a także sposobem użycia poszczególnych usług w celu utworzenia architektury wysokiej dostępności dla aplikacji VOD.

### <a name="sample"></a>Przykład

Dostępna jest przykład, która umożliwia zapoznanie się z wysoką dostępnością dzięki Media Services i wideo na żądanie (VOD). Bardziej szczegółowo opisano, jak usługi są używane do scenariusza VOD.  Przykład nie jest przeznaczony do użycia w środowisku produkcyjnym w jego bieżącej postaci.  Uważnie zapoznaj się z przykładowym kodem i Readme, szczególnie w sekcji dotyczącej [trybów awarii](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming) przed ich integracją z aplikacją produkcyjną.  W produkcyjnej implementacji wysokiej dostępności dla wideo na żądanie (VOD) należy również uważnie przejrzeć strategię Content Delivery Network (CDN).  Zapoznaj się z [kodem w serwisie GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming).

## <a name="overview-of-services"></a>Przegląd usług

Usługi używane w tej przykładowej architekturze obejmują:

| Ikona | Nazwa | Opis |
| :--: | ---- | ----------- |
|![Jest to ikona konta usługi Media Services.](./media/architecture-high-availability-encoding-concept/azure-media-services.svg)| Konto usługi Media Services | **Opis:**<br>Konto Media Services jest punktem początkowym do zarządzania, szyfrowania, kodowania, analizowania i przesyłania strumieniowego zawartości multimedialnej na platformie Azure. Jest ona skojarzona z zasobem konta usługi Azure Storage. Konto i wszystkie powiązane magazyny muszą znajdować się w tej samej subskrypcji platformy Azure.<br><br>**VOD:**<br>Są to usługi używane do kodowania i dostarczania zasobów wideo i audio.  Aby zapewnić wysoką dostępność, należy skonfigurować co najmniej dwa Media Services kont w innym regionie. [Dowiedz się więcej o Azure Media Services](media-services-overview.md). |
|![Jest to ikona konta magazynu.](./media/architecture-high-availability-encoding-concept/storage-account.svg)| Konto magazynu | **Opis:**<br>Konto usługi Azure Storage zawiera wszystkie obiekty danych usługi Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. Dane są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS.<br><br>Każde konto Media Services w każdym regionie będzie miało konto magazynu w tym samym regionie.<br><br>**VOD:**<br>Dane wejściowe i wyjściowe można przechowywać na potrzeby przetwarzania VOD i przesyłania strumieniowego. [Dowiedz się więcej o usłudze Azure Storage](../../storage/common/storage-introduction.md). |
|![Jest to ikona kolejki usługi Azure Storage.](./media/architecture-high-availability-encoding-concept/storage-account-queue.svg)| Kolejka usługi Azure Storage | **Opis:**<br>Azure Queue Storage to usługa do przechowywania dużej liczby komunikatów, do której można uzyskać dostęp z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionego połączenia za pomocą protokołu HTTP lub HTTPS.<br><br>**VOD:**<br>Kolejki mogą służyć do wysyłania i odbierania komunikatów w celu koordynowania działań między różnymi modułami. Przykład używa kolejki usługi Azure Storage, ale platforma Azure udostępnia inne typy kolejek, takich jak Service Bus i Service Fabric niezawodne kolejki, które mogą być lepiej dopasowane do Twoich potrzeb. [Dowiedz się więcej o usłudze Azure Queue](../../storage/queues/storage-queues-introduction.md). |
|![Jest to ikona Azure Cosmos DB.](./media/architecture-high-availability-encoding-concept/azure-cosmos-db.svg)| Azure Cosmos DB  | **Opis:**<br>Azure Cosmos DB to globalnie dystrybuowana, wielomodelowa usługa bazy danych firmy Microsoft, która niezależnie skaluje przepływność i magazyn w dowolnej liczbie regionów świadczenia usługi Azure na całym świecie.<br><br>**VOD:**<br>Tabele mogą służyć do przechowywania rekordów stanu wyjścia zadania oraz do śledzenia stanu kondycji każdego wystąpienia Media Services. Można także śledzić/rejestrować stan każdego wywołania interfejsu API Media Services. [Dowiedz się więcej o Azure Cosmos DB](../../cosmos-db/introduction.md).  |
|![Jest to ikona tożsamości zarządzanej.](./media/architecture-high-availability-encoding-concept/managed-identity.svg)| Tożsamość zarządzana | **Opis:**<br>Tożsamość zarządzana to funkcja usługi Azure AD, która zapewnia automatyczną tożsamość zarządzaną w usłudze Azure AD. Może służyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez zapisywania poświadczeń w kodzie.<br><br>**VOD:**<br>Azure Functions może używać tożsamości zarządzanej do uwierzytelniania w Media Services wystąpieniach w celu nawiązania połączenia z Key Vault. [Dowiedz się więcej o tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md). |
|![Jest to ikona Key Vault.](./media/architecture-high-availability-encoding-concept/key-vault.svg)| Key Vault | **Opis:**<br>Azure Key Vault może służyć do bezpiecznego przechowywania i ścisłego kontrolowania dostępu do tokenów, haseł, certyfikatów, kluczy interfejsu API i innych wpisów tajnych. Można go również użyć jako rozwiązania do zarządzania kluczami. Usługa Azure Key Vault ułatwia tworzenie i kontrolowanie kluczy szyfrowania używanych do szyfrowania danych. Może ona łatwo udostępniać i wdrażać publiczne i prywatne certyfikaty Transport Layer Security/Secure Sockets Layer (TLS/SSL) do użycia z platformą Azure i zasobami podłączonymi wewnętrznie. Wpisy tajne i klucze mogą być chronione przez oprogramowanie lub FIPS 140-2 poziom 2 sprawdzony sprzętowych modułów zabezpieczeń.<br><br>**VOD:**<br>Key Vault można użyć do skonfigurowania zasad dostępu dla nazwy głównej usługi dla aplikacji.  Może służyć do przechowywania parametrów połączenia na kontach magazynu. Używamy Key Vault do przechowywania parametrów połączenia do kont magazynu i usługi Cosmos DB. Za pomocą Key Vault można również przechowywać ogólną konfigurację klastra. Dla każdego wystąpienia usługi multimedialnej można przechowywać Identyfikator subskrypcji, nazwę grupy zasobów i nazwę konta. Aby uzyskać więcej informacji, zobacz jak jest używany w przykładzie. [Dowiedz się więcej o Key Vault](../../key-vault/general/overview.md). |
|![Jest to ikona Azure Functions.](./media/architecture-high-availability-encoding-concept/function-app.svg)| Azure Functions | **Opis:**<br>Uruchamianie małych fragmentów kodu (nazywanych "funkcjami") bez obaw o infrastrukturę aplikacji z Azure Functions. [Dowiedz się więcej o Azure Functions](../../azure-functions/functions-overview.md).<br><br>**VOD:**<br>Azure Functions może służyć do przechowywania modułów aplikacji VOD.  Moduły dla aplikacji VOD mogą obejmować:<br><br>**Moduł planowania zadań**<br>Moduł planowania zadań umożliwia przesyłanie nowych zadań do klastra Media Services (co najmniej dwa wystąpienia w różnych regionach). Będzie on śledzić stan kondycji każdego wystąpienia Media Services i przesłać nowe zadanie do następnego wystąpienia w dobrej kondycji.<br><br>**Moduł stanu zadania**<br>Moduł stanu zadania nasłuchuje zdarzeń wyjściowego stanu zadania pochodzących z usługi Azure Event Grid. Zdarzenia są przechowywane w magazynie zdarzeń, aby zminimalizować liczbę wywołań interfejsów API Media Services przez resztę modułów.<br><br>**Moduł kondycji wystąpienia**<br>Ten moduł śledzi przesłane zadania i określa stan kondycji dla każdego wystąpienia Media Services. Spowoduje to śledzenie ukończonych zadań, zakończonych niepowodzeniem zadań i zadań, które nigdy nie zostały zakończone.<br><br>**Moduł aprowizacji**<br>Ten moduł spowoduje udostępnienie przetworzonych zasobów. Spowoduje to skopiowanie danych zasobów do wszystkich wystąpień Media Services i skonfigurowanie usługi frontonu platformy Azure w celu zapewnienia, że zasoby mogą być przesyłane strumieniowo nawet wtedy, gdy niektóre wystąpienia Media Services niedostępne. Ponadto można skonfigurować lokalizatory przesyłania strumieniowego.<br><br>**Moduł weryfikacji zadania**<br>Ten moduł śledzi każde przesłane zadanie, ponownie przesyła zadania zakończone niepowodzeniem i przeprowadza czyszczenie danych zadania po pomyślnym zakończeniu zadania.  |
|![Jest to ikona App Service.](./media/architecture-high-availability-encoding-concept/application-service.svg)| App Service (i planowanie)  | **Opis:**<br>Azure App Service to usługa oparta na protokole HTTP do hostowania aplikacji sieci Web, interfejsów API REST i zaplecza mobilnego. Obsługuje platformę .NET, .NET Core, Java, Ruby, Node.js, PHP lub Python. Aplikacje są uruchamiane i skalowane w środowisku opartym na systemie Windows i Linux.<br><br>**VOD:**<br>Każdy moduł będzie hostowany przez App Service. [Dowiedz się więcej o App Service](../../app-service/overview.md). |
|![Jest to ikona drzwi frontonu platformy Azure.](./media/architecture-high-availability-encoding-concept/azure-front-door.svg)| Azure Front Door | **Opis:**<br>Drzwi frontonu platformy Azure służą do definiowania, zarządzania i monitorowania globalnego routingu ruchu internetowego przez optymalizację w celu uzyskania najlepszej wydajności i szybkiej globalnej pracy awaryjnej w celu zapewnienia wysokiej dostępności.<br><br>**VOD:**<br>Drzwi frontonu platformy Azure mogą służyć do kierowania ruchu do punktów końcowych przesyłania strumieniowego. [Dowiedz się więcej na temat zewnętrznych drzwi platformy Azure](../../frontdoor/front-door-overview.md).  |
|![Jest to ikona Azure Event Grid.](./media/architecture-high-availability-encoding-concept/event-grid-subscription.svg)| Azure Event Grid | **Opis:**<br>Utworzone dla architektur opartych na zdarzeniach, Event Grid ma wbudowaną obsługę zdarzeń pochodzących z usług platformy Azure, takich jak obiekty blob magazynu i grupy zasobów. Ma również obsługę niestandardowych zdarzeń tematu. Filtry mogą służyć do kierowania określonych zdarzeń do różnych punktów końcowych, multiemisji z wieloma punktami końcowymi i zapewnienia niezawodnego dostarczania zdarzeń. Maksymalizuje dostępność poprzez natywne rozmieszczenie w wielu domenach błędów w każdym regionie i w strefach dostępności.<br><br>**VOD:**<br>Event Grid może służyć do śledzenia wszystkich zdarzeń aplikacji i przechowywania ich w celu utrwalenia stanu zadania. [Dowiedz się więcej o Azure Event Grid](../../event-grid/overview.md). |
|![Jest to ikona Application Insights.](./media/architecture-high-availability-encoding-concept/application-insights.svg)| Application Insights | **Opis:** <br>Application Insights (funkcja usługi Azure Monitor) to rozszerzalna usługa do zrządzania wydajnością aplikacji dla deweloperów i ekspertów metodyki DevOps. Służy do monitorowania aplikacji na żywo. Wykrywa anomalie wydajności i zawiera narzędzia analityczne służące do diagnozowania problemów i zrozumienia, co użytkownicy robią z aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.<br><br>**VOD:**<br>Wszystkie dzienniki mogą być wysyłane do Application Insights. Można zobaczyć, jakie wystąpienie przetworzy każde zadanie przez wyszukiwanie pomyślnie utworzonych komunikatów zadań. Może zawierać wszystkie przesłane metadane zadania, takie jak unikatowy identyfikator i informacje o nazwie wystąpienia. [Dowiedz się więcej o Application Insights](../../azure-monitor/app/app-insights-overview.md). |
## <a name="architecture"></a>Architektura

Ten diagram wysokiego poziomu przedstawia architekturę podanej przykładu, aby rozpocząć pracę z wysoką dostępnością i usługami Media Services.

[![Diagram ](media/architecture-high-availability-encoding-concept/high-availability-architecture.svg) architektury High Level wideo na żądanie (VOD)](./media/architecture-high-availability-encoding-concept/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>Najlepsze rozwiązania

### <a name="regions"></a>Regiony

* [Utwórz](./account-create-how-to.md) co najmniej dwa konta Azure Media Services. Te dwa konta muszą znajdować się w różnych regionach. Aby uzyskać więcej informacji, zobacz [regiony, w których jest wdrażana usługa Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Przekaż multimedia do tego samego regionu, w którym planujesz przesłać zadanie. Aby uzyskać więcej informacji o sposobie uruchamiania kodowania, zobacz [Tworzenie danych wejściowych zadania z adresu URL https](./job-input-from-http-how-to.md) lub [Tworzenie danych wejściowych zadania z pliku lokalnego](./job-input-from-local-file-how-to.md).
* Jeśli następnie trzeba ponownie przesłać [zadanie](./transforms-jobs-concept.md) do innego regionu, można użyć `JobInputHttp` lub użyć, `Copy-Blob` Aby skopiować dane z kontenera zasobów źródłowych do kontenera zasobów w regionie alternatywnym.

### <a name="monitoring"></a>Monitorowanie

* Subskrybuj `JobStateChange` wiadomości na poszczególnych kontach za pośrednictwem Azure Event Grid.
    * [Rejestrowanie zdarzeń](./reacting-to-media-services-events.md) za pośrednictwem Azure Portal lub interfejsu wiersza polecenia (można go również uzyskać za pomocą zestawu SDK zarządzania Event Grid)
    * Użyj [zestawu SDK Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (który natywnie obsługuje zdarzenia Media Services).
    * Możesz również wykorzystać zdarzenia Event Grid za pośrednictwem Azure Functions.

    Więcej informacji:

    * Zobacz [przykład analizy audio](./transforms-jobs-concept.md) , który pokazuje, jak monitorować zadanie przy użyciu Azure Event Grid, w tym dodawanie rezerwy w przypadku opóźnienia komunikatów Azure Event Grid z jakiegoś powodu.
    * Zapoznaj się ze [schematami Azure Event Grid Media Services zdarzeń](./media-services-event-schemas.md).

* Podczas tworzenia [zadania](./transforms-jobs-concept.md):
    * Losowo wybierz konto z listy obecnie używanych kont (Ta lista będzie zazwyczaj zawierać oba konta, ale w przypadku wykrycia problemów może ono zawierać tylko jedno konto). Jeśli lista jest pusta, Zgłoś alert, aby operator mógł zbadać.
    * Utwórz rekord, aby śledzić każde zadanie numerów porządkowych określających oraz używane region/konto.
* Gdy `JobStateChange` program obsługi otrzyma powiadomienie o osiągnięciu zaplanowanego stanu zadania, należy zarejestrować czas wejścia w stan zaplanowany oraz używany region/konto.
* Gdy `JobStateChange` program obsługi pobierze powiadomienie, że zadanie osiągnęło stan przetwarzania, Oznacz rekord dla zadania jako przetwarzanie i zanotuj czas, w którym wprowadza stan przetwarzania.
* Gdy `JobStateChange` program obsługi pobierze powiadomienie, że zadanie osiągnęło stan końcowy (zakończono/zmieniono błąd/anulowano), Oznacz rekord dla zadania odpowiednio.
* Oddzielny proces, który okresowo przegląda rekordy zadań
    * Jeśli masz zadania w stanie zaplanowanym, które nie zostały zaawansowane do stanu przetwarzania w rozsądnym czasie dla danego regionu, Usuń ten region z listy obecnie używanych kont. W zależności od wymagań firmy można zdecydować, aby anulować te zadania od razu i ponownie przesłać je do innego regionu. Możesz też udostępnić im więcej czasu, aby przejść do następnego stanu.
    * Jeśli region został usunięty z listy kont, Monitoruj go pod kątem odzyskiwania przed dodaniem go z powrotem do listy. Kondycja regionalna może być monitorowana za pośrednictwem istniejących zadań w regionie (jeśli nie zostały anulowane i ponownie przesłane), przez dodanie konta z powrotem do listy po upływie okresu oraz przez operatorów monitorujących usługę Azure Communications o awarii, która może mieć wpływ na Azure Media Services.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [przykładami kodu](/samples/browse/?products=azure-media-services)