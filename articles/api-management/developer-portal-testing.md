---
title: Testowanie własnego portalu dla deweloperów
titleSuffix: Azure API Management
description: Dowiedz się, jak skonfigurować testy jednostkowe i testy end-to-end dla własnego portalu API Management portal.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ab6a7aa8fc4f11c34126415379294ef1e48fa286
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741887"
---
# <a name="test-the-self-hosted-developer-portal"></a>Testowanie własnego portalu dla deweloperów

W tym artykule wyjaśniono, jak skonfigurować testy jednostkowe i testy end-to-end dla [własnego portalu.](developer-portal-self-host.md)

## <a name="unit-tests"></a>Testy jednostkowe

Test jednostkowy jest podejściem do weryfikowania małych elementów funkcjonalności. Odbywa się to w izolacji od innych części aplikacji.

### <a name="example-scenario"></a>Przykładowy scenariusz

W tym scenariuszu testuje się kontrolkę wprowadzania hasła. Akceptuje tylko hasła zawierające co najmniej:

- Jedna litera

- Jedna liczba

- Jeden znak specjalny
 
Test sprawdzania poprawności tych wymagań wygląda następująco:

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>Struktura projektu

Często test jednostkowy jest nadal testem jednostkowym obok składnika, który ma zostać zweryfikowany.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>Pozoruj żądania HTTP

Istnieją przypadki, w których oczekujesz, że składnik będzie zgłaszać żądania HTTP. Składnik powinien prawidłowo reagować na różne rodzaje odpowiedzi. Aby zasymulować określone odpowiedzi HTTP, `MockHttpClient` użyj . Implementuje interfejs `HttpClient` używany przez wiele innych składników projektu.

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>Testy end-to-end

Test end-to-end wykonuje określony scenariusz użytkownika, który wykonuje dokładne kroki, których wykonanie oczekujesz od użytkownika. W aplikacji internetowej, np. na platformie Azure API Management portalu dla deweloperów, użytkownik przewija zawartość i wybiera opcje, aby osiągnąć określone wyniki. 

Aby replikować nawigację użytkownika, można użyć bibliotek pomocnika manipulowania przeglądarką, takich jak [Puppeteer.](https://github.com/puppeteer/puppeteer) Umożliwia symulowanie akcji użytkownika i automatyzowanie zakładanych scenariuszy. Ponadto puppeteer automatycznie tworzy zrzuty ekranu stron lub składników na dowolnym etapie testu. Porównaj je później z poprzednimi wynikami, aby wychwytują odchylenia i potencjalne regresje.

### <a name="example-scenario"></a>Przykładowy scenariusz

W tym scenariuszu należy zweryfikować przepływ logowania użytkownika. Ten scenariusz wymagałby następujących kroków:

1. Otwórz przeglądarkę i przejdź do strony logowania.

1. Wprowadź adres e-mail.

1. Wprowadź hasło.

1. Wybierz **pozycję Zaloguj się.**

1. Sprawdź, czy użytkownik został przekierowany do strony głównej.

1. Sprawdź, czy strona zawiera **element** menu Profil. Jest to jeden z możliwych wskaźników pomyślnego zalogowania.

Aby automatycznie uruchomić test, utwórz skrypt z dokładnie taką samą czynnością:

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> Ciągi takie jak "#email", "#password" i "#signin" to selektory podobne do CSS, które identyfikują elementy HTML na stronie. Aby dowiedzieć [się więcej, zobacz specyfikację](https://www.w3.org/TR/selectors-3/) Selektory poziom 3 W3C.

### <a name="ui-component-maps"></a>Mapy składników interfejsu użytkownika

Przepływy użytkownika często są za pośrednictwem tych samych stron lub składników. Dobrym przykładem jest główne menu witryny internetowej, które znajduje się na każdej stronie. 

Utwórz mapę składników interfejsu użytkownika, aby uniknąć konfigurowania i aktualizowania tych samych selektorów dla każdego testu. Na przykład możesz zastąpić kroki od 2 do 6 w poprzednim przykładzie tylko dwoma liniami:

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>Konfiguracja testowa

Niektóre scenariusze mogą wymagać wstępnie utworzonych danych lub konfiguracji. Na przykład może być konieczne zautomatyzowanie logowania użytkowników przy użyciu kont mediów społecznościowych. Trudno jest utworzyć te dane szybko lub łatwo.

W tym celu można dodać specjalny plik konfiguracji do scenariusza testowego. Skrypty testowe mogą zbierać wymagane dane z pliku. W zależności od potoku kompilacji i testowania testy mogą ściągać wpisy tajne z nazwanego bezpiecznego magazynu.

Oto przykład pliku, `validate.config.json` który będzie przechowywany w `src` folderze projektu.

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>Testy bezgłowe a normalne

Nowoczesne przeglądarki, takie jak Chrome Microsoft Edge, umożliwiają uruchamianie automatyzacji zarówno w trybie bezgłowym, jak i normalnym. Przeglądarka działa bez graficznego interfejsu użytkownika w trybie bezgłowym. Nadal wykonuje tę samą stronę i Document Object Model (DOM). Interfejs użytkownika przeglądarki zazwyczaj nie jest wymagany w potokach dostarczania. W takim przypadku uruchamianie testów w trybie bezgłowym jest doskonałym rozwiązaniem.

Podczas tworzenia skryptu testowego warto zobaczyć, co dokładnie dzieje się w przeglądarce. To dobry czas na użycie trybu normalnego.

Aby przełączać się między trybami, zmień `headless` opcję w `constants.ts` pliku. Znajduje się on w `tests` folderze w projekcie:

```typescript
export const LaunchOptions = {
    headless: false
};
```

Inną przydatną opcją jest `slowMo` . Wstrzymuje wykonywanie testu między poszczególnymi akcjami:

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>Uruchom testy

Istnieją dwa wbudowane sposoby wykonywania testów w tym projekcie:

**Polecenie npm**

```console
npm run test
```

**Eksplorator testów**

Rozszerzenie Eksploratora testów dla VS Code (na przykład Eksplorator testów [Mocha](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)) ma wygodny interfejs użytkownika i opcję automatycznego uruchamiania testów przy każdej zmianie kodu źródłowego:

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Zrzut ekranu Visual Studio Code Eksploratora testów":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów usługi Azure API Management — omówienie](api-management-howto-developer-portal.md)

- [Samodzielne hostnie portalu dla deweloperów](developer-portal-self-host.md)
