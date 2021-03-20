---
title: Definicje plików cookie
titleSuffix: Azure AD B2C
description: Zawiera definicje plików cookie używanych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e44e2e1eb37e808e60134a6fba5051552e84029
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85389347"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definicje plików cookie dla Azure AD B2C

Poniższe sekcje zawierają informacje o plikach cookie używanych w programie Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameSite

Usługa Microsoft Azure AD B2C jest zgodna z konfiguracjami przeglądarki SameSite, w tym obsługą dla `SameSite=None` `Secure` atrybutu.

Aby zapewnić ochronę dostępu do lokacji, przeglądarki sieci Web wprowadzają nowy model bezpieczny przez domyślny, który zakłada, że wszystkie pliki cookie powinny być chronione przed dostępem zewnętrznym, o ile nie określono inaczej. Przeglądarka Chrome jest pierwszą osobą implementującą tę zmianę, począwszy od programu [chrome 80 w lutym 2020](https://www.chromium.org/updates/same-site). Aby uzyskać więcej informacji na temat przygotowania do zmiany w programie Chrome, zobacz [Developers: Get Read for New SameSite = none; Bezpieczne ustawienia plików cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) w blogu chromu.

Deweloperzy muszą używać nowego ustawienia plików cookie, `SameSite=None` Aby wyznaczyć pliki cookie dla dostępu między lokacjami. Gdy `SameSite=None` atrybut jest obecny, `Secure` należy użyć dodatkowego atrybutu, aby można było uzyskać dostęp do plików cookie między lokacjami za pośrednictwem połączeń HTTPS. Weryfikuj i Testuj wszystkie aplikacje, w tym aplikacje, które używają Azure AD B2C.

Aby uzyskać więcej informacji, zobacz:

* [Obsługa zmian plików cookie SameSite w przeglądarce Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Wpływ na witryny sieci Web klienta oraz usługi i produkty firmy Microsoft w programie Chrome w wersji 80 lub nowszej](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Pliki cookie

W poniższej tabeli wymieniono pliki cookie używane w Azure AD B2C.

| Nazwa | Domena | Wygaśnięcie | Przeznaczenie |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Koniec [sesji przeglądarki](session-behavior.md) | Przechowuje dane członkostwa użytkowników między dzierżawcami. Dzierżawy, których użytkownik jest członkiem i poziom członkostwa (administrator lub użytkownik). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Służy do kierowania żądaniami do odpowiedniego wystąpienia produkcyjnego. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Służy do śledzenia transakcji (liczba żądań uwierzytelniania do Azure AD B2C) i bieżącej transakcji. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Używany do konserwacji sesji logowania jednokrotnego. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md), pomyślne uwierzytelnienie | Używane do obsługi stanu żądania. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Token podrabiania żądania dla wielu witryn używany do ochrony CRSF. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Używany do Azure AD B2C routingu sieciowego. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Kontekst |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Używany do przechowywania danych członkostwa dla dzierżawy dostawcy zasobów. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, domena z marką | Koniec [sesji przeglądarki](session-behavior.md) | Używany do przechowywania pliku cookie przekazywania. |
