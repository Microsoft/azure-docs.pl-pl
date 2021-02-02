---
title: Tłumaczenie linków i adresów URL aplikacja usługi Azure AD proxy | Microsoft Docs
description: Dowiedz się, jak przekierowywać trwale kodowane linki dla aplikacji opublikowanych przy użyciu serwera proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/15/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44266f7a7485b44ba60a27aadd7422e8d1c3acf4
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259410"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-ad-application-proxy"></a>Przekieruj trwale kodowane linki dla aplikacji opublikowanych przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD

Usługa Azure serwer proxy aplikacji usługi Azure AD udostępnia aplikacje lokalne użytkownikom zdalnie lub na ich własnych urządzeniach. Niektóre aplikacje zostały jednak opracowane przy użyciu lokalnych linków osadzonych w kodzie HTML. Te linki nie działają prawidłowo, gdy aplikacja jest używana zdalnie. Gdy masz kilka aplikacji lokalnych, użytkownicy oczekują linków do działania, gdy nie znajdują się one w biurze. 

Najlepszym sposobem, aby upewnić się, że linki działają tak samo zarówno wewnątrz sieci firmowej, jak i poza nią, ma na celu skonfigurowanie zewnętrznych adresów URL aplikacji tak, aby były takie same jak wewnętrzne adresy URL. Użyj [domen niestandardowych](application-proxy-configure-custom-domain.md) , aby skonfigurować zewnętrzne adresy URL w celu posiadania firmowej nazwy domeny zamiast domyślnej domeny serwera proxy aplikacji.


Jeśli w dzierżawie nie można używać domen niestandardowych, istnieje kilka innych opcji zapewniających tę funkcję. Wszystkie z nich są również zgodne z domenami niestandardowymi i innymi, w razie potrzeby można skonfigurować niestandardowe domeny i inne rozwiązania.

> [!NOTE]
> Tłumaczenie linków nie jest obsługiwane w przypadku zakodowanych wewnętrznych adresów URL generowanych za poorednictwem języka JavaScript.

**Opcja 1. Korzystanie z przeglądarki Microsoft Edge** — to rozwiązanie jest stosowane tylko wtedy, gdy planujesz zalecić lub wymagać, aby użytkownicy mieli dostęp do aplikacji za pomocą przeglądarki Microsoft Edge. Będzie obsługiwać wszystkie opublikowane adresy URL. 

**Opcja 2: korzystanie z rozszerzenia moje aplikacje** — to rozwiązanie wymaga od użytkowników zainstalowania rozszerzenia przeglądarki po stronie klienta, ale będzie obsługiwać wszystkie opublikowane adresy URL i współpracuje z najpopularniejszymi przeglądarkami. 

**Opcja 3: Użyj ustawienia tłumaczenia linku** — jest to ustawienie po stronie administratora, które jest niewidoczne dla użytkowników. Będą jednak obsługiwać adresy URL tylko w formacie HTML i CSS.   

Te trzy funkcje przechowują swoje linki niezależnie od tego, gdzie znajdują się użytkownicy. Jeśli masz aplikacje, które wskazują bezpośrednio na wewnętrzne punkty końcowe lub porty, możesz mapować te wewnętrzne adresy URL na opublikowane adresy URL serwera proxy aplikacji zewnętrznych. 

 
> [!NOTE]
> Ostatnia opcja dotyczy tylko dzierżawców, które z jakiegoś powodu nie mogą używać domen niestandardowych, aby mieć te same wewnętrzne i zewnętrzne adresy URL dla swoich aplikacji. Przed włączeniem tej funkcji Sprawdź, czy [w usłudze Azure serwer proxy aplikacji usługi Azure AD można korzystać z domen niestandardowych](application-proxy-configure-custom-domain.md) . 
> 
> Lub, jeśli aplikacja, którą chcesz skonfigurować przy użyciu translacji łączy, to SharePoint, zobacz [Konfigurowanie mapowań dostępu alternatywnego dla programu sharepoint 2013](/SharePoint/administration/configure-alternate-access-mappings) , aby uzyskać inne podejście do mapowania linków. 

 
### <a name="option-1-microsoft-edge-integration"></a>Opcja 1: integracja z programem Microsoft Edge 

