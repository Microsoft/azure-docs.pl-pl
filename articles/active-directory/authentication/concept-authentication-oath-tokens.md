---
title: Metoda uwierzytelniania tokenów OATH — Azure Active Directory
description: Dowiedz się więcej na temat używania tokenów OATH w Azure Active Directory, aby pomóc w ulepszaniu i zabezpieczaniu zdarzeń związanych z logowaniem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44016d81b18e8df7b6e2ed7c14559cf19ac0c07d
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106640"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Metody uwierzytelniania w tokenach OATH Azure Active Directory 

TOTP OATH (oparte na czasie, hasło jednorazowe) to otwarty standard, który określa sposób generowania kodów haseł jednorazowych (OTP). TOTP OATH można zaimplementować przy użyciu oprogramowania lub sprzętu w celu wygenerowania kodów. Usługa Azure AD nie obsługuje HOTP OATH, innego standardu generowania kodu.

## <a name="oath-software-tokens"></a>Tokeny oprogramowania OATH

Tokeny OATH oprogramowania są zazwyczaj aplikacjami, takimi jak aplikacja Microsoft Authenticator i inne aplikacje uwierzytelniające. Usługa Azure AD generuje klucz tajny lub inicjator, który jest wprowadzany do aplikacji i użyty do wygenerowania każdego uwierzytelniania OTP.

Aplikacja Authenticator automatycznie generuje kody po skonfigurowaniu do wykonywania powiadomień wypychanych, aby użytkownik miał kopię zapasową nawet wtedy, gdy ich urządzenie nie ma łączności. Można również użyć aplikacji innych firm, które używają TOTP OATH do generowania kodów.

Niektóre tokeny sprzętowe TOTP OATH są programowalne, co oznacza, że nie są one dostarczane z kluczem tajnym lub wstępnie zaprogramowanym inicjatorem. Te programowalne tokeny sprzętowe można skonfigurować przy użyciu klucza tajnego lub inicjatora uzyskanego w ramach przepływu instalacji tokenu oprogramowania. Klienci mogą zakupić te tokeny od wybranego dostawcy i używać klucza tajnego lub inicjatora w procesie instalacji dostawcy.

## <a name="oath-hardware-tokens-preview"></a>Tokeny sprzętowe OATH (wersja zapoznawcza)

Usługa Azure AD obsługuje użycie tokenów TOTP SHA-1, które odświeżają kody co 30 lub 60 sekund. Klienci mogą zakupić te tokeny od wybranego przez siebie dostawcy.

Tokeny sprzętowe TOTP OATH zazwyczaj pochodzą z kluczem tajnym lub inicjatorem, który jest wstępnie zaprogramowany w tokenie. Te klucze muszą być danymi wejściowymi do usługi Azure AD, zgodnie z opisem w poniższych krokach. Klucze tajne są ograniczone do 128 znaków, które mogą nie być zgodne ze wszystkimi tokenami. Klucz tajny może zawierać tylko znaki *a-z* lub *a-z* i cyfry *2-7*, a także musi być zakodowany w *Base32*.

Programowalne tokeny sprzętowe TOTP OATH można również skonfigurować za pomocą usługi Azure AD w przepływie instalacji tokenu oprogramowania.

Tokeny sprzętowe OATH są obsługiwane w ramach publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

![Przekazywanie tokenów OATH do bloku tokenów OATH usługi MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Po uzyskaniu tokenów należy je przekazać w formacie pliku wartości rozdzielanych przecinkami (CSV), w tym nazwy UPN, numeru seryjnego, klucza tajnego, interwału czasu, producenta i modelu, jak pokazano w następującym przykładzie:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Upewnij się, że dołączysz wiersz nagłówka do pliku CSV. Jeśli nazwa UPN ma pojedyncze cudzysłowy, należy to zrobić z innym pojedynczym cudzysłowem. Na przykład jeśli nazwa UPN to my user@domain.com , Zmień ją na mój "" user@domain.com podczas przekazywania pliku.

Po prawidłowym sformatowaniu pliku CSV Administrator globalny może następnie zalogować się do Azure Portal, przejść do **Azure Active Directory > zabezpieczenia > MFA > tokeny Oath** i przekazać plik CSV.

Proces może potrwać kilka minut, w zależności od rozmiaru pliku CSV. Wybierz przycisk **Odśwież** , aby uzyskać bieżący stan. W przypadku wystąpienia błędów w pliku można pobrać plik CSV, który zawiera listę błędów, które należy rozwiązać. Nazwy pól w pobranym pliku CSV różnią się od przekazanej wersji.  

Po rozwiązaniu jakichkolwiek błędów administrator może aktywować każdy klucz, wybierając pozycję **Aktywuj** dla tokenu i wprowadzając uwierzytelnianie OTP wyświetlane na tokenie. Maksymalnie 200 tokenów OATH można aktywować co 5 minut. 

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniania, takich jak Microsoft Authenticator aplikacji skonfigurowanych do użycia w dowolnym momencie. Tokeny OATH sprzętu nie mogą być przypisane do użytkowników-Gości w dzierżawie zasobów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [interfejsu API REST Microsoft Graph w wersji beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
