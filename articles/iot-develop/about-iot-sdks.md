---
title: Omówienie opcji zestawu SDK urządzeń Azure IoT
description: Dowiedz się, którego zestawu SDK urządzenia usługi Azure IoT użyć na podstawie Twojej roli i zadań programistyki.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c35a9045bf809c03630fbb7c57f9d31e7b143422
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876461"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Omówienie zestawów SDK urządzeń Azure IoT

Zestawy SDK urządzeń Azure IoT to zestaw bibliotek klienckich urządzeń, przewodników dla deweloperów, przykładów i dokumentacji. Zestawy SDK urządzeń ułatwiają programowe łączenie urządzeń z usługami Azure IoT.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagram przedstawiający różne zestawy SDK usługi Azure IoT":::

Jak pokazano na diagramie, dostępnych jest kilka zestawów SDK urządzeń dopasowanych do potrzeb twojego urządzenia i języka programowania. Wskazówki dotyczące wybierania odpowiedniego zestawu SDK urządzenia są dostępne w tece [Którego zestawu SDK należy użyć.](#which-sdk-should-i-use) Dostępne są również zestawy SDK usługi Azure IoT służące do łączenia aplikacji opartej na chmurze z usługami Azure IoT na za zaplecza. Ten artykuł koncentruje się na zestawach SDK urządzeń, ale więcej informacji na temat zestawów SDK usług platformy Azure można znaleźć [tutaj.](#service-sdks)

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Dlaczego warto używać zestawów SDK urządzeń Azure IoT?

Aby połączyć urządzenia z usługą Azure IoT, możesz utworzyć niestandardową warstwę połączenia lub użyć zestawów SDK urządzeń usługi Azure IoT. Korzystanie z zestawów SDK urządzeń Azure IoT ma kilka zalet:

| Koszt projektowania &nbsp; &nbsp; &nbsp;&nbsp; | Niestandardowa warstwa połączenia | Zestawy SDK urządzeń Azure IoT |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Pomoc techniczna | Musisz obsługiwać i dokumentować cokolwiek tworzysz | Mieć dostęp do pomocy technicznej firmy Microsoft (GitHub, Microsoft Q&A, Microsoft Docs, zespołów pomocy technicznej) |
| Nowe funkcje | Należy dodać nowe funkcje platformy Azure do niestandardowego oprogramowania pośredniczącego | Może natychmiast korzystać z nowych funkcji, które firma Microsoft stale dodaje do zestawów SDK IoT |
| Inwestycja | Zainwestuj setki godzin osadzonego kompilowania, kompilowania, testowania i konserwacji wersji niestandardowej | Może korzystać z bezpłatnych narzędzi typu open source. Jedyny koszt związany z zestawami SDK to krzywa uczenia. |

## <a name="which-sdk-should-i-use"></a>Którego zestawu SDK należy użyć?

Zestawy SDK urządzeń Usługi Azure IoT są dostępne w popularnych językach programowania, takich jak C, C#, Java, Node.js i Python. Podczas wyboru zestawu SDK należy wziąć pod uwagę dwa podstawowe zagadnienia: możliwości urządzeń i znajomość języka programowania przez zespół.

### <a name="device-capabilities"></a>Możliwości urządzenia

Podczas wybierania zestawu SDK należy wziąć pod uwagę limity urządzeń, których używasz. Urządzenie ograniczone to urządzenie z pojedynczym mikrokontrolerem (MCU) i ograniczoną pamięcią. Jeśli używasz urządzenia ograniczonego, zalecamy użycie osadzonego zestawu [SDK języka C.](#embedded-c-sdk) Ten zestaw SDK został zaprojektowany w celu zapewnienia minimalnego zestawu możliwości łączenia się z usługą Azure IoT. Możesz również wybrać składniki (klienta MQTT, TLS i biblioteki gniazd), które są najbardziej zoptymalizowane pod kątem urządzenia osadzonego. Jeśli na urządzeniu z ograniczeniami działa Azure RTOS, możesz użyć oprogramowania pośredniczącego Azure RTOS do nawiązania połączenia z usługą Azure IoT. Oprogramowanie Azure RTOS pośredniczące opakowywa osadzony zestaw SDK języka C z dodatkowymi funkcjami, aby uprościć łączenie urządzenia Azure RTOS z chmurą.

Urządzenie nieprzetrenowane to takie, które ma bardziej niezawodny procesor, który jest w stanie uruchamiać system operacyjny w celu obsługi środowiska uruchomieniowego języka, takiego jak .NET lub Python. Jeśli używasz nieprzetrenonego urządzenia, główną kwestią jest znajomość języka.

### <a name="your-teams-familiarity-with-the-programming-language"></a>Znajomość języka programowania przez Twój zespół

Zestawy SDK urządzeń Azure IoT są implementowane w wielu językach, dzięki czemu można wybrać preferowany język. Zestawy SDK urządzeń integrują się również z innymi znanymi narzędziami specyficznym dla języka. Możliwość pracy ze znanym językiem i narzędziami programistyki umożliwia zespołowi optymalizowanie cyklu opracowywania badań, tworzenia prototypów, opracowywania produktów i ciągłej konserwacji.

Jeśli to możliwe, wybierz zestaw SDK, który wydaje się dobrze znany zespołowi deweloperom. Wszystkie zestawy SDK usługi Azure IoT są open source i mają kilka przykładów dostępnych dla Twojego zespołu do oceny i testowania przed zatwierdzeniem do określonego zestawu SDK.

## <a name="how-can-i-get-started"></a>Jak można zacząć korzystać z tej usługi?

Na początek warto eksplorować repozytoria GitHub zestawów SDK urządzeń platformy Azure. Możesz również wypróbować przewodnik Szybki [start,](quickstart-send-telemetry-python.md) który pokazuje, jak szybko używać zestawu SDK do wysyłania danych telemetrycznych do usługi Azure IoT.

Opcje rozpoczęcia pracy zależą od rodzaju posiadanych urządzeń:
- W przypadku urządzeń z ograniczeniami użyj osadzonego zestawu [SDK języka C.](#embedded-c-sdk) 
- W przypadku urządzeń z systemem Azure RTOS można programować przy użyciu [oprogramowania pośredniczącego Azure RTOS](#azure-rtos-middleware). 
- W przypadku urządzeń, które nie są wytrenowane, możesz wybrać zestaw [SDK](#unconstrained-device-sdks) w wybranego języka. 

### <a name="constrained-device-sdks"></a>Zestawy SDK urządzeń z ograniczeniami
Te zestawy SDK są wyspecjalizowane do uruchamiania na urządzeniach z ograniczonymi zasobami obliczeniowymi lub pamięci. Aby dowiedzieć się więcej na temat typowych typów urządzeń, zobacz Overview of Azure IoT device types (Omówienie typów urządzeń [usługi Azure IoT).](concepts-iot-device-types.md)

#### <a name="embedded-c-sdk"></a>Osadzony zestaw SDK języka C
* [Repozytorium GitHub](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Samples](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Dokumentacja referencyjna](https://azure.github.io/azure-sdk-for-c/)
* [Jak skompilować osadzony zestaw SDK języka C](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Wykres rozmiarów urządzeń z ograniczeniami](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS pośredniczące

* [Repozytorium GitHub](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Wprowadzenie i inne](https://github.com/azure-rtos/getting-started) [przykłady](https://github.com/azure-rtos/samples)
* [Dokumentacja referencyjna](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>Zestawy SDK nieprzetrenowane urządzenia
Te zestawy SDK można uruchamiać na dowolnym urządzeniu, które może obsługiwać środowisko uruchomieniowe języka wyższego kolejności. Dotyczy to również urządzeń, takich jak komputery PC, Urządzenie Raspberry i smartfony. Są one wyróżniane głównie według języka, więc możesz wybrać bibliotekę, która najlepiej odpowiada Twojeowi zespołowi i scenariuszowi.

#### <a name="c-device-sdk"></a>Zestaw SDK urządzenia języka C
* [Repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c)
* [Samples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Pakiety](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Dokumentacja referencyjna](/azure/iot-hub/iot-c-sdk-ref/)
* [Dokumentacja referencyjna modułu edge](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Kompilowanie zestawu SDK urządzenia języka C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Przenoszenie zestawu SDK języka C na inne platformy](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Dokumentacja dla deweloperów](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) dotycząca kompilowania krzyżowego i rozpoczynania pracy na różnych platformach
* [Azure IoT Hub informacji o zużyciu zasobów zestawu SDK języka C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>Zestaw SDK urządzeń w języku C#

* [Repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-csharp)
* [Samples](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Pakiet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Dokumentacja referencyjna](/dotnet/api/microsoft.azure.devices)
* [Dokumentacja referencyjna modułu edge](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Zestaw SDK urządzeń Java

* [Repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-java)
* [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Pakiet](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Dokumentacja referencyjna](/java/api/com.microsoft.azure.sdk.iot.device)
* [Dokumentacja referencyjna modułu edge](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js SDK urządzenia

* [Repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-node)
* [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Pakiet](https://www.npmjs.com/package/azure-iot-device)
* [Dokumentacja referencyjna](/javascript/api/azure-iot-device/)
* [Dokumentacja referencyjna modułu edge](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Zestaw SDK urządzenia w języku Python

* [Repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-python)
* [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Pakiet](https://pypi.org/project/azure-iot-device/)
* [Dokumentacja referencyjna](/python/api/azure-iot-device)
* [Dokumentacja referencyjna modułu edge](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Zestawy SDK usług
Usługa Azure IoT oferuje również zestawy SDK usług, które umożliwiają tworzenie aplikacji po stronie rozwiązania w celu zarządzania urządzeniami, zdobywania szczegółowych informacji, wizualizowania danych i nie tylko. Te zestawy SDK są specyficzne dla każdej usługi Azure IoT i są dostępne w językach C#, Java, JavaScript i Python, aby uprościć środowisko programowania. 

#### <a name="iot-hub"></a>Usługa IoT Hub

Zestawy SDK IoT Hub umożliwiają tworzenie aplikacji, które łatwo współdziałają z IoT Hub do zarządzania urządzeniami i zabezpieczeniami. Za pomocą tych zestawów SDK można wysyłać komunikaty z chmury do urządzeń, wywoływać metody bezpośrednie na urządzeniach, aktualizować właściwości urządzeń i nie tylko.

[**Dowiedz się więcej o IoT Hub**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Spróbuj kontrolować urządzenie**](../iot-hub/quickstart-control-device-python.md)

**Zestaw SDK usługi IoT Hub C#**: [Dokumentacja referencyjna przykładów](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [pakietów](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [repozytorium](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [](/dotnet/api/microsoft.azure.devices) GitHub

**Java IoT Hub Service SDK:** [Dokumentacja referencyjna przykładów](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)pakietów  |  [](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [repozytorium](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [](/java/api/com.microsoft.azure.sdk.iot.service) GitHub

**JavaScript IoT Hub Service SDK:** [Dokumentacja referencyjna przykładów](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)pakietów  |  [](https://www.npmjs.com/package/azure-iothub)  |  [repozytorium](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [](/javascript/api/azure-iothub/) GitHub

**Python IoT Hub Service SDK:** [Dokumentacja referencyjna przykładów](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)pakietów  |  [](https://pypi.python.org/pypi/azure-iot-hub/)  |  [repozytorium](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [](/python/api/azure-iot-hub) GitHub

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins to oferta platformy jako usługi (PaaS), która umożliwia tworzenie grafów wiedzy opartych na cyfrowych modelach całych środowisk. Mogą to być budynki, fabryki, farmy, sieci energetyczne, miasta, miasta, a nawet całe miasta. Te modele cyfrowe mogą służyć do uzyskania szczegółowych informacji, które wpływają na lepsze produkty, zoptymalizowane operacje, niższe koszty i przełomowe doświadczenia klientów. Usługa Azure IoT oferuje zestawy SDK usług, które ułatwiają tworzenie aplikacji, które korzystają z możliwości Azure Digital Twins.

[**Dowiedz się więcej o Azure Digital Twins**](https://azure.microsoft.com/services/digital-twins/)  |  [ **Kod aplikacji ADT**](../digital-twins/tutorial-code.md)

**Zestaw SDK usługi ADT dla języka C#:** [dokumentacja referencyjna przykładów](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [pakietów](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [repozytorium](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [GitHub](/dotnet/api/overview/azure/digitaltwins/client)

**Zestaw SDK usługi Java ADT:** [dokumentacja referencyjna przykładów](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [pakietów](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [repozytorium](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [](/java/api/overview/azure/digitaltwins/client) GitHub

**Node.js SDK usługi ADT:** [Dokumentacja referencyjna przykładów](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)pakietów  |  [](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [repozytorium](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [](/javascript/api/@azure/digital-twins-core/) GitHub

**Zestaw SDK usługi ADT języka Python:** [dokumentacja referencyjna przykładów](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [pakietów](https://pypi.org/project/azure-digitaltwins-core/)  |  [repozytorium](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [](/python/api/azure-digitaltwins-core/azure.digitaltwins.core) GitHub

#### <a name="device-provisioning-service"></a>Device Provisioning Service

Usługa IoT Hub Device Provisioning Service (DPS) to usługa pomocnika dla usługi IoT Hub, która umożliwia bezdotykową aprowizowanie w odpowiednim centrum IoT bez konieczności interwencji człowieka. Usługa DPS umożliwia aprowizowanie milionów urządzeń w bezpieczny i skalowalny sposób. Zestawy SDK usługi DPS umożliwiają tworzenie aplikacji, które mogą bezpiecznie zarządzać urządzeniami przez tworzenie grup rejestracji i zbiorcze operacje.

[**Dowiedz się więcej o usłudze Device Provisioning Service**](../iot-dps/index.yml)  |  [ **Spróbuj utworzyć rejestrację grupową dla urządzeń X.509**](../iot-dps/quick-enroll-device-x509-csharp.md)

**Zestaw SDK usługi Device Provisioning w języku C#:** [dokumentacja referencyjna przykładów pakietów](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [repozytorium](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [](/dotnet/api/microsoft.azure.devices.provisioning.service) GitHub

**Java Device Provisioning Service SDK:** [Dokumentacja referencyjna przykładów](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [pakietów](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [repozytorium](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [](/java/api/com.microsoft.azure.sdk.iot.provisioning.service) GitHub

**Node.js DEVICE Provisioning Service SDK:** [Dokumentacja referencyjna przykładów pakietów](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [repozytorium](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [](/javascript/api/azure-iot-provisioning-service) GitHub

## <a name="next-steps"></a>Następne kroki

* [Szybki start: łączenie urządzenia z IoT Central (Python)](quickstart-send-telemetry-python.md)
* [Szybki start: łączenie urządzenia z IoT Hub (Python)](quickstart-send-telemetry-cli-python.md)
* [Wprowadzenie do opracowywania osadzonego](quickstart-device-development.md)
* Dowiedz się więcej o [korzyściach wynikających z używania zestawów SDK usługi Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)