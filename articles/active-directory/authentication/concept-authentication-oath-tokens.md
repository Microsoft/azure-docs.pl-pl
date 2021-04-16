---
title: Metoda uwierzytelniania tokenów OATH — Azure Active Directory
description: Dowiedz się więcej o używaniu tokenów OATH w Azure Active Directory, aby ułatwić ulepszanie i zabezpieczanie zdarzeń logowania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99d0dd081e3e1a681ba55e3457b79a548d6b2bb7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530389"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Metody uwierzytelniania w Azure Active Directory — tokeny OATH 

OATH TOTP (time-based One Time Password) to otwarty standard określający sposób generowania kodów haseł jednorazowych (OTP). OATH TOTP można zaimplementować przy użyciu oprogramowania lub sprzętu do generowania kodów. Usługa Azure AD nie obsługuje standardu OATH HOTP, czyli innego standardu generowania kodu.

## <a name="oath-software-tokens"></a>Tokeny oprogramowania OATH

Tokeny OATH oprogramowania to zazwyczaj aplikacje, takie jak Microsoft Authenticator i inne aplikacje wystawcy uwierzytelnień. Usługa Azure AD generuje klucz tajny ,czyli iniekcję, który jest wejściowy do aplikacji i używany do generowania poszczególnych uwierzytelniania OTP.

Aplikacja Authenticator automatycznie generuje kody w przypadku skonfigurowania powiadomień wypychanych, dzięki czemu użytkownik może utworzyć kopię zapasową, nawet jeśli urządzenie nie ma łączności. Można również używać aplikacji innych firm, które używają protokołu OATH TOTP do generowania kodów.

Niektóre tokeny sprzętowe OATH TOTP są programowalne, co oznacza, że nie są one ze wstępnie zaprogramowanym kluczem tajnym ani wstępnie zaprogramowanym iniekcją. Te programowalne tokeny sprzętowe można skonfigurować przy użyciu klucza tajnego lub iniekcyju uzyskanego z przepływu konfiguracji tokenu oprogramowania. Klienci mogą zakupić te tokeny od wybranego dostawcy i użyć klucza tajnego lub iniekta w procesie konfiguracji dostawcy.

## <a name="oath-hardware-tokens-preview"></a>Tokeny sprzętowe OATH (wersja zapoznawcza)

Usługa Azure AD obsługuje korzystanie z tokenów SHA-1 OATH-TOTP, które odświeżają kody co 30 lub 60 sekund. Klienci mogą zakupić te tokeny od wybranego dostawcy.

Tokeny sprzętowe OATH TOTP są zwykle wyposażone w klucz tajny lub iniekcję wstępnie zaprogramowane w tokenie. Te klucze muszą być wprowadzane do usługi Azure AD zgodnie z opisem w poniższych krokach. Klucze tajne są ograniczone do 128 znaków, które mogą nie być zgodne ze wszystkimi tokenami. Klucz tajny może zawierać tylko znaki *a–z* lub *A–Z* i *cyfry 2–7* i musi być zakodowany w *formacie Base32*.

Programowalne tokeny sprzętowe OATH TOTP, które można ponownie edytować, można również skonfigurować za pomocą usługi Azure AD w przepływie konfiguracji tokenu oprogramowania.

Tokeny sprzętowe OATH są obsługiwane w ramach publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz Dodatkowe warunki użytkowania dotyczące [wersji Microsoft Azure zapoznawczych.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Przekazywanie tokenów OATH do bloku tokenów uwierzytelniania wieloskładnikowego OATH](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Pozyskane tokeny należy przekazać w formacie pliku wartości rozdzielanych przecinkami (CSV), w tym nazwę UPN, numer seryjny, klucz tajny, przedział czasu, producenta i model, jak pokazano w poniższym przykładzie:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Upewnij się, że w pliku CSV znajduje się wiersz nagłówka. Jeśli upn ma pojedynczy cudzysłów, należy go ominąć innym pojedynczym cudzysłowem. Jeśli na przykład nazwa UPN to my, zmień ją na user@domain.com my'' user@domain.com podczas przekazywania pliku.

Po prawidłowym sformatowaniu jako plik CSV administrator globalny może zalogować się do usługi Azure Portal, przejść do usługi **Azure Active Directory > Security > MFA > tokenów OATH** i przekazać wynikowy plik CSV.

W zależności od rozmiaru pliku CSV przetwarzanie może potrwać kilka minut. Wybierz przycisk **Odśwież,** aby uzyskać bieżący stan. Jeśli w pliku występują błędy, możesz pobrać plik CSV zawierający listę błędów do rozwiązania. Nazwy pól w pobranym pliku CSV różnią się od przekazanej wersji.  

Po wprowadzeniu jakichkolwiek błędów administrator może aktywować  każdy klucz, wybierając pozycję Aktywuj dla tokenu i wprowadzając uwierzytelnianie OTP wyświetlane w tokenie. Co 5 minut można aktywować maksymalnie 200 tokenów OATH. 

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji wystawcy uwierzytelnień, takich jak Microsoft Authenticator aplikacji, skonfigurowanych do użycia w dowolnym momencie. Sprzętowych tokenów OATH nie można przypisać do użytkowników-gości w dzierżawie zasobów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [Microsoft Graph API REST.](/graph/api/resources/authenticationmethods-overview)
