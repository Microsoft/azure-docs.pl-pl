---
title: Konfigurowanie lokalnego tworzenia aplikacji dla Azure Static Web Apps
description: Dowiedz się, jak ustawić lokalne środowisko projektowe dla Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: e19d39a32d48ec55473bb957595d47ec5148e74b
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588789"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Konfigurowanie lokalnego tworzenia aplikacji dla wersji Azure Static Web Apps zapoznawczej

Po opublikowaniu w chmurze witryna Azure Static Web Apps ma wiele usług, które współpracują ze sobą tak, jakby były taką samą aplikacją. Te usługi to na przykład:

- Statyczna aplikacja internetowa
- Azure Functions API
- Usługi uwierzytelniania i autoryzacji
- Usługi routingu i konfiguracji

Te usługi muszą komunikować się ze sobą, Azure Static Web Apps będą obsługiwać tę integrację w chmurze.

Jednak te usługi działające lokalnie nie są automatycznie powiązane ze sobą.

Aby zapewnić podobne środowisko pracy z platformą Azure, interfejs wiersza [Azure Static Web Apps](https://github.com/Azure/static-web-apps-cli) udostępnia następujące usługi:

- Lokalny serwer lokacji statycznej
- Serwer proxy na serwerze dewelopera struktury frontou
- Serwer proxy dla punktów końcowych interfejsu API — dostępny za pośrednictwem Azure Functions Core Tools
- Makieta uwierzytelniania i serwera autoryzacji
- Wymuszanie lokalnych tras i ustawień konfiguracji

## <a name="how-it-works"></a>Jak to działa

Na poniższym wykresie pokazano, jak żądania są obsługiwane lokalnie.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Przepływ żądań i odpowiedzi interfejsu wiersza polecenia usługi Azure Static Web App":::

> [!IMPORTANT]
> Przejdź do , `http://localhost:4280` aby uzyskać dostęp do aplikacji obsługiwanej przez interfejs wiersza polecenia.

- **Żądania** przekazywane do portu `4280` są przekazywane do odpowiedniego serwera w zależności od typu żądania.

- **Żądania zawartości statycznej,** takie jak HTML lub CSS, są obsługiwane przez wewnętrzny serwer zawartości statycznej interfejsu wiersza polecenia lub przez serwer struktury frontou na potrzeby debugowania.

- **Żądania uwierzytelniania i** autoryzacji są obsługiwane przez emulator, który udostępnia aplikacji fałszywy profil tożsamości.

- **Środowisko uruchomieniowe narzędzi Functions Core Tools** obsługuje żądania do interfejsu API lokacji.

- **Odpowiedzi** ze wszystkich usług są zwracane do przeglądarki tak, jakby wszystkie były pojedynczą aplikacją.

## <a name="prerequisites"></a>Wymagania wstępne

- **Istniejąca Azure Static Web Apps:** jeśli jej nie masz, zacznij od aplikacji [starter vanilla-api.](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate)
- **[Node.js](https://nodejs.org) za pomocą programu npm:** uruchom [Node.js LTS,](https://nodejs.org) która obejmuje dostęp do [npm.](https://www.npmjs.com/)
- **[Visual Studio Code:](https://code.visualstudio.com/)** służy do debugowania aplikacji interfejsu API, ale nie jest wymagany dla interfejsu wiersza polecenia.

## <a name="get-started"></a>Rozpoczęcie pracy

Otwórz terminal w folderze głównym istniejącej Azure Static Web Apps lokacji.

1. Zainstaluj interfejs wiersza polecenia.

    `npm install -g @azure/static-web-apps-cli`

1. Skompilowanie aplikacji, jeśli jest to wymagane przez aplikację.

    Uruchom `npm run build` polecenie lub równoważne polecenie dla projektu.

1. Zmień katalog na katalog wyjściowy aplikacji. Foldery wyjściowe są często nazywane _kompilacją_ lub podobną.

1. Uruchom interfejs wiersza polecenia.

    `swa start`

1. Przejdź do http://localhost:4280 strony , aby wyświetlić aplikację w przeglądarce.

### <a name="other-ways-to-start-the-cli"></a>Inne sposoby uruchamiania interfejsu wiersza polecenia

| Opis | Polecenie |
|--- | --- |
| Podaj określony folder | `swa start ./output-folder` |
| Korzystanie z działającego serwera dewelopera struktury | `swa start http://localhost:3000` |
| Uruchamianie aplikacji usługi Functions w folderze | `swa start ./output-folder --api ./api` |
| Korzystanie z uruchomionej aplikacji usługi Functions | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Emulacja autoryzacji i uwierzytelniania

Interfejs Static Web Apps wiersza polecenia emuluje przepływ [zabezpieczeń zaimplementowany na](./authentication-authorization.md) platformie Azure. Gdy użytkownik się zaloguje, możesz zdefiniować fałszywy profil tożsamości zwracany do aplikacji.

Na przykład podczas próby nawigowania do witryny jest zwracana strona umożliwiająca `/.auth/login/github` zdefiniowanie profilu tożsamości.

> [!NOTE]
> Emulator współpracuje z dowolnym dostawcą zabezpieczeń, a nie tylko z usługą GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Lokalny emulator uwierzytelniania i autoryzacji":::

Emulator udostępnia stronę umożliwiającą podanie następujących [wartości głównych](./user-information.md#client-principal-data) klienta:

| Wartość | Opis |
| --- | --- |
| **Nazwa użytkownika** | Nazwa konta skojarzona z dostawcą zabezpieczeń. Ta wartość jest wyświetlana jako właściwość w obiekcie głównym klienta i jest generowana automatycznie, jeśli nie `userDetails` poniesiesz wartości. |
| **Identyfikator użytkownika** | Wartość wygenerowana automatycznie przez interfejs wiersza polecenia.  |
| **Role** | Lista nazw ról, w której każda nazwa znajduje się w nowym wierszu.  |

Po zalogowaniu:

- Możesz użyć punktu `/.auth/me` końcowego lub punktu końcowego funkcji, aby pobrać jednostkę [klienta użytkownika](./user-information.md).

- Nawigowanie w `./auth/logout` celu wyczyszczenia jednostki klienta i wylogowania użytkownika makiety.

## <a name="debugging"></a>Debugowanie

W statycznej aplikacji internetowej istnieją dwa konteksty debugowania. Pierwsza z nich jest dla witryny zawartości statycznej, a druga dla funkcji interfejsu API. Debugowanie lokalne jest możliwe dzięki Static Web Apps interfejsu wiersza polecenia na używanie serwerów programistów dla jednego lub obu tych kontekstów.

W poniższych krokach przedstawiono wspólny scenariusz, w którym oba konteksty debugowania są używane na serwerach programistów.

1. Uruchom serwer tworzenia lokacji statycznej. To polecenie jest specyficzne dla używanej struktury front endu, ale często ma postać poleceń, takich jak `npm run build` `npm start` , lub `npm run dev` .

1. Otwórz folder aplikacji interfejsu API w Visual Studio Code i rozpocznij sesję debugowania.

1. Przekaż adresy serwera statycznego i serwera interfejsu API do `swa start` polecenia, wymieniając je w kolejności.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

Na poniższych zrzutach ekranu przedstawiono terminale dla typowego scenariusza debugowania:

Witryna zawartości statycznej działa za pośrednictwem usługi `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Serwer dewelopera lokacji statycznej":::

Aplikacja Azure Functions API uruchamia sesję debugowania w Visual Studio Code.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio Code API debugowania":::

Interfejs Static Web Apps wiersza polecenia jest uruchomiony przy użyciu obu serwerów programistyki.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure Static Web Apps interfejsu wiersza polecenia":::

Teraz żądania, które przejdą przez port, są kierowane do serwera tworzenia zawartości statycznej `4280` lub sesji debugowania interfejsu API.

Aby uzyskać więcej informacji na temat różnych scenariuszy debugowania ze wskazówkami na temat dostosowywania portów i adresów serwerów, zobacz repozytorium interfejsu wiersza [Azure Static Web Apps interfejsu wiersza polecenia](https://github.com/Azure/static-web-apps-cli).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji](configuration.md)
