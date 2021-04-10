---
title: Co to jest Azure Static Web Apps?
description: Najważniejsze funkcje i funkcje statycznej Web Apps platformy Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: e81f0a9e4fc50cf0d80f2905b9328af3c721865c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166409"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Co to jest usługa Azure static Web Apps Preview?

Azure static Web Apps to usługa, która automatycznie kompiluje i wdraża aplikacje sieci Web w pełnym stosie na platformie Azure z repozytorium kodu.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Diagram omówienia Web Apps statycznej platformy Azure":::

Przepływ pracy Web Apps statycznej platformy Azure jest dostosowany do codziennego przepływu pracy dewelopera. Aplikacje są kompilowane i wdrażane na podstawie zmian w kodzie.

Po utworzeniu zasobu statycznego Web Apps platformy Azure współdziała bezpośrednio z usługą GitHub lub Azure DevOps w celu monitorowania wybranej gałęzi. Przy każdym wypchnięciu zatwierdzeń lub zaakceptowaniu żądań ściągnięcia do rozgałęzienia czujka kompilacja jest uruchamiana automatycznie, a aplikacja i interfejs API są wdrażane na platformie Azure.

Statyczne aplikacje sieci Web są często kompilowane przy użyciu bibliotek i platform, takich jak kątowy, reaguje, Svelte, VUE lub Blazor, gdzie renderowanie po stronie serwera nie jest wymagane. Te aplikacje obejmują zasoby HTML, CSS, JavaScript i obrazy, które tworzą aplikację. W przypadku tradycyjnego serwera sieci Web te zasoby są obsługiwane z jednego serwera wraz z dowolnym wymaganym punktami końcowymi interfejsu API.

W przypadku statycznych Web Apps zasoby statyczne są oddzielone od tradycyjnego serwera sieci Web i zamiast tego są obsługiwane z punktów geograficznie rozmieszczonych na całym świecie. Takie rozproszenie sprawia, że pliki są obsługiwane znacznie szybciej, ponieważ znajdują się fizycznie bliżej użytkowników końcowych. Ponadto punkty końcowe interfejsu API są hostowane przy użyciu [architektury bezserwerowej](../azure-functions/functions-overview.md), co pozwala uniknąć konieczności pełnego serwera zaplecza.

## <a name="key-features"></a>Najważniejsze funkcje

- **Hosting internetowy** dla zawartości statycznej, takiej jak HTML, CSS, JavaScript i obrazy.
- **Zintegrowana obsługa interfejsu API** zapewniana przez Azure Functions.
- **Pierwsza klasa Integracja usług GitHub i Azure DevOps, w** której zmiany repozytorium wyzwala kompilacje i wdrożenia.
- **Globalnie dystrybuowana** zawartość statyczna, która umieszcza zawartość bliżej użytkowników.
- **Bezpłatne certyfikaty SSL**, które są automatycznie odnawiane.
- **Domeny niestandardowe** umożliwiające dostosowanie marką do aplikacji.
- **Bezproblemowe model zabezpieczeń** z zwrotnym serwerem proxy podczas wywoływania interfejsów API, które nie wymagają konfiguracji mechanizmu CORS.
- **Integracja dostawcy uwierzytelniania** z usługami Azure Active Directory, Facebook, Google, GitHub i Twitter.
- **Dostosowywalne definicje i przypisania ról autoryzacji** .
- **Reguły routingu zaplecza** umożliwiają pełną kontrolę nad zawartością i trasami, które obsługujesz.
- **Wygenerowane wersje przemieszczania** obsługiwane przez żądania ściągnięcia włączające wersje zapoznawcze witryny przed opublikowaniem.

## <a name="what-you-can-do-with-static-web-apps"></a>Co możesz zrobić za pomocą Web Apps statycznej

- Twórz **nowoczesne aplikacje sieci Web** za pomocą platform i bibliotek języka JavaScript, takich jak [kątowy](getting-started.md?tabs=angular), [reaguje](getting-started.md?tabs=react), [Svelte](/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md?tabs=react)lub [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) , aby tworzyć aplikacje webassembly przy użyciu zaplecza [Azure Functions](apis.md) .
- **Publikuj Lokacje statyczne** przy użyciu struktur takich jak [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Wdrażaj aplikacje sieci Web** za pomocą platform, takich jak [Next.js](deploy-nextjs.md) i [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie pierwszej aplikacji statycznej](getting-started.md)