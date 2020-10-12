---
title: Często zadawane pytania dotyczące usług FHIR Services na platformie Azure — Azure API for FHIR
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API platformy Azure dla usługi FHIR, takich jak lokalizacja przechowywania danych za interfejsy API FHIR i obsługa wersji.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 35b59fb0583911b5b9faee96276d1bb09a8d6679
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269713"
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

### <a name="can-i-create-a-custom-fhir-resource"></a>Czy można utworzyć niestandardowy zasób FHIR?

Nie zezwalamy na używanie niestandardowych zasobów FHIR. Jeśli potrzebujesz niestandardowego zasobu FHIR, możesz utworzyć zasób niestandardowy na podstawie [podstawowego zasobu](http://www.hl7.org/fhir/basic.html) z rozszerzeniami. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Czy [rozszerzenia](https://www.hl7.org/fhir/extensibility.html) są obsługiwane w interfejsie API platformy Azure dla FHIR?

Zezwalamy na ładowanie dowolnych prawidłowych danych JSON FHIR na serwer. Jeśli chcesz przechowywać definicję struktury definiującą rozszerzenie, możesz ją zapisać jako zasób definicji struktury. Obecnie nie można wyszukiwać rozszerzeń.

### <a name="what-is-the-limit-on-_count"></a>Jaki jest limit _count?

Bieżący limit liczby wynosi 100.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Czy istnieją jakieś ograniczenia dotyczące funkcji eksportowania grup?

W przypadku eksportu grupy eksportuje tylko dołączone odwołania z grupy, nie wszystkie właściwości [zasobu grupy](https://www.hl7.org/fhir/group.html).

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Czy mogę opublikować pakiet w interfejsie API platformy Azure dla usługi FHIR?

Obecnie obsługujemy ogłaszanie [pakietów wsadowych](https://www.hl7.org/fhir/valueset-bundle-type.html) , ale nie obsługują ogłaszania pakietów transakcji w interfejsie API platformy Azure dla FHIR. Można użyć serwera FHIR "open source", który jest obsługiwany przez program SQL do publikowania pakietów transakcji.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Jak mogę uzyskać dostęp do wszystkich zasobów dla pojedynczego pacjenta w interfejsie API platformy Azure dla usługi FHIR?

Obsługujemy [Wyszukiwanie przedziałów](https://www.hl7.org/fhir/compartmentdefinition.html) w interfejsie API platformy Azure dla FHIR. Dzięki temu można uzyskać wszystkie zasoby związane z konkretnym pacjentem. Należy zauważyć, że przedziały teraz obejmują wszystkie zasoby związane z pacjentem, ale nie sam pacjenta, dlatego należy również przeszukać, aby uzyskać pacjenta, jeśli potrzebujesz zasobu pacjenta w wynikach.

Poniżej przedstawiono przykłady poniżej:

* Pobierz pacjenta/<id>/*
* Pobierz pacjent/ <id> /Observation
* POBRAĆ pacjenta/ <id> /Observation? Code = 8302-2

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Gdzie mogę zobaczyć przykłady użycia interfejsu API platformy Azure dla usługi FHIR w ramach przepływu pracy?

W witrynie [GitHub architektury kondycji](https://github.com/microsoft/health-architectures)dostępna jest kolekcja architektur referencyjnych.

## <a name="azure-iot-connector-for-fhir-preview"></a>Łącznik usługi Azure IoT dla FHIR (wersja zapoznawcza)

### <a name="what-is-iomt"></a>Co to jest IoMT?
IoMT to Internet rzeczy medycznych i jest kategorią urządzeń IoT, które przechwytują i wymieniają dane o kondycji i wellness z innymi systemami IT z branży IT za pośrednictwem sieci. Niektóre przykłady urządzeń IoMT obejmują przydatność i kliniczne noszenia, czujniki monitorowania, śledzenie działań, punkty kiosków opieki, a nawet inteligentne pill.

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>Ile jest potrzebnych łączników usługi Azure IoT for FHIR (wersja zapoznawcza)?
Pojedynczy łącznik usługi Azure IoT dla FHIR * może służyć do pozyskiwania danych z dużej liczby różnych typów urządzeń. Nadal możesz zdecydować się na użycie różnych łączników z następujących powodów:
- **Skala**: w publicznej wersji zapoznawczej łącznik usługi Azure IoT dla FHIR pojemność zasobów został rozwiązany i oczekuje się, że zapewnia przepływność na około 200 komunikatów na sekundę. Jeśli potrzebujesz większej przepływności, możesz dodać więcej łącznika usługi Azure IoT dla FHIR.
- **Typ urządzenia**: możesz skonfigurować oddzielny łącznik usługi Azure IoT dla FHIR dla każdego typu urządzeń IoMT z przyczyn związanych z zarządzaniem urządzeniami.

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>Czy istnieje ograniczenie liczby łącznika usługi Azure IoT for FHIR (wersja zapoznawcza) w publicznej wersji zapoznawczej?
Tak. możesz utworzyć tylko dwa łączniki usługi Azure IoT dla FHIR na subskrypcję, gdy ta funkcja jest w publicznej wersji zapoznawczej. Ten limit istnieje, aby zapobiec nieoczekiwanemu kosztowi, ponieważ funkcja jest dostępna bezpłatnie w wersji zapoznawczej. Na żądanie ten limit może zostać podniesiony do maksymalnie pięciu łączników usługi Azure IoT dla FHIR.

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>Które regiony platformy Azure Azure IoT Connector for FHIR (wersja zapoznawcza) są dostępne w ramach publicznej wersji zapoznawczej?
Łącznik usługi Azure IoT dla FHIR jest dostępny we wszystkich regionach świadczenia usługi Azure, w których dostępny jest interfejs Azure API for FHIR.

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Czy mogę skonfigurować skalowanie pojemności dla łącznika usługi Azure IoT for FHIR (wersja zapoznawcza)?
Ponieważ łącznik usługi Azure IoT for FHIR jest bezpłatny w publicznej wersji zapoznawczej, jego pojemność skalowania jest stała i ograniczona. Łącznik usługi Azure IoT dla konfiguracji FHIR dostępny w publicznej wersji zapoznawczej powinien zapewniać przepływność na około 200 komunikatów na sekundę. Część konfiguracji wydajności zasobów zostanie udostępniona w ogólnej dostępności.

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Jaka wersja FHIR jest obsługiwana przez łącznik usługi Azure IoT dla FHIR (wersja zapoznawcza)?
Łącznik usługi Azure IoT dla FHIR obecnie obsługuje tylko FHIR w wersji R4. W związku z tym ta funkcja jest widoczna tylko w wystąpieniach usługi Azure API for FHIR, a firma Microsoft nie planuje obsługi wersji STU3 w tym momencie.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Dlaczego nie mogę zainstalować łącznika usługi Azure IoT for FHIR (wersja zapoznawcza), gdy w interfejsie API platformy Azure dla usługi FHIR jest włączony link prywatny?
Łącznik usługi Azure IoT dla programu FHIR nie obsługuje teraz funkcji prywatnego linku. W związku z tym, jeśli masz link prywatny włączony w interfejsie API platformy Azure dla usługi FHIR, nie możesz zainstalować łącznika usługi Azure IoT dla FHIR i na odwrót. To ograniczenie jest oczekiwane, gdy łącznik usługi Azure IoT for FHIR jest dostępny do ogólnej dostępności.

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>Jaka jest różnica między łącznikiem typu open source IoMT FHIR dla platformy Azure i łącznika usługi Azure IoT dla FHIR (wersja zapoznawcza) usługi Azure API for FHIR?
Łącznik usługi Azure IoT dla FHIR to hostowana i zarządzana wersja łącznika FHIR typu open source dla platformy Azure. W usłudze zarządzanej firma Microsoft udostępnia wszystkie czynności konserwacyjne i aktualizacje.

Gdy korzystasz z łącznika IoMT FHIR dla platformy Azure, masz bezpośredni dostęp do podstawowych zasobów. Jednak użytkownik jest odpowiedzialny za utrzymywanie i aktualizowanie serwera oraz wszystkich wymaganych czynności związanych ze zgodnością, Jeśli przechowujesz dane Fi.

Z punktu widzenia projektowania każda funkcja jest wdrażana na łączniku IoMT FHIR dla platformy Azure w pierwszej kolejności. Po sprawdzeniu poprawności w wersji open source zostanie ona wydana do PaaS Azure IoT Connector for FHIR Feature API for FHIR Service. Czas między wydaniem w składniku typu open source i PaaS zależy od złożoności funkcji i innych priorytetów mapy drogowej.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono kilka często zadawanych pytań dotyczących interfejsu API platformy Azure dla usługi FHIR. Przeczytaj o obsługiwanych funkcjach w programie FHIR Server for Azure:
 
>[!div class="nextstepaction"]
>[Obsługiwane funkcje FHIR](fhir-features-supported.md)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza).

FHIR to zastrzeżony znak towarowy firmy HL7 i jest używany za jej pozwoleniem.