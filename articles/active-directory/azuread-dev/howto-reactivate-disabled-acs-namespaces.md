---
title: Aktywuj ponownie wyłączone przestrzenie nazw usługi Azure Access Control Service (ACS)
description: Znajdź i Włącz przestrzenie nazw platformy Azure Access Control Service (ACS) i poproś o rozszerzenie, aby je włączyć do 4 lutego 2019.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7bb572e9e22519491290e54417f5ca350d6c0b5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383721"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Jak ponownie uaktywnić wyłączone Access Control Service przestrzenie nazw

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

W listopadzie 2017 ogłoszono, że Microsoft Azure Access Control Service (ACS), usługa Azure Active Directory (Azure AD) zostanie wycofana w dniu 7 listopada 2018.

Od tego momentu wysłaliśmy wiadomości e-mail do administratora usługi ACS w celu uzyskania informacji o wycofaniu usług ACS przez 12 miesięcy, 9 miesięcy, 6 miesięcy, 3 miesiące, 1 miesiąc, 2 tygodnie, 1 tydzień i 1 dzień przed datą wycofania 7 listopada 2018.

3 października 2018 ogłoszone (za pośrednictwem poczty e-mail i [wpisu w blogu](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) oferty rozszerzenia dla klientów, którzy nie mogą zakończyć migracji przed 7 listopada 2018. Anons zawiera również instrukcje dotyczące żądania rozszerzenia.

## <a name="why-your-namespace-is-disabled"></a>Dlaczego przestrzeń nazw jest wyłączona

Jeśli nie wybrano rozszerzenia, zaczniemy wyłączyć przestrzenie nazw ACS od 7 listopada 2018. Użytkownik musi mieć zażądane rozszerzenie 4 lutego 2019; w przeciwnym razie nie będzie można włączyć przestrzeni nazw za pomocą programu PowerShell.

> [!NOTE]
> Musisz być administratorem usługi lub współadministratorem subskrypcji, aby uruchomić polecenia programu PowerShell i zażądać rozszerzenia.

## <a name="find-and-enable-your-acs-namespaces"></a>Znajdowanie i włączanie przestrzeni nazw ACS

Za pomocą programu ACS PowerShell można wyświetlić listę wszystkich przestrzeni nazw usługi ACS i ponownie uaktywnić te, które zostały wyłączone.

1. Pobierz i zainstaluj program PowerShell ACS:
    1. Przejdź do Galeria programu PowerShell i Pobierz [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Zainstaluj moduł:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Pobierz listę wszystkich możliwych poleceń:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Aby uzyskać pomoc dotyczącą określonego polecenia, uruchom polecenie:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        gdzie `[Command-Name]` to nazwa polecenia ACS.
1. Połącz się z usługą ACS przy użyciu polecenia cmdlet **Connect-AcsAccount** . 

    Aby można było uruchomić polecenie, może być konieczna zmiana zasad wykonywania przez uruchomienie polecenia **Set-executionpolicy** .
1. Wyświetl listę dostępnych subskrypcji platformy Azure za pomocą polecenia cmdlet **Get-AcsSubscription** .
1. Utwórz listę przestrzeni nazw ACS przy użyciu polecenia cmdlet **Get-AcsNamespace** .
1. Upewnij się, że przestrzenie nazw zostały wyłączone przez potwierdzenie, że `State` jest to `Disabled` .

    [![Upewnij się, że przestrzenie nazw są wyłączone](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Możesz również użyć `nslookup {your-namespace}.accesscontrol.windows.net` , aby potwierdzić, czy domena jest nadal aktywna.

1. Włącz przestrzenie nazw ACS przy użyciu polecenia cmdlet **enable-AcsNamespace** .

    Po włączeniu przestrzeni nazw możesz zażądać rozszerzenia, aby przestrzenie nazw nie były ponownie wyłączone przed 4 lutego 2019. Po tej dacie wszystkie żądania do usługi ACS zakończą się niepowodzeniem.

## <a name="request-an-extension"></a>Żądaj rozszerzenia

Pobieramy nowe żądania rozszerzenia od 21 stycznia 2019.

Firma Microsoft zacznie wyłączać obszary nazw dla klientów, którzy zażądali rozszerzeń 4 lutego 2019. Nadal można ponownie włączyć przestrzenie nazw za pomocą programu PowerShell, ale przestrzenie nazw zostaną wyłączone ponownie po 48 godzinach.

Po 4 marca 2019 klienci nie będą już mogli ponownie włączać żadnych przestrzeni nazw przy użyciu programu PowerShell.

Dalsze rozszerzenia nie będą już automatycznie zatwierdzane. Jeśli potrzebujesz dodatkowego czasu na przeprowadzenie migracji, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/#create/Microsoft.Support) , aby zapewnić szczegółową oś czasu migracji.

### <a name="to-request-an-extension"></a>Aby zażądać rozszerzenia

1. Zaloguj się do Azure Portal i Utwórz [nowe żądanie obsługi](https://portal.azure.com/#create/Microsoft.Support).
1. Wypełnij formularz nowe żądanie obsługi, jak pokazano w poniższym przykładzie.

    | Pole Support request | Wartość |
    |-----------------------|--------------------|
    | **Typ problemu** | `Technical` |
    | **Subskrypcja** | Ustaw na subskrypcję |
    | **Usługa** | `All services` |
    | **Zasób** | `General question/Resource not available` |
    | **Typ problemu** | `ACS to SAS Migration` |
    | **Temat** | Opisz problem |

   ![Pokazuje przykład nowego żądania pomocy technicznej](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Pomoc i obsługa techniczna

- Jeśli po wykonaniu tych czynności wystąpią problemy, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Jeśli masz pytania lub opinie na temat emerytury usługi ACS, skontaktuj się z nami pod adresem acsfeedback@microsoft.com .

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z informacjami na temat wycofania usługi ACS w temacie [How to: Migracja z Access Control Service platformy Azure](active-directory-acs-migration.md).
