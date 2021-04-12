---
title: Skonfiguruj domenę wydawcy aplikacji | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować domenę wydawcy aplikacji, aby umożliwić użytkownikom zapoznanie się z informacjami o tym, gdzie są wysyłane.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 9b3840ffb39a204cfa24dcb0430f20ac16587582
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104182"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Instrukcje: Konfigurowanie domeny wydawcy aplikacji

Domena wydawcy aplikacji jest wyświetlana użytkownikom z [monitem o zgodę aplikacji](application-consent-experience.md) , aby poinformować użytkowników o tym, gdzie są wysyłane dane. Aplikacje z wieloma dzierżawcami, które są zarejestrowane po 21 maja 2019, które nie mają domeny wydawcy, są wyświetlane jako **niezweryfikowane**. Aplikacje wielodostępne są aplikacjami, które obsługują konta poza pojedynczym katalogiem organizacji; na przykład obsługują wszystkie konta usługi Azure AD lub obsługują wszystkie konta usługi Azure AD i osobiste konta Microsoft.

## <a name="new-applications"></a>Nowe aplikacje

Po zarejestrowaniu nowej aplikacji domena wydawcy aplikacji może być ustawiona na wartość domyślną. Wartość jest zależna od lokalizacji, w której zarejestrowano aplikację, szczególnie w przypadku, gdy aplikacja jest zarejestrowana w dzierżawie i czy dzierżawa ma domeny zweryfikowane przez dzierżawcę.

Jeśli istnieją domeny zweryfikowane przez dzierżawcę, domena wydawcy aplikacji będzie domyślnie mieć podstawową zweryfikowaną domenę dzierżawy. Jeśli nie ma żadnych zweryfikowanych domen dzierżawców (w przypadku, gdy aplikacja nie jest zarejestrowana w dzierżawie), domena wydawcy aplikacji zostanie ustawiona na wartość null.

Poniższa tabela zawiera podsumowanie domyślnego zachowania wartości domeny wydawcy.  

| Domeny zweryfikowane przez dzierżawcę | Domyślna wartość domeny wydawcy |
|-------------------------|----------------------------|
| null | null |
| *. onmicrosoft.com | *. onmicrosoft.com |
| -*. onmicrosoft.com<br/>-domain1.com<br/>-domain2.com (podstawowy) | domain2.com |

Jeśli domena wydawcy aplikacji wielodostępnej nie została ustawiona lub jeśli jest ustawiona na domenę kończącą się na. onmicrosoft.com, monit o zgodę aplikacji będzie zawierał **niezweryfikowane** miejsce w domenie wydawcy.

## <a name="grandfathered-applications"></a>Aplikacje nabyte

Jeśli aplikacja została zarejestrowana przed 21 maja 2019, monit o zgodę aplikacji nie będzie wyświetlany, **Jeśli nie** ustawisz domeny wydawcy. Zalecamy ustawienie wartości domeny wydawcy, aby użytkownicy mogli zobaczyć te informacje w monicie o zgodę aplikacji.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Skonfiguruj domenę wydawcy przy użyciu Azure Portal

Aby ustawić domenę wydawcy aplikacji, wykonaj następujące kroki.

1. Zaloguj się w witrynie <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym zarejestrowano aplikację.
1. Przejdź do [Azure Active Directory > rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) , aby znaleźć i wybrać aplikację, którą chcesz skonfigurować.

   Po wybraniu aplikacji zobaczysz stronę **omówienia** aplikacji.
1. W obszarze **Zarządzaj** wybierz **znakowanie**.
1. Znajdź pole **domena wydawcy** i wybierz jedną z następujących opcji:

   - Wybierz opcję **Konfiguruj domenę** , jeśli jeszcze nie skonfigurowano domeny.
   - Wybierz pozycję **Aktualizuj domenę** , jeśli domena została już skonfigurowana.

