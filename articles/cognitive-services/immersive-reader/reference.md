---
title: Dokumentacja zestawu SDK czytnika immersyjny
titleSuffix: Azure Cognitive Services
description: Zestaw SDK czytnika immersyjny zawiera bibliotekę języka JavaScript, która umożliwia integrację czytnika immersyjny z aplikacją.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metang
ms.openlocfilehash: f2f5c8193454a3b7fa6be1cea7a1236b613d6c8f
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636531"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Dokumentacja zestawu SDK języka JavaScript czytnika immersyjny (v 1.1)

Zestaw SDK czytnika immersyjny zawiera bibliotekę języka JavaScript, która umożliwia integrację czytnika immersyjny z aplikacją.

## <a name="functions"></a>Funkcje

Zestaw SDK udostępnia funkcje:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

Uruchamia czytnik immersyjny w `iframe` aplikacji sieci Web. Należy pamiętać, że rozmiar zawartości jest ograniczony do maksymalnie 50 MB.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>Parametry launchAsync

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| `token` | ciąg | Token uwierzytelniania usługi Azure AD. Aby uzyskać więcej informacji [, zobacz jak utworzyć zasób czytnika immersyjny](./how-to-create-immersive-reader.md) . |
| `subdomain` | string | Niestandardowa poddomena zasobu czytnika immersyjny na platformie Azure. Aby uzyskać więcej informacji [, zobacz jak utworzyć zasób czytnika immersyjny](./how-to-create-immersive-reader.md) . |
| `content` | [Zawartość](#content) | Obiekt zawierający zawartość, która ma zostać pokazana w czytniku immersyjny. |
| `options` | [Opcje](#options) | Opcje konfigurowania niektórych zachowań czytnika immersyjny. Opcjonalny. |

#### <a name="returns"></a>Zwraca

Zwraca obiekt `Promise<LaunchResponse>` , który jest rozpoznawany, gdy czytnik immersyjny jest ładowany. Jest to `Promise` rozwiązanie rozpoznawane jako [`LaunchResponse`](#launchresponse) obiekt.

#### <a name="exceptions"></a>Wyjątki

Zwracana wartość `Promise` zostanie odrzucona z [`Error`](#error) obiektem, jeśli czytnik immersyjny nie zostanie załadowany. Aby uzyskać więcej informacji, zobacz [kody błędów](#error-codes).

<br>

## <a name="close"></a>close

Zamyka czytnik immersyjny.

Przykładowy przypadek użycia tej funkcji to jeśli przycisk Zakończ jest ukryty przez ustawienie ```hideExitButton: true``` w [opcjach](#options). Następnie inny przycisk (na przykład strzałka wstecz nagłówka komórkowego) może wywołać tę ```close``` funkcję, gdy zostanie kliknięty.

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>Przycisk uruchamiania czytnika immersyjny

Zestaw SDK zawiera domyślne style dla przycisku umożliwiającego uruchomienie czytnika immersyjny. Użyj `immersive-reader-button` atrybutu Class, aby włączyć ten styl. Aby uzyskać więcej informacji [, zobacz Jak dostosować przycisk czytnika immersyjny](./how-to-customize-launch-button.md) .

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>Atrybuty opcjonalne

Użyj następujących atrybutów, aby skonfigurować wygląd i działanie przycisku.

| Atrybut | Opis |
| --------- | ----------- |
| `data-button-style` | Ustawia styl przycisku. Może być `icon` , `text` lub `iconAndText` . Wartość domyślna to `icon` . |
| `data-locale` | Ustawia ustawienia regionalne. Na przykład: `en-US` lub `fr-FR`. Domyślnie jest używany język angielski `en` . |
| `data-icon-px-size` | Ustawia rozmiar ikony w pikselach. Wartość domyślna to 20px. |

<br>

## <a name="renderbuttons"></a>renderButtons

```renderButtons```Funkcja nie jest konieczna, jeśli używasz wskazówki dotyczącej [dostosowywania dla czytnika](./how-to-customize-launch-button.md) .

Ta funkcja stylów i aktualizuje elementy przycisku czytnika immersyjny dokumentu. Jeśli ```options.elements``` jest podany, przyciski będą renderowane w obrębie każdego elementu dostarczonego w ```options.elements``` . Użycie ```options.elements``` parametru jest przydatne, gdy w dokumencie znajduje się wiele sekcji, na których można uruchomić czytnik immersyjny, i chcesz uproszczony sposób renderowania wielu przycisków przy użyciu tego samego stylu lub aby renderować przyciski przy użyciu prostego i spójnego wzorca projektowego. Aby użyć tej funkcji z parametrem [renderButtons Options](#renderbuttons-options) , wywołaj metodę ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` ładowania strony, jak pokazano w poniższym fragmencie kodu. W przeciwnym razie przyciski będą renderowane w obrębie elementów dokumentu, które zawierają klasy ```immersive-reader-button``` , jak pokazano w temacie [jak dostosować przycisk czytnika immersyjny](./how-to-customize-launch-button.md) .

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

Więcej opcji renderowania można znaleźć w powyższych [opcjonalnych atrybutach](#optional-attributes) . Aby użyć tych opcji, Dodaj dowolny z atrybutów opcji do każdego ```HTMLDivElement``` na stronie HTML.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>Parametry renderButtons

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| `options` | [Opcje renderButtons](#renderbuttons-options) | Opcje konfigurowania niektórych zachowań funkcji renderButtons. Opcjonalny. |

### <a name="renderbuttons-options"></a>Opcje renderButtons

Opcje renderowania przycisków czytnika szczegółowego.

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>Parametry opcji renderButtons

| Ustawienie | Typ | Opis |
| ------- | ---- | ----------- |
| elementy | HTMLDivElement[] | Elementy do renderowania przycisków czytnika immersyjny w. |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

Zawiera odpowiedź z wywołania do `ImmersiveReader.launchAsync` . Należy zauważyć, że odwołanie do programu zawierającego `iframe` czytnik immersyjny jest dostępne za pośrednictwem `container.firstChild` .

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>Parametry LaunchResponse

| Ustawienie | Typ | Opis |
| ------- | ---- | ----------- |
| kontener | HTMLDivElement | Element HTML, który zawiera obiekt iframe czytnika immersyjny. |
| sessionId | String | Unikatowy identyfikator globalny dla tej sesji używany do debugowania. |
 
## <a name="error"></a>Błąd

Zawiera informacje o błędzie.

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>Parametry błędu

| Ustawienie | Typ | Opis |
| ------- | ---- | ----------- |
| kod | String | Jeden z zestawów kodów błędów. Zobacz [Kody błędów](#error-codes). |
| message | String | Czytelna dla człowieka Reprezentacja błędu. |

#### <a name="error-codes"></a>Kody błędów

| Kod | Opis |
| ---- | ----------- |
| BadArgument | Podany argument jest nieprawidłowy, patrz `message` parametr [błędu](#error). |
| Limit czasu | Nie można załadować czytnika immersyjny w określonym limicie czasu. |
| TokenExpired | Podany token wygasł. |
| Ograniczone | Przekroczono limit liczby wywołań. |

<br>

## <a name="types"></a>Typy

### <a name="content"></a>Zawartość

Zawiera zawartość, która ma zostać pokazana w czytniku immersyjny.

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>Parametry zawartości

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| title | Ciąg | Tekst tytułu wyświetlany u góry czytnika immersyjny (opcjonalnie) |
| fragmenty | [Fragment []](#chunk) | Tablica fragmentów |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Fragment

Pojedynczy fragment danych, który zostanie przesłany do zawartości czytnika immersyjny.

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>Parametry fragmentu

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| zawartość | String | Ciąg, który zawiera zawartość wysłaną do czytnika immersyjny. |
| bibliografi | String | Język tekstu, wartość jest w formacie języka IETF BCP 47, np. en, es-ES. Język zostanie wykryty automatycznie, jeśli nie zostanie określony. Zobacz [Obsługiwane języki](#supported-languages). |
| mimeType | string | Obsługiwane są formaty w formacie zwykłego tekstu, MathML, HTML & Word. Aby uzyskać więcej informacji, zobacz [obsługiwane typy MIME](#supported-mime-types) . |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>Obsługiwane typy MIME

| Typ MIME | Opis |
| --------- | ----------- |
| tekst/zwykły | Zwykły tekst. |
| text/html | Zawartość HTML. [Dowiedz się więcej](#html-support)|
| Application/MathML + XML | Język matematycznych znaczników (MathML). [Dowiedz się więcej](./how-to/display-math.md).
| ument aplikacji/vnd.openxmlformats-officedocument.wordprocessingml.doc | Dokument formatu programu Microsoft Word. docx.


<br>

## <a name="options"></a>Opcje

Zawiera właściwości, które konfigurują pewne zachowania czytnika immersyjny.

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>Parametry opcji

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| uiLang | String | Język interfejsu użytkownika — wartość to format znacznika języka IETF BCP 47, np. en, es-ES. Jeśli nie zostanie określony, domyślnie jest używany język przeglądarki. |
| timeout | Liczba | Czas trwania (w milisekundach) przed [launchAsync](#launchasync) kończy się niepowodzeniem z błędem przekroczenia limitu czasu (wartość domyślna to 15000 MS). Ten limit czasu ma zastosowanie tylko do początkowego uruchomienia strony czytnika, gdzie powodzenie jest zaobserwowane, gdy zostanie otwarta strona czytelnik i rozpocznie się pokrętło. Dostosowanie limitu czasu nie powinno być konieczne. |
| uiZIndex | Liczba | Indeks z elementu iframe, który zostanie utworzony (wartość domyślna to 1000). |
| useWebview | Boolean| Użyj tagu WebView zamiast elementu iframe, aby zapewnić zgodność z aplikacjami programu Chrome (wartość domyślna to false). |
| Zakończ | Funkcja | Wykonuje się, gdy czytnik immersyjny zostanie zakończony. |
| allowFullscreen | Boolean | Możliwość przełączania trybu pełnoekranowego (wartość domyślna to true). |
| hideExitButton | Boolean | Określa, czy ukryć strzałkę przycisku zakończenia czytnika immersyjny (wartość domyślna to false). Powinno to być spełnione tylko w przypadku, gdy istnieje alternatywny mechanizm do zamykania czytnika immersyjny (np. strzałka wstecz na pasku narzędzi dla urządzeń przenośnych). |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | Ustawienie użycia pliku cookie czytnika immersyjny (wartość domyślna to *CookiePolicy. Disable* ). Aplikacja hosta jest odpowiedzialna za uzyskanie wszelkich niezbędnych wyrazów zgody użytkownika zgodnie z zasadami zgodności plików cookie UE. Zobacz [Opcje zasad dotyczących plików cookie](#cookiepolicy-options). |
| disableFirstRun | Boolean | Wyłącz środowisko pierwszego uruchomienia. |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | Opcje konfigurowania odczytywania na głos. |
| translationOptions | [TranslationOptions](#translationoptions) | Opcje konfigurowania tłumaczenia. |
| displayOptions | [DisplayOptions](#displayoptions) | Opcje konfigurowania rozmiaru tekstu, czcionki itp. |
| Preferencje | String | Ciąg zwrócony z onPreferencesChanged reprezentujący preferencje użytkownika w czytniku immersyjny. Aby uzyskać więcej informacji, zobacz [parametry ustawień](#settings-parameters) i [sposób przechowywania preferencji użytkownika](./how-to-store-user-preferences.md) . |
| onPreferencesChanged | Funkcja | Wykonuje się, gdy zmienią się preferencje użytkownika. Aby uzyskać więcej informacji [, zobacz jak zachować preferencje użytkownika](./how-to-store-user-preferences.md) . |
| Że element customdomain | String | Zarezerwowane do użytku wewnętrznego. Domena niestandardowa, w której jest hostowany webapp czytnika immersyjny (domyślnie ma wartość null). |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> **Ważne** Nie należy podejmować próby programistycznej zmiany wartości `-preferences` ciągu wysyłanego do i z aplikacji czytnika immersyjny, ponieważ może to spowodować nieoczekiwane zachowanie w przypadku użytkowników. Aplikacje hosta nigdy nie powinny przypisywać wartości niestandardowych do ciągu ani manipulować nimi `-preferences` . Korzystając z `-preferences` opcji String, należy używać tylko dokładnej wartości, która została zwrócona z `-onPreferencesChanged` opcji wywołania zwrotnego.

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>Parametry ReadAloudOptions

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| głos | String | Głos, "kobieta" lub "męski". Należy pamiętać, że nie wszystkie języki obsługują obie płci. |
| szybkość | Liczba | Szybkość odtwarzania musi mieścić się w przedziale od 0,5 do 2,5 włącznie. |
| Autoodtwarzania | Boolean | Automatycznie Rozpocznij odczytywanie po załadowaniu czytnika immersyjny. |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> Ze względu na ograniczenia przeglądarki funkcja autoodtwarzania nie jest obsługiwana w przeglądarce Safari.

<br>

## <a name="translationoptions"></a>TranslationOptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>Parametry TranslationOptions

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| language | String | Ustawia język tłumaczenia, wartość jest w formacie języka IETF BCP 47, np. fr-FR, es-MX, zh-Hans-CN. Wymagane, aby automatycznie włączyć tłumaczenie wyrazu lub dokumentu. |
| autoEnableDocumentTranslation | Boolean | Automatycznie Przetłumacz cały dokument. |
| autoEnableWordTranslation | Boolean | Automatyczne włączenie tłumaczenia wyrazów. |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>Parametry DisplayOptions

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| Wartość parametru TEXTSIZE | Liczba | Ustawia wybrany rozmiar tekstu. |
| increaseSpacing | Boolean | Określa, czy odstępy tekstu mają być włączane czy wyłączane. |
| fontFamily | String | Ustawia wybraną czcionkę ("Calibri", "ComicSans" lub "Sitka"). |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>Opcje CookiePolicy

```typescript
enum CookiePolicy { Disable, Enable }
```

**Ustawienia wymienione poniżej służą wyłącznie do celów informacyjnych** . Czytnik immersyjny przechowuje jego ustawienia lub Preferencje użytkownika w plikach cookie. Ta opcja *cookiePolicy* domyślnie **wyłącza** korzystanie z plików cookie w celu zachowania zgodności z przepisami w zakresie plików cookie UE. Jeśli chcesz ponownie włączyć pliki cookie i przywrócić domyślną funkcję preferencji użytkownika dla czytnika, musisz upewnić się, że witryna sieci Web lub aplikacja uzyskuje odpowiednią zgodę od użytkownika w celu włączenia plików cookie. Następnie, aby ponownie włączyć pliki cookie w czytniku immersyjny, należy jawnie ustawić opcję *cookiePolicy* na *cookiePolicy. Enable* podczas uruchamiania czytnika immersyjny. W poniższej tabeli opisano ustawienia, które są przechowywane w pliku cookie przez czytniki immersyjny po włączeniu opcji *cookiePolicy* .

#### <a name="settings-parameters"></a>Parametry ustawień

| Ustawienie | Typ | Opis |
| ------- | ---- | ----------- |
| Wartość parametru TEXTSIZE | Liczba | Ustawia wybrany rozmiar tekstu. |
| fontFamily | String | Ustawia wybraną czcionkę ("Calibri", "ComicSans" lub "Sitka"). |
| textodstępy | Liczba | Określa, czy odstępy tekstu mają być włączane czy wyłączane. |
| formattingEnabled | Boolean | Określa, czy formatowanie HTML ma być włączone czy wyłączone. |
| tematów | String | Ustawia wybrany motyw (na przykład "lekki", "ciemny"...). |
| syllabificationEnabled | Boolean | Określa, czy syllabification jest włączony czy wyłączony. |
| nounHighlightingEnabled | Boolean | Określa, czy wyróżnianie rzeczowników jest włączane czy wyłączane. |
| nounHighlightingColor | String | Ustawia wybrany kolor wyróżniania rzeczowników. |
| verbHighlightingEnabled | Boolean | Określa, czy wyróżnianie zleceń jest włączane, czy wyłączane. |
| verbHighlightingColor | String | Ustawia wybrany kolor podświetlania zlecenia. |
| adjectiveHighlightingEnabled | Boolean | Określa, czy wyróżnianie przymiotnika jest włączone, czy wyłączone. |
| adjectiveHighlightingColor | String | Ustawia wybrany kolor podświetlania przymiotników. |
| adverbHighlightingEnabled | Boolean | Określa, czy wyróżnianie parametrów jest włączone, czy wyłączone. |
| adverbHighlightingColor | String | Ustawia wybrany kolor wyróżniania parametrów. |
| pictureDictionaryEnabled | Boolean | Określa, czy słownik obrazu ma być włączony, czy wyłączony. |
| posLabelsEnabled | Boolean | Określa, czy etykieta tekstu indeksu górnego każdej wyróżnionej części mowy jest włączana, czy wyłączona.  |

<br>

## <a name="supported-languages"></a>Obsługiwane języki

Funkcja tłumaczenia w czytniku immersyjny obsługuje wiele języków. Aby uzyskać więcej informacji, zobacz [Obsługa języka](./language-support.md) .

<br>

## <a name="html-support"></a>Obsługa HTML

Po włączeniu formatowania następująca zawartość będzie renderowana jako HTML w czytniku immersyjny.

| HTML | Obsługiwana zawartość |
| --------- | ----------- |
| Style czcionki | Pogrubienie, kursywa, podkreślenie, kod, przekreślenie, indeks górny, dolny indeks |
| Listy nieuporządkowane | Dysk, okrąg, kwadrat |
| Uporządkowane listy | Dziesiętny, górny alfa, niższy-alfa, wielkie litery, Lower-Roman |

Nieobsługiwane Tagi będą renderowane w sposób porównywalny. Obrazy i tabele nie są obecnie obsługiwane.

<br>

## <a name="browser-support"></a>Obsługa przeglądarki

Najnowsze wersje następujących przeglądarek są używane w celu uzyskania najlepszego środowiska z czytnikiem immersyjny.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>Następne kroki

* Eksplorowanie [zestawu SDK czytnika immersyjny w witrynie GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Szybki Start: Tworzenie aplikacji sieci Web, która uruchamia czytnik immersyjny (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
