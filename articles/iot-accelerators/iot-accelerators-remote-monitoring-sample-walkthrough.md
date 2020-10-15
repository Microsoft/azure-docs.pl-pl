---
title: Omówienie akceleratora rozwiązań zdalnego monitorowania — Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie niektórych najważniejszych elementów rozwiązania do monitorowania zdalnego, dzięki którym można zrozumieć, jak to działa.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: f9477b11b52431211dc91a0ea6ff0767a9d495be
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073601"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Omówienie akceleratora rozwiązania do monitorowania zdalnego

[Akcelerator rozwiązania](../iot-accelerators/about-iot-accelerators.md) do monitorowania zdalnego implementuje kompleksowe rozwiązanie do monitorowania dla wielu maszyn w zdalnych lokalizacjach. Rozwiązanie to łączy najważniejsze usługi platformy Azure, aby umożliwić ogólną implementację scenariusza biznesowego. Możesz go użyć jako punktu wyjścia dla Twojej własnej implementacji i je [dostosować](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md), aby spełniało Twoje własne określone wymagania biznesowe.

W tym artykule omówiono niektóre kluczowe elementy rozwiązania do monitorowania zdalnego, dzięki którym można zrozumieć, jak to działa. Ta wiedza ułatwi Ci:

* Rozwiązywanie problemów w rozwiązaniu.
* Planowanie sposobu dostosowywania rozwiązania, aby spełniało Twoje wymagania.
* Projektowanie własnego rozwiązania IoT korzystającego z usług Azure.

Kod akceleratora rozwiązania do monitorowania zdalnego jest dostępny w witrynie GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Architektura logiczna

Na poniższym diagramie przedstawiono składniki logiczne akceleratora rozwiązania do monitorowania zdalnego przełożonego na [architekturę IoT](../iot-fundamentals/iot-introduction.md):

