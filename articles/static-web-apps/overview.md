---
title: Co to jest Azure Static Web Apps?
description: Najważniejsze funkcje i funkcje statycznej Web Apps platformy Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 47140bccc8c2a1b69f083502d031a9db6d21294e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835786"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Co to jest usługa Azure static Web Apps Preview?

Azure static Web Apps to usługa, która automatycznie kompiluje i wdraża aplikacje sieci Web w pełnym stosie na platformie Azure z repozytorium GitHub.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Statyczne Web Apps — Omówienie":::

Przepływ pracy Web Apps statycznej platformy Azure jest dostosowany do codziennego przepływu pracy dewelopera. Aplikacje są kompilowane i wdrażane w oparciu o interakcje w serwisie GitHub.

Po utworzeniu zasobu usługi Azure Static Web Apps platforma Azure skonfiguruje przepływ pracy funkcji GitHub Actions w repozytorium kodu źródłowego aplikacji, które monitoruje wybraną przez Ciebie gałąź. Za każdym razem, gdy wypychanie zatwierdzeń lub zaakceptowanie żądań ściągnięcia do rozgałęzienia obserwowane, Akcja GitHub automatycznie kompiluje i wdraża aplikację oraz jej interfejs API na platformie Azure.

Statyczne aplikacje internetowe są często kompilowane przy użyciu bibliotek i struktur, takich jak Angular, React, Svelte lub Vue. Te aplikacje obejmują zasoby HTML, CSS, JavaScript i obrazy, które tworzą aplikację. W przypadku tradycyjnego serwera sieci Web te zasoby są obsługiwane z jednego serwera wraz z dowolnym wymaganym punktami końcowymi interfejsu API.

W przypadku statycznych Web Apps zasoby statyczne są oddzielone od tradycyjnego serwera sieci Web i zamiast tego są obsługiwane z punktów geograficznie rozmieszczonych na całym świecie. Takie rozproszenie sprawia, że pliki są obsługiwane znacznie szybciej, ponieważ znajdują się fizycznie bliżej użytkowników końcowych. Ponadto punkty końcowe interfejsu API są hostowane przy użyciu [architektury bezserwerowej](../azure-functions/functions-overview.md), co pozwala uniknąć konieczności pełnego serwera zaplecza.

## <a name="key-features"></a>Najważniejsze funkcje

- **Hosting internetowy** dla zawartości statycznej, takiej jak HTML, CSS, JavaScript i obrazy.
- **Zintegrowana obsługa interfejsu API** zapewniana przez Azure Functions.
- **Integracja z usługą GitHub dla pierwszej firmy** , w której zmiany repozytoriów wyzwalają kompilacje i wdrożenia.
- **Globalnie dystrybuowana** zawartość statyczna, która umieszcza zawartość bliżej użytkowników.
- **Bezpłatne certyfikaty SSL**, które są automatycznie odnawiane.
- **Domeny niestandardowe** \* , aby zapewnić dostosowywać markę do aplikacji.
- **Bezproblemowe model zabezpieczeń** z zwrotnym serwerem proxy podczas wywoływania interfejsów API, które nie wymagają konfiguracji mechanizmu CORS.
- **Integracja dostawcy uwierzytelniania** z usługami Azure Active Directory, Facebook, Google, GitHub i Twitter.
- **Dostosowywalne definicje i przypisania ról autoryzacji** .
- **Reguły routingu zaplecza** umożliwiają pełną kontrolę nad zawartością i trasami, które obsługujesz.
- **Wygenerowane wersje przemieszczania** obsługiwane przez żądania ściągnięcia włączające wersje zapoznawcze witryny przed opublikowaniem.

## <a name="what-you-can-do-with-static-web-apps"></a>Co możesz zrobić za pomocą Web Apps statycznej

- **Twórz nowoczesne aplikacje języka JavaScript** za pomocą platform i bibliotek, takich jak [kątowy](getting-started.md#tabpanel_CeZOj-G++Q_angular), [reaguje](getting-started.md#tabpanel_CeZOj-G++Q_react), [Svelte](https://docs.microsoft.com/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md#tabpanel_CeZOj-G++Q_vue) [Azure Functions](apis.md) z zapleczem.
- **Publikuj Lokacje statyczne** przy użyciu struktur takich jak [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Wdrażaj aplikacje sieci Web** za pomocą platform, takich jak [Next.js](deploy-nextjs.md) i [Nuxt.js](deploy-nuxtjs.md).

\*Rejestracje domeny Apex nie są obsługiwane w trakcie okresu zapoznawczego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie pierwszej aplikacji statycznej](getting-started.md)
