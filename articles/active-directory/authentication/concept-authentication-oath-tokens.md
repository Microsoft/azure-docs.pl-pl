---
title: Metoda uwierzytelniania tokenów OATH — Azure Active Directory
description: Dowiedz się więcej na temat używania tokenów OATH w Azure Active Directory, aby pomóc w ulepszaniu i zabezpieczaniu zdarzeń związanych z logowaniem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c62b531b0d189ad698a930d65506fff0ebee3a69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532747"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Metody uwierzytelniania w tokenach OATH Azure Active Directory

TOTP OATH (oparte na czasie, hasło jednorazowe) to otwarty standard, który określa sposób generowania kodów haseł jednorazowych (OTP). TOTP OATH można zaimplementować przy użyciu oprogramowania lub sprzętu w celu wygenerowania kodów. Usługa Azure AD nie obsługuje HOTP OATH, innego standardu generowania kodu.

## <a name="oath-software-tokens"></a>Tokeny oprogramowania OATH

Tokeny OATH oprogramowania są zazwyczaj aplikacjami, takimi jak aplikacja Microsoft Authenticator i inne aplikacje uwierzytelniające. Usługa Azure AD generuje klucz tajny lub inicjator, który jest wprowadzany do aplikacji i użyty do wygenerowania każdego uwierzytelniania OTP.

Aplikacja Authenticator automatycznie generuje kody po skonfigurowaniu do wykonywania powiadomień wypychanych, aby użytkownik miał kopię zapasową nawet wtedy, gdy ich urządzenie nie ma łączności. Można również użyć aplikacji innych firm, które używają TOTP OATH do generowania kodów.

Niektóre tokeny sprzętowe TOTP OATH są programowalne, co oznacza, że nie są one dostarczane z kluczem tajnym lub wstępnie zaprogramowanym inicjatorem. Te programowalne tokeny sprzętowe można skonfigurować przy użyciu klucza tajnego lub inicjatora uzyskanego w ramach przepływu instalacji tokenu oprogramowania. Klienci mogą zakupić te tokeny od wybranego dostawcy i używać klucza tajnego lub inicjatora w procesie instalacji dostawcy.

## <a name="oath-hardware-tokens-preview"></a>Tokeny sprzętowe OATH (wersja zapoznawcza)

Usługa Azure AD obsługuje użycie tokenów TOTP SHA-1, które odświeżają kody co 30 lub 60 sekund. Klienci mogą zakupić te tokeny od wybranego przez siebie dostawcy.

Tokeny sprzętowe TOTP OATH zazwyczaj pochodzą z kluczem tajnym lub inicjatorem, który jest wstępnie zaprogramowany w tokenie. Te klucze muszą być danymi wejściowymi do usługi Azure AD, zgodnie z opisem w poniższych krokach. Klucze tajne są ograniczone do 128 znaków, które mogą nie być zgodne ze wszystkimi tokenami. Klucz tajny może zawierać tylko znaki *a-z* lub *a-z* i cyfry *1-7*, a także musi być zakodowany w *Base32*.

Programowalne tokeny sprzętowe TOTP OATH można również skonfigurować za pomocą usługi Azure AD w przepływie instalacji tokenu oprogramowania.

Tokeny sprzętowe OATH są obsługiwane w ramach publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure

![Przekazywanie tokenów OATH do bloku tokenów OATH usługi MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Po uzyskaniu tokenów należy je przekazać w formacie pliku wartości rozdzielanych przecinkami (CSV), w tym nazwy UPN, numeru seryjnego, klucza tajnego, interwału czasu, producenta i modelu, jak pokazano w następującym przykładzie:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Upewnij się, że dołączysz wiersz nagłówka do pliku CSV.

Po poprawnym sformatowaniu pliku CSV administrator może następnie zalogować się do Azure Portal, przejść do **Azure Active Directory > zabezpieczenia > MFA > tokeny Oath**i przekazać plik CSV.

Proces może potrwać kilka minut, w zależności od rozmiaru pliku CSV. Wybierz przycisk **Odśwież** , aby uzyskać bieżący stan. W przypadku wystąpienia błędów w pliku można pobrać plik CSV, który zawiera listę błędów, które należy rozwiązać. Nazwy pól w pobranym pliku CSV różnią się od przekazanej wersji.

Po rozwiązaniu jakichkolwiek błędów administrator może aktywować każdy klucz, wybierając pozycję **Aktywuj** dla tokenu i wprowadzając uwierzytelnianie OTP wyświetlane na tokenie.

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniania, takich jak Microsoft Authenticator aplikacji skonfigurowanych do użycia w dowolnym momencie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [interfejsu API REST Microsoft Graph w wersji beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
