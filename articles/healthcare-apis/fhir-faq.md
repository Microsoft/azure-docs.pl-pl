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
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "84871778"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Często zadawane pytania dotyczące interfejsu API platformy Azure dla usługi FHIR

## <a name="what-is-fhir"></a>Co to jest FHIR?
Zasoby współdziałania w ramach usługi opieki zdrowotnej (FHIR "Fire") to standard interoperacyjności, który umożliwia wymianę danych opieki zdrowotnej między różnymi systemami kondycji. Ten standard został opracowany przez organizację HL7 i jest przyjmowany przez organizacje opieki zdrowotnej na całym świecie. Najnowsza dostępna wersja FHIR to R4 (wersja 4). Interfejs API platformy Azure dla usługi FHIR obsługuje R4, a także obsługuje poprzednią wersję STU3 (standard do użycia w wersji próbnej 3). Aby uzyskać więcej informacji na temat FHIR, odwiedź stronę [HL7.org](http://hl7.org/fhir/summary.html).

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Czy dane znajdują się w ramach interfejsów API FHIR przechowywanych na platformie Azure?

Tak, dane są przechowywane w zarządzanych bazach danych na platformie Azure. Interfejs API platformy Azure dla usługi FHIR nie zapewnia bezpośredniego dostępu do podstawowego magazynu danych.

## <a name="what-identity-provider-do-you-support"></a>Jaki dostawca tożsamości jest obsługiwany?

Obecnie obsługujemy Microsoft Azure Active Directory jako dostawcę tożsamości.

## <a name="what-fhir-version-do-you-support"></a>Jaka wersja FHIR jest obsługiwana?

Obsługujemy wersje 4.0.0 i 3.0.1 na platformie Azure API for FHIR (PaaS) i FHIR Server for Azure (Open Source).

Aby uzyskać szczegółowe informacje, zobacz [obsługiwane funkcje](fhir-features-supported.md). Przeczytaj o tym, co się zmieniło między wersjami w [historii wersji dla HL7 FHIR](https://hl7.org/fhir/R4/history.html).

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Jaka jest różnica między programem Microsoft FHIR Server for Azure i interfejsem API platformy Azure dla FHIR?

Interfejs Azure API for FHIR to hostowana i zarządzana wersja programu Microsoft FHIR Server typu open source dla platformy Azure. W usłudze zarządzanej firma Microsoft udostępnia wszystkie czynności konserwacyjne i aktualizacje. 

Gdy korzystasz z programu FHIR Server for Azure, masz bezpośredni dostęp do podstawowych usług. Jednak użytkownik jest odpowiedzialny za utrzymywanie i aktualizowanie serwera oraz wszystkich wymaganych czynności związanych ze zgodnością, Jeśli przechowujesz dane Fi.

Z punktu widzenia projektowania każda funkcja jest wdrażana na serwerze programu Microsoft FHIR na platformie Azure w pierwszej kolejności. Po sprawdzeniu poprawności w programie Open Source zostanie on opublikowany w PaaS Azure API for FHIR Solution. Czas między wydaniem w programie typu open source i PaaS zależy od złożoności funkcji i innych priorytetów związanych z planem. 

## <a name="what-is-smart-on-fhir"></a>Co to jest funkcja SMART on FHIR?

INTELIGENTNE (podstawiane aplikacje medyczne i technologia wielokrotnego użytku) w systemie FHIR to zestaw otwartych specyfikacji umożliwiających integrację aplikacji partnerskich z serwerami FHIR i innymi systemami IT o kondycji, takimi jak rejestry kondycji elektronicznej i wymiana informacji o kondycji. Tworząc INTELIGENTNą aplikację FHIR, możesz zapewnić dostęp do aplikacji i wykorzystać ją przez mnóstwo różnych systemów.
Uwierzytelnianie i interfejs API platformy Azure dla usługi FHIR. Aby dowiedzieć się więcej na temat inteligentnych, odwiedź stronę [Smart Health](https://smarthealthit.org/).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono kilka często zadawanych pytań dotyczących interfejsu API platformy Azure dla usługi FHIR. Przeczytaj o obsługiwanych funkcjach w programie FHIR Server for Azure:
 
>[!div class="nextstepaction"]
>[Obsługiwane funkcje FHIR](fhir-features-supported.md)