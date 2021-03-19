---
title: Nagrywanie wideo — Azure
description: W kontekście systemu zarządzania wideo dla kamer CCTV nagranie wideo odnosi się do procesu przechwytywania wideo z kamer i rejestrowania go do wyświetlania za pośrednictwem aplikacji mobilnych i przeglądarki. Nagrywanie wideo można klasyfikować jako ciągłe nagrywanie filmów wideo i rejestrowanie wideo oparte na zdarzeniach.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84260946"
---
# <a name="video-recording"></a>Nagrywanie wideo

W kontekście systemu zarządzania wideo dla kamer CCTV nagranie wideo odnosi się do procesu przechwytywania wideo z kamer i rejestrowania go do wyświetlania za pośrednictwem aplikacji mobilnych i przeglądarki. Nagrywanie wideo można klasyfikować jako ciągłe nagrywanie filmów wideo i rejestrowanie wideo oparte na zdarzeniach. 

## <a name="continuous-video-recording"></a>Ciągłe nagrywanie wideo  

Stałe nagrywanie wideo (CVR) odnosi się do procesu ciągłego rejestrowania całego wideo przechwytywanego ze źródła wideo. CVR gwarantuje, że wszystkie filmy wideo są dostępne (podyktowane [zasadami rejestrowania](#recording-policy)) do analizowania i/lub inspekcji w późniejszym czasie.

## <a name="event-based-video-recording"></a>Nagrywanie wideo oparte na zdarzeniach  

Nagrywanie wideo oparte na zdarzeniach (EVR) odnosi się do procesu nagrywania wideo wyzwalanego przez zdarzenie. Dane zdarzenie może pochodzić z powodu przetwarzania samego sygnału wideo lub z niezależnego źródła (na przykład z czujnika drzwi). Rejestrowanie wideo oparte na zdarzeniach (EVR) może powodować oszczędności magazynu, ale wymaga dodatkowych składników, które generują zdarzenia i wyzwalają nagranie wideo (zgodnie ze wstępnie zdefiniowanymi zasadami). Innymi słowy, EVR wymaga podejmowania dodatkowych decyzji dotyczących zdarzeń, które powinny wyzwalać nagranie wideo, oraz zasad skojarzonych z nagrywaniem wideo (nazywanego również zasadami rejestrowania). Przykładem zasad może być coś: nagrywanie filmu wideo przez 2 minuty, począwszy od 30 sekund przed czasem zdarzenia. Zdarzenia, które mogą pochodzić z powodu przetwarzania wideo w aparacie. Przykładowo kilka kamer obsługuje generowanie zdarzeń sygnału wykrywania ruchu, gdy ruch zostanie wykryty w ramach wstępnie skonfigurowanej strefy interesującej w okienku ekranu aparatu. Zdarzenia mogą być również generowane przez przetwarzanie wideo na innym urządzeniu, które przechwytuje wideo z aparatu i analizuje je przy użyciu prostych technik przetwarzania obrazu lub zaawansowanych modeli uczenia maszynowego. 

## <a name="choosing-recording-modes"></a>Wybieranie trybów rejestrowania  

Wybór użycia CVR lub EVR zależy od celów firmy. Funkcja analizy filmów wideo na żywo na IoT Edge zapewnia możliwości platformy zarówno dla CVR, jak i EVR. Więcej informacji na ten temat można znaleźć w artykułach scenariusza [CVR](continuous-video-recording-concept.md) i [EVR](event-based-video-recording-concept.md) .

## <a name="recording-policy"></a>Rejestrowanie zasad  

Zasady rejestrowania odwołują się do zasad, które określają czas rozpoczęcia/zakończenia rejestrowania (w przypadku EVR), czas trwania rejestrowania i usuwanie rejestrowania. Rejestrowanie zasad pozwala zrównoważyć koszt magazynu z wymaganiami biznesowymi. Rejestrowanie zasad różni się między CVR i EVR. W przypadku CVR zasady rejestrowania określają, ile dni wideo powinno być przechowywane (na przykład w ciągu ostatnich 7 dni). W przypadku EVR zasady rejestrowania definiują, kiedy nagrywanie powinno zacząć się i kończyć wraz z czasem trwania filmu wideo. Więcej informacji na ten temat można znaleźć w artykułach scenariusza [CVR](continuous-video-recording-concept.md) i [EVR](event-based-video-recording-concept.md) .

## <a name="next-steps"></a>Następne kroki

* [Wykrywanie ruchu, nagrywanie klipów wideo na platformie Azure Media Services](detect-motion-record-video-clips-media-services-quickstart.md)
* [Wykrywaj ruch, nagrywaj klipy wideo na urządzeniach brzegowych](detect-motion-record-video-clips-edge-devices-quickstart.md)

