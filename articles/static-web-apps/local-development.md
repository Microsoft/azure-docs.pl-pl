---
title: Skonfiguruj lokalne Programowanie dla Web Apps statycznej platformy Azure
description: Dowiedz się, jak skonfigurować lokalne środowisko deweloperskie dla Web Apps statycznej platformy Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 8a45d490d060febc18d77c8487c9f562fd2a914a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275520"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Skonfiguruj lokalne Programowanie dla usługi Azure static Web Apps Preview

Po opublikowaniu w chmurze lokacja usługi Azure static Web Apps ma wiele usług, które współpracują ze sobą, tak jakby znajdowały się one w tej samej aplikacji. Te usługi to na przykład:

- Statyczna aplikacja internetowa
- Interfejs API Azure Functions
- Usługi uwierzytelniania i autoryzacji
- Usługi routingu i konfiguracji

Te usługi muszą się komunikować ze sobą, a usługa Azure static Web Apps obsługuje tę integrację w chmurze.

Uruchamianie lokalne, jednak te usługi nie są automatycznie powiązane ze sobą.

Aby zapewnić podobne środowisko w przypadku systemu Azure, [interfejs wiersza polecenia usługi Azure Static Web Apps](https://github.com/Azure/static-web-apps-cli) udostępnia następujące usługi:

- Lokalny serwer lokacji statycznej
- Serwer proxy do serwera deweloperskiego platformy frontonu
- Serwer proxy dla punktów końcowych interfejsu API — dostępne za pomocą Azure Functions Core Tools
- Uwierzytelnianie i serwer autoryzacji obrazu
- Wymuszanie ustawień konfiguracji i trasy lokalne

## <a name="how-it-works"></a>Jak to działa

Poniższy wykres pokazuje, jak żądania są obsługiwane lokalnie.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Żądanie i przepływ odpowiedzi dla statycznej aplikacji internetowej platformy Azure":::

> [!IMPORTANT]
> Przejdź do programu, aby [http://localhost:4280](http://localhost:4280) uzyskać dostęp do aplikacji obsługiwanej przez interfejs wiersza polecenia.

- **Żądania** kierowane do portu `4280` są przekazywane do odpowiedniego serwera w zależności od typu żądania.

- **Statyczne żądania zawartości** , takie jak HTML lub CSS, są obsługiwane przez wewnętrzny serwer zawartości interfejsu wiersza polecenia lub przez serwer platformy frontonu na potrzeby debugowania.

- Żądania **uwierzytelniania i autoryzacji** są obsługiwane przez emulator, który zapewnia fałszywy profil tożsamości aplikacji.

- **Środowisko uruchomieniowe podstawowych narzędzi usługi Functions** obsługuje żądania kierowane do interfejsu API lokacji.

- **Odpowiedzi** ze wszystkich usług są zwracane do przeglądarki, tak jakby znajdowały się one w jednej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- **Istniejąca lokacja Web Apps statycznej platformy Azure**: Jeśli jej nie masz, Rozpocznij od aplikacji ["Start" interfejsu API](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) .
- **[Node.js](https://nodejs.org) z npm**: Uruchom [Node.js wersję LTS](https://nodejs.org) , która obejmuje dostęp do [npm](https://www.npmjs.com/).
- **[Visual Studio Code](https://code.visualstudio.com/)**: służy do debugowania aplikacji interfejsu API, ale nie jest wymagana dla interfejsu wiersza polecenia.

## <a name="get-started"></a>Rozpoczęcie pracy

Otwórz terminal w folderze głównym istniejącej witryny Web Apps statycznej platformy Azure.

1. Zainstaluj interfejs wiersza polecenia.

    `npm install -g @azure/static-web-apps-cli`

1. Skompiluj aplikację, jeśli jest to wymagane przez aplikację.

    Uruchom `npm run build` polecenie lub równoważne polecenie dla projektu.

1. Przejdź do katalogu wyjściowego aplikacji. Foldery wyjściowe są często nazwanym _kompilacją_ lub podobną.

1. Uruchom interfejs wiersza polecenia.

    `swa start`

1. Przejdź do, aby [http://localhost:4280](http://localhost:4280) wyświetlić aplikację w przeglądarce.

### <a name="other-ways-to-start-the-cli"></a>Inne sposoby uruchamiania interfejsu wiersza polecenia

| Opis | Polecenie |
|--- | --- |
| Obsługuj określony folder | `swa start ./output-folder` |
| Korzystanie z działającego programu Framework Server | `swa start http://localhost:3000` |
| Uruchamianie aplikacji funkcji w folderze | `swa start ./output-folder --api ./api` |
| Korzystanie z działającej aplikacji Functions | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Autoryzacja i emulacja uwierzytelniania

Interfejs wiersza polecenia Web Apps static emuluje [przepływ zabezpieczeń](./authentication-authorization.md) zaimplementowany na platformie Azure. Po zalogowaniu się użytkownika można zdefiniować fałszywy profil tożsamości zwracany do aplikacji.

Na przykład podczas próby przejścia do programu `/.auth/login/github` zwracana jest strona, która umożliwia zdefiniowanie profilu tożsamości.

> [!NOTE]
> Emulator współpracuje z dowolnym dostawcą zabezpieczeń, a nie tylko w serwisie GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Emulator uwierzytelniania lokalnego i autoryzacji":::

Emulator zawiera stronę umożliwiającą podanie następujących wartości [głównych klienta](./user-information.md#client-principal-data) :

| Wartość | Opis |
| --- | --- |
| **Nazwa użytkownika** | Nazwa konta skojarzona z dostawcą zabezpieczeń. Ta wartość jest wyświetlana jako `userDetails` Właściwość w głównej siedzibie klienta i jest generowana automatycznie, jeśli nie podano wartości. |
| **Identyfikator użytkownika** | Wartość wygenerowana automatycznie przez interfejs wiersza polecenia.  |
| **Role** | Lista nazw ról, w których każda nazwa znajduje się w nowym wierszu.  |

Po zalogowaniu:

- `/.auth/me`Do pobrania [podmiotu zabezpieczeń klienta](./user-information.md)można użyć punktu końcowego lub punktu końcowego funkcji.

- Nawigowanie w celu `./auth/logout` wyczyszczenia podmiotu zabezpieczeń klienta i wylogowania użytkownika z makietą.

## <a name="debugging"></a>Debugowanie

W statycznej aplikacji sieci Web znajdują się dwa konteksty debugowania. Pierwszy jest przeznaczony dla witryny zawartości statycznej, a druga — dla funkcji interfejsu API. Debugowanie lokalne jest możliwe, ponieważ interfejs wiersza polecenia Web Apps static będzie używać serwerów deweloperskich dla jednego lub obu tych kontekstów.

W poniższych krokach przedstawiono typowy scenariusz, który używa serwerów deweloperskich dla obu kontekstów debugowania.

1. Uruchom serwer tworzenia lokacji statycznej. To polecenie jest specyficzne dla platformy frontonu, z której korzystasz, ale często występuje w formie poleceń takich jak `npm run build` , `npm start` , lub `npm run dev` .

1. Otwórz folder aplikacja interfejsu API w Visual Studio Code i Rozpocznij sesję debugowania.

1. Przekaż adresy serwera statycznego i serwera interfejsu API do `swa start` polecenia, umieszczając je w określonej kolejności.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

Poniższe zrzuty ekranu przedstawiają terminale typowego scenariusza debugowania:

Witryna zawartości statycznej jest uruchomiona za pośrednictwem programu `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Serwer tworzenia lokacji statycznej":::

Aplikacja interfejsu API Azure Functions uruchamia sesję debugowania w programie Visual Studio Code.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Debugowanie Visual Studio Code interfejsu API":::

Interfejs wiersza polecenia Web Apps static jest uruchamiany przy użyciu obu serwerów deweloperskich.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Terminal interfejsu wiersza polecenia usługi Azure static Web Apps":::

Teraz żądania, które przechodzą przez port `4280` są kierowane do serwera projektowego zawartości statycznej, lub sesji debugowania interfejsu API.

Aby uzyskać więcej informacji na temat różnych scenariuszy debugowania, ze wskazówkami dotyczącymi sposobu dostosowywania portów i adresów serwerów, zobacz [repozytorium interfejsu wiersza polecenia usługi Azure Static Web Apps](https://github.com/Azure/static-web-apps-cli).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji](configuration.md)
