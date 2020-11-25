---
title: Odporne interfejsy z procesami zewnętrznymi przy użyciu Azure AD B2C | Microsoft Docs
description: Metody tworzenia odpornych interfejsów z procesami zewnętrznymi
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c96856c988cae891e64ddf460d61851102e4666c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919813"
---
# <a name="resilient-interfaces-with-external-processes"></a>Odporne interfejsy z procesami zewnętrznymi

Ten artykuł zawiera wskazówki dotyczące planowania i implementowania interfejsów API RESTFul w podróży użytkowników oraz zwiększania odporności aplikacji na awarie interfejsów API.

![Obraz przedstawia interfejsy ze składnikami procesu zewnętrznego](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>Zapewnij poprawne umieszczanie interfejsów API

Zasady programu Identity Experience Framework (IEF) umożliwiają wywoływanie systemu zewnętrznego przy użyciu [profilu technicznego interfejsu API RESTful](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile). Systemy zewnętrzne nie są kontrolowane przez środowisko uruchomieniowe IEF i są potencjalnym punktem awarii.

### <a name="how-to-manage-external-systems-using-apis"></a>Jak zarządzać systemami zewnętrznymi przy użyciu interfejsów API

- Podczas wywoływania interfejsu w celu uzyskania dostępu do określonych danych sprawdź, czy dane mają na celu podjęcie decyzji uwierzytelniania. Oceń, czy informacje są istotne dla podstawowych funkcji aplikacji. Na przykład handlu elektronicznego a funkcjami dodatkowymi, takimi jak administracja. Jeśli informacje nie są potrzebne do uwierzytelniania i są wymagane tylko w przypadku scenariuszy pomocniczych, rozważ przeniesienie wywołania do logiki aplikacji.

- Jeśli dane, które są niezbędne do uwierzytelniania, są stosunkowo statyczne i małe i nie mają innych powodów firmy, które mają być zewnętrzne z katalogu, należy rozważyć ich utworzenie w katalogu.

- Jeśli to możliwe, należy usunąć wywołania interfejsu API ze wstępnie uwierzytelnionej ścieżki. Jeśli nie jest to możliwe, należy umieścić rygorystyczne zabezpieczenia dla ataków typu "odmowa usługi" (DoS) i rozproszonego ataku typu "odmowa usługi" (DDoS) przed interfejsem API. Osoby atakujące mogą załadować stronę logowania i spróbować zapełnić interfejs API atakami DoS i Cripple aplikację. Na przykład przy użyciu usługi CAPTCHA w ramach logowania może być pomocna Rejestracja w usłudze Flow.

- Użyj [łączników interfejsu API wbudowanego przepływu użytkownika tworzenia konta](https://docs.microsoft.com/azure/active-directory-b2c/api-connectors-overview) , wszędzie tam, gdzie jest to możliwe do integracji z internetowymi interfejsami API albo po zalogowaniu się przy użyciu dostawcy tożsamości lub przed utworzeniem użytkownika. Ponieważ przepływy użytkowników są już szeroko przetestowane, prawdopodobnie nie trzeba wykonywać testów funkcjonalnych, wydajności i skalowania na poziomie przepływu użytkownika. Nadal konieczne jest przetestowanie aplikacji w celu zapewnienia funkcjonalności, wydajności i skalowania.

- [Profile techniczne](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile) interfejsu API usługi Azure AD RESTFul nie zapewniają żadnych zachowań buforowania. Zamiast tego profil interfejsu API RESTFul implementuje logikę ponowień i przekroczenie limitu czasu, który jest wbudowany w zasady.

- W przypadku interfejsów API, które wymagają zapisywania danych, należy kolejkować zadanie w celu wykonania takich zadań przez proces roboczy w tle. Mogą być używane usługi, takie jak [kolejki platformy Azure](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction) . Dzięki temu interfejs API może efektywnie zwiększyć wydajność wykonywania zasad.  

## <a name="api-error-handling"></a>Obsługa błędów interfejsu API

Jako że interfejsy API na żywo spoza systemu Azure AD B2C, muszą mieć prawidłową obsługę błędów w profilu technicznym. Upewnij się, że użytkownik końcowy jest odpowiednio poinformowany i aplikacja może zająć się niepowodzeniem.

### <a name="how-to-gracefully-handle-api-errors"></a>Jak bezpiecznie obsługiwać błędy interfejsu API

- Interfejs API może się nie powieść z różnych powodów, aby aplikacja była odporna na awarie. [Zwróć komunikat o błędzie HTTP 4xx](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#returning-validation-error-message) , jeśli interfejs API nie może wykonać żądania. W zasadach Azure AD B2C spróbuj łagodnie obsłużyć niedostępność interfejsu API i być może to spowodować zmniejszenie wydajności.

- [Łagodnie Obsługuj błędy przejściowe](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#error-handling). Profil interfejsu API RESTFul umożliwia skonfigurowanie komunikatów o błędach dla różnych [wyłączników](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker).

- Proaktywne monitorowanie i używanie ciągłej integracji/ciągłego dostarczania (CICD), obracanie poświadczeń dostępu do interfejsu API, takich jak hasła i certyfikaty używane przez [aparat profilu technicznego](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile).

## <a name="api-management---best-practices"></a>API Management — najlepsze rozwiązania

Podczas wdrażania interfejsów API REST i konfigurowania profilu technicznego RESTful, postępując zgodnie z zalecanymi najlepszymi rozwiązaniami, nie można wyszukiwać często spotykanych błędów i elementów.

### <a name="how-to-manage-apis"></a>Jak zarządzać interfejsami API

- API Management (APIM) publikuje i analizuje interfejsy API oraz zarządza nimi. APIM obsługuje również uwierzytelnianie w celu zapewnienia bezpiecznego dostępu do usług zaplecza i mikrousług. Użyj bramy interfejsu API do skalowania wdrożeń interfejsu API, buforowania i równoważenia obciążenia.

- Zalecenie polega na uzyskaniu odpowiedniego tokenu na początku podróży użytkownika, zamiast wywoływać wiele razy dla każdego interfejsu API i [zabezpieczyć interfejs API usługi Azure APIM](https://docs.microsoft.com/azure/active-directory-b2c/secure-api-management?tabs=app-reg-ga).

## <a name="next-steps"></a>Następne kroki

- [Zasoby dotyczące odporności dla deweloperów Azure AD B2C](resilience-b2c.md)
  - [Odporne środowisko użytkownika końcowego](resilient-end-user-experience.md)
  - [Odporność na najlepsze rozwiązania dla deweloperów](resilience-b2c-developer-best-practices.md)
  - [Odporność dzięki monitorowaniu i analizie](resilience-with-monitoring-alerting.md)
- [Tworzenie odporności w infrastrukturze uwierzytelniania](resilience-in-infrastructure.md)
- [Zwiększanie odporności uwierzytelniania i autoryzacji w aplikacjach](resilience-app-development-overview.md)
