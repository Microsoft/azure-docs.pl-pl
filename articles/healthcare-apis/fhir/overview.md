---
title: Co to jest Azure API for FHIR? — Azure API for FHIR
description: Interfejs API platformy Azure dla usługi FHIR umożliwia szybkie wymianę danych za pomocą interfejsów API FHIR. Pozyskiwanie, zarządzanie i utrwalanie chronionych informacji o zdrowiu Fi za pomocą zarządzanej usługi w chmurze.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/13/2020
ms.author: matjazl
ms.openlocfilehash: 479e8f870692cc16345d0ca19b8eb065caa28fa8
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019594"
---
# <a name="what-is-azure-api-for-fhirreg"></a>Co to jest Azure API for FHIR &reg; ?

Interfejs API platformy Azure dla usługi FHIR umożliwia szybkie wymianę danych za pomocą interfejsów API szybkich zasobów współdziałania (FHIR®), które są obsługiwane przez zarządzaną ofertę platformy jako usługi (PaaS) w chmurze. Ułatwiają one pracę z danymi dotyczącymi kondycji w celu pozyskiwania, zarządzania i utrwalania chronionych informacji o zdrowiu [Fi](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) w chmurze: 

- Zarządzana usługa FHIR, obsługiwana w chmurze w ciągu kilku minut 
- ® FHIRy punkt końcowy oparty na klasie korporacyjnej na platformie Azure na potrzeby dostępu do danych i magazyn w formacie FHIR®
- Wysoka wydajność, małe opóźnienia
- Bezpieczne zarządzanie chronionymi informacjami o zdrowiu w zgodnym środowisku chmury
- Inteligentne FHIR dla implementacji aplikacji mobilnych i sieci Web
- Kontrolowanie własnych danych na dużą skalę dzięki kontroli dostępu opartej na rolach (RBAC)
- Śledzenie dzienników inspekcji na potrzeby dostępu, tworzenia, modyfikowania i odczytywania w ramach poszczególnych magazynów danych

Interfejs Azure API for FHIR umożliwia tworzenie i wdrażanie usługi FHIR w zaledwie kilka minut, aby korzystać z elastycznego skalowania w chmurze.  Płacisz tylko za wymaganą przepływność i magazyn. Usługi platformy Azure, które korzystają z interfejsu API platformy Azure dla usługi FHIR, są przeznaczone do szybkiej wydajności niezależnie od tego, jakiego rozmiaru są zarządzane.

Interfejs API FHIR i zgodny magazyn danych umożliwiają bezpieczne łączenie się z systemem i korzystanie z nich przy użyciu interfejsów API FHIR.  Firma Microsoft podejmuje działania, konserwację, aktualizacje i wymagania dotyczące zgodności w ofercie PaaS, dzięki czemu można zwolnić własne zasoby operacyjne i programistyczne. 

Poniższy film wideo przedstawia przegląd interfejsu API platformy Azure dla usługi FHIR:

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Wykorzystywanie mocy danych za pomocą FHIR

