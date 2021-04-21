---
title: Omówienie przemysłowego IoT na platformie Azure
description: Ten artykuł zawiera omówienie przemysłowego IoT. Objaśnia ona łączność i składniki zabezpieczeń w sieci IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813276"
---
# <a name="what-is-industrial-iot-iiot"></a>Co to jest przemysłowe IoT (IIoT)?

![Przemysłowe IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure Industrial Internet rzeczy (IIoT) to pakiet modułów i usług platformy Azure, które integrują możliwości chmury z podłogami w sklepach przemysłowych i produkcyjnych. Korzystając ze standardowych w branży otwartych interfejsów, takich jak ujednolicona architektura [OPC UA (Open Platform Communications Unified Architecture),](https://opcfoundation.org/about/opc-technologies/opc-ua/)usługa Azure IIoT zapewnia możliwość integracji danych z zasobów i czujników — w tym tych, które już działają w fabryce — z chmurą platformy Azure. Dzięki temu dane w chmurze mogą być używane szybciej i elastyczniej jako informacje zwrotne do opracowywania transformacyjnych procesów biznesowych i przemysłowych.

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Odnajdywanie i rejestrowanie zasobów przemysłowych na platformie Azure oraz zarządzanie nimi

Usługa Azure Industrial IoT umożliwia operatorom zakładów odnajdywanie serwerów z obsługą OPC UA w sieci fabrycznej i rejestrowanie ich w Azure IoT Hub. Personel operacyjny może subskrybować i reagować na zdarzenia w fabryce z dowolnego miejsca na świecie, odbierać alerty i alarmy oraz reagować na nie w czasie rzeczywistym.

IIoT udostępnia zestaw mikrousług, które implementują funkcje OPC UA. Interfejsy API REST mikrousług odzwierciedlają usługi OPC UA po stronie brzegowej. Są one zabezpieczone przy użyciu uwierzytelniania OAUTH i autoryzacji Azure Active Directory (AAD). Dzięki temu aplikacje w chmurze mogą przeglądać przestrzenie adresowe serwera lub zmienne odczytu/zapisu i wykonywać metody przy użyciu protokołu HTTPS i prostych ładunków JSON OPC UA. Usługi brzegowe są implementowane jako Azure IoT Edge i uruchamiane lokalnie. Mikrousługi w chmurze są implementowane jako mikrousługi ASP.NET z interfejsem REST i uruchamiane w zarządzanych usługach Azure Kubernetes Services lub autonomicznie w Azure App Service. W przypadku usług brzegowych i usług w chmurze technologia IIoT udostępnia wstępnie sbudowaną kontenery platformy Docker w Microsoft Container Registry (MCR), co usuwa ten krok dla klienta. Usługi brzegowe i usługi w chmurze są używane wzajemnie i muszą być używane razem. IIoT udostępnia również łatwe w użyciu skrypty wdrażania, które umożliwiają wdrożenie całej platformy za pomocą jednego polecenia.

Ponadto interfejsy API REST mogą być używane z dowolnym językiem programowania za pośrednictwem jego ujawnionej specyfikacji open API (Swagger). Oznacza to, że podczas integrowania usługi OPC UA z rozwiązaniami do zarządzania chmurą deweloperzy mogą wybrać technologię, która odpowiada ich umiejętnościom, zainteresowania i architekturze. Na przykład programista internetowy pełnego stosu, który opracowuje aplikację dla pulpitu nawigacyjnego alarmów i zdarzeń, może napisać logikę do reagowania na zdarzenia w języku JavaScript lub TypeScript bez konieczności stosowania zestawu SDK OPC UA, C, C++, Java lub C#.

## <a name="manage-certificates-and-trust-groups"></a>Zarządzanie certyfikatami i grupami zaufania

Usługa Azure Industrial IoT zarządza certyfikatami aplikacji OPC UA i listami zaufania maszyn i systemów sterowania w fabryce, aby zapewnić bezpieczeństwo komunikacji klienta OPC UA z serwerem. Ogranicza to, który klient może rozmawiać z którym serwerem. Magazyn kluczy prywatnych i podpisywanie certyfikatów jest wspierany przez program Azure Key Vault, który obsługuje zabezpieczenia sprzętowe (HSM).

## <a name="industrial-iot-components"></a>Przemysłowe składniki IoT

Rozwiązania Azure IIoT są zbudowane na podstawie określonych składników. Są to m.in. następujące.

- **Co najmniej jedna Azure IoT Hub.**
- **IoT Edge urządzeń.**
- **Przemysłowe moduły brzegowe.**

### <a name="iot-hub"></a>Usługa IoT Hub
[Azure IoT Hub]( działa jako centralne centrum komunikatów do bezpiecznej, dwukierunkowej komunikacji między dowolną aplikacją https://azure.microsoft.com/services/iot-hub/ IoT i urządzeniami, które zarządza. Jest to otwarta i elastyczna platforma jako usługa w chmurze (PaaS), która obsługuje zestawy SDK typu open source i wiele protokołów. 

Zbieranie danych przemysłowych i biznesowych na IoT Hub umożliwia bezpieczne przechowywanie danych, przeprowadzanie analiz biznesowych i dotyczących wydajności oraz generowanie na ich podstawie raportów. Można również stosować Microsoft Azure i narzędzia, takie jak [Power BI](https://powerbi.microsoft.com), do skonsolidowanych danych.

### <a name="iot-edge-devices"></a>IoT Edge urządzeń
Usługi [brzegowe są](https://azure.microsoft.com/services/iot-edge/) implementowane jako Azure IoT Edge i uruchamiane lokalnie. Mikrousługi w chmurze są implementowane jako mikrousługi ASP.NET z interfejsem REST i uruchamiane w zarządzanych usługach Azure Kubernetes Services lub autonomicznie w Azure App Service. W przypadku usług brzegowych i usług w chmurze firma Microsoft dostarcza wstępnie sbudowaną kontenery platformy Docker w Microsoft Container Registry (MCR), usuwając ten krok dla klienta. Usługi brzegowe i usługi w chmurze są wzajemnie używane i muszą być używane razem. Oferujemy również łatwe w użyciu skrypty wdrażania, które umożliwiają wdrożenie całej platformy za pomocą jednego polecenia.

Urządzenie IoT Edge składa się ze środowiska uruchomieniowego usługi Edge i modułów brzegowych.
- **Moduły usługi Edge** to kontenery platformy Docker, które są najmniejszą jednostką obliczeniową, na przykład Wydawca OPC i Bliźniacze reprezentacji OPC. 
- **Urządzenie brzegowe** służy do wdrażania takich modułów, które pełnią rolę między serwerem OPC UA a IoT Hub w chmurze.

### <a name="industrial-edge-modules"></a>Przemysłowe moduły brzegowe
- **Wydawca OPC:** wydawca OPC działa wewnątrz IoT Edge. Łączy się ona z serwerami OPC UA i publikuje dane telemetryczne zakodowane w formacie JSON z tych serwerów w formacie OPC UA "Pub/Sub" Azure IoT Hub. Można używać wszystkich protokołów transportu obsługiwanych przez zestaw SDK klienta Azure IoT Hub, np. HTTPS, AMQP i MQTT.
- **Bliźniacze reprezentacji OPC:** Bliźniacze reprezentacji OPC składa się z mikrousług, które używają Azure IoT Edge i IoT Hub do łączenia chmury z siecią fabryki. Bliźniaczą reprezentacji OPC zapewnia odnajdywanie, rejestrację i zdalne sterowanie urządzeniami przemysłowymi za pośrednictwem interfejsów API REST. Bliźniacze reprezentacji OPC nie wymaga zestawu SDK OPC Unified Architecture (OPC UA). Jest on niezależny od języka programowania i może być uwzględniony w bez serwerach przepływu pracy.
- **Odnajdywanie:** moduł odnajdywania reprezentowany przez tożsamość odnajdywania udostępnia usługi odnajdywania na brzegu sieci, w tym odnajdowanie serwera OPC UA. Jeśli odnajdywanie zostanie skonfigurowane i włączone, moduł wyśle wyniki sondy skanowania za pośrednictwem ścieżki IoT Edge i IoT Hub do usługi dołączania. Usługa przetwarza wyniki i aktualizuje wszystkie powiązane tożsamości w rejestrze.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, czym jest przemysłowe IoT, możesz przeczytać o przemysłowej platformie IoT i wydawcy OPC:

> [!div class="nextstepaction"]
> [Co to jest wydawca OPC?](overview-what-is-opc-publisher.md)