Aby zapewnić lepszą ochronę aplikacji i zawartości, możesz użyć przeglądarki Microsoft Edge. Aby można było korzystać z tego rozwiązania, należy wymagać/zalecać użytkownikom dostęp do aplikacji za pomocą przeglądarki Microsoft Edge. Wszystkie wewnętrzne adresy URL opublikowane przy użyciu serwera proxy aplikacji zostaną rozpoznane przez brzeg i przekierowane do odpowiedniego zewnętrznego adresu URL. Gwarantuje to, że wszystkie zakodowane wewnętrzne adresy URL działają i jeśli użytkownik przejdzie do przeglądarki i bezpośrednio wpisze wewnętrzny adres URL, działa nawet wtedy, gdy użytkownik jest zdalny.  

Aby dowiedzieć się więcej, w tym o sposobie konfigurowania tej opcji, zobacz artykuł [Zarządzanie dostępem do sieci Web za pomocą programu Edge dla systemów iOS i Android z Microsoft Intune](/mem/intune/apps/manage-microsoft-edge) dokumentacją.  

### <a name="option-2-myapps-browser-extension"></a>Opcja 2: rozszerzenie przeglądarki Moje aplikacje 

Dzięki rozszerzeniu przeglądarki Moje aplikacje wszystkie wewnętrzne adresy URL opublikowane przy użyciu serwera proxy aplikacji są rozpoznawane przez rozszerzenie i przekierowywane do odpowiedniego zewnętrznego adresu URL. Gwarantuje to, że wszystkie zakodowane wewnętrzne adresy URL działają i jeśli użytkownik przechodzi do paska adresu przeglądarki i bezpośrednio wpisze wewnętrzny adres URL, działa nawet wtedy, gdy użytkownik jest zdalny.  

Aby użyć tej funkcji, użytkownik musi pobrać rozszerzenie i być zalogowany. Dla administratorów lub użytkowników nie jest wymagana żadna inna konfiguracja. 

Aby dowiedzieć się więcej, w tym o sposobie konfigurowania tej opcji, zobacz dokumentację [rozszerzenia przeglądarki Moje aplikacje](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) .

### <a name="option-3-link-translation-setting"></a>Opcja 3: łączenie ustawienia tłumaczenia 

Po włączeniu translacji łączy usługa serwera proxy aplikacji przeszukuje kod HTML i CSS w poszukiwaniu opublikowanych linków wewnętrznych i tłumaczy je tak, aby użytkownicy mogli korzystać z nieprzerwanego środowiska. Korzystanie z rozszerzenia przeglądarki Moje aplikacje jest preferowane dla ustawienia tłumaczenia linku, ponieważ zapewnia użytkownikom bardziej wydajne środowisko.

> [!NOTE]
> Jeśli używasz opcji 2 lub 3, tylko jeden z nich powinien być włączony w danym momencie.

## <a name="how-link-translation-works"></a>Jak działa tłumaczenie linków

Po uwierzytelnieniu, gdy serwer proxy przekazuje dane aplikacji do użytkownika, serwer proxy aplikacji skanuje aplikację pod kątem stałych linków i zastępuje je odpowiednimi opublikowanymi zewnętrznymi adresami URL.

Serwer proxy aplikacji zakłada, że aplikacje są kodowane w formacie UTF-8. Jeśli tak się nie dzieje, określ typ kodowania w nagłówku odpowiedzi HTTP, np `Content-Type:text/html;charset=utf-8` ..

### <a name="which-links-are-affected"></a>Na jakie linki są one modyfikowane?

Funkcja tłumaczenia łączy wyszukuje tylko linki, które znajdują się w tagach kodu w treści aplikacji. Serwer proxy aplikacji ma osobną funkcję do tłumaczenia plików cookie lub adresów URL w nagłówkach. 

Istnieją dwa popularne typy linków wewnętrznych w aplikacjach lokalnych:

- **Względne linki wewnętrzne** wskazujące na zasób udostępniony w strukturze plików lokalnych, takich jak `/claims/claims.html` . Te linki automatycznie pracują w aplikacjach publikowanych za pomocą serwera proxy aplikacji i nadal pracują z tłumaczeniem linków lub bez niego. 
- **Stałe kodowane linki wewnętrzne** do innych aplikacji lokalnych, takich jak `http://expenses` lub pliki opublikowane, takie jak `http://expenses/logo.jpg` . Funkcja tłumaczenia łączy działa na stałych kodowanych łączach wewnętrznych i zmienia je w taki sposób, aby wskazywały zewnętrzne adresy URL, do których użytkownicy zdalni muszą przejść.

