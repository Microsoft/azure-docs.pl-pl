---
title: Azure Media Services kodowanie wysokiej dostępności
description: Dowiedz się, jak przełączyć się w tryb failover na pomocnicze konto Media Services, jeśli wystąpi awaria lub niepowodzenie centrum danych.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78934198"
---
# <a name="media-services-high-availability-encoding"></a>Media Services kodowanie wysokiej dostępności 

Usługa kodowania Azure Media Services jest regionalną platformą przetwarzania wsadowego i nie jest obecnie przeznaczona do wysokiej dostępności w ramach jednego regionu. Usługa kodowania obecnie nie zapewnia natychmiastowej pracy awaryjnej usługi, jeśli istnieje regionalna awaria centrum danych lub niepowodzenie podstawowego składnika lub usług zależnych (takich jak Storage, SQL). W tym artykule opisano sposób wdrażania Media Services w celu utrzymania architektury wysokiej dostępności z trybem failover i zapewnienia optymalnej dostępności aplikacji.

Postępując zgodnie z wytycznymi i najlepszymi rozwiązaniami opisanymi w artykule, należy obniżyć ryzyko błędów, opóźnień i skrócić czas odzyskiwania w przypadku wystąpienia awarii w jednym regionie.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Jak utworzyć system kodowania między różnymi regionami

* [Utwórz](create-account-cli-how-to.md) co najmniej dwa konta Azure Media Services.

    Te dwa konta muszą znajdować się w różnych regionach. Aby uzyskać więcej informacji, zobacz [regiony, w których jest wdrażana usługa Azure Media Services](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Przekaż multimedia do tego samego regionu, w którym planujesz przesłać zadanie. Aby uzyskać więcej informacji o sposobie uruchamiania kodowania, zobacz [Tworzenie danych wejściowych zadania z adresu URL https](job-input-from-http-how-to.md) lub [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md).

    Jeśli następnie trzeba ponownie przesłać [zadanie](transforms-jobs-concept.md) do innego regionu, możesz użyć JobInputHttp lub użyć polecenie [copy-BLOB](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) , aby skopiować dane z kontenera zasobów źródłowych do kontenera zasobów w regionie alternatywnym.
* Subskrybuj wiadomości JobStateChange na poszczególnych kontach za pośrednictwem Azure Event Grid. Aby uzyskać więcej informacji, zobacz:

    * [Przykład analizy audio](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) , który pokazuje, jak monitorować zadanie przy użyciu Azure Event Grid, w tym dodawania rezerwy w przypadku opóźnienia komunikatów Azure Event Grid z jakiegoś powodu.
    * [Schematy Azure Event Grid dla zdarzeń Media Services](media-services-event-schemas.md)
    * [Rejestrowanie zdarzeń za pośrednictwem Azure Portal lub interfejsu wiersza polecenia](reacting-to-media-services-events.md) (można go również uzyskać za pomocą zestawu SDK EventGrid Management)
    * [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (który obsługuje zdarzenia Media Services natywnie).

    Możesz również wykorzystać zdarzenia Event Grid za pośrednictwem Azure Functions.
* Podczas tworzenia [zadania](transforms-jobs-concept.md):

    * Losowo wybierz konto z listy obecnie używanych kont (Ta lista będzie zazwyczaj zawierać oba konta, ale w przypadku wykrycia problemów może ono zawierać tylko jedno konto). Jeśli lista jest pusta, Zgłoś alert, aby operator mógł zbadać.
    * Ogólne wskazówki to wymagana jedna [Jednostka zarezerwowana multimediów](media-reserved-units-cli-how-to.md) na [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (chyba że używasz [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) , gdzie 3 jednostki zarezerwowane multimediów na JobOutput).
    * Pobierz liczbę jednostek zarezerwowanych multimediów (MRUs) dla wybranego konta. Jeśli bieżąca liczba **jednostek zarezerwowanych multimediów** nie jest jeszcze wartością maksymalną, należy dodać liczbę MRUs wymaganych przez zadanie i zaktualizować usługę. Jeśli szybkość przesyłania zadań jest wysoka i często trwa badanie MRUs w celu znalezienia maksymalnego limitu, użyj rozproszonej pamięci podręcznej dla wartości z rozsądnym limitem czasu.
    * Zachowaj liczbę zadań numerów porządkowych określających.

* Gdy program obsługi JobStateChange pobiera powiadomienie o osiągnięciu zaplanowanego stanu zadania, należy zarejestrować czas wprowadzania stanu harmonogramu oraz używanego regionu/konta.
* Gdy program obsługi JobStateChange pobiera powiadomienie, że zadanie osiągnęło stan przetwarzania, Oznacz rekord dla zadania jako przetwarzanie.
* Gdy program obsługi JobStateChange pobiera powiadomienie, że zadanie osiągnęło stan zakończono/błąd/Anulowano, Oznacz rekord dla zadania jako końcowy i zmniejsz liczbę zadań numerów porządkowych określających. Pobierz liczbę jednostek zarezerwowanych multimediów dla wybranego konta i porównaj bieżący numer MRU z liczbą zadań numerów porządkowych określających. Jeśli liczba numerów porządkowych określających jest mniejsza niż liczba MRU, Zmniejsz ją i zaktualizuj usługę.
* Oddzielny proces, który okresowo przegląda rekordy zadań
    
    * Jeśli masz zadania w stanie zaplanowanym, które nie zostały zaawansowane do stanu przetwarzania w rozsądnym czasie dla danego regionu, Usuń ten region z listy obecnie używanych kont.  W zależności od wymagań firmy można zdecydować, aby anulować te zadania od razu i ponownie przesłać je do innego regionu. Możesz też udostępnić im więcej czasu, aby przejść do następnego stanu.
    * W zależności od liczby jednostek zarezerwowanych multimediów skonfigurowanych na koncie i szybkości przesyłania mogą istnieć również zadania w stanie umieszczonym w kolejce, gdy system nie został jeszcze pobrany do przetwarzania.  Jeśli lista zadań w stanie umieszczonych w kolejce rośnie poza akceptowalnym limitem w regionie, te zadania można anulować i przesłać do innego regionu.  Jednak może to być objaw braku wystarczającej liczby jednostek zarezerwowanych multimediów skonfigurowanych na koncie dla bieżącego obciążenia.  W razie potrzeby możesz zażądać wyższego przydziału jednostek zarezerwowanych multimediów w ramach pomocy technicznej systemu Azure.
    * Jeśli region został usunięty z listy kont, Monitoruj go pod kątem odzyskiwania przed dodaniem go z powrotem do listy.  Kondycja regionalna może być monitorowana za pośrednictwem istniejących zadań w regionie (jeśli nie zostały anulowane i ponownie przesłane), przez dodanie konta z powrotem do listy po upływie okresu oraz przez operatorów monitorujących usługę Azure Communications o awarii, która może mieć wpływ na Azure Media Services.
    
Jeśli okaże się, że liczba MRU jest migotanie w górę i w dół, Przenieś logikę zmniejszania do zadania okresowego. Zapoznaj się z logiką przesyłania przed zadaniami Porównaj liczbę numerów porządkowych określających z bieżącą liczbą MRU, aby sprawdzić, czy wymaga ona aktualizacji MRUs.

## <a name="next-steps"></a>Następne kroki

* [Kompiluj strumieniowe przesyłanie wideo na żądanie obejmujące wiele regionów](media-services-high-availability-streaming.md)
* Zapoznaj się z [przykładami kodu](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
