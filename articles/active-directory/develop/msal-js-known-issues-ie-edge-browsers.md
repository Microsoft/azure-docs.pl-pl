---
title: Problemy w programie Internet Explorer & Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Informacje o znanych problemach dotyczących używania biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) z przeglądarkami Internet Explorer i Microsoft Edge.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2a471504b88791b5bfb6ce6cc7c81d60bfbe5028
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83772084"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Znane problemy w programie Internet Explorer i przeglądarki Microsoft Edge (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problemy ze względu na strefy zabezpieczeń
Mamy wiele raportów o problemach z uwierzytelnianiem w programie IE i Microsoft Edge (od aktualizacji *przeglądarki Microsoft Edge do 40.15063.0.0*). Śledzimy te informacje i znamy zespół Microsoft Edge. Chociaż przeglądarka Microsoft Edge działa w ramach rozwiązania, poniżej przedstawiono opis często występujących problemów oraz możliwe obejścia, które można zaimplementować.

### <a name="cause"></a>Przyczyna
Przyczyny większości tych problemów są następujące. Magazyn sesji i magazyn lokalny są podzielone na partycje za pomocą stref zabezpieczeń w przeglądarce Microsoft Edge. W tej konkretnej wersji programu Microsoft Edge, gdy aplikacja jest przekierowywana między strefami, magazyn sesji i magazyn lokalny są wyczyszczone. W tym celu magazyn sesji jest czyszczony w zwykłej nawigacji przeglądarki, a sesja i magazyn lokalny są wyczyszczone w trybie InPrivate przeglądarki. MSAL.js Zapisuje określony stan w magazynie sesji i opiera się na sprawdzaniu tego stanu podczas przepływów uwierzytelniania. Po wyczyszczeniu magazynu sesji ten stan zostanie utracony i dlatego powoduje uszkodzenie środowiska.

### <a name="issues"></a>Problemy

- **Nieskończone pętle przekierowania i ponowne ładowanie stron podczas uwierzytelniania**. Gdy użytkownicy logują się do aplikacji w przeglądarce Microsoft Edge, są przekierowywani z powrotem ze strony logowania usługi AAD i są zablokowane w nieskończonej pętli przekierowania, co spowodowało powtarzanie ponownych prób ładowania strony. Zwykle towarzyszy `invalid_state` błąd w magazynie sesji.

- **Nieskończony czas uzyskiwania pętli tokenów i błąd AADSTS50058**. Gdy aplikacja uruchomiona w przeglądarce Microsoft Edge próbuje uzyskać token dla zasobu, aplikacja może zostać zablokowana w nieskończonej pętli wywołania tokenu uzyskiwania wraz z następującym błędem z usługi AAD w ramach śledzenia sieci:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Okno podręczne nie jest zamykane lub jest zablokowane w przypadku używania logowania za pośrednictwem menu podręcznego do uwierzytelniania**. W przypadku uwierzytelniania za pomocą okna podręcznego w przeglądarce Microsoft Edge lub IE (InPrivate) po wprowadzeniu poświadczeń i zalogowaniu się w przypadku korzystania z wielu domen w obszarze nawigacji okno podręczne nie jest zamykane, ponieważ MSAL.js utraci uchwyt do okna podręcznego.  

### <a name="update-fix-available-in-msaljs-023"></a>Aktualizacja: Poprawka dostępna w MSAL.js 0.2.3
Poprawki dotyczące problemów z pętlą przekierowania uwierzytelniania zostały wydane w [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Włącz flagę `storeAuthStateInCookie` w konfiguracji MSAL.js, aby skorzystać z tej poprawki. Domyślnie ta flaga jest ustawiona na wartość false.

Gdy `storeAuthStateInCookie` flaga jest włączona, MSAL.js będzie używać plików cookie w przeglądarce do przechowywania stanu żądania wymaganego do weryfikacji przepływów uwierzytelniania.

> [!NOTE]
> Ta poprawka nie jest jeszcze dostępna dla otok msal-kątowych i msal-AngularJS. Ta poprawka nie rozwiązuje problemu z oknami podręcznymi.

Użyj obejść poniżej.

#### <a name="other-workarounds"></a>Inne obejścia
Upewnij się, że Twój problem występuje tylko w przypadku określonej wersji przeglądarki Microsoft Edge i działa w innych przeglądarkach przed zastosowaniem tych rozwiązań.  
1. Pierwszym krokiem w celu rozwiązania tych problemów jest upewnienie się, że domena aplikacji i inne Lokacje związane z przekierowaniami przepływu uwierzytelniania są dodawane jako zaufane lokacje w ustawieniach zabezpieczeń przeglądarki, tak aby znajdowały się w tej samej strefie zabezpieczeń.
W tym celu wykonaj następujące czynności:
    - Otwórz program **Internet Explorer** i kliknij **Ustawienia** (ikonę koła zębatego) w prawym górnym rogu
    - Wybieranie **opcji internetowych**
    - Wybierz kartę **zabezpieczenia**
    - W obszarze **Zaufane Lokacje** kliknij przycisk **Lokacje** i Dodaj adresy URL w otwartym oknie dialogowym.

2. Jak wspomniano wcześniej, ponieważ tylko magazyn sesji jest czyszczony podczas zwykłej nawigacji, można skonfigurować MSAL.js do używania magazynu lokalnego. Tę wartość można ustawić jako `cacheLocation` parametr konfiguracji podczas inicjowania MSAL.

Należy zauważyć, że nie rozwiąże to problemu dotyczącego przeglądania InPrivate, ponieważ zarówno sesja, jak i lokalne są wyczyszczone.

## <a name="issues-due-to-popup-blockers"></a>Problemy związane z blokowaniem okien podręcznych

Istnieją przypadki, gdy okienka podręczne są blokowane w programie IE lub w przeglądarce Microsoft Edge, na przykład w przypadku drugiego okna podręcznego podczas [uwierzytelniania wieloskładnikowego](../authentication/concept-mfa-howitworks.md). W przeglądarce zostanie wyświetlony alert umożliwiający wyświetlenie okna podręcznego lub zawsze. Jeśli wybierzesz opcję Zezwalaj, przeglądarka automatycznie otworzy okno podręczne i zwróci `null` do niego uchwyt. W związku z tym biblioteka nie ma dojścia do okna i nie ma możliwości zamykania okna podręcznego. Ten sam problem nie występuje w programie Chrome, gdy zostanie wyświetlony komunikat z prośbą o zezwolenie na wyskakujące okienka, ponieważ nie powoduje automatycznego otwarcia okna podręcznego.

W ramach tego **problemu** deweloperzy będą musieli zezwolić na wyskakujące okienka w programie IE i przeglądarce Microsoft Edge przed rozpoczęciem korzystania z aplikacji, aby uniknąć tego problemu.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [korzystaniu z MSAL.js w programie Internet Explorer](msal-js-use-ie-browser.md).
