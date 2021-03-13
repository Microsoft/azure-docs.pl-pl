---
title: Azure Active Directory INTELIGENTNEgo serwera proxy FHIR
description: W tym samouczku opisano, jak używać serwera proxy do włączania inteligentnych aplikacji FHIR przy użyciu interfejsu API platformy Azure dla FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 04/02/2019
ms.openlocfilehash: 2e13a9fc32964781dda07e5534e5cab79868ddf0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020732"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>Samouczek: Azure Active Directory INTELIGENTNEgo serwera proxy FHIR

[Inteligentne w FHIR](https://docs.smarthealthit.org/) to zestaw otwartych specyfikacji umożliwiających integrację aplikacji partnerskich z serwerami FHIR i systemami rekordów elektronicznych w postaci elektronicznej, które mają interfejsy FHIR. Jednym z głównych celów specyfikacji jest opisywanie, w jaki sposób aplikacja ma wykrywać punkty końcowe uwierzytelniania dla serwera FHIR i uruchamiać sekwencję uwierzytelniania. 

Uwierzytelnianie opiera się na OAuth2. Jednak ponieważ funkcja SMART on FHIR używa konwencji nazewnictwa parametrów, które nie są bezpośrednio zgodne z Azure Active Directory (Azure AD), interfejs API platformy Azure dla FHIR ma wbudowaną funkcję usługi Azure AD SMART na serwerze proxy FHIR, która umożliwia podzbiór sekwencji uruchamiania INTELIGENTNEgo na FHIR. W związku z tym serwer proxy włącza [sekwencję uruchamiania pacjentów](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence).

W tym samouczku opisano, jak używać serwera proxy do włączania inteligentnych aplikacji FHIR przy użyciu interfejsu API platformy Azure dla FHIR.

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie interfejsu API platformy Azure dla usługi FHIR
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Konfigurowanie rejestracji w usłudze Azure AD

Usługa SMART on FHIR wymaga, aby identyfikator `Audience` URI identyfikatora był równy identyfikatorowi URI usługi FHIR. Standardowa konfiguracja interfejsu API platformy Azure dla usługi FHIR używa `Audience` wartości `https://azurehealthcareapis.com` . Można jednak ustawić wartość zgodną z określonym adresem URL usługi FHIR (na przykład `https://MYFHIRAPI.azurehealthcareapis.com` ). Jest to wymagane w przypadku pracy z INTELIGENTNYm serwerem proxy FHIR.

Potrzebna będzie również Rejestracja aplikacji klienta. Najbardziej Inteligentne aplikacje FHIR to jednostronicowe aplikacje JavaScript. Należy zatem postępować zgodnie z instrukcjami dotyczącymi konfigurowania [publicznej aplikacji klienckiej usługi Azure AD](register-public-azure-ad-client-app.md).

Po wykonaniu tych kroków należy wykonać następujące czynności:

- Serwer FHIR z grupami RGE `https://MYFHIRAPI.azurehealthcareapis.com` , gdzie `MYFHIRAPI` jest nazwą interfejsu API platformy Azure dla wystąpienia FHIR.
- Zarejestrowanie publicznej aplikacji klienckiej. Zanotuj identyfikator aplikacji dla tej aplikacji klienckiej.

## <a name="enable-the-smart-on-fhir-proxy"></a>Włączanie INTELIGENTNEgo serwera proxy FHIR

Włącz INTELIGENTNEgo serwera proxy usługi FHIR w ustawieniach **uwierzytelniania** interfejsu API platformy Azure dla wystąpienia FHIR, zaznaczając pole wyboru **inteligentnego serwera proxy FHIR** :

![Opcje włączania INTELIGENTNEgo serwera proxy FHIR](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>Włączanie mechanizmu CORS

Ponieważ najbardziej Inteligentne aplikacje FHIR to jednostronicowe aplikacje JavaScript, należy [włączyć udostępnianie zasobów między źródłami (CORS)](configure-cross-origin-resource-sharing.md) dla interfejsu API platformy Azure dla FHIR:

![Wybory dotyczące włączania mechanizmu CORS](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>Konfigurowanie adresu URL odpowiedzi

INTELIGENTNY serwer proxy FHIR działa jako pośrednik między aplikacją INTELIGENTNą w FHIR i usługą Azure AD. Odpowiedź uwierzytelniania (kod uwierzytelniania) musi przechodzić do INTELIGENTNEgo serwera proxy FHIR zamiast samej aplikacji. Następnie serwer proxy przekazuje odpowiedź do aplikacji. 

Z powodu tego dwuetapowego przekazywania kodu uwierzytelniania należy ustawić adres URL odpowiedzi (wywołanie zwrotne) dla aplikacji klienckiej usługi Azure AD na adres URL, który jest kombinacją adresu URL odpowiedzi dla INTELIGENTNEgo serwera proxy FHIR oraz adres URL odpowiedzi dla aplikacji SMART on FHIR. Łączny adres URL odpowiedzi przyjmuje następującą formę:

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

W tej odpowiedzi `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` jest to bezpieczna w adresie URL wersja adresu URL odpowiedzi dla aplikacji Smart on FHIR. W przypadku uruchamiania aplikacji SMART on FHIR, gdy aplikacja działa lokalnie, adres URL odpowiedzi to `https://localhost:5001/sampleapp/index.html` . 

Łączny adres URL odpowiedzi można wygenerować za pomocą skryptu w następujący sposób:

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

Dodaj adres URL odpowiedzi do publicznej aplikacji klienckiej utworzonej wcześniej dla usługi Azure AD:

![Adres URL odpowiedzi skonfigurowany dla klienta publicznego](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>Uzyskiwanie pacjenta testowego

W celu przetestowania interfejsu API platformy Azure dla usługi FHIR i INTELIGENTNEgo serwera proxy FHIR należy mieć co najmniej jednego pacjenta w bazie danych. Jeśli nie masz jeszcze współpracy z interfejsem API i nie masz danych w bazie danych, postępuj zgodnie z [samouczkiem FHIR interfejsu API](access-fhir-postman-tutorial.md) w celu załadowania pacjenta. Zanotuj identyfikator określonego pacjenta.

## <a name="download-the-smart-on-fhir-app-launcher"></a>Pobieranie INTELIGENTNEgo uruchamiania aplikacji FHIR

Serwer FHIR typu "open source" [dla repozytorium platformy Azure](https://github.com/Microsoft/fhir-server) zawiera proste inteligentne uruchamianie aplikacji FHIR i przykładową aplikację inteligentną w FHIR. W tym samouczku Użyj tego INTELIGENTNEgo uruchamiania programu FHIR lokalnie w celu przetestowania instalacji.

Repozytorium GitHub można sklonować i przejść do aplikacji przy użyciu następujących poleceń:

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

Aplikacja wymaga kilku ustawień konfiguracji, które można ustawić w `appsettings.json` :

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

Zalecamy korzystanie z tej `dotnet user-secrets` funkcji:

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

Użyj tego polecenia, aby uruchomić aplikację:

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>Testowanie INTELIGENTNEgo serwera proxy FHIR

Po uruchomieniu INTELIGENTNEgo uruchamiania aplikacji FHIR możesz wskazać przeglądarkę `https://localhost:5001` , w której powinien zostać wyświetlony następujący ekran:

![Inteligentne uruchamianie aplikacji FHIR](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

Gdy wprowadzisz informacje o **pacjentach**, **napotykasz** lub **lekarzach** , zobaczysz, że **kontekst uruchamiania** został zaktualizowany. Gdy korzystasz z interfejsu API platformy Azure dla FHIR, kontekstem uruchamiania jest po prostu dokument JSON zawierający informacje o pacjentach, lekarzach i innych. Ten kontekst uruchamiania jest kodowany algorytmem Base64 i przeszedł do INTELIGENTNEj aplikacji FHIR jako `launch` parametr zapytania. Zgodnie ze specyfikacją SMART on FHIR ta zmienna jest nieprzezroczysta dla INTELIGENTNEj aplikacji FHIR i przenoszona do dostawcy tożsamości. 

INTELIGENTNY serwer proxy FHIR używa tych informacji do wypełniania pól w odpowiedzi na token. Aplikacja SMART on FHIR *może* używać tych pól do kontrolowania tego, którego pacjenta żąda dane, i sposobu renderowania aplikacji na ekranie. INTELIGENTNY serwer proxy FHIR obsługuje następujące pola:

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

Te pola mają na celu zapewnienie wskazówek dla aplikacji, ale nie zapewniają żadnych informacji o zabezpieczeniach. Aplikacja SMART on FHIR może je zignorować.

Zauważ, że usługa INTELIGENTNEgo uruchamiania aplikacji FHIR aktualizuje informacje o **uruchomieniu adresu URL** w dolnej części strony. Wybierz pozycję **Uruchom** , aby uruchomić przykładową aplikację, a następnie zobacz coś podobne do tego przykładu:

![Aplikacja SMART on FHIR](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

Zbadaj odpowiedź tokenu, aby zobaczyć, jak pola kontekstu uruchamiania są przesyłane do aplikacji.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano Azure Active Directory INTELIGENTNEgo serwera proxy FHIR. Aby poznać korzystanie z inteligentnych aplikacji FHIR przy użyciu interfejsu API platformy Azure dla FHIR i serwera typu open source FHIR dla platformy Azure, przejdź do repozytorium przykładów serwera FHIR w witrynie GitHub:

>[!div class="nextstepaction"]
>[Przykłady serwera FHIR](https://github.com/Microsoft/fhir-server-samples)
