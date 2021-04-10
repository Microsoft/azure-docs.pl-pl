---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu Arkose Labs
titleSuffix: Azure AD B2C
description: Samouczek konfigurowania Azure Active Directory B2C przy użyciu laboratoriów Arkose Labs w celu identyfikowania ryzykownych i fałszywych użytkowników
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c2aceedd57bcc7cd88c4e822c7b696e36b28bd8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579794"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>Samouczek: Konfigurowanie Arkose Labs przy użyciu Azure Active Directory B2C

W tym przykładowym samouczku dowiesz się, jak zintegrować uwierzytelnianie Azure Active Directory (AD) B2C z [Arkose Labs](https://www.arkoselabs.com/). Arkose Labs pomagają organizacjom przed atakami bot, atakami polegającymi na przejęciu kont i oszustwem.  

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

- Konto [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) .

## <a name="scenario-description"></a>Opis scenariusza

Integracja z Arkose Labs obejmuje następujące składniki:

- **Arkose Labs** — usługa oszustw i nadużycia w celu ochrony przed botów i innymi zautomatyzowanymi nadużyciami.

- **Azure AD B2C przepływ użytkownika rejestracji** — środowisko rejestracji, które będzie korzystać z usługi Arkose Labs. Program użyje niestandardowych interfejsów HTML i JavaScript oraz łączników interfejsu API do integracji z usługą Arkose Labs.

- **Azure Functions** — punkt końcowy interfejsu API hostowany przez użytkownika, który współpracuje z funkcją łączników interfejsu API. Ten interfejs API jest odpowiedzialny za wykonywanie walidacji po stronie serwera tokenu sesji Arkose Labs.

Na poniższym diagramie opisano, jak Arkose Labs integrują się z Azure AD B2C.

![Obraz przedstawia diagram architektury Arkose Labs](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| Krok  | Opis |
|---|---|
|1     | Użytkownik rejestruje się i tworzy konto. Gdy użytkownik wybierze opcję Prześlij, pojawi się wyzwanie Arkose Labs.         |
|2     |  Gdy użytkownik zakończy wyzwanie, Azure AD B2C wysyła stan do Arkose Labs w celu wygenerowania tokenu. |
|3     |  Arkose Labs generuje token i wysyła je z powrotem do Azure AD B2C.   |
|4     |  Azure AD B2C wywołuje pośredni internetowy interfejs API, aby przekazać formularz rejestracji.      |
|5     |  Pośredni internetowy interfejs API wysyła formularz rejestracji do laboratorium Arkose Lab w celu weryfikacji tokenu.    |
|6     | Arkose Lab przetwarza i wysyła wyniki weryfikacji z powrotem do pośredniego interfejsu API sieci Web.|
|7     | Pośredni internetowy interfejs API wysyła wyniki sukcesu lub niepowodzenia z wyzwania do Azure AD B2C. |
|8     | Jeśli wyzwanie zostało zakończone pomyślnie, formularz rejestracji zostanie przesłany do Azure AD B2C i Azure AD B2C kończy uwierzytelnianie.|

## <a name="onboard-with-arkose-labs"></a>Dołączanie do Arkose Labs

1. Skontaktuj się z [Arkose](https://www.arkoselabs.com/book-a-demo/) i Utwórz konto.

2. Po utworzeniu konta przejdź do https://dashboard.arkoselabs.com/login  

3. Na pulpicie nawigacyjnym przejdź do ustawień witryny, aby znaleźć klucz publiczny i klucz prywatny. Te informacje będą konieczne później w celu skonfigurowania Azure AD B2C. Wartości kluczy publiczny i prywatny są określane jako `ARKOSE_PUBLIC_KEY` i `ARKOSE_PRIVATE_KEY` w [przykładowym kodzie](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose).

## <a name="integrate-with-azure-ad-b2c"></a>Integracja z usługą Azure AD B2C

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>Część 1 — Tworzenie niestandardowego atrybutu ArkoseSessionToken

Aby utworzyć atrybut niestandardowy, wykonaj następujące kroki:  

1. Przejdź do **Azure Portal**  >  **Azure AD B2C**

2. Wybierz **atrybuty użytkownika**

3. Wybierz pozycję **Dodaj**.

4. Wprowadź **ArkoseSessionToken** jako nazwę atrybutu

5. Wybierz pozycję **Utwórz**

Dowiedz się więcej o [atrybutach niestandardowych](./user-flow-custom-attributes.md?pivots=b2c-user-flow).

### <a name="part-2---create-a-user-flow"></a>Część 2 — Tworzenie przepływu użytkownika

Przepływ użytkownika może być zarówno w przypadku **rejestracji** , jak i **logowania** **.** Przepływ użytkownika Arkose Labs będzie wyświetlany tylko podczas rejestracji.

1. Zapoznaj się z [instrukcjami dotyczącymi](./tutorial-create-user-flows.md) tworzenia przepływu użytkownika. Jeśli używasz istniejącego przepływu użytkownika, musi to być Zalecany typ wersji **(wersja zapoznawcza nowej generacji)** .

2. W obszarze Ustawienia przepływu użytkownika przejdź do pozycji **atrybuty użytkownika** i wybierz **ArkoseSessionToken** .

![Obraz przedstawia sposób wybierania atrybutów niestandardowych](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>Część 3 — Konfigurowanie niestandardowych układów HTML, JavaScript i stron

Przejdź do podanego [skryptu HTML](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html). Plik zawiera szablon HTML z tagami języka JavaScript `<script>` , które będą wykonywać trzy czynności:

1. Załaduj skrypt Arkose Labs, który renderuje widżet Arkose Labs i wykonuje weryfikację Arkose laboratoriów po stronie klienta.

2. Ukrywanie `extension_ArkoseSessionToken` elementu wejściowego i etykiety, odpowiadającego `ArkoseSessionToken` atrybutowi niestandardowemu, z interfejsu użytkownika widocznego dla użytkownika.

3. Gdy użytkownik ukończy wyzwanie Arkose Labs, Arkose laboratoria weryfikuje odpowiedź użytkownika i generuje token. Wywołanie zwrotne `arkoseCallback` w niestandardowym języku JavaScript ustawia wartość `extension_ArkoseSessionToken` na wartość wygenerowanego tokenu. Ta wartość zostanie przesłana do punktu końcowego interfejsu API zgodnie z opisem w następnej sekcji.

    Zapoznaj się z [tym artykułem](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup) , aby dowiedzieć się więcej na temat weryfikacji po stronie klienta Arkose Labs.

Wykonaj powyższe kroki, aby użyć niestandardowego kodu HTML i języka JavaScript dla przepływu użytkownika.

1. Zmodyfikuj plik [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) w taki sposób, aby `<ARKOSE_PUBLIC_KEY>` pasował do wartości wygenerowanej dla weryfikacji po stronie klienta i użyty do załadowania skryptu Arkose Labs dla Twojego konta.

2. Hostowanie strony HTML w punkcie końcowym sieci Web udostępniania zasobów między źródłami (CORS). [Utwórz konto usługi Azure Blob Storage](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) i [Skonfiguruj mechanizm CORS](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  >[!NOTE]
  >Jeśli masz własny niestandardowy kod HTML, skopiuj i wklej `<script>` elementy na stronie HTML.

3. Wykonaj następujące kroki, aby skonfigurować układy stron

   a. W Azure Portal przejdź do **Azure AD B2C**

   b. Przejdź do **przepływu użytkownika** i wybierz swój przepływ użytkownika

   c. Wybieranie **układów strony**

   d. Wybierz pozycję **Utwórz konto lokalne — układ strony**

   e. Przełącz **użycie niestandardowej zawartości strony** na **wartość tak**

   f. Wklej identyfikator URI, w którym mieszka niestandardowy kod HTML, **Użyj niestandardowej zawartości strony**

   przykład Jeśli używasz dostawców tożsamości społecznościowych, powtórz **krok e** i **f** dla opcji **Utwórz konto społecznościowe** .

   ![obraz przedstawiający układy stron](media/partner-arkose-labs/page-layouts.png)

4. W przepływie użytkownika przejdź do pozycji **Właściwości** , a następnie wybierz pozycję **Włącz obsługę języka JavaScript** wymuszanie układu strony (wersja zapoznawcza). Zobacz ten [artykuł](./javascript-and-page-layout.md?pivots=b2c-user-flow) , aby dowiedzieć się więcej.

### <a name="part-4---create-and-deploy-your-api"></a>Część 4 — Tworzenie i wdrażanie interfejsu API

Zainstaluj [rozszerzenie Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) dla Visual Studio Code.

>[!Note]
>Kroki opisane w tej sekcji założono, że używasz Visual Studio Code do wdrożenia funkcji platformy Azure. Możesz również użyć Azure Portal, terminalu lub wiersza polecenia lub dowolnego innego edytora kodu do wdrożenia.

#### <a name="run-the-api-locally"></a>Uruchamianie interfejsu API lokalnie

1. Przejdź do rozszerzenia Azure w programie Visual Studio Code na lewym pasku nawigacyjnym. Wybierz folder **lokalnego projektu** reprezentujący lokalną funkcję platformy Azure.

2. Naciśnij klawisz **F5** lub użyj menu **Debuguj**  >  **Rozpocznij debugowanie** , aby uruchomić debuger i dołączyć do hosta Azure Functions. To polecenie automatycznie używa pojedynczej konfiguracji debugowania utworzonej przez funkcję platformy Azure.

3. Rozszerzenie funkcji platformy Azure automatycznie wygeneruje kilka plików na potrzeby lokalnego projektowania, zainstaluje zależności i zainstaluje podstawowe narzędzia funkcji, jeśli jeszcze nie istnieją. Te narzędzia ułatwiają debugowanie.

4. Dane wyjściowe narzędzia podstawowe funkcje są wyświetlane w panelu **terminalu** Visual Studio Code. Po rozpoczęciu działania hosta **naciśnij klawisz Alt i kliknij** lokalny adres URL wyświetlany w danych wyjściowych, aby otworzyć przeglądarkę i uruchomić funkcję. W Eksploratorze Azure Functions kliknij prawym przyciskiem myszy funkcję, aby wyświetlić adres URL funkcji hostowanej lokalnie.

Aby ponownie wdrożyć lokalne wystąpienie podczas testowania, powtórz kroki od 1 do 4.

#### <a name="add-environment-variables"></a>Dodawanie zmiennych środowiskowych

Ten przykład chroni punkt końcowy interfejsu API sieci Web przy użyciu [uwierzytelniania podstawowego protokołu HTTP](https://tools.ietf.org/html/rfc7617).

Nazwa użytkownika i hasło są przechowywane jako zmienne środowiskowe, a nie jako część repozytorium. Aby uzyskać więcej informacji, zobacz [local.settings.js](../azure-functions/functions-run-local.md?tabs=macos%2ccsharp%2cbash#local-settings-file) pliku.

1. Utwórz local.settings.jsw pliku w folderze głównym

2. Skopiuj i wklej poniższy kod do pliku:

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
Wartości **BASIC_AUTH_USERNAME** i **BASIC_AUTH_PASSWORD** są poświadczeniami używanymi do uwierzytelniania wywołania interfejsu API w funkcji platformy Azure. Wybierz żądane wartości.

`<ARKOSE_PRIVATE_KEY>`Jest to klucz tajny po stronie serwera wygenerowany w usłudze Arkose Labs. Służy do wywoływania [interfejsu API walidacji po stronie serwera Arkose Labs](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) w celu sprawdzenia wartości `ArkoseSessionToken` wygenerowanej przez fronton.

`<B2C_EXTENSIONS_APP_ID>`To identyfikator aplikacji używanej przez Azure AD B2C do przechowywania atrybutów niestandardowych w katalogu. Ten identyfikator aplikacji można znaleźć, przechodząc do Rejestracje aplikacji, wyszukując pozycję B2C-Extensions-App i kopiując identyfikator aplikacji (klienta) z okienka **Przegląd** . Usuń `-` znaki.

![Obraz przedstawia wyszukiwanie według identyfikatora aplikacji](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>Wdrażanie aplikacji w sieci Web

1. Wykonaj kroki opisane w [tym](/azure/javascript/tutorial-vscode-serverless-node-04) przewodniku, aby wdrożyć funkcję platformy Azure w chmurze. Skopiuj adres URL sieci Web punktu końcowego funkcji platformy Azure.

2. Po wdrożeniu wybierz opcję **przekazywania ustawień** . Spowoduje to przekazanie zmiennych środowiskowych do [ustawień aplikacji](../azure-functions/functions-develop-vs-code.md?tabs=csharp#application-settings-in-azure) usługi App Service. Te ustawienia aplikacji można także skonfigurować lub [zarządzać nimi za pośrednictwem Azure Portal.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)

Zapoznaj się z [tym artykułem](../azure-functions/functions-develop-vs-code.md?tabs=csharp#republish-project-files) , aby dowiedzieć się więcej na temat Visual Studio Code tworzenia aplikacji Azure Functions.

#### <a name="configure-and-enable-the-api-connector"></a>Konfigurowanie i włączanie łącznika interfejsu API

[Utwórz łącznik interfejsu API](./add-api-connector.md) i włącz go dla przepływu użytkownika. Konfiguracja łącznika interfejsu API powinna wyglądać następująco:

![Obraz przedstawia sposób konfigurowania łącznika interfejsu API](media/partner-arkose-labs/configure-api-connector.png)

- **Adres URL punktu końcowego** — jest to adres URL funkcji skopiowany wcześniej podczas wdrażania funkcji platformy Azure.

- **Nazwa użytkownika i hasło** — to nazwa użytkownika i hasło zdefiniowane wcześniej jako zmienne środowiskowe.

Aby włączyć łącznik interfejsu API, w ustawieniach **łącznika interfejsu API** dla przepływu użytkownika wybierz łącznik interfejsu API, który ma zostać wywołany, **przed utworzeniem kroku użytkownika** . Spowoduje to wywołanie interfejsu API, gdy użytkownik wybierze opcję **Utwórz** w przepływie rejestracji. Interfejs API wykona weryfikację po stronie serwera `ArkoseSessionToken` wartości, która została ustawiona przez wywołanie zwrotne widżetu Arkose `arkoseCallback` .

![Obraz przedstawiający Włączanie łącznika interfejsu API](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze zasady wybierz pozycję **przepływy użytkownika**.

2. Wybierz wcześniej utworzony przepływ użytkownika.

3. Wybierz pozycję **Uruchom przepływ użytkownika** i wybierz ustawienia:

   a. Aplikacja: wybór zarejestrowanej aplikacji (przykład: JWT)

   b. Adres URL odpowiedzi: Wybierz adres URL przekierowania

   c. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź do przepływu rejestracji i Utwórz konto

5. Wyloguj się

6. Przechodzenie przez przepływ logowania  

7. Po wybraniu opcji **Kontynuuj** zostanie wyświetlona Arkose Labs.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Przykładowe kody](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) dla przepływu użytkownika dotyczącego rejestrowania Azure AD B2C

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)