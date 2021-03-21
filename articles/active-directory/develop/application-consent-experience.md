---
title: Funkcje wyrażania zgody aplikacji usługi Azure AD
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o usłudze Azure AD, aby zobaczyć, jak można jej używać podczas zarządzania aplikacjami i opracowywania ich w usłudze Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: c4a4af81c6a216119ae2e1b0221c06ddc349452f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92478137"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Opis środowisk zgody dla aplikacji usługi Azure AD

Dowiedz się więcej na temat sposobu korzystania z aplikacji Azure Active Directory (Azure AD). Dzięki temu można inteligentnie zarządzać aplikacjami dla organizacji i/lub opracowywać aplikacje z bardziej bezproblemowe.

## <a name="consent-and-permissions"></a>Zgoda i uprawnienia

Zgoda polega na tym, że użytkownik udzielający autoryzacji aplikacji dostępu do chronionych zasobów w ich imieniu. Administrator lub użytkownik może zostać poproszony o zgodę, aby zezwolić na dostęp do swojej organizacji/poszczególnych danych.

Rzeczywiste środowisko użytkownika udzielania zgody będzie różnić się w zależności od zasad ustawionych w dzierżawie użytkownika, zakresu uwierzytelniania użytkownika (lub roli) oraz typu [uprawnień](../azuread-dev/v1-permissions-consent.md) wymaganych przez aplikację kliencką. Oznacza to, że deweloperzy aplikacji i Administratorzy dzierżawy mają pewną kontrolę nad doświadczeniem w zakresie wyrażania zgody. Administratorzy mają elastyczność ustawiania i wyłączania zasad w dzierżawie lub aplikacji w celu kontrolowania sposobu wyrażania zgody w dzierżawie. Deweloperzy aplikacji mogą określać, jakie typy uprawnień są wymagane i czy chcą przeprowadzić użytkowników przez przepływ zgody użytkownika lub przepływ zgody administratora.

- **Przepływ zgody użytkownika** polega na tym, że deweloper aplikacji kieruje użytkowników do punktu końcowego autoryzacji z zamiarem zarejestrowania zgody wyłącznie dla bieżącego użytkownika.
- **Przepływ zgody administratora** polega na tym, że deweloper aplikacji kieruje użytkowników do punktu końcowego zgody na administratora z zamiarem zarejestrowania zgody dla całej dzierżawy. Aby zapewnić prawidłowe działanie przepływu zgody administratora, deweloperzy aplikacji muszą wyświetlić listę wszystkich uprawnień we `RequiredResourceAccess` właściwości w manifeście aplikacji. Aby uzyskać więcej informacji, zobacz [manifest aplikacji](./reference-app-manifest.md).

## <a name="building-blocks-of-the-consent-prompt"></a>Bloki konstrukcyjne monitu o zgodę

Monit o zgodę został zaprojektowany w celu zapewnienia, że użytkownicy mają wystarczającą ilość informacji, aby określić, czy ufają aplikacji klienckiej w celu uzyskania dostępu do chronionych zasobów w ich imieniu. Zrozumienie bloków konstrukcyjnych pomoże użytkownikom w udzielaniu zgody na podejmowanie bardziej świadomych decyzji i ułatwia deweloperom tworzenie lepszych środowisk użytkowników.

Poniższy diagram i tabela zawierają informacje dotyczące bloków konstrukcyjnych monitu o zgodę.

![Bloki konstrukcyjne monitu o zgodę](./media/application-consent-experience/consent_prompt.png)

