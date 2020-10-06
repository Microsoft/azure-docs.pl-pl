---
title: Azure telemetria usługi Application Insights Data Model — kontekst telemetrii | Microsoft Docs
description: Application Insights model danych kontekstu telemetrii
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: cbd717243ed35fc90772cb2b727f7670bb77ee70
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757303"
---
# <a name="telemetry-context-application-insights-data-model"></a>Kontekst telemetrii: Application Insights model danych

Każdy element telemetrii może mieć jednoznacznie wpisane pola kontekstowe. Każde pole umożliwia określony scenariusz monitorowania. Za pomocą kolekcji właściwości niestandardowych można przechowywać niestandardowe lub specyficzne dla aplikacji informacje kontekstowe.


## <a name="application-version"></a>Wersja aplikacji

Informacje w polach kontekstu aplikacji zawsze dotyczą aplikacji wysyłającej dane telemetryczne. Wersja aplikacji służy do analizowania zmian trendów w zachowaniu aplikacji i jej korelacji z wdrożeniami.

Maksymalna długość: 1024


## <a name="client-ip-address"></a>Adres IP klienta

Adres IP urządzenia klienckiego. Obsługiwane są protokoły IPv4 i IPv6. Gdy dane telemetryczne są wysyłane z usługi, kontekst lokalizacji dotyczy użytkownika, który zainicjował operację w usłudze. Application Insights Wyodrębnij informacje o lokalizacji geograficznej z adresu IP klienta, a następnie obcinanie go. Dlatego nie można używać protokołu IP klienta jako informacji umożliwiających identyfikację użytkownika końcowego. 

Maksymalna długość: 46


## <a name="device-type"></a>Typ urządzenia

Pierwotnie to pole było używane do wskazywania typu urządzenia, z którego korzysta użytkownik końcowy aplikacji. Obecnie używane głównie do rozróżnienia danych telemetrycznych języka JavaScript z typem urządzenia "Browser" z telemetrii po stronie serwera z typem urządzenia "PC".

Maksymalna długość: 64


## <a name="operation-id"></a>Identyfikator operacji

Unikatowy identyfikator operacji głównej. Ten identyfikator umożliwia grupowanie danych telemetrycznych w wielu składnikach. Aby uzyskać szczegółowe informacje, zobacz [korelacja telemetrii](./correlation.md) . Identyfikator operacji jest tworzony przez żądanie lub widok strony. Wszystkie inne dane telemetryczne ustawiają to pole na wartość dla zawierającego żądanie lub widok strony. 

Maksymalna długość: 128


## <a name="parent-operation-id"></a>Identyfikator operacji nadrzędnej

Unikatowy identyfikator nadrzędnego elementu telemetrii. Aby uzyskać szczegółowe informacje, zobacz [korelacja telemetrii](./correlation.md) .

Maksymalna długość: 128


## <a name="operation-name"></a>Nazwa operacji

Nazwa (Grupa) operacji. Nazwa operacji jest tworzona przez żądanie lub widok strony. Wszystkie inne elementy telemetrii ustawiają to pole na wartość dla zawierającego żądanie lub widok strony. Nazwa operacji jest używana do znajdowania wszystkich elementów telemetrii dla grupy operacji (na przykład "GET Home/index"). Ta właściwość kontekstu służy do udzielania odpowiedzi na pytania, takie jak typowe wyjątki zgłoszone na tej stronie.

Maksymalna długość: 1024


## <a name="synthetic-source-of-the-operation"></a>Źródło syntetyczne operacji

Nazwa źródła syntetycznego. Niektóre dane telemetryczne z aplikacji mogą reprezentować ruch syntetyczny. Może to być indeksowanie witryny sieci Web, testy dostępności witryny lub ślady z bibliotek diagnostycznych, takie jak Application Insights SDK.

Maksymalna długość: 1024


## <a name="session-id"></a>Identyfikator sesji

Identyfikator sesji — wystąpienie interakcji użytkownika z aplikacją. Informacje w kontekście sesji zawsze dotyczą użytkownika końcowego. Gdy dane telemetryczne są wysyłane z usługi, kontekst sesji dotyczy użytkownika, który zainicjował operację w usłudze.

Maksymalna długość: 64


## <a name="anonymous-user-id"></a>Identyfikator użytkownika anonimowego

Identyfikator użytkownika anonimowego. Reprezentuje użytkownika końcowego aplikacji. Gdy dane telemetryczne są wysyłane z usługi, kontekst użytkownika dotyczy użytkownika, który zainicjował operację w usłudze.

[Próbkowanie](./sampling.md) jest jedną z technik, aby zminimalizować ilość zbieranych danych telemetrycznych. Algorytm próbkowania próbuje wykonać próbkowanie do lub wypróbowanie wszystkich skorelowanych danych telemetrycznych. Identyfikator użytkownika anonimowego jest używany do generowania wyników próbkowania. Identyfikator użytkownika anonimowego powinien być wystarczająco losowy. 

Użycie anonimowego identyfikatora użytkownika do przechowywania nazwy użytkownika jest nieprawidłowym polem. Użyj identyfikatora uwierzytelnionego użytkownika.

Maksymalna długość: 128


## <a name="authenticated-user-id"></a>Identyfikator uwierzytelnionego użytkownika

Identyfikator uwierzytelnionego użytkownika. W przeciwieństwie do identyfikatora użytkownika anonimowego, to pole reprezentuje użytkownika z przyjazną nazwą. Ponieważ informacje OSOBowe nie są zbierane domyślnie przez większość SDK.

Maksymalna długość: 1024


## <a name="account-id"></a>Identyfikator konta

W aplikacjach z wieloma dzierżawcami jest to identyfikator lub nazwa konta, z którym użytkownik działa. Przykłady mogą być IDENTYFIKATORem subskrypcji dla Azure Portal lub nazwy blogu dla platformy do obsługi blogów.

Maksymalna długość: 1024


## <a name="cloud-role"></a>Rola w chmurze

Nazwa roli, do której należy aplikacja. Mapuje bezpośrednio na nazwę roli na platformie Azure. Można również użyć do rozróżniania mikrousług, które są częścią pojedynczej aplikacji.

Maksymalna długość: 256


## <a name="cloud-role-instance"></a>Wystąpienie roli w chmurze

Nazwa wystąpienia, w którym jest uruchomiona aplikacja. Nazwa komputera dla lokalnego, nazwa wystąpienia platformy Azure.

Maksymalna długość: 256


## <a name="internal-sdk-version"></a>Wewnętrzny: wersja zestawu SDK

Wersja zestawu SDK. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md) .

Maksymalna długość: 64


## <a name="internal-node-name"></a>Wewnętrzny: nazwa węzła

To pole reprezentuje nazwę węzła używanego na potrzeby rozliczeń. Służy do zastępowania standardowego wykrywania węzłów.

Maksymalna długość: 256


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak rozciągnąć i filtrować dane telemetryczne](./api-filtering-sampling.md).
- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- Sprawdź [konfigurację](./configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)kolekcji właściwości kontekstu standardowego.

