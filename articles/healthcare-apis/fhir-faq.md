---
title: Często zadawane pytania dotyczące usług FHIR Services na platformie Azure — Azure API for FHIR
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API platformy Azure dla usługi FHIR, takich jak lokalizacja przechowywania danych za interfejsy API FHIR i obsługa wersji.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: af891935fd474e6f1f83ff1c2ce56ef71cd065c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536729"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Często zadawane pytania dotyczące interfejsu API platformy Azure dla usługi FHIR

## <a name="azure-api-for-fhir"></a>Interfejs API platformy Azure dla standardu FHIR

### <a name="what-is-fhir"></a>Co to jest FHIR?
Zasoby współdziałania w ramach usługi opieki zdrowotnej (FHIR "Fire") to standard interoperacyjności, który umożliwia wymianę danych opieki zdrowotnej między różnymi systemami kondycji. Ten standard został opracowany przez organizację HL7 i jest przyjmowany przez organizacje opieki zdrowotnej na całym świecie. Najnowsza dostępna wersja FHIR to R4 (wersja 4). Interfejs API platformy Azure dla usługi FHIR obsługuje R4, a także obsługuje poprzednią wersję STU3 (standard do użycia w wersji próbnej 3). Aby uzyskać więcej informacji na temat FHIR, odwiedź stronę [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Czy dane znajdują się w ramach interfejsów API FHIR przechowywanych na platformie Azure?

Tak, dane są przechowywane w zarządzanych bazach danych na platformie Azure. Interfejs API platformy Azure dla usługi FHIR nie zapewnia bezpośredniego dostępu do podstawowego magazynu danych.

### <a name="what-identity-provider-do-you-support"></a>Jaki dostawca tożsamości jest obsługiwany?

Obecnie obsługujemy Microsoft Azure Active Directory jako dostawcę tożsamości.

### <a name="what-fhir-version-do-you-support"></a>Jaka wersja FHIR jest obsługiwana?

Obsługujemy wersje 4.0.0 i 3.0.1 na platformie Azure API for FHIR (PaaS) i FHIR Server for Azure (Open Source).

Aby uzyskać szczegółowe informacje, zobacz [obsługiwane funkcje](fhir-features-supported.md). Przeczytaj o tym, co się zmieniło między wersjami w [historii wersji dla HL7 FHIR](https://hl7.org/fhir/R4/history.html).

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Jaka jest różnica między programem Microsoft FHIR Server for Azure i interfejsem API platformy Azure dla FHIR?

Interfejs Azure API for FHIR to hostowana i zarządzana wersja programu Microsoft FHIR Server typu open source dla platformy Azure. W usłudze zarządzanej firma Microsoft udostępnia wszystkie czynności konserwacyjne i aktualizacje. 

Gdy korzystasz z programu FHIR Server for Azure, masz bezpośredni dostęp do podstawowych usług. Jednak użytkownik jest odpowiedzialny za utrzymywanie i aktualizowanie serwera oraz wszystkich wymaganych czynności związanych ze zgodnością, Jeśli przechowujesz dane Fi.

Z punktu widzenia projektowania każda funkcja jest wdrażana na serwerze programu Microsoft FHIR na platformie Azure w pierwszej kolejności. Po sprawdzeniu poprawności w programie Open Source zostanie on opublikowany w PaaS Azure API for FHIR Solution. Czas między wydaniem w programie typu open source i PaaS zależy od złożoności funkcji i innych priorytetów związanych z planem. 

### <a name="what-is-smart-on-fhir"></a>Co to jest funkcja SMART on FHIR?

INTELIGENTNE (podstawiane aplikacje medyczne i technologia wielokrotnego użytku) w systemie FHIR to zestaw otwartych specyfikacji umożliwiających integrację aplikacji partnerskich z serwerami FHIR i innymi systemami IT o kondycji, takimi jak rejestry kondycji elektronicznej i wymiana informacji o kondycji. Tworząc INTELIGENTNą aplikację FHIR, możesz zapewnić dostęp do aplikacji i wykorzystać ją przez mnóstwo różnych systemów.
Uwierzytelnianie i interfejs API platformy Azure dla usługi FHIR. Aby dowiedzieć się więcej na temat inteligentnych, odwiedź stronę [Smart Health](https://smarthealthit.org/).


## <a name="iot-connector-preview"></a>Łącznik IoT (wersja zapoznawcza)

### <a name="what-is-iomt"></a>Co to jest IoMT?
IoMT to Internet rzeczy medycznych i jest kategorią urządzeń IoT, które przechwytują i wymieniają dane o kondycji i wellness z innymi systemami IT z branży IT za pośrednictwem sieci. Niektóre przykłady urządzeń IoMT obejmują przydatność i kliniczne noszenia, czujniki monitorowania, śledzenie działań, punkty kiosków opieki, a nawet inteligentne pill.

### <a name="how-many-iot-connectors-do-i-need"></a>Ile łączników IoT jest potrzebnych?
Pojedynczy łącznik usługi IoT może służyć do pozyskiwania danych z dużej liczby różnych typów urządzeń. Nadal możesz zdecydować się na użycie różnych łączników z następujących powodów:
- **Skala**: dla publicznej wersji zapoznawczej pojemność zasobów łącznika usługi IoT jest stała i oczekuje na przepływność na około 200 komunikatów na sekundę. Jeśli potrzebujesz większej przepływności, możesz dodać więcej łączników IoT.
- **Typ urządzenia**: można skonfigurować oddzielny łącznik usługi IoT dla każdego typu urządzeń IoMT z przyczyn związanych z zarządzaniem urządzeniami.

### <a name="is-there-a-limit-on-number-of-iot-connectors-during-public-preview"></a>Czy istnieje ograniczenie liczby łączników IoT w publicznej wersji zapoznawczej?
Tak. możesz utworzyć tylko dwa łączniki IoT na subskrypcję, gdy ta funkcja jest w publicznej wersji zapoznawczej. Ten limit istnieje, aby zapobiec nieoczekiwanemu kosztowi, ponieważ funkcja jest dostępna bezpłatnie w wersji zapoznawczej. Na żądanie ten limit może zostać podniesiony do maksymalnie pięciu łączników IoT.

### <a name="what-azure-regions-iot-connector-feature-is-available-during-public-preview"></a>Jakie funkcje łącznika IoT platformy Azure są dostępne w ramach publicznej wersji zapoznawczej?
Łącznik IoT jest dostępny we wszystkich regionach świadczenia usługi Azure, w których dostępny jest interfejs Azure API for FHIR.

### <a name="can-i-configure-scaling-capacity-for-iot-connector"></a>Czy mogę skonfigurować pojemność skalowania dla łącznika IoT?
Ponieważ łącznik IoT jest bezpłatny w publicznej wersji zapoznawczej, jego pojemność skalowania jest stała i ograniczona. Konfiguracja łącznika IoT dostępna w publicznej wersji zapoznawczej powinna zapewniać przepływność na około 200 komunikatów na sekundę. Część konfiguracji wydajności zasobów zostanie udostępniona w ogólnej dostępności.

### <a name="what-fhir-version-does-iot-connector-support"></a>Jaka wersja FHIR jest obsługiwana przez łącznik IoT?
Łącznik IoT obecnie obsługuje tylko FHIR w wersji R4. W związku z tym ta funkcja jest widoczna tylko w wystąpieniach usługi Azure API for FHIR, a firma Microsoft nie planuje obsługi wersji STU3 w tym momencie.

### <a name="why-cant-i-install-iot-connector-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Dlaczego nie mogę zainstalować łącznika IoT po włączeniu prywatnego linku w interfejsie API platformy Azure dla usługi FHIR?
Łącznik IoT nie obsługuje teraz funkcji prywatnego linku. W związku z tym, jeśli masz link prywatny włączony w interfejsie API platformy Azure dla usługi FHIR, nie możesz zainstalować łącznika IoT i na odwrót. To ograniczenie jest oczekiwane w przypadku dostępności łącznika IoT na potrzeby ogólnie dostępnej wersji.

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-iot-connector-feature-of-azure-api-for-fhir-service"></a>Jaka jest różnica między łącznikiem typu "Open Source IoMT FHIR Connector for Azure i IoT Connector" interfejsu API platformy Azure dla usługi FHIR?
IoT Connector to hostowana i zarządzana wersja łącznika typu open source IoMT FHIR dla platformy Azure. W usłudze zarządzanej firma Microsoft udostępnia wszystkie czynności konserwacyjne i aktualizacje.

Gdy korzystasz z łącznika IoMT FHIR dla platformy Azure, masz bezpośredni dostęp do podstawowych zasobów. Jednak użytkownik jest odpowiedzialny za utrzymywanie i aktualizowanie serwera oraz wszystkich wymaganych czynności związanych ze zgodnością, Jeśli przechowujesz dane Fi.

Z punktu widzenia projektowania każda funkcja jest wdrażana na łączniku IoMT FHIR dla platformy Azure w pierwszej kolejności. Po sprawdzeniu poprawności w programie Open Source zostanie on opublikowany w funkcji łącznika usługi PaaS IoT w usłudze Azure API for FHIR. Czas między wydaniem w składniku typu open source i PaaS zależy od złożoności funkcji i innych priorytetów mapy drogowej.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono kilka często zadawanych pytań dotyczących interfejsu API platformy Azure dla usługi FHIR. Przeczytaj o obsługiwanych funkcjach w programie FHIR Server for Azure:
 
>[!div class="nextstepaction"]
>[Obsługiwane funkcje FHIR](fhir-features-supported.md)