---
title: Samouczek — wprowadzenie do Azure Active Directory poświadczenia do zweryfikowania przy użyciu przykładowej aplikacji (wersja zapoznawcza)
description: W tym samouczku dowiesz się, jak wystawiać zweryfikowane poświadczenia przy użyciu naszej przykładowej aplikacji i dzierżawy testowej
ms.service: identity
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ms.openlocfilehash: deebaf31197d8b7335f887ae447f05add45278b2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222889"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>Samouczek — wprowadzenie do Azure Active Directory poświadczenia do zweryfikowania przy użyciu przykładowej aplikacji (wersja zapoznawcza)

W tym samouczku przejdziemy do kroków niezbędnych do wystawienia pierwszego zweryfikowanego poświadczenia: zweryfikowanej karty eksperta Credential. Następnie możesz użyć tej karty, aby udowodnić weryfikatorowi, że jesteś zweryfikowanym ekspertem poświadczania, który jest zarządzany w formie cyfrowego poświadczenia. Rozpocznij pracę z poświadczeniami do zweryfikowania Azure Active Directory przy użyciu przykładowej aplikacji do zweryfikowania poświadczeń w celu wystawienia pierwszego zweryfikowanego poświadczenia.

![To jest obraz karty przykładowej](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

- [NodeJS](https://nodejs.org/en/download/) w wersji 10,14 lub nowszej zainstalowanej w naszym systemie testowym.
- Jeśli chcesz sklonować repozytorium obsługujące przykładową aplikację, musisz mieć zainstalowaną usługę [git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/Download)
- System do hostowania naszej przykładowej witryny.
- Urządzenie przenośne z zainstalowaną Microsoft Authenticator w wersji 6.2005.3599 lub nowszej.
- [NGROK](https://ngrok.com/) bezpłatnie.

## <a name="download-the-sample-code"></a>Pobieranie przykładowego kodu

Aby samodzielnie wystawić zweryfikowaną kartę eksperta, musisz uruchomić witrynę sieci Web na komputerze lokalnym. Witryna internetowa służy do inicjowania możliwego do zweryfikowania procesu wystawiania poświadczeń. Firma Microsoft udostępniła prostą witrynę sieci Web, która jest zapisywana w NodeJS, która jest używana w tym samouczku.

Najpierw pobierz nasz przykładowy kod z usługi GitHub [tutaj](https://github.com/Azure-Samples/active-directory-verifiable-credentials)lub Sklonuj repozytorium na komputerze lokalnym:

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

Możesz chcieć zaznajomić się z kodem w przykładowych witrynach sieci Web. `issuer`Folder zawiera cały kod używany do wydawania zweryfikowanego poświadczenia. Więcej szczegółów można znaleźć w [pliku Readme](https://github.com/Azure-Samples/active-directory-verifiable-credentials)przykładu.

## <a name="run-the-issuer-website"></a>Uruchamianie witryny sieci Web wystawcy

Kroki można wykonać z poziomu Visual Studio Code lub dowolnego wiersza polecenia dostępnego w systemie operacyjnym. 

1. Przejdź do folderu `issuer`. 

    ```terminal
    cd issuer
    ```

2. Gdy musimy zainstalować wszystkie wymagane pakiety i uruchomić lokację.

   ```terminal
    npm install
    node app.js
    ```

3. W terminalu zobaczysz teraz, że aplikacja wystawcy nasłuchuje na porcie 8081. Teraz Skonfigurujmy zwrotny serwer proxy za pomocą Ngrok, dzięki czemu wystawca może komunikować się z Twoją aplikacją. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Tworzenie zwrotnego serwera proxy za pomocą Ngrok

Po uruchomieniu przykładowej witryny sieci Web urządzenie musi komunikować się z serwerem węzła uruchomionym na komputerze lokalnym. Zalecamy używanie [ngrok](https://ngrok.com/) jako łatwego sposobu, aby lokalny serwer programistyczny był dostępny za pośrednictwem Internetu.

1.  Po pobraniu i wyodrębnieniu **ngrok** musimy uruchomić:

    ```terminal
     ngrok http 8081
    ```

Domyślnie Przykładowa witryna sieci Web jest uruchamiana na porcie `8081` . **Ngrok** wyprowadza dwa adresy URL przekazywania dla serwera. Skopiuj adres URL z `https://` prefiksem.

![ngrok pomaga zapewnić dostęp do punktów końcowych aplikacji za pośrednictwem Internetu](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> Jeśli używasz programu PowerShell, może być konieczne wpisanie polecenia, aby `./ngrok` rozpoznać polecenie.

Teraz, gdy port lokalny jest uwidoczniony w Internecie przy użyciu usługi ngrok, witryna Przykładowa automatycznie używa nazwy hosta wygenerowanej przez ngrok. Otwórz przeglądarkę i przejdź do adresu URL przekazywania https ngrok. Powinno być możliwe pomyślne odwiedzenie strony głównej witryny przykładowej. Jeśli zostanie otwarta strona, urządzenie może komunikować się z przykładową aplikacją uruchomioną na serwerze lokalnym. Teraz możesz przystąpić do samodzielnego wystawienia zweryfikowanej karty eksperta.

## <a name="issue-a-credential"></a>Wystawianie poświadczeń

1. Zainstaluj wystawcę uwierzytelnienia na urządzeniu przenośnym. Microsoft Authenticator jest używany do odbierania, przechowywania i prezentowania poświadczeń do zweryfikowania zainteresowanym stronom.

2. Następnie wystawiaj samodzielnie poświadczenia do zweryfikowania. **Kliknij** przycisk **Pobierz poświadczenia** . Po kliknięciu przycisku **Pobierz poświadczenia** w przykładowej witrynie sieci Web zostanie wyświetlony kod QR, który można skanować przy użyciu uwierzytelniania. Jeśli zobaczysz witrynę z przeglądarki na swoim urządzeniu przenośnym, kliknij przycisk **Pobierz poświadczenia** wyzwala link głębokiego, który otwiera aplikację Authenticator i nie wymaga skanowania kodu QR.

   ![Przycisk uzyskiwania poświadczeń](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Przeskanuj kod QR witryny sieci Web przy użyciu uwierzytelniania lub, Jeśli uzyskujesz dostęp do witryny sieci Web za pośrednictwem urządzenia przenośnego, kliknij przycisk Pobierz poświadczenia, aby wyzwolić link bezpośredni. 

   ![Kod QR ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. Zauważ, że w tym momencie przycisk **Dodaj** jest wyszarzony. Wybierz pozycję **Zaloguj się do swojego konta** poniżej obrazu karty.

   ![Zaloguj ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. Przed rozpoczęciem korzystania z karty eksperta Credential dzierżawca wymaga podania informacji o uwierzytelnianiu. Jeśli po raz pierwszy przeprowadzisz Cię przez samouczek, nie masz konta eksperta poświadczeń, Utwórz nowe konto użytkownika w naszej dzierżawie B2C.

   ![Uwierzytelnij przed kontynuowaniem](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. Po zalogowaniu przycisk **Dodaj** nie jest już wyszarzony. Wybierz pozycję **Dodaj** , aby zaakceptować swój nowy obwód wirtualny.

   ![Wybierz pozycję Dodaj po uwierzytelnieniu](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Gratulacje! Masz teraz zweryfikowanego eksperta poświadczenie VC.

   ![Dodano eksperta ds. poświadczeń](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
Następnie należy sprawdzić poświadczenie.

## <a name="validate-credentials"></a>Weryfikowanie poświadczeń

Po ukończeniu części wystawiania samouczka w ramach uwierzytelniania można sprawdzić, czy masz poświadczenia do zweryfikowania w ramach swojej aplikacji weryfikującej.

1.  Zatrzymaj uruchamianie usługi wystawcy ngrok.

    ```terminal
     control+c
    ```


2. W innym oknie terminalu Otwórz folder App-Verifier i uruchom go podobnie jak w przypadku uruchomienia aplikacji wystawcy.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Teraz uruchom ngrok z portem weryfikatora 8082.

    ```terminal
    ngrok http 8082
    ```

4. Otwórz adres URL przekazywania https ngrok w przeglądarce i naciśnij przycisk **Weryfikuj poświadczenia** .  

   ![przycisk Weryfikuj eksperta poświadczeń](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Otwórz wystawcę uwierzytelniania i Zeskanuj kod QR.

   ![Skanuj kod QR, aby zweryfikować poświadczenie](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > W systemie iOS jest to pierwsze prawo i w systemie Android, które jest w prawym dolnym rogu. Zeskanuj kod QR.

6. Wybierz pozycję **Zezwalaj** na nowym ekranie żądania uprawnień w programie Authenticator. Naciskając pozycję Zezwól, logujesz się do zweryfikowanej prezentacji z identyfikatorem użytkownika (w sposób Niescentralizowany), aby potwierdzić, że w rzeczywistości kontrolujesz to zweryfikowane poświadczenia.

   ![nowe żądanie uprawnienia](media/get-started-verifiable-credentials/new-permission-request.png)

    Po pomyślnym zakończeniu prezentacji trzy rzeczy należy zaktualizować:

   1. Na stronie sieci Web powinna zostać wyświetlona wartość "gratulacje, Twoja nazwa" + to zweryfikowany biegły Credential.
   
    ![Gratulacje, sprawdź ponownie](media/get-started-verifiable-credentials/congratulations.png)


   2. Terminal aplikacji weryfikatora powinien również wyświetlać ten sam komunikat z dzienników.
   
    ![aktywność aplikacji w wierszu polecenia](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. W programie Authenticator powinien istnieć wpis dotyczący ostatniej aktywności tej prezentacji.

    ![Działanie w usłudze Authenticator](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> Podczas uruchamiania aplikacji weryfikatora ngrok może przestać działać i wyświetlić błąd, że istnieje zbyt wiele połączeń. Znaleźliśmy, że można to uniknąć, rejestrując konto w usłudze ngrok. 

## <a name="next-steps"></a>Następne kroki

Po pomyślnym ukończeniu przewodnika Szybki Start można utworzyć własny identyfikator zdecentralizowany w usłudze poświadczeń do weryfikacji usługi Azure AD i wydać własne zweryfikowane poświadczenia.

>[!div class="nextstepaction"] 
>[Konfigurowanie własnego wystawcy przy użyciu przykładowej aplikacji z poświadczeniami do zweryfikowania](./enable-your-tenant-verifiable-credentials.md)
