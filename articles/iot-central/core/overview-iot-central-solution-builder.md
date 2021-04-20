---
title: Tworzenie rozwiązań dla Azure IoT Central | Microsoft Docs
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT. Ten artykuł zawiera omówienie tworzenia zintegrowanych rozwiązań za pomocą IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: e762b8c2e2d7f72b89629c520560b205cedcd036
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728561"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central przewodnik konstruktora rozwiązań

*Ten artykuł dotyczy konstruktorów rozwiązań.*

Aplikacja IoT Central umożliwia monitorowanie milionów urządzeń i zarządzanie nimi w całym cyklu życia. Ten przewodnik jest dla konstruktorów rozwiązań, którzy używają IoT Central do tworzenia zintegrowanych rozwiązań. Aplikacja IoT Central umożliwia zarządzanie urządzeniami, analizowanie telemetrii urządzeń i integrację z innymi usługami back-end.

Konstruktor rozwiązań:

- Konfiguruje pulpity nawigacyjne i widoki w IoT Central internetowym.
- Używa wbudowanych reguł i narzędzi analitycznych do uzyskania szczegółowych informacji biznesowych z połączonych urządzeń.
- Korzysta z możliwości eksportowania danych i reguł w celu IoT Central z innymi usługami back-end.

## <a name="configure-dashboards-and-views"></a>Konfigurowanie pulpitów nawigacyjnych i widoków

Aplikacja IoT Central może mieć co najmniej jeden pulpit nawigacyjny, za pomocą których operatorzy mogą wyświetlać aplikację i wchodzić z nimi w interakcje. Konstruktor rozwiązań może dostosować domyślny pulpit nawigacyjny i utworzyć wyspecjalizowane pulpity nawigacyjne:

- Aby wyświetlić kilka przykładów dostosowanych pulpitów nawigacyjnych, zobacz [Szablony ukierunkowane na branżę.](concepts-app-templates.md#industry-focused-templates)
- Aby dowiedzieć się więcej na temat pulpitów nawigacyjnych, zobacz [Tworzenie wielu](howto-create-personal-dashboards.md) pulpitów nawigacyjnych i zarządzanie nimi oraz [Konfigurowanie pulpitu nawigacyjnego aplikacji.](howto-add-tiles-to-your-dashboard.md)

Gdy urządzenie łączy się z IoT Central, jest skojarzone z szablonem urządzenia dla typu urządzenia. Szablon urządzenia ma dostosowywalne widoki, których operator używa do zarządzania poszczególnymi urządzeniami. Jako deweloper rozwiązań możesz tworzyć i dostosowywać dostępne widoki dla typu urządzenia. Aby dowiedzieć się więcej, zobacz [Dodawanie widoków.](howto-set-up-template.md#add-views)

## <a name="use-built-in-rules-and-analytics"></a>Korzystanie z wbudowanych reguł i analiz

Deweloper rozwiązania może dodać do aplikacji IoT Central reguły, które uruchamiają dostosowywalne akcje. Reguły oceniają warunki na podstawie danych pochodzących z urządzenia, aby określić, kiedy należy uruchomić akcję. Aby dowiedzieć się więcej na temat reguł, zobacz:

- [Samouczek: Tworzenie reguły i konfigurowanie powiadomień w aplikacji usługi Azure IoT Central](tutorial-create-telemetry-rules.md)
- [Tworzenie akcji elementów webhook dla reguł w usłudze Azure IoT Central](howto-create-webhooks.md)
- [Grupowanie wielu akcji do uruchomienia z co najmniej jednej reguły](howto-use-action-groups.md)

IoT Central ma wbudowane funkcje analityczne, których operator może użyć do analizowania danych przepływających z połączonych urządzeń. Aby dowiedzieć się więcej, zobacz [Jak używać analizy do analizowania danych urządzenia.](howto-create-analytics.md)

## <a name="integrate-with-other-services"></a>Integracja z innymi usługami

Konstruktor rozwiązań może korzystać z funkcji eksportowania danych i reguł w usłudze IoT Central do integracji z innymi usługami. Aby dowiedzieć się więcej, zobacz:

- [Eksportowanie danych IoT do miejsc docelowych w chmurze przy użyciu eksportu danych](howto-export-data.md)
- [Przekształcanie danych na IoT Central](howto-transform-data.md)
- [Integrowanie aplikacji w Azure IoT Central z innymi usługami w chmurze przy użyciu przepływów pracy](howto-configure-rules-advanced.md)
- [Rozszerzanie usługi Azure IoT Central o niestandardowe reguły przy użyciu usług Stream Analytics, Azure Functions i SendGrid](howto-create-custom-rules.md)
- [Rozszerzanie usługi Azure IoT Central o niestandardowe analizy przy użyciu usługi Azure Databricks](howto-create-custom-analytics.md)
- [Wizualizowanie i analizowanie Azure IoT Central danych na pulpicie nawigacyjnym Power BI nawigacyjnym](howto-connect-powerbi.md)

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej na temat korzystania z IoT Central, sugerowane następne kroki to wypróbowanie przewodników Szybki start, począwszy od tematu Tworzenie Azure IoT Central [aplikacji.](./quick-deploy-iot-central.md)