| # | Składnik | Przeznaczenie |
| ----- | ----- | ----- |
| 1 | Identyfikator użytkownika | Ten identyfikator reprezentuje użytkownika, którego aplikacja kliencka żąda dostępu do chronionych zasobów w imieniu. |
| 2 | Tytuł | Tytuł zmienia się w zależności od tego, czy użytkownicy przechodzą przez przepływ zgody użytkownika lub administratora. W przepływie zgody użytkownika tytuł będzie "żądanymi uprawnieniami" w przepływie zgody administratora, a tytuł będzie miał dodatkowy wiersz "Akceptuj dla organizacji". |
| 3 | Logo aplikacji | Ten obraz powinien ułatwić użytkownikom wizualne określenie, czy ta aplikacja jest aplikacją, do której mają dostęp. Ten obraz jest dostarczany przez deweloperów aplikacji, a własność tego obrazu nie jest zweryfikowana. |
| 4 | Nazwa aplikacji | Ta wartość powinna informować użytkowników, którzy aplikacje żądają dostępu do swoich danych. Należy pamiętać, że ta nazwa jest dostarczana przez deweloperów, a własność tej nazwy aplikacji nie jest zweryfikowana. |
| 5 | Domena wydawcy | Ta wartość powinna zapewnić użytkownikom domenę, że może ona być w stanie oszacować pod kątem wiarygodności. Ta domena jest świadczona przez deweloperów i sprawdzana jest własność tej domeny wydawcy. |
| 6 | Uprawnienia | Ta lista zawiera uprawnienia wymagane przez aplikację kliencką. Użytkownicy powinni zawsze oszacować typy żądanych uprawnień, aby zrozumieć, jakie dane aplikacja kliencka będzie uprawniona do uzyskiwania dostępu w ich imieniu, jeśli zostaną one zaakceptowane. Deweloper aplikacji najlepiej zażądać dostępu do uprawnień o najniższych uprawnieniach. |
| 7 | Opis uprawnienia | Ta wartość jest dostarczana przez usługę, która uwidacznia uprawnienia. Aby wyświetlić opisy uprawnień, należy przełączyć cudzysłów ostrokątny obok uprawnienia. |
| 8 | Warunki aplikacji | Warunki te zawierają linki do warunków użytkowania usługi i zasad zachowania poufności informacji aplikacji. Wydawca jest odpowiedzialny za tworzenie reguł w swoich warunkach użytkowania. Ponadto Wydawca jest odpowiedzialny za odzamykanie sposobu używania i udostępniania danych użytkownika w zasadach zachowania poufności informacji. Jeśli Wydawca nie poda linków do tych wartości dla aplikacji wielodostępnych, w monicie o zgodę zostanie pogrubione ostrzeżenie. |
| 9 | https://myapps.microsoft.com | Jest to link, w którym użytkownicy mogą przeglądać i usuwać aplikacje inne niż firmy Microsoft, które aktualnie mają dostęp do swoich danych. |

## <a name="common-consent-scenarios"></a>Typowe scenariusze wyrażania zgody

Poniżej przedstawiono sposób, w jaki użytkownik może zobaczyć w typowych scenariuszach zgody:

1. Osoby, które uzyskują dostęp do aplikacji, która kieruje je do przepływu wyrażania zgody użytkownika, ale wymaga zestawu uprawnień znajdującego się w zakresie urzędu certyfikacji.
    
    1. Administratorzy będą widzieć dodatkową kontrolę nad tradycyjnym monitem o zgodę, który zezwoli na ich zgodę w imieniu całej dzierżawy. Formant zostanie przyznany jako wyłączony, więc tylko wtedy, gdy Administratorzy jawnie zaznaczą pole, zgoda zostanie udzielona w imieniu całej dzierżawy. Obecnie to pole wyboru będzie wyświetlane tylko dla roli administratora globalnego, więc administrator chmury i administrator aplikacji nie będą widzieć tego pola wyboru.

        ![Monit o zgodę dla scenariusza 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Użytkownicy będą widzieć tradycyjny monit o zgodę.

        ![Zrzut ekranu pokazujący tradycyjny monit o zgodę.](./media/application-consent-experience/consent_prompt_1b.png)

2. Osoby, które uzyskują dostęp do aplikacji, która wymaga co najmniej jednego uprawnienia poza ich zakresem uprawnień.
    1. Administratorzy będą widzieli ten sam monit co 1. pokazane powyżej.
    2. Użytkownicy będą mogli udzielać zgody na dostęp do aplikacji i otrzymają monit o poproszenie administratora o udzielenie dostępu do aplikacji. 
                
        ![Zrzut ekranu przedstawiający monit o zgodę informujący użytkownika o konieczności uzyskania dostępu do aplikacji.](./media/application-consent-experience/consent_prompt_2b.png)

3. Osoby, które poruszają się lub kierują do przepływu zgody administratora.
    1. Administratorzy będą widzieli monit o zgodę administratora. Tytuł i opisy uprawnień zostały zmienione w tym monicie, natomiast w przypadku zmiany zostanie wyświetlona informacja o tym, że ten monit zostanie udzielony, udziel aplikacji dostępu do żądanych danych w imieniu całej dzierżawy.
        
        ![Monit o zgodę dla scenariusza 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Użytkownicy niebędący administratorami zobaczą ten sam ekran co 2. II powyżej.

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z omówieniem krok po kroku [na temat sposobu, w jaki platforma zgody usługi Azure AD implementuje wyrażanie zgody](./quickstart-register-app.md).
- Aby uzyskać większą głębokość, Dowiedz się, w [jaki sposób aplikacja wielodostępna może używać struktury zgody](./howto-convert-app-to-be-multi-tenant.md) do implementowania zgody "User" i "admin", co pozwala na bardziej zaawansowane wzorce aplikacji wielowarstwowej.
- Dowiedz się [, jak skonfigurować domenę wydawcy aplikacji](howto-configure-publisher-domain.md).