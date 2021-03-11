---
title: Omówienie opcji zestawu SDK urządzeń Azure IoT
description: Dowiedz się, który zestaw SDK urządzeń Azure IoT ma być używany w oparciu o rolę i zadania deweloperskie.
author: elhorton
ms.author: elhorton
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c9624e9a23d005185429c82199324ac570cbd63e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607734"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Omówienie zestawów SDK urządzeń usługi Azure IoT

Zestawy SDK urządzeń Azure IoT to zestaw bibliotek klientów urządzeń, przewodników dla deweloperów, przykładów i dokumentacji. Zestawy SDK urządzeń ułatwiają programowe Łączenie urządzeń z usługami Azure IoT.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagram przedstawiający różne zestawy SDK usługi Azure IoT":::

Jak widać na diagramie, istnieje kilka zestawów SDK urządzeń dostępnych do zaspokojenia potrzeb Twojego urządzenia i języka programowania. Wskazówki dotyczące wybierania odpowiedniego zestawu SDK urządzeń są dostępne w [zestawie SDK, którego należy użyć](#which-sdk-should-i-use). Istnieją również zestawy SDK usługi Azure IoT dostępne do łączenia aplikacji opartej na chmurze z usługami Azure IoT w zapleczu. Ten artykuł koncentruje się na zestawach SDK urządzeń, ale w [tym miejscu](#service-sdks)możesz dowiedzieć się więcej o zestawach SDK usług platformy Azure.

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Dlaczego warto używać zestawów SDK urządzeń usługi Azure IoT?

Aby podłączyć urządzenia do usługi Azure IoT, możesz utworzyć niestandardową warstwę połączenia lub użyć zestawów SDK urządzeń usługi Azure IoT. Istnieje kilka zalet używania zestawów SDK urządzeń Azure IoT:

| &nbsp; &nbsp; Koszt &nbsp; programowania&nbsp; | Niestandardowa warstwa połączenia | Zestawy SDK urządzeń usługi Azure IoT |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Pomoc techniczna | Konieczność obsługi i dokumentowania dowolnych kompilacji | Uzyskaj dostęp do pomocy technicznej firmy Microsoft (GitHub, Microsoft Q&A, Microsoft Docs, zespoły pomocy technicznej dla klientów) |
| Nowe funkcje | Należy dodać nowe funkcje platformy Azure do niestandardowego oprogramowania pośredniczącego | Może natychmiast korzystać z nowych funkcji, które firma Microsoft ciągle dodaje do zestawów SDK IoT |
| Inwestycja | Zainwestowaj setki godzin projektowania wbudowanego na projektowanie, kompilowanie, testowanie i konserwowanie wersji niestandardowej | Może korzystać z bezpłatnych narzędzi typu open source. Jedyną opłatą skojarzoną z zestawami SDK jest krzywa szkoleniowa. |

## <a name="which-sdk-should-i-use"></a>Którego zestawu SDK należy użyć?

Zestawy SDK urządzeń Azure IoT są dostępne w popularnych językach programowania, takich jak C, C#, Java, Node.js i Python. Po wybraniu zestawu SDK można wziąć pod uwagę dwa podstawowe zagadnienia: możliwości urządzenia i znajomość Twojego zespołu w języku programowania.

### <a name="device-capabilities"></a>Możliwości urządzenia

Po wybraniu zestawu SDK należy wziąć pod uwagę limity urządzeń, z których korzystasz. Ograniczone urządzenie to takie, które ma jeden mikrokontroler (MIKROKONTROLERY) i ograniczoną ilość pamięci. Jeśli używasz ograniczonego urządzenia, zalecamy użycie [osadzonego zestawu C SDK](#embedded-c-sdk). Ten zestaw SDK został zaprojektowany z założenia, aby zapewniał minimalny zestaw funkcji do nawiązywania połączeń z usługą Azure IoT. Można również wybrać składniki (MQTT klienta, TLS i Sockets), które są najbardziej zoptymalizowane dla urządzenia osadzonego. Jeśli ograniczone urządzenie również działa na platformie Azure RTO, możesz użyć oprogramowania pośredniczącego Azure RTO do nawiązania połączenia z usługą Azure IoT. Oprogramowanie pośredniczące platformy Azure RTO zawija osadzony zestaw C SDK z dodatkowymi funkcjami, które upraszczają łączenie urządzenia z usługą Azure RTO z chmurą.

Urządzenie nieograniczone to takie, które ma bardziej niezawodny procesor CPU, co umożliwia uruchamianie systemu operacyjnego w celu obsługi środowiska uruchomieniowego języka, takiego jak .NET lub Python. Jeśli używasz urządzenia nieograniczonego, głównym zagadnieniem jest znajomość języka.

### <a name="your-teams-familiarity-with-the-programming-language"></a>Znajomość Twojego zespołu w języku programowania

Zestawy SDK urządzeń Azure IoT są implementowane w wielu językach, dzięki czemu można wybrać język preferowany przez użytkownika. Zestawy SDK urządzeń integrują się również z innymi znanymi narzędziami specyficznymi dla języka. Możliwość pracy z znanym językiem i narzędziami deweloperskimi pozwala zespołowi zoptymalizować cykl rozwoju badań, tworzenia prototypów, opracowywania produktów i trwającej konserwacji.

Jeśli to możliwe, wybierz zestaw SDK, który może być zaznajomiony z zespołem programistycznym. Wszystkie zestawy SDK usługi Azure IoT są typu open source i mają kilka próbek dostępnych dla Twojego zespołu do oszacowania i przetestowania przed zatwierdzeniem do określonego zestawu SDK.

## <a name="how-can-i-get-started"></a>Jak można zacząć korzystać z tej usługi?

Aby rozpocząć, należy poznać repozytoria GitHub zestawów SDK urządzeń platformy Azure. Możesz również skorzystać z [przewodnika Szybki Start](quickstart-send-telemetry-python.md) , który pokazuje, jak szybko używać zestawu SDK do wysyłania danych telemetrycznych do usługi Azure IoT.

Opcje umożliwiające rozpoczęcie pracy zależą od rodzaju posiadanego urządzenia:
- W przypadku urządzeń z ograniczeniami Użyj [osadzonego zestawu C SDK](#embedded-c-sdk). 
- W przypadku urządzeń z systemem Azure RTO można opracowywać [oprogramowanie na platformie Azure RTO](#azure-rtos-middleware). 
- W przypadku urządzeń, które są nieograniczone, możesz [wybrać zestaw SDK](#unconstrained-device-sdks) w wybranym języku. 

### <a name="constrained-device-sdks"></a>Zestawy SDK urządzeń z ograniczeniami
Te zestawy SDK są wyspecjalizowane do uruchamiania na urządzeniach z ograniczoną ilością zasobów obliczeniowych lub pamięci. Aby dowiedzieć się więcej o typowych typach urządzeń, zobacz [Omówienie typów urządzeń usługi Azure IoT](concepts-iot-device-types.md).

#### <a name="embedded-c-sdk"></a>Osadzony zestaw SDK języka C
* [Repozytorium GitHub](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Samples](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Dokumentacja referencyjna](https://azure.github.io/azure-sdk-for-c/)
* [Jak skompilować osadzony zestaw SDK języka C](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Wykres rozmiaru dla urządzeń z ograniczeniami](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Oprogramowanie pośredniczące platformy Azure RTO

* [Repozytorium GitHub](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Wprowadzenie prowadnice](https://github.com/azure-rtos/getting-started) i [więcej przykładów](https://github.com/azure-rtos/samples)
* [Dokumentacja referencyjna](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>Zestawy SDK urządzeń nieograniczone
Te zestawy SDK można uruchamiać na dowolnym urządzeniu, które może obsługiwać środowisko uruchomieniowe języka wyższego rzędu. Obejmuje to urządzenia, takie jak komputery PC, Raspberry PiS i smartfony. Są one odróżniane głównie według języka, aby można było wybrać bibliotekę, która najlepiej odpowiada Twojemu zespołowi i scenariuszowi.

#### <a name="c-device-sdk"></a>Zestaw SDK urządzeń C
* [Repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c)
* [Samples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Pakiety](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Dokumentacja referencyjna](/azure/iot-hub/iot-c-sdk-ref/)
* [Dokumentacja dotycząca modułu brzegowego](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Kompilowanie zestawu SDK urządzenia C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Przenoszenie zestawu SDK języka C na inne platformy](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Dokumentacja dla deweloperów](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) , która zawiera informacje na temat tworzenia krzyżowego i rozpoczynania pracy na różnych platformach
* [Informacje o zużyciu zasobów usługi Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>Zestaw SDK urządzenia w języku C#

* [Repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-csharp)
* [Samples](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Pakiet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Dokumentacja referencyjna](/dotnet/api/microsoft.azure.devices)
* [Dokumentacja dotycząca modułu brzegowego](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Zestaw SDK urządzeń Java

* [Repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-java)
* [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Pakiet](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Dokumentacja referencyjna](/java/api/com.microsoft.azure.sdk.iot.device)
* [Dokumentacja dotycząca modułu brzegowego](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Zestaw SDK urządzeń Node.js

* [Repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-node)
* [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Pakiet](https://www.npmjs.com/package/azure-iot-device)
* [Dokumentacja referencyjna](/javascript/api/azure-iot-device/)
* [Dokumentacja dotycząca modułu brzegowego](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Zestaw SDK urządzenia w języku Python

* [Repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-python)
* [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Pakiet](https://pypi.org/project/azure-iot-device/)
* [Dokumentacja referencyjna](/python/api/azure-iot-device)
* [Dokumentacja dotycząca modułu brzegowego](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Zestawy SDK usług
Usługa Azure IoT oferuje również zestawy SDK usług, które umożliwiają tworzenie aplikacji po stronie rozwiązania do zarządzania urządzeniami, uzyskiwanie szczegółowych informacji, wizualizowanie danych i inne. Te zestawy SDK są specyficzne dla każdej usługi Azure IoT i są dostępne w językach C#, Java, JavaScript i Python, aby uprościć środowisko programistyczne. 

#### <a name="iot-hub"></a>Usługa IoT Hub

Zestawy SDK usługi IoT Hub umożliwiają tworzenie aplikacji, które łatwo współpracują z IoT Hub w celu zarządzania urządzeniami i zabezpieczeniami. Te zestawy SDK umożliwiają wysyłanie komunikatów z chmury do urządzeń, wywoływanie metod bezpośrednich na urządzeniach, aktualizowanie właściwości urządzenia i nie tylko.

[**Dowiedz się więcej o IoT Hub**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Spróbuj sterować urządzeniem**](../iot-hub/quickstart-control-device-python.md)

**C# IoT Hub Service SDK**: [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [Dokumentacja](/dotnet/api/microsoft.azure.devices) pakietu repozytorium GitHub — przykłady

**Java IoT Hub Service SDK**: [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [Dokumentacja](/java/api/com.microsoft.azure.sdk.iot.service) pakietu repozytorium GitHub — przykłady

**JavaScript IoT Hub Service SDK**: [](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)  |  [](https://www.npmjs.com/package/azure-iothub)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [Dokumentacja](/javascript/api/azure-iothub/) pakietu repozytorium GitHub — przykłady

**Zestaw SDK usługi IoT Hub języka Python**: [](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [](https://pypi.python.org/pypi/azure-iot-hub/)  |  [](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [dokumentacja referencyjna](/python/api/azure-iot-hub) pakietu repozytorium GitHub

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Usługa Azure Digital bliźniaczych reprezentacji to oferta platformy jako usługi (PaaS), która umożliwia tworzenie wykresów wiedzy na podstawie cyfrowych modeli całego środowiska. Te środowiska mogą być budynkami, fabrykami, gospodarstwami, sieciami energetycznymi, liniami, Stadium i innymi — nawet w całym miejscowości. Te modele cyfrowe mogą służyć do uzyskiwania szczegółowych informacji, które zapewniają lepsze produkty, zoptymalizowane działania, zmniejszone koszty i przełomowe środowiska klienta. Usługa Azure IoT oferuje zestawy SDK, które ułatwiają tworzenie aplikacji, które wykorzystują możliwości usługi Azure Digital bliźniaczych reprezentacji.

[**Dowiedz się więcej o usłudze Azure Digital bliźniaczych reprezentacji**](https://azure.microsoft.com/services/digital-twins/)  |  [ **Kod aplikacji ADT**](../digital-twins/tutorial-code.md)

**Zestaw SDK języka C# ADT**: [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [dokumentacja referencyjna](/dotnet/api/overview/azure/digitaltwins/client) pakietu repozytorium GitHub

**Java ADT Service SDK**: [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [Dokumentacja](/java/api/overview/azure/digitaltwins/client) pakietu repozytorium GitHub — przykłady

**Node.js ADT Service SDK**: [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [Dokumentacja](/javascript/api/@azure/digital-twins-core/) pakietu repozytorium GitHub — przykłady

**Zestaw SDK ADT usługi Python**: [](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://pypi.org/project/azure-digitaltwins-core/)  |  [](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)  |  [dokumentacja referencyjna](/python/api/azure-digitaltwins-core/azure.digitaltwins.core) pakietu repozytorium GitHub

#### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub Device Provisioning Service (DPS) to usługa pomocnika dla IoT Hub, która umożliwia zainicjowanie obsługi "just in Time" do odpowiedniego centrum IoT Hub bez konieczności interwencji człowieka. Usługa DPS umożliwia bezpieczne i skalowalne Inicjowanie obsługi milionów urządzeń. Zestawy SDK usługi DPS umożliwiają tworzenie aplikacji, które mogą bezpiecznie zarządzać urządzeniami przez tworzenie grup rejestracji i wykonywanie operacji zbiorczych.

[**Dowiedz się więcej o usłudze**](../iot-dps/index.yml)  |  Device Provisioning [ **Spróbuj utworzyć rejestrację grupową dla urządzeń X. 509**](../iot-dps/quick-enroll-device-x509-csharp.md)

**Zestaw SDK usługi aprowizacji urządzeń w języku C#**: [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [Dokumentacja](/dotnet/api/microsoft.azure.devices.provisioning.service) pakietu repozytorium GitHub — przykłady

**Zestaw SDK usługi aprowizacji urządzeń Java**: [](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [dokumentacja referencyjna](/java/api/com.microsoft.azure.sdk.iot.provisioning.service) pakietu repozytorium usługi GitHub

**Node.js zestawu SDK usługi Device Provisioning Service**: [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [Dokumentacja dokumentacji](/javascript/api/azure-iot-provisioning-service) pakietu repozytorium GitHub

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: łączenie urządzenia z IoT Central (Python)](quickstart-send-telemetry-python.md)
* [Szybki Start: łączenie urządzenia z IoT Hub (Python)](quickstart-send-telemetry-cli-python.md)
* [Wprowadzenie do projektowania osadzonego](quickstart-device-development.md)
* Dowiedz się więcej o [korzyściach związanych z programowaniem przy użyciu zestawów SDK usługi Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)