Pełna lista atrybutów w znacznikach kodu HTML, które serwer proxy aplikacji obsługuje translację linków dla dołączenia:
* a (href)
* audio (SRC)
* Base (href)
* przycisk (formaction)
* DIV (dane-tło, styl, dane — SRC)
* Osadź (SRC)
* formularz (Akcja)
* Ramka (SRC)
* nagłówek (profil)
* HTML (manifest)
* iframe (longdesc, SRC)
* img (longdesc, SRC)
* dane wejściowe (formaction, src, Value)
* Link (href)
* MenuItem (ikona)
* meta (zawartość)
* Obiekt (Archiwalny, dane, baza kodu)
* skrypt (SRC)
* Źródło (SRC)
* Śledź (SRC)
* wideo (src, plakat)

Ponadto w obszarze CSS atrybut adresu URL jest również tłumaczony.

### <a name="how-do-apps-link-to-each-other"></a>Jak aplikacje łączą się ze sobą?

Tłumaczenie linków jest włączone dla każdej aplikacji, dzięki czemu masz kontrolę nad czynnościami użytkownika na poziomie aplikacji. Włącz tłumaczenie łączy dla aplikacji, jeśli chcesz, aby linki *z* tej aplikacji były tłumaczone, a nie linki *do* tej aplikacji. 

Załóżmy na przykład, że masz trzy aplikacje publikowane przy użyciu serwera proxy aplikacji, które łączą się ze sobą: korzyści, wydatki i podróże. Istnieje czwarta aplikacja, która nie jest publikowana za pomocą serwera proxy aplikacji.

Po włączeniu tłumaczenia linków dla aplikacji korzyści linki do wydatków i podróży są przekierowywane do zewnętrznych adresów URL dla tych aplikacji, ale link do opinii nie zostanie przekierowany z powodu braku zewnętrznego adresu URL. Linki z wydatków i podróż z powrotem do korzyści nie działają, ponieważ nie włączono tłumaczenia linków dla tych dwóch aplikacji.

![Linki od korzyści do innych aplikacji po włączeniu translacji łączy](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Które linki nie są tłumaczone?

Aby zwiększyć wydajność i bezpieczeństwo, niektóre linki nie są tłumaczone:

- Linki poza tagami kodu. 
- Linki, które nie są w formacie HTML ani CSS. 
- Linki w formacie z kodowaniem URL.
- Linki wewnętrzne otwarte z innych programów. Linki wysyłane za pośrednictwem poczty e-mail lub wiadomości błyskawicznych lub zawarte w innych dokumentach nie będą tłumaczone. Użytkownicy muszą wiedzieć, aby przejść do zewnętrznego adresu URL.

Jeśli potrzebujesz obsługi jednego z tych dwóch scenariuszy, Użyj tych samych wewnętrznych i zewnętrznych adresów URL zamiast tłumaczenia linków.  

## <a name="enable-link-translation"></a>Włącz tłumaczenie linków

Wprowadzenie do tłumaczenia linków jest tak proste, jak kliknięcie przycisku:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do pozycji **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje** > wybierz aplikację, którą chcesz zarządzać > **serwerem proxy aplikacji**.
3. Włącz opcję **tłumaczenie adresów URL w treści aplikacji** na **wartość tak**.

   ![Wybierz opcję tak, aby przetłumaczyć adresy URL w treści aplikacji](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

Teraz, gdy użytkownicy uzyskują dostęp do tej aplikacji, serwer proxy automatycznie przeskanuje pod kątem wewnętrznych adresów URL, które zostały opublikowane za pomocą serwera proxy aplikacji w dzierżawie.

## <a name="send-feedback"></a>Wyślij opinię

Chcemy, aby Twoja funkcja działała we wszystkich aplikacjach. Przeszukiwane są ponad 30 tagów w formacie HTML i CSS. Jeśli masz przykład wygenerowanych linków, które nie są tłumaczone, Wyślij fragment kodu do [opinii serwera proxy aplikacji](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Następne kroki
[Używanie domen niestandardowych w usłudze Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md) w celu posiadania tego samego wewnętrznego i zewnętrznego adresu URL

[Konfigurowanie mapowań dostępu alternatywnego dla programu SharePoint 2013](/SharePoint/administration/configure-alternate-access-mappings)