Branża opieki zdrowotnej szybko przekształca dane dotyczące kondycji na powstający standard [FHIR &reg; ](https://hl7.org/fhir) (szybkie współdziałanie z usługą opieki zdrowotnej). FHIR umożliwia niezawodne, rozszerzalny model danych ze standardową semantyką i wymianą danych, dzięki czemu wszystkie systemy wykorzystujące FHIR współdziałają ze sobą.  Przekształcanie danych na FHIR umożliwia szybkie łączenie istniejących źródeł danych, takich jak systemy rejestrowania kondycji elektronicznej lub bazy danych badawczych. FHIR umożliwia również szybkie wymianę danych w nowoczesnych implementacjach tworzenia aplikacji mobilnych i internetowych. Co najważniejsze, FHIR może uprościć pozyskiwanie danych i przyspieszyć Programowanie przy użyciu narzędzi do analizy i uczenia maszynowego.  

### <a name="securely-manage-health-data-in-the-cloud"></a>Bezpieczne zarządzanie danymi kondycji w chmurze

Interfejs API platformy Azure dla usługi FHIR umożliwia wymianę danych za pośrednictwem spójnych, RESTful, FHIR interfejsów API opartych na specyfikacji HL7 FHIR. Obsługiwane przez zarządzaną ofertę PaaS na platformie Azure zapewnia również skalowalne i bezpieczne środowisko do zarządzania i przechowywania danych chronionych kondycji (Fi) w formacie natywnym FHIR.  

### <a name="free-up-your-resources-to-innovate"></a>Zwolnij swoje zasoby, aby wprowadzać innowacje

Możesz zainwestować zasoby budynku i uruchamiania własnej usługi FHIR, ale za pomocą interfejsu API platformy Azure dla FHIR firma Microsoft podejmuje obciążenie związane z operacjami, konserwacją, aktualizacjami i wymaganiami dotyczącymi zgodności, umożliwiając zwolnienie własnych zasobów operacyjnych i programistycznych.

### <a name="enable-interoperability-with-fhir"></a>Włącz współdziałanie z FHIR

Korzystanie z interfejsu API platformy Azure dla usługi FHIR umożliwia łączenie się z dowolnym systemem, który wykorzystuje interfejsy API FHIR do odczytu, zapisu, wyszukiwania i innych funkcji.  Może służyć jako zaawansowane narzędzie do konsolidowania, normalizacji i stosowania uczenia maszynowego z danymi klinicznymi z elektronicznych rejestrów kondycji, clinicianów i pulpitów nawigacyjnych pacjentów, programów do zdalnego monitorowania lub z bazami danych spoza systemu, które mają interfejsy API FHIR.

### <a name="control-data-access-at-scale"></a>Kontrolowanie dostępu do danych na dużą skalę

Kontrolujesz swoje dane. Kontrola dostępu oparta na rolach (RBAC) umożliwia zarządzanie sposobem przechowywania danych i uzyskiwania do nich dostępu.  Zapewnianie zwiększonych zabezpieczeń i zmniejszenie obciążenia administracyjnego należy określić, kto ma dostęp do utworzonych zestawów danych, na podstawie definicji ról utworzonych dla danego środowiska.  

### <a name="audit-logs-and-tracking"></a>Inspekcja dzienników i śledzenia 

Szybko śledź, gdzie znajdują się dane z wbudowanymi dziennikami inspekcji. Śledź dostęp, tworzenie, modyfikowanie i odczyty w ramach każdego magazynu danych.

### <a name="secure-your-data"></a>Zabezpieczanie danych

Chroń swój Fi Dzięki niezrównanej analizie zabezpieczeń.  Dane są odizolowane od unikatowej bazy danych na wystąpienie interfejsu API i chronione za pomocą trybu failover w wieloregionach. Interfejs API platformy Azure dla usługi FHIR implementuje warstwową i kompleksową ochronę przed zagrożeniami dla danych.  

## <a name="applications-for-a-fhir-service"></a>Aplikacje dla usługi FHIR

Serwery FHIR są kluczowymi narzędziami do współdziałania z danymi o kondycji.  Interfejs API platformy Azure dla usługi FHIR został zaprojektowany jako interfejs API i usługa, którą można szybko utworzyć, wdrożyć i zacząć korzystać z programu.  W miarę jak FHIR Standard rozszerza się w dziedzinie opieki zdrowotnej, przypadki użycia będą nadal wzrastać, ale niektóre początkowe aplikacje klienta, w których jest przydatny interfejs Azure API for FHIR, są następujące: 

- **Programowanie uruchamiania/IoT i aplikacji:**  Klienci korzystający z aplikacji skoncentrowanej na dostawcy (mobilnej lub sieci Web) mogą korzystać z interfejsu API platformy Azure dla FHIR jako w pełni zarządzanej usługi zaplecza. Interfejs API platformy Azure dla usługi FHIR zapewnia cenny zasób, w którym klienci mogą zarządzać danymi i wymieniać dane w bezpiecznym środowisku chmury zaprojektowanym pod kątem danych dotyczących kondycji, korzystać z inteligentnych wytycznych dotyczących implementacji FHIR i umożliwiać korzystanie z ich technologii przez wszystkie systemy dostawców (na przykład większość EHRs z włączonymi FHIR odczytem interfejsów API).   
- **Ekosystemy opieki zdrowotnej:**  Chociaż EHRs istnieje jako podstawowa "Źródło prawdy" w wielu ustawieniach klinicznych, dostawcy nie mogą mieć wielu baz danych, które nie są połączone ze sobą ani nie przechowują danych w różnych formatach.  Korzystanie z interfejsu API platformy Azure dla FHIR jako usługi, która znajduje się na szczycie tych systemów, umożliwia ujednolicenie danych w formacie FHIR.  Pozwala to na włączenie wymiany danych w wielu systemach przy użyciu spójnego formatu danych. 

- **Badania:** Pracownicy zajmujący się opieką zdrowotną mogą ogólnie znaleźć Standard FHIR oraz interfejs API platformy Azure dla FHIR użyteczny, ponieważ normalizuje dane dotyczące wspólnego modelu danych FHIR i zmniejsza obciążenie uczenia maszynowego i udostępniania danych.
Wymiana danych za pośrednictwem interfejsu API platformy Azure dla usługi FHIR zapewnia dzienniki inspekcji i kontroli dostępu, które ułatwiają sterowanie przepływem danych i dostęp do typów danych. 

## <a name="fhir-from-microsoft"></a>FHIR od firmy Microsoft

Funkcje FHIR firmy Microsoft są dostępne w dwóch konfiguracjach:

* Azure API for FHIR — oferta PaaS na platformie Azure, która jest łatwo obsługiwana w Azure Portal i zarządzana przez firmę Microsoft.
* Serwer FHIR dla platformy Azure — projekt open source, który można wdrożyć w ramach subskrypcji platformy Azure, dostępnej w witrynie GitHub w witrynie https://github.com/Microsoft/fhir-server .

W przypadku przypadków użycia, które wymagają rozszerzenia lub dostosowania serwera FHIR lub wymagania dostępu do podstawowych usług, takich jak baza danych — bez przechodzenia przez interfejsy API FHIR, deweloperzy powinni wybrać serwer typu open source FHIR dla platformy Azure.   W celu wdrożenia z podwyższeniem poziomu, gotowego do produkcji interfejsu API FHIR i usługi zaplecza, w której dane utrwalane mają być dostępne tylko za pomocą interfejsu API FHIR, deweloperzy powinni wybrać interfejs API platformy Azure dla usługi FHIR

## <a name="azure-iot-connector-for-fhir-preview"></a>Łącznik usługi Azure IoT dla FHIR (wersja zapoznawcza)

Łącznik usługi Azure IoT dla szybkich zasobów współdziałania (FHIR&#174;) * to opcjonalna funkcja interfejsu API platformy Azure dla FHIR, która zapewnia możliwość pozyskiwania danych z Internetu (IoMT) urządzeń. Internet rzeczy medycznych to kategoria urządzeń IoT, które przechwytują i programu Exchange Health & dane Wellness z innymi systemami informatycznymi IT za pośrednictwem sieci. Niektóre przykłady urządzeń IoMT obejmują przydatność i kliniczne noszenia, czujniki monitorowania, śledzenie działań, punkty kiosków opieki, a nawet inteligentne pill. Funkcja łącznik usługi Azure IoT dla FHIR umożliwia szybkie skonfigurowanie usługi do pozyskiwania danych IoMT w usłudze Azure API dla FHIR w sposób skalowalny, bezpieczny i zgodny.

Łącznik usługi Azure IoT dla FHIR może akceptować komunikaty oparte na notacji JSON wysyłane przez urządzenie IoMT. Te dane są najpierw przekształcane w odpowiednie FHIR [obserwacje](https://www.hl7.org/fhir/observation.html) oparte na systemie, a następnie utrwalane w interfejsie API platformy Azure dla FHIR. Logika transformacji danych jest definiowana za pośrednictwem pary szablonów mapowania skonfigurowanych w oparciu o schemat wiadomości i wymagania FHIR. Dane urządzenia mogą być wypychane bezpośrednio do łącznika usługi Azure IoT dla FHIR lub bezproblemowo wykorzystywane w połączeniu z innymi rozwiązaniami Azure IoT ([azure IoT Hub](../../iot-hub/index.yml) i [Azure IoT Central](../../iot-central/index.yml)). Łącznik usługi Azure IoT dla FHIR zapewnia bezpieczny Potok danych, a jednocześnie umożliwia zarządzanie rozwiązaniami Azure IoT i konserwacją urządzeń fizycznych.

### <a name="applications-of-azure-iot-connector-for-fhir-preview"></a>Aplikacje łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza)

Korzystanie z urządzeń IoMT w ramach opieki zdrowotnej i łącznika usługi Azure IoT dla FHIR została zaprojektowana w celu zamostowania przerwy w zakresie przenoszenia danych z wielu urządzeń z zabezpieczeniami i zgodnością w usłudze Azure API dla FHIR. Umieszczenie danych IoMT na serwerze FHIR umożliwia całościowe wglądy w dane oraz innowacyjne kliniczne przepływy pracy. Niektóre typowe scenariusze dotyczące łącznika usługi Azure IoT dla FHIR są następujące:
- **Monitorowanie zdalnego pacjenta/telekondycja:** Zdalne monitorowanie pacjentów umożliwia zbieranie danych dotyczących kondycji pacjenta poza tradycyjnymi ustawieniami opieki zdrowotnej. Instytucje opieki zdrowotnej mogą używać łącznika usługi Azure IoT for FHIR do przenoszenia danych o kondycji generowanych przez urządzenia zdalne do interfejsu API platformy Azure dla FHIR. Te dane mogą być używane do dokładnego śledzenia stanu kondycji pacjentów, monitorowania pacjentów przestrzegania planu obróbki i zapewnienia spersonalizowanej opieki.
- **Badania i nauki przyrodnicze:** Badania kliniczne szybko przyjmują urządzenia IoMT, takie jak Czujniki biometryczne, noszenia, aplikacje mobilne do przechwytywania danych z wersji próbnej. Te wersje próbne umożliwiają korzystanie z łącznika usługi Azure IoT for FHIR do przesyłania danych urządzenia do interfejsu API platformy Azure w celu zapewnienia bezpiecznego, wydajnego i efektywnego sposobu. Raz w interfejsie API platformy Azure dla FHIR można użyć danych wersji próbnej do uruchomienia analizy danych w czasie rzeczywistym.
- **Zaawansowana analiza:** Urządzenia IoMT mogą zapewniać duże ilości danych w dużej szybkości, co sprawia, że są one bardzo przydatne do obsługi szkoleń i testowania danych dla modeli uczenia maszynowego. Łącznik usługi Azure IoT dla FHIR został zaprojektowany z myślą o pracy z szeroką gamęm częstotliwości danych, elastycznym schematem danych i skalowaniem chmury z małymi opóźnieniami. Te atrybuty sprawiają, że łącznik usługi Azure IoT dla FHIR doskonale nadaje się do przechwytywania danych urządzenia na potrzeby zaawansowanych analiz.
- **Inteligentne szpitale/kliniki:** Obecnie inteligentne szpitale i kliniki konfigurują infrastrukturę połączonych zasobów cyfrowych. Łącznik usługi Azure IoT dla FHIR może służyć do przechwytywania i integrowania danych z tych połączonych składników. Szczegółowe informacje z możliwością działania z tego zestawu danych umożliwiają lepszą opiekę przed pacjentami i wydajność operacyjną.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z interfejsem API platformy Azure dla usługi FHIR, wykonaj 5-minutowy Przewodnik Szybki Start, aby wdrożyć interfejs API platformy Azure dla usługi FHIR.

>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-portal-quickstart.md)

Aby wypróbować funkcję łącznika usługi Azure IoT for FHIR, zapoznaj się z przewodnikiem Szybki Start dotyczącym wdrażania łącznika usługi Azure IoT dla FHIR przy użyciu Azure Portal.

>[!div class="nextstepaction"]
>[Wdrażanie łącznika usługi Azure IoT dla FHIR](iot-fhir-portal-quickstart.md)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza). FHIR jest zastrzeżonym znakiem towarowym HL7 i jest używany z uprawnieniem HL7. 
