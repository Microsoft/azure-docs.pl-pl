---
title: Co to jest Azure Static Web Apps?
description: Najważniejsze funkcje i funkcje Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: 945979daa3c766b737e5b312a6c14e60204a6a55
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874067"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Co to jest Azure Static Web Apps zapoznawcza?

Azure Static Web Apps to usługa, która automatycznie tworzy i wdraża aplikacje internetowe pełnego stosu na platformie Azure z repozytorium kodu.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Azure Static Web Apps diagram przeglądowy":::

Przepływ pracy Azure Static Web Apps jest dostosowany do codziennego przepływu pracy dewelopera. Aplikacje są budowane i wdrażane na podstawie zmian w kodzie.

Po utworzeniu zasobu Azure Static Web Apps platforma Azure współdziała bezpośrednio z usługą GitHub lub Azure DevOps w celu monitorowania wybranej gałęzi. Za każdym razem, gdy wypychasz zatwierdzenia lub akceptujesz żądania ściągnięć do obserwowanych gałęzi, kompilacja jest uruchamiana automatycznie, a aplikacja i interfejs API są wdrażane na platformie Azure.

Statyczne aplikacje internetowe są często budowane przy użyciu bibliotek i platform, takich jak Angular, React, Svelte, Vue lub Blazor, gdzie renderowanie po stronie serwera nie jest wymagane. Te aplikacje obejmują zasoby HTML, CSS, JavaScript i obrazy, które tworzą aplikację. W przypadku tradycyjnego serwera internetowego te zasoby są obsługiwane z jednego serwera wraz z dowolnymi wymaganymi punktami końcowymi interfejsu API.

W Static Web Apps statyczne są oddzielone od tradycyjnego serwera internetowego i zamiast nich są obsługiwane z punktów geograficznie rozproszonych po całym świecie. Takie rozproszenie sprawia, że pliki są obsługiwane znacznie szybciej, ponieważ znajdują się fizycznie bliżej użytkowników końcowych. Ponadto punkty końcowe interfejsu API są hostowane przy użyciu architektury bez serwera [,](../azure-functions/functions-overview.md)co pozwala uniknąć konieczności stosowania pełnego serwera końcowego.

## <a name="key-features"></a>Najważniejsze funkcje

- **Hosting internetowy** zawartości statycznej, np. HTML, CSS, JavaScript i obrazy.
- **Zintegrowana obsługa interfejsu API** zapewniana przez Azure Functions.
- **Najwyższej klasy usługa GitHub i Azure DevOps,** w której zmiany repozytorium wyzwalają kompilacje i wdrożenia.
- **Globalnie dystrybuowana** zawartość statyczna, przybliżając zawartość do użytkowników.
- **Bezpłatne certyfikaty SSL,** które są automatycznie odnawiane.
- **Domeny niestandardowe** w celu zapewnienia dostosowywania aplikacji pod marką.
- **Bezproblemowy model zabezpieczeń** z zwrotny serwer proxy podczas wywoływania interfejsów API, który nie wymaga konfiguracji mechanizmu CORS.
- **Integracje dostawców uwierzytelniania z** Azure Active Directory, Facebook, Google, GitHub i Twitter.
- **Dostosowywalne definicje i przypisania roli** autoryzacji.
- **Reguły rozsyłania na** zadomowiu umożliwiające pełną kontrolę nad zawartością i trasami, które są przez Ciebie dostarczane.
- **Wygenerowano wersje przejściowe obsługiwane przez** żądania ściągnięcia umożliwiające wersje zapoznawcze witryny przed opublikowaniem.

## <a name="what-you-can-do-with-static-web-apps"></a>Co można zrobić za pomocą Static Web Apps

- Twórz nowoczesne **aplikacje** internetowe za pomocą platform i bibliotek języka JavaScript, takich jak [Angular,](getting-started.md?tabs=angular) [React,](getting-started.md?tabs=react) [Svelte,](/learn/modules/publish-app-service-static-web-app-api/) [Vue](getting-started.md?tabs=vue)lub używając [blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) do tworzenia aplikacji WebAssembly z Azure Functions serwera. [](apis.md)
- **Publikowanie witryn statycznych** przy użyciu platform takich jak [Gatsby,](publish-gatsby.md) [Hugo,](publish-hugo.md) [VuePress.](publish-vuepress.md)
- **Wdrażanie aplikacji internetowych za** pomocą platform takich [ jakNext.js](deploy-nextjs.md) i [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie pierwszej aplikacji statycznej](getting-started.md)
