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
ms.openlocfilehash: bd6f32213a511987c0508dcc954252b2fd4e48f6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737595"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Często zadawane pytania dotyczące interfejsu API platformy Azure dla usługi FHIR

## <a name="azure-api-for-fhir-the-basics"></a>Azure API for FHIR — podstawy

### <a name="what-is-fhir"></a>Co to jest FHIR?
Zasoby współdziałania w ramach usługi opieki zdrowotnej (FHIR "Fire") to standard interoperacyjności, który umożliwia wymianę danych opieki zdrowotnej między różnymi systemami kondycji. Ten standard został opracowany przez organizację HL7 i jest przyjmowany przez organizacje opieki zdrowotnej na całym świecie. Najnowsza dostępna wersja FHIR to R4 (wersja 4). Interfejs API platformy Azure dla usługi FHIR obsługuje R4, a także obsługuje poprzednią wersję STU3 (standard do użycia w wersji próbnej 3). Aby uzyskać więcej informacji na temat FHIR, odwiedź stronę [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Czy dane znajdują się w ramach interfejsów API FHIR przechowywanych na platformie Azure?

Tak, dane są przechowywane w zarządzanych bazach danych na platformie Azure. Interfejs API platformy Azure dla usługi FHIR nie zapewnia bezpośredniego dostępu do podstawowego magazynu danych.

### <a name="what-identity-provider-do-you-support"></a>Jaki dostawca tożsamości jest obsługiwany?

Obecnie obsługujemy Microsoft Azure Active Directory jako dostawcę tożsamości.

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Jaki jest cel punktu odzyskiwania (RPO) dla interfejsu API platformy Azure dla usługi FHIR?
Usługa Azure API for FHIR jest obsługiwana przez Cosmos DB jako nasz dostawca trwałości. W związku z tym cel punktu odzyskiwania dla usługi jest równy [Cosmos dB (pojedynczy region)](../cosmos-db/consistency-levels.md) i jest < 240 minut.

### <a name="what-fhir-version-do-you-support"></a>Jaka wersja FHIR jest obsługiwana?

Obsługujemy wersje 4.0.0 i 3.0.1 na platformie Azure API for FHIR (PaaS) i FHIR Server for Azure (Open Source).

Aby uzyskać szczegółowe informacje, zobacz [obsługiwane funkcje](fhir-features-supported.md). Zapoznaj się z informacjami o tym, co zmieniło się w wersjach FHIR (tj. STU3 do R4) w [historii wersji dla HL7 FHIR](https://hl7.org/fhir/R4/history.html).

Łącznik usługi Azure IoT dla FHIR (wersja zapoznawcza) obecnie obsługuje tylko FHIR w wersji R4 i jest widoczny tylko w wystąpieniach usługi Azure API dla FHIR.

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Jaka jest różnica między elementami "Microsoft FHIR Server for Azure" i "Azure API for FHIR"?

Interfejs Azure API for FHIR to hostowana i zarządzana wersja programu Microsoft FHIR Server typu open source dla platformy Azure. W usłudze zarządzanej firma Microsoft udostępnia wszystkie czynności konserwacyjne i aktualizacje. 

Po uruchomieniu serwera FHIR dla platformy Azure użytkownik ma bezpośredni dostęp do podstawowych usług, ale jest odpowiedzialny za konserwację i aktualizowanie serwera oraz wszystkich wymaganych czynności związanych z zgodnością, Jeśli przechowujesz dane Fi.

W przypadku rozwoju punktu widzenia każda funkcja, która nie dotyczy usługi zarządzanej, jest najpierw wdrażana na serwerze typu open source programu Microsoft FHIRe dla platformy Azure. Po sprawdzeniu poprawności w programie Open Source zostanie on opublikowany w PaaS Azure API for FHIR Solution. Czas między wydaniem w programie typu open source i PaaS zależy od złożoności funkcji i innych priorytetów związanych z planem. Jest to ten sam proces dla wszystkich naszych usług, takich jak Azure IoT Connector for FHIR (wersja zapoznawcza).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Gdzie mogę zobaczyć, co jest uwalniane do interfejsu API platformy Azure dla usługi FHIR?

Aby zobaczyć, co jest zwalniane w interfejsie API platformy Azure dla usługi FHIR, zapoznaj się z [wersją](https://github.com/microsoft/fhir-server/releases) serwera FHIR typu open source. Począwszy od listopada 2020, oznakowano elementy z użyciem platformy Azure-API-for-FHIR, jeśli element "open source" zostanie zwolniony do usługi zarządzanej. Te funkcje są zwykle dostępne dwa tygodnie po stronie wydania w programie Open Source. Zawarto również instrukcje dotyczące testowania kompilacji [tutaj] ( https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) Jeśli chcesz przetestować w Twoim środowisku. Oceniamy, jak najlepiej udostępniać dodatkowe zarządzane Aktualizacje usług.

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>W jakich regionach jest dostępny interfejs API platformy Azure dla usługi FHIR?

Obecnie ogólnie dostępna jest ogólna dostępność zarówno dla publiczności, jak i dla instytucji rządowych w [wielu regionach geograficznych](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia). Aby uzyskać informacje o usługach w chmurze dla instytucji rządowych firmy Microsoft, zapoznaj się z [usługami platformy Azure według FedRAMP](../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Gdzie mogę zobaczyć, co jest uwalniane do interfejsu API platformy Azure dla usługi FHIR?

Aby zobaczyć, co jest zwalniane w interfejsie API platformy Azure dla usługi FHIR, zapoznaj się z [wersją](https://github.com/microsoft/fhir-server/releases) serwera FHIR typu open source. Firma Microsoft pracowała w oznaczeniu elementów za pomocą platformy Azure-API-for-FHIR, jeśli zostaną one wydane do usługi zarządzanej i są zwykle dostępne dwa tygodnie po stronie wydania w programie Open Source. Uwzględniono również instrukcje dotyczące testowania kompilacji w [tym miejscu](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) , jeśli chcesz przetestować w środowisku własnym. Oceniamy, jak najlepiej udostępniać dodatkowe zarządzane Aktualizacje usług.

### <a name="what-is-smart-on-fhir"></a>Co to jest funkcja SMART on FHIR?

INTELIGENTNE (podstawiane aplikacje medyczne i technologia wielokrotnego użytku) w systemie FHIR to zestaw otwartych specyfikacji umożliwiających integrację aplikacji partnerskich z serwerami FHIR i innymi systemami IT o kondycji, takimi jak rejestry kondycji elektronicznej i wymiana informacji o kondycji. Tworząc INTELIGENTNą aplikację FHIR, możesz zapewnić dostęp do aplikacji i wykorzystać ją przez mnóstwo różnych systemów.
Uwierzytelnianie i interfejs API platformy Azure dla usługi FHIR. Aby dowiedzieć się więcej na temat inteligentnych, odwiedź stronę [Smart Health](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Gdzie mogę sprawdzić, jaka wersja FHIR jest uruchomiona w mojej bazie danych. 

Dokładną wersję FHIR można znaleźć uwidocznioną w instrukcji możliwości we właściwości "fhirVersion".

## <a name="fhir-implementations-and-specifications"></a>Implementacje i specyfikacje FHIR

### <a name="can-i-create-a-custom-fhir-resource"></a>Czy można utworzyć niestandardowy zasób FHIR?

Nie zezwalamy na używanie niestandardowych zasobów FHIR. Jeśli potrzebujesz niestandardowego zasobu FHIR, możesz utworzyć zasób niestandardowy na podstawie [podstawowego zasobu](http://www.hl7.org/fhir/basic.html) z rozszerzeniami. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Czy [rozszerzenia](https://www.hl7.org/fhir/extensibility.html) są obsługiwane w interfejsie API platformy Azure dla FHIR?

Zezwalamy na ładowanie dowolnych prawidłowych danych JSON FHIR na serwer. Jeśli chcesz przechowywać definicję struktury definiującą rozszerzenie, możesz ją zapisać jako zasób definicji struktury. Obecnie nie można wyszukiwać rozszerzeń.

### <a name="what-is-the-limit-on-_count"></a>Jaki jest limit _count?

Bieżący limit _count to 100. W przypadku ustawienia _count na więcej niż 100, w pakiecie zostanie wyświetlone ostrzeżenie, że będą wyświetlane tylko rekordy 100.

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

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>Co to jest domyślne sortowanie podczas wyszukiwania zasobów w usłudze Azure API for FHIR?

Obsługujemy sortowanie według daty ostatniej aktualizacji: _sort = _lastUpdated. Aby uzyskać więcej informacji na temat innych obsługiwanych parametrów wyszukiwania, zapoznaj się z naszymi [obsługiwanymi funkcjami](./fhir-features-supported.md#search).

### <a name="does-the-azure-api-for-fhir-support-everything"></a>Czy usługa Azure API for FHIR obsługuje $everything? 

Nie. W tej chwili nie obsługujemy $everything. Można go jednak osiągnąć przy użyciu dwóch wywołań interfejsu API. Aby na przykład uzyskać dostęp do wszystkich elementów pacjenta $, możesz najpierw pobrać rekord pacjenta przy użyciu/Patient/[ID], a następnie drugie wywołanie do pobrania wszystkich danych pacjenta przy użyciu/Patient/[ID]/*.

Więcej szczegółów można znaleźć w tym [wpisie społeczności](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/.24everything.20with.20_type). 

### <a name="how-does-export-work"></a>Jak działa $export?

$export jest częścią specyfikacji FHIR: https://hl7.org/fhir/uv/bulkdata/export/index.html . Jeśli usługa FHIR jest skonfigurowana przy użyciu tożsamości zarządzanej i konta magazynu, a zarządzana tożsamość ma dostęp do tego konta magazynu — możesz po prostu wywołać $export w interfejsie API FHIR, a wszystkie zasoby FHIR zostaną wyeksportowane do konta magazynu. Aby uzyskać więcej informacji, zapoznaj się [z artykułem na $Export](./export-data.md).

## <a name="using-azure-api-for-fhir"></a>Korzystanie z interfejsu API platformy Azure dla usługi FHIR

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Jak mogę włączyć usługę log Analytics dla usługi Azure API for FHIR?

Włączamy rejestrowanie diagnostyczne i zezwalają na przeglądanie przykładowych zapytań dla tych dzienników. Aby uzyskać szczegółowe informacje na temat włączania dzienników inspekcji i przykładowych zapytań, zapoznaj się z [tą sekcją](./enable-diagnostic-logging.md). Jeśli chcesz uwzględnić dodatkowe informacje w dziennikach, Wyewidencjonuj [przy użyciu niestandardowych nagłówków HTTP](./use-custom-headers.md).

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Gdzie mogę zobaczyć przykłady użycia interfejsu API platformy Azure dla usługi FHIR w ramach przepływu pracy?

W witrynie [GitHub architektury kondycji](https://github.com/microsoft/health-architectures)dostępna jest kolekcja architektur referencyjnych.

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>Gdzie można zobaczyć przykład łączenia aplikacji sieci Web z usługą Azure API for FHIR?

Mamy stronę usługi [GitHub o architekturze kondycji](https://aka.ms/health-architectures) , która zawiera przykładowe aplikacje i scenariusze. Pokazano, jak połączyć aplikację sieci Web z interfejsem API platformy Azure dla FHIR.  

## <a name="azure-api-for-fhir-features-and-services"></a>Interfejs API platformy Azure dla funkcji i usług FHIR 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>Czy istnieje sposób, aby zaszyfrować dane przy użyciu klucza osobistego nie klucza domyślnego?

Tak, usługa Azure API for FHIR umożliwia konfigurowanie kluczy zarządzanych przez klienta, korzystanie z pomocy technicznej Cosmos DB. Aby uzyskać więcej informacji na temat szyfrowania danych za pomocą klucza osobistego, zapoznaj się z [tą sekcją](./customer-managed-key.md).

## <a name="azure-api-for-fhir-preview-features"></a>Azure API for FHIR: funkcje w wersji zapoznawczej

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Czy mogę skonfigurować skalowanie pojemności dla łącznika usługi Azure IoT for FHIR (wersja zapoznawcza)?

Ponieważ łącznik usługi Azure IoT for FHIR jest bezpłatny w publicznej wersji zapoznawczej, jego pojemność skalowania jest stała i ograniczona. Łącznik usługi Azure IoT dla konfiguracji FHIR dostępny w publicznej wersji zapoznawczej powinien zapewniać przepływność na około 200 komunikatów na sekundę. Część konfiguracji wydajności zasobów zostanie udostępniona w ogólnej dostępności.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Dlaczego nie mogę zainstalować łącznika usługi Azure IoT for FHIR (wersja zapoznawcza), gdy w interfejsie API platformy Azure dla usługi FHIR jest włączony link prywatny?

Łącznik usługi Azure IoT dla programu FHIR nie obsługuje teraz funkcji prywatnego linku. W związku z tym, jeśli masz link prywatny włączony w interfejsie API platformy Azure dla usługi FHIR, nie możesz zainstalować łącznika usługi Azure IoT dla FHIR i na odwrót. To ograniczenie jest oczekiwane, gdy łącznik usługi Azure IoT for FHIR jest dostępny do ogólnej dostępności.