![Architektura logiczna](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Dlaczego mikrousługi?

Architektura chmury została rozwinięta od czasu udostępnienia pierwszego akceleratora rozwiązań przez firmę Microsoft. [Mikrousługi](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) okazały się sprawdzoną metodą osiągnięcia skalowalności i elastyczności bez pogorszenia szybkości tworzenia oprogramowania. Kilka usług firmy Microsoft korzysta z tego wzorca architektury wewnętrznie z doskonałymi wynikami niezawodności i skalowalności. Zaktualizowane Akceleratory rozwiązań wprowadzają te informacje do ćwiczeń, dzięki czemu można z nich korzystać.

> [!TIP]
> Aby dowiedzieć się więcej na temat architektury mikrousług, zobacz [Architektura aplikacji .NET](https://www.microsoft.com/net/learn/architecture) i [Mikrousługi: rewolucja w aplikacjach wspierana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Łączność urządzeń

Rozwiązanie obejmuje następujące składniki w ramach łączności urządzenia w ramach architektury logicznej:

### <a name="real-devices"></a>Rzeczywiste urządzenia

Można połączyć rzeczywiste urządzenia z rozwiązaniem. Zachowanie symulowanych urządzeń można zaimplementować przy użyciu zestawów SDK urządzeń usługi Azure IoT.

Rzeczywiste urządzenia można udostępnić z poziomu pulpitu nawigacyjnego w portalu rozwiązania.

### <a name="device-simulation-microservice"></a>Mikrousługa symulacji urządzenia

Rozwiązanie obejmuje [mikrousługę symulacji urządzenia](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) , która umożliwia zarządzanie pulą symulowanych urządzeń z portalu rozwiązania w celu przetestowania kompleksowego przepływu w rozwiązaniu. Symulowane urządzenia:

* Generuj dane telemetryczne z urządzenia do chmury.
* Odpowiadanie na wywołania metody z chmury do urządzenia z IoT Hub.

Mikrousługa udostępnia RESTful punkt końcowy, aby można było tworzyć, uruchamiać i zatrzymywać symulacje. Każda symulacja składa się z zestawu urządzeń wirtualnych różnych typów, które wysyła telemetrię i reaguje na wywołania metod.

Symulowane urządzenia można udostępnić z poziomu pulpitu nawigacyjnego w portalu rozwiązania.

### <a name="iot-hub"></a>IoT Hub

Usługa [IoT Hub](../iot-hub/index.yml) pozyskuje dane telemetryczne wysyłane z rzeczywistych i symulowanych urządzeń do chmury. Usługa IoT Hub udostępnia dane telemetryczne dla usług w zapleczu rozwiązania IoT do przetwarzania.

Usługa IoT Hub w rozwiązaniu wykonuje ponadto następujące czynności:

* Obsługuje rejestr tożsamości przechowujący identyfikatory i klucze uwierzytelniania wszystkich urządzeń, które mogą nawiązywać połączenia z portalem.
* Wywołuje metody na urządzeniach w imieniu akceleratora rozwiązania.
* Obsługuje bliźniacze reprezentacje wszystkich zarejestrowanych urządzeń. W bliźniaczej reprezentacji urządzenia są przechowywane wartości właściwości zgłaszanych przez urządzenie. W bliźniaczej reprezentacji urządzenia są także przechowywane żądane właściwości ustawione w portalu rozwiązania, które mają zostać pobrane przez urządzanie podczas nawiązywania następnego połączenia.
* Planuje zadania ustawiania właściwości dla wielu urządzeń lub wywoływania metod na wielu urządzeniach.

## <a name="data-processing-and-analytics"></a>Przetwarzanie danych i analiza

Rozwiązanie obejmuje następujące składniki w części przetwarzanie danych i analiza architektury logicznej:

### <a name="iot-hub-manager-microservice"></a>Mikrousługa IoT Hub Manager

Rozwiązanie obejmuje [mikrousługę IoT Hub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) do obsługi interakcji z Centrum IoT, takich jak:

* Tworzenie urządzeń IoT i zarządzanie nimi.
* Zarządzanie bliźniaczych reprezentacji urządzeń.
* Wywoływanie metod na urządzeniach.
* Zarządzanie poświadczeniami IoT.

Ta usługa uruchamia również IoT Hub zapytania, aby pobrać urządzenia należące do grup zdefiniowanych przez użytkownika.

Mikrousługa udostępnia RESTful punkt końcowy do zarządzania urządzeniami i bliźniaczych reprezentacji urządzeń, wywoływania metod i uruchamiania zapytań IoT Hub.

### <a name="device-telemetry-microservice"></a>Mikrousługa telemetrii urządzenia

[Mikrousługa telemetrii urządzenia](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) udostępnia punkt końcowy RESTful na potrzeby dostępu do odczytu do danych telemetrycznych urządzenia przechowywanych w Time Series Insights. Punkt końcowy RESTful umożliwia również wykonywanie operacji CRUD na zasadach i dostęp do odczytu/zapisu dla definicji alarmów z magazynu.

### <a name="storage-adapter-microservice"></a>Mikrousługa kart pamięci

[Mikrousługa karty magazynu](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) zarządza parami klucz-wartość, abstrakcją semantyki usługi magazynu i przedstawieniem prostego interfejsu do przechowywania danych dowolnego formatu przy użyciu Azure Cosmos DB.

Wartości są zorganizowane w kolekcje. Możesz korzystać z poszczególnych wartości lub pobrać całe kolekcje. Złożone struktury danych są serializowane przez klientów i zarządzane jako zwykły ładunek tekstu.

Usługa udostępnia punkt końcowy RESTful dla operacji CRUD w parach klucz-wartość. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Wdrożenia akceleratora rozwiązań używają [Azure Cosmos DB](../cosmos-db/index.yml) do przechowywania zasad, alertów, ustawień konfiguracji i wszystkich innych magazynów w chłodnym magazynie.

### <a name="azure-stream-analytics-manager-microservice"></a>Mikrousługa Azure Stream Analytics Manager

[Mikrousługa Azure Stream Analytics Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) zarządza zadaniami Azure Stream Analytics (ASA), w tym ustawiania ich konfiguracji, uruchamiania i zatrzymywania oraz monitorowania ich stanu.

Zadanie ASA jest obsługiwane przez dwa zestawy danych referencyjnych. Jeden zestaw danych definiuje reguły i jeden definiuje grupy urządzeń. Dane referencyjne reguł są generowane na podstawie informacji zarządzanych przez mikrousługę telemetrii urządzenia. Mikrousługa Azure Stream Analytics Manager przekształca reguły telemetrii w logikę przetwarzania strumienia.

Dane referencyjne grup urządzeń są używane do identyfikowania grupy reguł, które mają zostać zastosowane do przychodzącego komunikatu telemetrii. Grupy urządzeń są zarządzane przez mikrousługę konfiguracji i używają zapytań bliźniaczych dla usługi Azure IoT Hub Device.

Zadania ASA zapewniają dane telemetryczne z połączonych urządzeń, aby Time Series Insights do magazynu i analizy.

### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics

[Azure Stream Analytics](../stream-analytics/index.yml) to aparat przetwarzania zdarzeń, który umożliwia badanie dużych ilości danych przesyłanych strumieniowo z urządzeń.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](../time-series-insights/index.yml) przechowuje dane telemetryczne z urządzeń podłączonych do akceleratora rozwiązania. Umożliwia także wizualizację i wykonywanie zapytań dotyczących telemetrii urządzenia w interfejsie użytkownika sieci Web rozwiązania.

### <a name="configuration-microservice"></a>Mikrousługa konfiguracji

[Mikrousługa konfiguracji](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) udostępnia punkt końcowy RESTful dla operacji CRUD w grupach urządzeń, ustawieniach rozwiązania i ustawieniach użytkownika w akceleratorze rozwiązania. Współpracuje z mikrousługą karty pamięci, aby zachować dane konfiguracyjne.

### <a name="authentication-and-authorization-microservice"></a>Mikrousługa uwierzytelniania i autoryzacji

[Mikrousługa uwierzytelniania i autoryzacji](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) zarządza użytkownikami uprawnionymi do uzyskiwania dostępu do akceleratora rozwiązań. Zarządzanie użytkownikami można wykonać przy użyciu dowolnego dostawcy usługi tożsamości, który obsługuje [OpenID Connect Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Wdrożenia akceleratora rozwiązań używają [Azure Active Directory](../active-directory/index.yml) jako dostawcy połączenia OpenID Connect. Azure Active Directory przechowuje informacje o użytkowniku i udostępnia certyfikaty do weryfikowania podpisów tokenów JWT.

## <a name="presentation"></a>Prezentacja

Rozwiązanie obejmuje następujące składniki w części Prezentacja architektury logicznej:

[Interfejs użytkownika sieci Web to aplikacja JavaScript do reagowania](https://github.com/Azure/pcs-remote-monitoring-webui). Aplikacja:

* Używa tylko do obsługi skryptów języka JavaScript i działa wyłącznie w przeglądarce.
* Jest stylem CSS.
* Współdziała z przychodzącymi mikrousługami za pomocą wywołań AJAX.

Interfejs użytkownika przedstawia wszystkie funkcje akceleratora rozwiązań i współdziała z innymi mikrousługami, takimi jak:

* Mikrousługa uwierzytelniania i autoryzacji do ochrony danych użytkownika.
* Mikrousługa IoT Hub Manager, aby wyświetlić listę urządzeń IoT i zarządzać nimi.

Interfejs użytkownika integruje Eksploratora Azure Time Series Insights, aby umożliwić wykonywanie zapytań i analizy danych telemetrycznych urządzeń.

Mikrousługa konfiguracji umożliwia interfejsowi użytkownika przechowywanie i pobieranie ustawień konfiguracji.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz poznać kod źródłowy i dokumentację dla deweloperów, Zacznij od jednego z dwóch repozytoriów usługi GitHub:

* [Akcelerator rozwiązania do zdalnego monitorowania za pomocą usługi Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Akcelerator rozwiązania do zdalnego monitorowania za pomocą usługi Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Szczegółowe diagramy architektury rozwiązań:
* [Akcelerator rozwiązania dla architektury zdalnego monitorowania](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Aby uzyskać więcej informacji o pojęciach dotyczących akceleratora rozwiązania do monitorowania zdalnego, zobacz [Dostosowywanie akceleratora rozwiązania](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).