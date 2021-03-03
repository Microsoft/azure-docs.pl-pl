---
title: Metoda uwierzytelniania tokenów OATH — Azure Active Directory
description: Dowiedz się więcej na temat używania tokenów OATH w Azure Active Directory, aby pomóc w ulepszaniu i zabezpieczaniu zdarzeń związanych z logowaniem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2270ff360c7bb923555c9b4ffb0c35ccd4382d0e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647493"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Metody uwierzytelniania w tokenach OATH Azure Active Directory

TOTP OATH (oparte na czasie, hasło jednorazowe) to otwarty standard, który określa sposób generowania kodów haseł jednorazowych (OTP). TOTP OATH można zaimplementować przy użyciu oprogramowania lub sprzętu w celu wygenerowania kodów. Usługa Azure AD nie obsługuje HOTP OATH, innego standardu generowania kodu.

## <a name="oath-software-tokens"></a>Tokeny oprogramowania OATH

Tokeny OATH oprogramowania są zazwyczaj aplikacjami, takimi jak aplikacja Microsoft Authenticator i inne aplikacje uwierzytelniające. Usługa Azure AD generuje klucz tajny lub inicjator, który jest wprowadzany do aplikacji i użyty do wygenerowania każdego uwierzytelniania OTP.

Aplikacja Authenticator automatycznie generuje kody po skonfigurowaniu do wykonywania powiadomień wypychanych, aby użytkownik miał kopię zapasową nawet wtedy, gdy ich urządzenie nie ma łączności. Można również użyć aplikacji innych firm, które używają TOTP OATH do generowania kodów.

Niektóre tokeny sprzętowe TOTP OATH są programowalne, co oznacza, że nie są one dostarczane z kluczem tajnym lub wstępnie zaprogramowanym inicjatorem. Te programowalne tokeny sprzętowe można skonfigurować przy użyciu klucza tajnego lub inicjatora uzyskanego w ramach przepływu instalacji tokenu oprogramowania. Klienci mogą zakupić te tokeny od wybranego dostawcy i używać klucza tajnego lub inicjatora w procesie instalacji dostawcy.

## <a name="oath-hardware-tokens"></a>Tokeny sprzętowe OATH

Usługa Azure AD obsługuje użycie tokenów TOTP SHA-1, które odświeżają kody co 30 lub 60 sekund. Klienci mogą zakupić te tokeny od wybranego przez siebie dostawcy.

Tokeny sprzętowe TOTP OATH zazwyczaj pochodzą z kluczem tajnym lub inicjatorem, który jest wstępnie zaprogramowany w tokenie. Te klucze muszą być danymi wejściowymi do usługi Azure AD, zgodnie z opisem w poniższych krokach. Klucze tajne są ograniczone do 128 znaków, które mogą nie być zgodne ze wszystkimi tokenami. Klucz tajny może zawierać tylko znaki *a-z* lub *a-z* i cyfry *2-7*, a także musi być zakodowany w *Base32*.

Programowalne tokeny sprzętowe TOTP OATH można również skonfigurować za pomocą usługi Azure AD w przepływie instalacji tokenu oprogramowania.

![Przekazywanie tokenów OATH do bloku tokenów OATH usługi MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Po uzyskaniu tokenów należy je przekazać w formacie pliku wartości rozdzielanych przecinkami (CSV), w tym nazwy UPN, numeru seryjnego, klucza tajnego, interwału czasu, producenta i modelu, jak pokazano w następującym przykładzie:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef1234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Upewnij się, że dołączysz wiersz nagłówka do pliku CSV. Jeśli nazwa UPN ma pojedyncze cudzysłowy, należy to zrobić z innym pojedynczym cudzysłowem. Na przykład jeśli nazwa UPN to my user@domain.com , Zmień ją na mój "" user@domain.com podczas przekazywania pliku.

Po poprawnym sformatowaniu pliku CSV administrator może następnie zalogować się do Azure Portal, przejść do **Azure Active Directory > zabezpieczenia > MFA > tokeny Oath** i przekazać plik CSV.

Proces może potrwać kilka minut, w zależności od rozmiaru pliku CSV. Wybierz przycisk **Odśwież** , aby uzyskać bieżący stan. W przypadku wystąpienia błędów w pliku można pobrać plik CSV, który zawiera listę błędów, które należy rozwiązać. Nazwy pól w pobranym pliku CSV różnią się od przekazanej wersji.  

Po rozwiązaniu jakichkolwiek błędów administrator może aktywować każdy klucz, wybierając pozycję **Aktywuj** dla tokenu i wprowadzając uwierzytelnianie OTP wyświetlane na tokenie. Maksymalnie 200 tokenów OATH można aktywować co 5 minut. 

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniania, takich jak Microsoft Authenticator aplikacji skonfigurowanych do użycia w dowolnym momencie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [interfejsu API REST Microsoft Graph w wersji beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
