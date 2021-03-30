---
title: Co to jest usługa Azure SignalR Service?
description: Lepiej zrozumieć, jakie typowe scenariusze przypadków użycia mają korzystać z usługi Azure Signal, i Poznaj najważniejsze zalety usługi Azure sygnalizujące.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 92da400616ace9f24da121d81798d5ebd9222e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88921889"
---
# <a name="what-is-azure-signalr-service"></a>Co to jest usługa Azure SignalR Service?

Usługa Azure SignalR Service upraszcza proces dodawania funkcji internetowych w czasie rzeczywistym do aplikacji za pośrednictwem protokołu HTTP. Ta funkcja w czasie rzeczywistym umożliwia usłudze wypychanie aktualizacji zawartości do połączonych klientów, takich jak pojedyncza strona internetowa lub aplikacja mobilna. W związku z tym klienci otrzymują aktualizacje bez konieczności sondowania serwera czy przesyłania nowych żądań HTTP o aktualizacje.


Ten artykuł zawiera omówienie usługi Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Do czego służy usługa Azure SignalR Service?

Każdy scenariusz wymagający wypychania danych z serwera do klienta w czasie rzeczywistym może korzystać z usługi Azure Signal Service.

Tradycyjne funkcje w czasie rzeczywistym, które często wymagają sondowania z serwera, mogą również korzystać z usługi Azure Signal Service.

Usługa Azure Signal Service została użyta w wielu branżach, dla dowolnego typu aplikacji, który wymaga aktualizacji zawartości w czasie rzeczywistym. Przedstawiono kilka przykładów, które są przydatne do korzystania z usługi Azure Signal Service:

* **Aktualizacje danych o wysokiej częstotliwości:** gry, głosowania, sondowanie, aukcja.
* **Pulpity nawigacyjne i monitorowanie:** firmowy pulpit nawigacyjny, dane finansów finansowych, błyskawiczna aktualizacja sprzedaży, tablica lidera gier w ramach gry i monitorowanie IoT.
* **Czat:** pokój czatu na żywo, czat bot, pomoc techniczna dla klientów w czasie rzeczywistym, asystent zakupów w trakcie pracy, Messenger, rozmowa w grach i tak dalej.
* **Lokalizacja w czasie rzeczywistym na mapie:** śledzenie logistyczne, śledzenie stanu dostarczania, aktualizacje stanu transportu, aplikacje GPS.
* **Planowane reklamy w czasie rzeczywistym:** spersonalizowane reklamy i oferty wypychane w czasie rzeczywistym, interaktywne reklamy.
* **Aplikacje do współpracy:** Współtworzenie, aplikacje do tablic i oprogramowanie do spotkań zespołu.
* **Powiadomienia wypychane:** sieć społecznościowa, poczta e-mail, gra, alert dotyczący podróży.
* **Emisja w czasie rzeczywistym:** emisja audio/wideo, Live Caption, tłumaczenie, zdarzenia/wiadomości — emisje.
* **Urządzenia IoT i połączone:** metryki IoT w czasie rzeczywistym, zdalne sterowanie, stan w czasie rzeczywistym i śledzenie lokalizacji.
* **Automatyzacja:** wyzwalacz czasu rzeczywistego z zdarzeń nadrzędnych.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Jakie korzyści wiążą się z użyciem usługi Azure Signal Service?

**Na podstawie standardu:**

