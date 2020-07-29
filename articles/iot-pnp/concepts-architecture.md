---
title: Architektura Plug and Play IoT | Microsoft Docs
description: Jako Konstruktor rozwiązań można zrozumieć najważniejsze elementy architektury Plug and Play IoT.
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f2d9407d9c7172839027e2650486695244f67889
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352344"
---
# <a name="iot-plug-and-play-preview-architecture"></a>Architektura Plug and Play IoT w wersji zapoznawczej

Wersja zapoznawcza Plug and Play IoT umożliwia konstruktorom rozwiązań integrację urządzeń inteligentnych z ich rozwiązaniami bez konieczności ręcznej konfiguracji. Podstawą Plug and Play IoT jest _model_ urządzeń, który opisuje możliwości urządzenia w aplikacji z obsługą Plug and Play IoT. Ten model jest strukturalny jako zestaw interfejsów, które definiują:

- _Właściwości_ reprezentujące stan tylko do odczytu lub do zapisu urządzenia lub innej jednostki. Na przykład numer seryjny urządzenia może być właściwością tylko do odczytu, a docelowa temperatura w termostatie może być właściwością umożliwiającą zapis.
- Dane _telemetryczne_ , które są emitowane przez urządzenie, niezależnie od tego, czy dane są regularnym strumieniem odczytów czujnika, okazjonalnym błędem czy komunikatem informacyjnym.
- _Polecenia_ opisujące funkcję lub operację, którą można wykonać na urządzeniu. Na przykład polecenie może ponownie uruchomić bramę lub zrobić zdjęcie przy użyciu zdalnej kamery.

Każdy model i interfejs ma unikatowy identyfikator.

Na poniższym diagramie przedstawiono kluczowe elementy rozwiązania IoT Plug and Play:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="Architektura Plug and Play IoT":::

## <a name="model-repository"></a>Repozytorium modelu

[Repozytorium modelu](./concepts-model-repository.md) jest magazynem dla definicji modelu i interfejsu. Modele i interfejsy można definiować przy użyciu [języka Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl).

Interfejs użytkownika sieci Web umożliwia zarządzanie modelami i interfejsami.

Repozytorium modelu używa RBAC, aby umożliwić ograniczenie dostępu do definicji interfejsu.

## <a name="devices"></a>Urządzenia

Konstruktor urządzeń implementuje kod do uruchomienia na urządzeniu inteligentnym IoT przy użyciu jednego z [zestawów SDK urządzeń usługi Azure IoT](./libraries-sdks.md). Zestawy SDK urządzeń pomagają konstruktorowi urządzeń:

- Bezpieczne nawiązywanie połączenia z usługą IoT Hub.
- Zarejestruj urządzenie w usłudze IoT Hub i Ogłoś Identyfikator modelu, który identyfikuje kolekcję interfejsów implementowanych przez urządzenie.
- Zaktualizuj właściwości zdefiniowane w interfejsach DTDL, które implementuje urządzenie. Te właściwości są implementowane za pomocą Digital bliźniaczych reprezentacji, który zarządza synchronizacją w usłudze IoT Hub.
- Dodaj programy obsługi poleceń dla poleceń zdefiniowanych w interfejsach DTDL, które implementuje urządzenie.
- Wyślij dane telemetryczne do centrum IoT Hub.

## <a name="iot-hub"></a>Usługa IoT Hub

[IoT Hub](../iot-hub/about-iot-hub.md) to usługa hostowana w chmurze, która działa jako centralny centrum komunikatów do komunikacji dwukierunkowej między rozwiązaniem IoT i zarządzanymi urządzeniami.

Centrum IoT:

- Sprawia, że identyfikator modelu zaimplementowany przez urządzenie jest dostępne dla rozwiązania zaplecza.
- Utrzymuje cyfrowe sznurki skojarzone z każdym Plug and Playm urządzeniem podłączonym do centrum.
- Przekazuje strumienie telemetrii do innych usług w celu przetworzenia lub magazynowania.
- Kieruje do innych usług zdarzenia zmiany cyfrowych sznurów, aby umożliwić monitorowanie urządzeń.

## <a name="backend-solution"></a>Rozwiązanie zaplecza

Rozwiązanie zaplecza monitoruje i kontroluje połączone urządzenia, współpracując z bliźniaczych reprezentacji Digital w centrum IoT. Zaimplementuj rozwiązanie zaplecza przy użyciu jednego z zestawów SDK usługi. Aby poznać możliwości połączonego urządzenia, zaplecze rozwiązania:

1. Pobiera identyfikator modelu urządzenia zarejestrowanego w usłudze IoT Hub.
1. Używa identyfikatora modelu do pobrania definicji interfejsu z dowolnego repozytorium modeli.
1. Używa parsera modelu do wyodrębniania informacji z definicji interfejsu.

Rozwiązanie zaplecza może używać informacji z definicji interfejsu do:

- Odczytaj wartości właściwości raportowane przez urządzenia.
- Aktualizowanie właściwości do zapisu na urządzeniu.
- Wywoływanie poleceń zaimplementowanych przez urządzenie.
- Informacje o formacie telemetrii wysyłanej przez urządzenie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już masz Przegląd architektury rozwiązania IoT Plug and Play, kolejne kroki zawierają informacje na temat:

- [Repozytorium modelu](./concepts-model-repository.md)
- [Proces odnajdywania modelu](./concepts-model-discovery.md)
- [Opracowywanie Plug and Play IoT](./concepts-developer-guide.md)
