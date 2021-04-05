---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010706"
---
Ten artykuł zawiera następny poziom szczegółowości w zakresie zabezpieczania infrastruktury Internet rzeczy (IoT) opartej na usłudze Azure IoT. Łączy się on z szczegółowymi informacjami na temat konfigurowania i wdrażania każdego składnika. Zawiera również porównania i wybory między różnymi metodami konkurującymi.

Zabezpieczanie wdrożenia usługi Azure IoT można podzielić na następujące trzy obszary zabezpieczeń:

* **Zabezpieczenia urządzeń**: Zabezpieczanie urządzenia IoT podczas jego wdrażania w ramach środowiska naturalnego.

* **Zabezpieczenia połączeń**: zapewnianie, że wszystkie dane przesyłane między urządzeniem IoT i IoT Hub są poufne i nie będą sprawdzane.

* **Zabezpieczenia w chmurze**: zapewnianie środków do zabezpieczania danych podczas ich przechodzenia przez program i jest przechowywany w chmurze.

![Trzy obszary zabezpieczeń](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Bezpieczne Inicjowanie obsługi i uwierzytelnianie urządzeń

Akcelerator rozwiązań IoT zabezpiecza urządzenia IoT przy użyciu następujących dwóch metod:

* Dostarczając unikatowy klucz tożsamości (tokeny zabezpieczające) dla każdego urządzenia, który może być używany przez urządzenie do komunikowania się z IoT Hub.

* Użycie [certyfikatu X. 509](https://www.itu.int/rec/T-REC-X.509-201210-S) na urządzeniu i klucza prywatnego jako środka do uwierzytelniania urządzenia w IoT Hub. Ta metoda uwierzytelniania zapewnia, że klucz prywatny na urządzeniu nie jest znany poza urządzeniem w dowolnym momencie, zapewniając wyższy poziom zabezpieczeń.

Metoda token zabezpieczający zapewnia uwierzytelnianie dla każdego wywołania wykonanego przez urządzenie w celu IoT Hub przez skojarzenie klucza symetrycznego z każdym wywołaniem. Uwierzytelnianie oparte na X. 509 umożliwia uwierzytelnianie urządzenia IoT w warstwie fizycznej w ramach ustanowienia połączenia TLS. Metoda oparta na tokenach zabezpieczających może być używana bez uwierzytelniania X. 509, który jest mniej bezpiecznym wzorcem. Wybór między tymi dwiema metodami zależy przede wszystkim od tego, jak należy zapewnić bezpieczeństwo uwierzytelniania urządzenia oraz zapewnić dostępność bezpiecznego magazynu na urządzeniu (w celu bezpiecznego przechowywania klucza prywatnego).

## <a name="iot-hub-security-tokens"></a>IoT Hub tokeny zabezpieczające

IoT Hub używa tokenów zabezpieczających do uwierzytelniania urządzeń i usług, aby uniknąć wysyłania kluczy w sieci. Ponadto tokeny zabezpieczające są ograniczone do okresu ważności i zakresu. Zestawy SDK usługi Azure IoT automatycznie generują tokeny bez konieczności konfigurowania specjalnej konfiguracji. Niektóre scenariusze wymagają jednak, aby użytkownik mógł bezpośrednio generować tokeny zabezpieczające i korzystać z nich. Te scenariusze obejmują bezpośrednie użycie powierzchni MQTT, AMQP lub HTTP lub implementację wzorca usługi tokenu.

Więcej szczegółowych informacji na temat struktury tokenu zabezpieczającego i jego użycia można znaleźć w następujących artykułach:

* [Struktura tokenu zabezpieczającego](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [Używanie tokenów SAS jako urządzenia](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Każda IoT Hub ma [Rejestr tożsamości](../articles/iot-hub/iot-hub-devguide-identity-registry.md) , który może służyć do tworzenia zasobów dla urządzeń w usłudze, takich jak Kolejka zawierająca komunikaty z chmury do urządzenia i do zezwalania na dostęp do punktów końcowych dostępnych na urządzeniu. Rejestr tożsamości IoT Hub zapewnia bezpieczny magazyn tożsamości urządzeń i kluczy zabezpieczeń dla rozwiązania. Osoby lub grupy tożsamości urządzeń można dodawać do listy dozwolonych lub zablokowanych, umożliwiając pełną kontrolę nad dostępem urządzeń. Poniższe artykuły zawierają więcej szczegółowych informacji na temat struktury rejestru tożsamości i obsługiwanych operacji.

[IoT Hub obsługuje protokoły takie jak MQTT, AMQP i http](../articles//iot-hub/iot-hub-devguide-security.md). Każdy z tych protokołów używa tokenów zabezpieczających z urządzenia IoT do IoT Hub w różny sposób:

* AMQP: SASL PLAIN i AMQP zabezpieczenia oparte na oświadczeniach (z tokenami na `{policyName}@sas.root.{iothubName}` poziomie Centrum IoT, `{deviceId}` z tokenami w zakresie urządzeń).

* MQTT: Połącz pakiet używa `{deviceId}` jako `{ClientId}` , `{IoThubhostname}/{deviceId}` w polu **Nazwa użytkownika** i token sygnatury dostępu współdzielonego w polu **hasło** .

* HTTP: prawidłowy token znajduje się w nagłówku żądania autoryzacji.

Rejestr tożsamości IoT Hub może służyć do konfigurowania poświadczeń zabezpieczeń poszczególnych urządzeń i kontroli dostępu. Jeśli jednak rozwiązanie IoT ma już znaczną inwestycję w [niestandardowym rejestrze tożsamości urządzeń i/lub schemat uwierzytelniania](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication), można je zintegrować z istniejącą infrastrukturą IoT Hub przez utworzenie usługi tokenu.

### <a name="x509-certificate-based-device-authentication"></a>Uwierzytelnianie urządzenia oparte na certyfikatach X. 509

Użycie [certyfikatu X. 509 opartego na urządzeniu](../articles/iot-hub/iot-hub-devguide-security.md) i skojarzonej pary kluczy prywatnych i publicznych umożliwia dodatkowe uwierzytelnianie w warstwie fizycznej. Klucz prywatny jest bezpiecznie przechowywany na urządzeniu i nie jest odnajdowany poza urządzeniem. Certyfikat X. 509 zawiera informacje o urządzeniu, takie jak identyfikator urządzenia i inne szczegóły organizacyjne. Sygnatura certyfikatu jest generowana przy użyciu klucza prywatnego.

Przepływ obsługi urządzenia wysokiego poziomu:

* Skojarz identyfikator z urządzeniem fizycznym — z tożsamością urządzenia i/lub z certyfikatem X. 509 skojarzonym z urządzeniem podczas produkcji lub tworzenia urządzeń.

* Utwórz odpowiedni wpis tożsamości w IoT Hub — tożsamość urządzenia i powiązane informacje o urządzeniu w rejestrze tożsamości IoT Hub.

* Bezpiecznie przechowuj odcisk palca certyfikatu X. 509 w rejestrze IoT Hub tożsamości.

### <a name="root-certificate-on-device"></a>Certyfikat główny na urządzeniu

Podczas ustanawiania bezpiecznego połączenia TLS z IoT Hub urządzenie IoT uwierzytelnia IoT Hub przy użyciu certyfikatu głównego, który jest częścią zestawu SDK urządzenia. W przypadku zestawu SDK klienta C certyfikat znajduje się w folderze " \\ \\ Certyfikaty C" w katalogu głównym repozytorium. Chociaż te certyfikaty główne są długotrwałe, nadal mogły wygasnąć lub zostać odwołane. Jeśli nie ma możliwości aktualizacji certyfikatu na urządzeniu, urządzenie może nie być w stanie połączyć się z IoT Hub (ani z żadną inną usługą w chmurze). Posiadanie środka do aktualizowania certyfikatu głównego, gdy urządzenie IoT zostanie wdrożone skutecznie zmniejsza to ryzyko.

## <a name="securing-the-connection"></a>Zabezpieczanie połączenia

Połączenie internetowe między urządzeniem IoT a IoT Hub jest zabezpieczone przy użyciu standardu Transport Layer Security (TLS). Usługa Azure IoT obsługuje protokoły [tls 1,2](https://tools.ietf.org/html/rfc5246), TLS 1,1 i TLS 1,0 w tej kolejności. Obsługa protokołu TLS 1,0 jest świadczona tylko w celu zapewnienia zgodności z poprzednimi wersjami. Sprawdź [obsługę protokołu TLS w IoT Hub](../articles/iot-hub/iot-hub-tls-support.md) , aby dowiedzieć się, jak skonfigurować koncentrator do korzystania z protokołu TLS 1,2, ponieważ zapewnia największą ochronę.

## <a name="securing-the-cloud"></a>Zabezpieczanie chmury

Usługa Azure IoT Hub umożliwia definiowanie [zasad kontroli dostępu](../articles/iot-hub/iot-hub-devguide-security.md) dla każdego klucza zabezpieczeń. Używa następującego zestawu uprawnień, aby udzielić dostępu do każdego z punktów końcowych IoT Hub. Uprawnienia ograniczają dostęp do usługi IoT Hub na podstawie funkcjonalności.

* **RegistryRead**. Przyznaje dostęp do odczytu do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz [Rejestr tożsamości](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **RegistryReadWrite**. Przyznaje dostęp do odczytu i zapisu do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz [Rejestr tożsamości](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **Serviceconnect**. Przyznaje dostęp do punktów końcowych komunikacji i monitorowania dla usługi w chmurze. Na przykład przyznaje uprawnienia do usług w chmurze zaplecza, aby odbierać komunikaty z urządzenia do chmury, wysyłać komunikaty z chmury do urządzenia i pobierać odpowiednie potwierdzenia dostarczania.

* **DeviceConnect**. Przyznaje dostęp do punktów końcowych dostępnych dla urządzenia. Na przykład przyznaje uprawnienia do wysyłania komunikatów z urządzenia do chmury i otrzymywania komunikatów z chmury do urządzenia. To uprawnienie jest używane przez urządzenia.

Istnieją dwa sposoby uzyskania uprawnień **DeviceConnect** IoT Hub z [tokenami zabezpieczający](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)przy użyciu klucza tożsamości urządzenia lub klucza dostępu współdzielonego. Ponadto należy pamiętać, że wszystkie funkcje dostępne z urządzeń są udostępniane przez projektowanie w punktach końcowych z prefiksem `/devices/{deviceId}` .

[Składniki usługi mogą generować tylko tokeny zabezpieczające](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) przy użyciu zasad dostępu współdzielonego, przyznając odpowiednie uprawnienia.

Usługa Azure IoT Hub i inne usługi, które mogą być częścią rozwiązania, umożliwiają zarządzanie użytkownikami przy użyciu Azure Active Directory.

Dane pozyskane przez usługę Azure IoT Hub mogą być używane przez różne usługi, takie jak Azure Stream Analytics i magazyn obiektów blob platformy Azure. Te usługi umożliwiają dostęp do zarządzania. Przeczytaj więcej na temat tych usług i dostępnych opcji:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Skalowalna, w pełni indeksowana usługa bazy danych dla danych z częściową strukturą, która zarządza metadanymi dla zainicjowanych urządzeń, takich jak atrybuty, konfiguracja i właściwości zabezpieczeń. Azure Cosmos DB oferuje przetwarzanie wysokiej wydajności i wysoką przepływność, niezależny od indeksowanie danych i bogaty interfejs zapytań SQL.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): Przetwarzanie strumienia w czasie rzeczywistym w chmurze, które pozwala szybko opracowywać i wdrażać rozwiązanie do analizy o niskich kosztach w celu odkrywania szczegółowych informacji z urządzeń, czujników, infrastruktury i aplikacji. Dane z tej w pełni zarządzanej usługi mogą być skalowane do dowolnego woluminu i nadal osiągać wysoką przepływność, małe opóźnienia i odporność.

* [Azure App Services](https://azure.microsoft.com/services/app-service/): platforma w chmurze umożliwiająca tworzenie zaawansowanych aplikacji sieci Web i mobilnych, które łączą się z danymi w dowolnym miejscu; w chmurze lub lokalnie. Twórz atrakcyjne aplikacje mobilne dla systemów iOS, Android i Windows. Integracja z oprogramowaniem jako usługą (SaaS) i aplikacjami przedsiębiorstwa dzięki wbudowanej łączności z dziesiątami z wielu usług w chmurze i aplikacji dla przedsiębiorstw. Twórz kod w ulubionym języku i środowisku IDE (.NET, Node.js, PHP, Python lub Java), aby szybciej tworzyć aplikacje sieci Web i interfejsy API.

* [Logic Apps](https://azure.microsoft.com/services/app-service/logic/): funkcja Logic Apps Azure App Service umożliwia integrację rozwiązania IoT z istniejącymi systemami biznesowymi i automatyzowanie procesów przepływu pracy. Logic Apps umożliwia deweloperom projektowanie przepływów pracy rozpoczynających się od wyzwalacza, a następnie wykonanie serii kroków — reguł i akcji, które używają zaawansowanych łączników do integracji z procesami biznesowymi. Logic Apps oferuje wbudowaną łączność z obszernym ekosystemem aplikacji SaaS, opartych na chmurze i lokalnych.

* [Usługa Azure Blob Storage](https://azure.microsoft.com/services/storage/): niezawodny i ekonomiczny magazyn w chmurze dla danych wysyłanych przez urządzenia do chmury.

## <a name="conclusion"></a>Podsumowanie

Ten artykuł zawiera omówienie szczegółowych poziomów implementacji związanych z projektowaniem i wdrażaniem infrastruktury IoT przy użyciu usługi Azure IoT. Skonfigurowanie każdego składnika do zabezpieczenia jest kluczem do zabezpieczania ogólnej infrastruktury IoT. Opcje projektowe dostępne w usłudze Azure IoT zapewniają pewien poziom elastyczności i wyboru; Jednak każdy wybór może mieć wpływ na bezpieczeństwo. Zaleca się, aby każda z tych opcji była oceniana przez ocenę ryzyka/kosztów.