Jeśli Twoja aplikacja jest zarejestrowana w dzierżawie, zobaczysz dwie karty do wyboru: **Wybierz zweryfikowaną domenę** i **Sprawdź nową domenę**.

Jeśli aplikacja nie jest zarejestrowana w dzierżawie, zobaczysz tylko opcję weryfikacji nowej domeny dla aplikacji.

### <a name="to-verify-a-new-domain-for-your-app"></a>Aby zweryfikować nową domenę dla aplikacji

1. Utwórz plik o nazwie `microsoft-identity-association.json` i wklej następujący fragment kodu JSON.

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. Zastąp symbol zastępczy *{The-App-ID-in}* nazwą aplikacji (klienta) odpowiadającą aplikacji.

1. Hostowanie pliku w: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json` . Zastąp symbol zastępczy *{domeną tutaj}* , aby pasował do zweryfikowanej domeny.

1. Kliknij przycisk **Weryfikuj i Zapisz domenę** .

### <a name="to-select-a-verified-domain"></a>Aby wybrać zweryfikowaną domenę

- Jeśli dzierżawca ma zweryfikowane domeny, wybierz jedną z domen z listy rozwijanej **Wybierz zweryfikowaną domenę** .

>[!Note]
> Oczekiwany nagłówek "Content-Type", który ma zostać zwrócony `application/json` . Może wystąpić błąd, jak wspomniano poniżej, jeśli używasz innych elementów, takich jak `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Konsekwencje dotyczące monitu o zgodę aplikacji

Skonfigurowanie domeny wydawcy ma wpływ na to, co użytkownicy widzą w monicie o zgodę aplikacji. Aby w pełni zrozumieć składniki monitu o zgodę, zapoznaj się z artykułem dotyczącym sposobu [wyrażania zgody na aplikacje](application-consent-experience.md).

W poniższej tabeli opisano zachowanie aplikacji utworzonych przed 21 maja 2019.

![Monit o zgodę dla aplikacji utworzonych przed 21 maja 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Zachowanie nowych aplikacji utworzonych po 21 maja 2019 będzie zależeć od domeny wydawcy i typu aplikacji. W poniższej tabeli opisano zmiany, które powinny być widoczne w różnych kombinacjach konfiguracji.

![Monit o zgodę dla aplikacji utworzonych po 21 maja 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Konsekwencje dotyczące identyfikatorów URI przekierowania

Aplikacje logujące użytkowników przy użyciu dowolnego konta służbowego lub osobiste konta Microsoft (z[wieloma dzierżawcami](single-and-multi-tenant-apps.md)) podlegają kilku ograniczeniom podczas określania identyfikatorów URI przekierowania.

### <a name="single-root-domain-restriction"></a>Ograniczenie pojedynczej domeny głównej

Jeśli wartość domeny wydawcy dla aplikacji wielodostępnych ma wartość null, aplikacje są ograniczone do współużytkowania jednej domeny katalogu głównego dla identyfikatorów URI przekierowania. Na przykład następująca kombinacja wartości nie jest dozwolona, ponieważ domena główna, contoso.com, nie jest zgodna z fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Ograniczenia poddomen

Domeny podrzędne są dozwolone, ale należy jawnie zarejestrować domenę główną. Na przykład, chociaż następujące identyfikatory URI współużytkują jedną domenę główną, kombinacja nie jest dozwolona.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Jeśli jednak deweloper jawnie doda domenę główną, można użyć kombinacji.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Wyjątki

Następujące przypadki nie podlegają ograniczeniu pojedynczej domeny głównej:

- Aplikacje pojedynczej dzierżawy lub aplikacje, które są kontami docelowymi w jednym katalogu
- Użycie localhost jako identyfikatorów URI przekierowania
- Przekieruj identyfikatory URI ze schematami niestandardowymi (nie HTTP lub HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Programowo Konfiguruj domenę wydawcy

Obecnie nie ma interfejsu API REST ani obsługi programu PowerShell w celu programistycznego konfigurowania domeny wydawcy.