Usługa SignalR udostępnia abstrakcję szeregu technik służących do tworzenia aplikacji internetowych działających w czasie rzeczywistym. [Protokół WebSockets](https://wikipedia.org/wiki/WebSocket) stanowi optymalną metodę transportu, ale inne techniki, takie jak [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) i Long Polling są używane, gdy nie są dostępne inne opcje. Biblioteka SignalR automatycznie wykrywa i inicjuje odpowiednią metodę transportu na podstawie funkcji obsługiwanych przez serwer i klienta.

**Obsługa natywnych ASP.NET Core:**

Usługa sygnalizująca zapewnia natywne środowisko programistyczne z użyciem ASP.NET Core i ASP.NET. Tworzenie nowej aplikacji sygnalizującej za pomocą usługi sygnalizującej lub Migrowanie z istniejącej aplikacji sygnalizującej do usługi sygnalizującej wymaga minimalnego wysiłku.
Usługa sygnalizująca obsługuje również nową funkcję ASP.NET Core po stronie serwera.

**Szeroka obsługa klientów:**

Usługa sygnalizująca współpracuje z szerokim zakresem klientów, takich jak przeglądarki sieci Web, aplikacje mobilne, aplikacje mobilne, proces serwera, urządzenia IoT i konsole gier. Usługa sygnalizująca oferuje zestawy SDK w różnych językach. Oprócz natywnych zestawów SDK ASP.NET Core lub ASP.NET C#, usługa sygnalizująca udostępnia również zestaw SDK klienta języka JavaScript, umożliwiając klientom sieci Web i wiele platform JavaScript. Zestaw SDK klienta Java jest również obsługiwany w przypadku aplikacji Java, w tym aplikacji natywnych systemu Android. Usługa sygnalizująca obsługuje interfejs API REST oraz bezserwerowe integrację z Azure Functions i Event Grid.

**Obsługa połączeń klientów na dużą skalę:**

Usługa sygnalizująca została zaprojektowana na potrzeby aplikacji w czasie rzeczywistym na dużą skalę. Usługa sygnalizująca umożliwia współpracę wielu wystąpień w celu skalowania do milionów połączeń klientów. Usługa obsługuje również wiele regionów globalnych na potrzeby fragmentowania, wysokiej dostępności lub odzyskiwania po awarii.

**Usuń obciążenie do sygnału samoobsługowego:**

W porównaniu z nieobsługiwanymi aplikacjami sygnalizującymi, przełączenie do usługi sygnalizującej spowoduje usunięcie konieczności zarządzania płaszczyznami wstecznymi, które obsługują skale i połączenia klientów. W pełni zarządzana usługa upraszcza również aplikacje sieci Web i zapisuje koszt hostingu. Usługa sygnalizująca oferuje globalne, ogólnoświatowe i światowe klasy centrum danych i sieć, skaluje się do milionów połączeń, gwarantuje umowę SLA, a jednocześnie zapewnia zgodność i bezpieczeństwo platformy Azure w warstwie Standardowa.

![Zarządzana usługa sygnalizująca](./media/signalr-overview/managed-signalr-service.png)

**Zaawansowane interfejsy API oferują różne wzorce obsługi komunikatów:**

Usługa sygnalizująca umożliwia serwerowi wysyłanie komunikatów do określonego połączenia, wszystkich połączeń lub podzbioru połączeń należących do określonego użytkownika lub umieszczonych w dowolnej grupie.

## <a name="how-to-use-azure-signalr-service"></a>Jak używać usługi Azure SignalR Service

Istnieje wiele różnych sposobów programowania w usłudze Azure Signal Service, ponieważ niektóre przykłady zostały wymienione tutaj:

- **[Skalowanie aplikacji SignalR platformy ASP.NET Core](signalr-concept-scale-aspnet-core.md)** — integracja usługi Azure SignalR Service z aplikacją SignalR platformy ASP.NET Core w celu skalowania do setek tysięcy połączeń.
- **[Tworzenie bezserwerowych aplikacji w czasie rzeczywistym](signalr-concept-azure-functions.md)** — wykorzystanie integracji usługi Azure Functions z usługą Azure SignalR Service do tworzenia bezserwerowych aplikacji w czasie rzeczywistym w językach takich jak JavaScript, C# i Java.
- **[Wysyłanie komunikatów z serwera do klientów za pośrednictwem interfejsu API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** — usługa Azure SignalR Service udostępnia interfejs API REST, aby umożliwić aplikacjom wysyłanie komunikatów do połączonych klientów przy użyciu usługi SignalR Service, w dowolnym języku programowania obsługiwanym przez REST.
