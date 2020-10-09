---
title: Samouczek aplikacji sieci Web — testowanie połączenia z interfejsem API platformy Azure dla usługi FHIR
description: Te samouczki zapoznają się z przykładem wdrożenia prostej aplikacji sieci Web. W tej części samouczka przedstawiono testowanie łączenia z serwerem FHIR za pomocą programu Poster
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852926"
---
# <a name="testing-the-fhir-api"></a>Testowanie interfejsu API FHIR
W poprzednich dwóch krokach wdrożono interfejs API platformy Azure dla FHIR i zarejestrowano aplikację kliencką. Teraz można przystąpić do testowania, czy interfejs API platformy Azure dla usługi FHIR został skonfigurowany za pomocą aplikacji klienckiej. 

## <a name="retrieve-capability-statement"></a>Pobierz instrukcję możliwości
Najpierw otrzymamy instrukcję możliwości dla interfejsu API platformy Azure dla FHIR. 
1. Otwórz notkę
1. Pobierz instrukcję możliwości, pobierając https:// \<FHIR-SERVER-NAME> . azurehealthcareapis.com/Metadata. Na obrazie poniżej nazwy serwera FHIR jest **fhirserver**.

![Instrukcja możliwości](media/tutorial-web-app/postman-capability-statement.png)

Następnie Podejmiemy próbę pobrania pacjenta. Aby pobrać pacjenta, wprowadź GET https:// \<FHIR-SERVER-NAME> . azurehealthcareapis.com/Patient. Zostanie wyświetlony komunikat o błędzie 401 nieautoryzowany. Ten błąd jest spowodowany tym, że nie sprawdzono, czy użytkownik powinien mieć dostęp do danych pacjenta.

## <a name="get-patient-from-fhir-server"></a>Pobierz pacjenta z serwera FHIR
![Niepowodzenie pacjenta](media/tutorial-web-app/postman-patient-authorization-failed.png)

Aby uzyskać dostęp, wymagany jest token dostępu.
1. W obszarze Poster wybierz pozycję **autoryzacja** i ustaw typ **uwierzytelniania OAuth 2.0** .
1. Wybierz pozycję **Pobierz nowy token dostępu**
1. Wypełnij pola i wybierz **token żądania**. Poniżej znajdują się wartości poszczególnych pól dla tego samouczka.

|Pole                |Wartość                                                               |
|---------------------|--------------------------------------------------------------------|
|Nazwa tokenu           |Nazwa tokenu                                               |
|Typ udzielenia           |Kod autoryzacji                                                  |
|Adres URL wywołania zwrotnego         |https://www.getpostman.com/oauth2/callback                          |
|Adres URL uwierzytelniania             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID>/OAuth2/? Resource = https:// \<FHIR-SERVER-NAME> . azurehealthcareapis.com|
|Adres URL tokenu dostępu     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID>/oauth2/token|
|Identyfikator klienta            |Identyfikator klienta skopiowany podczas poprzednich kroków             |
|Klucz tajny klienta        |\<BLANK>                                                            |
|Zakres                |\<BLANK>                                                            |
|State                |1234                                                                |
|Uwierzytelnianie klienta|Wyślij poświadczenia klienta w treści                                     |

4. Zaloguj się przy użyciu swoich poświadczeń i wybierz pozycję **Akceptuj**
1. Przewiń w dół na wynik i wybierz pozycję **Użyj tokenu**
1. Wybierz pozycję **Wyślij** ponownie u góry, a tym razem Poproś o ![ pomyślną utratę wyniku](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>Publikuj pacjenta na serwerze FHIR
Teraz masz dostęp, możesz utworzyć nowego pacjenta. Oto przykład prostego pacjenta, którą można dodać do serwera FHIR. Wprowadź Poniższy kod do sekcji **treść** programu Poster.

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
Ten wpis spowoduje utworzenie nowego pacjenta na serwerze FHIR o nazwie Kuba Tiberious Kirka.
![Opublikuj pacjenta](media/tutorial-web-app/postman-post-patient.png)

W przypadku powyższego kroku GET w celu ponownego pobrania pacjenta zobaczysz w danych wyjściowych polecenie Kuba Tiberious Kirka.

## <a name="troubleshooting-access-issues"></a>Rozwiązywanie problemów z dostępem
Jeśli wystąpią problemy podczas wykonywania któregokolwiek z tych kroków, przejrzyj dokumenty, które zostały umieszczone w Azure Active Directory i interfejs API platformy Azure dla FHIR. 

* [Azure AD i Azure API for FHIR](azure-ad-hcapi.md) — ten dokument zawiera podstawowe zasady Azure Active Directory i sposób współdziałania z interfejsem API platformy Azure dla FHIR.
* [Sprawdzanie poprawności tokenu dostępu](azure-ad-hcapi-token-validation.md) — ten przewodnik zawiera bardziej szczegółowe informacje dotyczące sprawdzania poprawności tokenu dostępu i czynności, które należy podjąć w celu rozwiązania problemów z dostępem.

## <a name="next-steps"></a>Następne kroki
Teraz, po pomyślnym nawiązaniu połączenia z aplikacją kliencką, możesz przystąpić do pisania aplikacji sieci Web.

>[!div class="nextstepaction"]
>[Napisz aplikację sieci Web](tutorial-web-app-write-web-app.md)



