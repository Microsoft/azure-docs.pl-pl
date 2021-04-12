---
title: Samouczek — Konfigurowanie Azure Active Directory w celu wydawania zweryfikowanych poświadczeń (wersja zapoznawcza)
description: W tym samouczku utworzysz środowisko potrzebne do wdrożenia zweryfikowanych poświadczeń w dzierżawie
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: cd39f6c484ebe116918611bb1d543c1919a3cb0a
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222949"
---
# <a name="tutorial---configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Samouczek — Konfigurowanie Azure Active Directory w celu wydawania zweryfikowanych poświadczeń (wersja zapoznawcza)

W tym samouczku tworzymy pracę wykonywaną w ramach artykułu [wprowadzenie](get-started-verifiable-credentials.md) i skonfigurujemy Azure Active Directory (Azure AD) przy użyciu własnego [identyfikatora zdecentralizowanego](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (go). Używamy identyfikatora zdecentralizowanego, aby wystawić zweryfikowane poświadczenia za pomocą przykładowej aplikacji i wystawcy. Jednak w tym samouczku nadal korzystamy z przykładowej dzierżawy usługi Azure B2C na potrzeby uwierzytelniania.  W naszym następnym samouczku zajmiemy się dodatkowymi krokami, aby skonfigurować aplikację do pracy z usługą Azure AD.

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule:

> [!div class="checklist"]
> * Należy utworzyć niezbędne usługi, aby dołączyć do usługi Azure AD w celu zweryfikowania poświadczeń 
> * Tworzymy Twoją
> * Dostosowujemy reguły i pliki wyświetlane
> * Skonfiguruj zweryfikowane poświadczenia w usłudze Azure AD.


## <a name="prerequisites"></a>Wymagania wstępne

Aby można było pomyślnie ukończyć ten samouczek, należy najpierw wykonać następujące czynności:

- Zakończ pracę [.](get-started-verifiable-credentials.md)
- Posiadanie konta platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure AD z [licencją](https://azure.microsoft.com/pricing/details/active-directory/)P2. Postępuj zgodnie z instrukcjami [tworzenia bezpłatnego konta dewelopera](how-to-create-a-free-developer-account.md) , jeśli go nie masz.
- Wystąpienie [Azure Key Vault](../../key-vault/general/overview.md) , w którym masz uprawnienia do tworzenia kluczy i wpisów tajnych.

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

Zanim będziemy mogli zacząć, potrzebujemy dzierżawy usługi Azure AD. Gdy dzierżawa jest włączona w celu zweryfikowania poświadczeń, ma przypisany identyfikator zdecentralizowany (DID) i jest włączona za pomocą usługi wystawcy do wystawiania zweryfikowanych poświadczeń. Wszystkie zweryfikowane poświadczenia, które wystawiasz, są wystawiane przez dzierżawcę. Został również użyty podczas weryfikowania zweryfikowanych poświadczeń.
Jeśli została utworzona testowa subskrypcja platformy Azure, dzierżawa nie musi być wypełniona kontami użytkowników, ale musisz mieć co najmniej jedno konto testowe użytkownika, aby ukończyć późniejsze samouczki.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Podczas pracy z poświadczeniami możliwymi do zweryfikowania masz pełną kontrolę nad kluczami kryptograficznymi używanymi przez dzierżawcę do cyfrowego podpisywania poświadczeń zweryfikowanych i zarządzania nimi. Aby wystawiać i weryfikować poświadczenia, musisz zapewnić usłudze Azure AD dostęp do własnego wystąpienia Azure Key Vault.

1. W menu Azure Portal lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.
2. W polu wyszukiwania wprowadź wartość **Magazyn kluczy**.
3. Na liście wyników wybierz pozycję **Key Vault**.
4. W sekcji Key Vault, wybierz przycisk **Utwórz**.
5. W sekcji **Tworzenie magazynu kluczy** podaj następujące informacje:
    - **Subskrypcja**: wybierz subskrypcję.
    - W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów, na przykład **VC-Resource-Group**. Używamy tej samej nazwy grupy zasobów w wielu artykułach.
    - **Nazwa**: wymagana jest unikatowa nazwa. Używamy **drewna w języku VC-KV** , aby zastąpić tę wartość własną unikatową nazwą.
    - W menu rozwijanym **Lokalizacja** wybierz lokalizację.
    - Dla pozostałych opcji zostaw wartości domyślne.
6. Po podania powyższych informacji wybierz pozycję **zasady dostępu**

    ![Tworzenie strony magazynu kluczy](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. Na ekranie zasady dostępu wybierz pozycję **Dodaj zasady dostępu**

    >[!NOTE]
    > Domyślnie konto, które tworzy magazyn kluczy, jest jedynym elementem z dostępem. Usługa poświadczeń do zweryfikowania musi mieć dostęp do magazynu kluczy. Magazyn kluczy musi mieć zasady dostępu pozwalające administratorowi na **Tworzenie kluczy**, mieć możliwość **usuwania kluczy** , Jeśli zrezygnujesz, i **Zaloguj** się, aby utworzyć powiązanie domeny w celu zweryfikowania poświadczeń. Jeśli korzystasz z tego samego konta podczas testowania, pamiętaj, aby zmodyfikować zasady domyślne w celu przyznania **użytkownikom konta oprócz** domyślnych uprawnień udzielonych twórcom magazynu.

8. W przypadku administratora użytkownika upewnij się, że w sekcji uprawnienia klucza została **utworzona**, **usunięta** i **włączona.** Domyślnie tworzenie i usuwanie jest już włączone, a znak powinien być jedynym uprawnieniem, które należy zaktualizować. 

    ![Uprawnienia Key Vault](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. Wybierz pozycję **Przejrzyj i utwórz**.
10. Wybierz pozycję **Utwórz**.
11. Przejdź do magazynu i zanotuj nazwę magazynu i identyfikator URI

Zanotuj dwie poniższe właściwości:

- **Nazwa magazynu**: w tym przykładzie nazwa wartości to **Woodgrove-VC-KV**. Ta nazwa jest używana do innych kroków.
- **Identyfikator URI magazynu**: w tym przykładzie ta wartość to https://woodgrove-vc-kv.vault.azure.net/ . Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

>[!NOTE]
> Każda transakcja magazynu kluczy skutkuje dodatkowymi kosztami subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) .

>[!IMPORTANT]
> W wersji zapoznawczej Azure Active Directory poświadczenia, klucze i wpisy tajne utworzone w magazynie nie powinny być modyfikowane po utworzeniu. Usunięcie, wyłączenie lub aktualizacja kluczy i wpisów tajnych unieważnia wszystkie wystawione poświadczenia. Nie należy modyfikować kluczy ani wpisów tajnych w wersji zapoznawczej.

## <a name="create-a-modified-rules-and-display-file"></a>Tworzenie zmodyfikowanych reguł i plików wyświetlanych

W tej sekcji używamy zasad i plików wyświetlanych z przykładowej aplikacji wystawcy i zmodyfikujesz je nieco, aby utworzyć pierwsze poświadczenia do zweryfikowania dzierżawy.

1. Skopiuj reguły i Wyświetl pliki JSON do folderu tymczasowego i zmień ich nazwy **MyFirstVC-display.jsna** i **MyFirstVC-rules.js** odpowiednio. Oba pliki można znaleźć w obszarze **wystawca \ issuer_config**

   ![Wyświetlanie plików i reguł w ramach przykładowego katalogu aplikacji](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![Wyświetlanie plików i reguł w folderze tymczasowym](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. Otwórz MyFirstVC-rules.jsw pliku w edytorze kodu. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

Teraz Zmieńmy wartość pola typ na "MyFirstVC". 

  ```json
   "type": ["MyFirstVC"]
  
  ```

Zapisz tę zmianę.

 >[!NOTE]
   > W tym punkcie samouczka nie zmieniamy **"konfiguracji"** ani **"client_id"** . Nadal korzystamy z dzierżawy B2C firmy Microsoft, która [została](get-started-verifiable-credentials.md)użyta w temacie Wprowadzenie. Będziemy używać usługi Azure AD w następnym samouczku.

3. Otwórz MyFirstVC-display.jsw pliku w edytorze kodu.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

Umożliwia wprowadzenie kilku modyfikacji, aby to zweryfikowane poświadczenia wyglądały inaczej niż wersja przykładowego kodu. 
    
```json
     "card": {
        "title": "My First VC",
        "issuedBy": "Your Issuer Name",
        "backgroundColor": "#ffffff",
        "textColor": "#000000",
```

Zapisz te zmiany.
## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Przed utworzeniem pierwszego zweryfikowanego poświadczenia należy utworzyć kontener Blob Storage, który będzie przechowywać pliki konfiguracji i reguł.

1. Utwórz konto magazynu, korzystając z opcji przedstawionych poniżej. Szczegółowe instrukcje znajdują się w artykule [Tworzenie konta magazynu](../../storage/common/storage-account-create.md?tabs=azure-portal) .

   - **Subskrypcja:** Wybierz subskrypcję używaną dla tych samouczków.
   - **Grupa zasobów:** Wybierz tę samą grupę zasobów, która została użyta we wcześniejszych samouczkach (**VC-Resource-Group**).
   - **Nazwa:**  Unikatowa nazwa.
   - **Lokalizacja:** (US) Wschodnie stany USA.
   - **Wydajność:** Standardowa.
   - **Rodzaj konta:** Magazyn v2.
   - **Replikacja:** Lokalnie nadmiarowy.
 
   ![Tworzenie nowego konta magazynu](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. Po utworzeniu konta magazynu musimy utworzyć kontener. Wybierz **kontenery** w obszarze **BLOB Storage** i Utwórz kontener przy użyciu wartości podanych poniżej:

    - **Nazwa:** VC-Container
    - **Poziom dostępu publicznego:** Prywatny (bez dostępu anonimowego)

   ![Tworzenie kontenera](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. Teraz wybierz nowy kontener i przekaż zarówno nowe reguły, jak i Wyświetl pliki **MyFirstVC-display.js** i **MyFirstVC-rules.js** utworzone wcześniej.

   ![Przekaż plik reguł](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>Przypisywanie roli obiektu BLOB

Przed utworzeniem poświadczeń musimy najpierw nadać zalogowanemu użytkownikowi prawidłowe przypisanie roli, aby mogły uzyskać dostęp do plików w magazynie obiektów BLOB.

1. Przejdź do   >  **kontenera** magazynu.
2. Wybierz pozycję **Access Control (IAM)** z menu po lewej stronie.
3. Wybierz **przypisania ról**.
4. Wybierz pozycję **Dodaj**.
5. W sekcji **rola** wybierz **czytnik danych magazynu obiektów BLOB**.
6. W obszarze **Przypisz dostęp do** wybierz **zasady użytkownika, grupy lub usługi**.
7. W obszarze **Wybierz**: wybierz konto, którego używasz, aby wykonać te kroki.
8. Wybierz pozycję **Zapisz** , aby zakończyć przypisanie roli.


   ![Dodaj przypisanie roli](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >Domyślnie twórcy kontenerów uzyskują przypisaną rolę **właściciela** . Rola **właściciela** jest zbyt mała. Twoje konto musi mieć rolę **czytnika danych obiektu blob magazynu** . Aby uzyskać więcej informacji [, zobacz Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../../storage/common/storage-auth-aad-rbac-portal.md)

## <a name="set-up-verifiable-credentials-preview"></a>Skonfiguruj poświadczenia do zweryfikowania (wersja zapoznawcza)

Teraz należy wykonać ostatni krok w celu skonfigurowania dzierżawy do zweryfikowania poświadczeń.

1. W Azure Portal Wyszukaj **poświadczenia do zweryfikowania**.
2. Wybierz pozycję **poświadczenia do zweryfikowania (wersja zapoznawcza)**.
3. Wybierz pozycję **Rozpocznij**
4. Musimy skonfigurować organizację i podać nazwę organizacji, domenę i Magazyn kluczy. Przyjrzyjmy się każdej z wartości.

      - **Nazwa organizacji**: Ta nazwa polega na odwoływaniu się do firmy w ramach zweryfikowanej usługi poświadczeń. Ta wartość nie jest połączona z klientem.
      - **Domena:** Wprowadzona domena zostanie dodana do punktu końcowego usługi w twoim dokumencie. [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) i inne portfele używają tych informacji w celu sprawdzenia, czy Twoje [połączenie zostało połączone z domeną](how-to-dnsbind.md). Jeśli portfel jest w stanie zweryfikować, zostanie wyświetlony zweryfikowany symbol. Jeśli portfel nie jest w stanie zweryfikować, informuje użytkownika, że poświadczenie zostało wystawione przez organizację, której nie mogło zweryfikować. Domena wiąże się z tym, co jest związane z tym, że użytkownik może wiedzieć o Twojej firmie.
      - **Magazyn kluczy:** Podaj nazwę utworzonego wcześniej Key Vault.
 
   >[!IMPORTANT]
   > Domena nie może być przekierowaniem, w przeciwnym razie nie można połączyć się z nią i domeną. Upewnij się, że używasz https://www.domain.com formatu.
    
5. Wybierz pozycję **Zapisz i Utwórz poświadczenie**

    ![Skonfiguruj swoją tożsamość organizacyjną](media/enable-your-tenant-verifiable-credentials/save-create.png)

Gratulacje, Twoja dzierżawa jest teraz włączona na potrzeby zweryfikowania poświadczeń.

## <a name="create-your-vc-in-the-portal"></a>Tworzenie VC w portalu

Poprzedni krok pozostawia użytkownika na stronie **Tworzenie nowej poświadczenia** . 

   ![Wprowadzenie do zweryfikowania poświadczeń rozpoczyna się](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. W obszarze Nazwa poświadczenia Dodaj nazwę **MyFirstVC**. Ta nazwa jest używana w portalu do identyfikowania poświadczeń do zweryfikowania i jest uwzględniona w ramach kontraktu z poświadczeniami możliwymi do zweryfikowania.
2. W sekcji plik wyświetlania wybierz pozycję **Konfiguruj plik wyświetlany** .
3. W sekcji **konta magazynu** wybierz pozycję **woodgrovevcstorage**.
4. Z listy dostępnych kontenerów wybierz pozycję **VC-Container**.
5. Wybierz **MyFirstVC-display.js** utworzony wcześniej plik.
6. W sekcji **Tworzenie nowego poświadczenia** w **pliku reguł** wybierz pozycję **Konfiguruj plik reguł** .
7. W sekcji **konta magazynu** wybierz pozycję **woodgrovecstorage**
8. Wybierz pozycję **VC-Container**.
9. Wybierz **MyFirstVC-rules.jsna**
10. Na ekranie **Tworzenie nowego poświadczenia** wybierz pozycję **Utwórz**.

   ![Utwórz nowe poświadczenie](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>Adres URL poświadczeń

Teraz, gdy masz nowe poświadczenie, skopiuj adres URL poświadczeń.

   ![Adres URL poświadczeń problemu](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>Adres URL poświadczeń jest kombinacją reguł i plików wyświetlania. Jest to adres URL, który jest obliczany przez wystawcę przed wyświetleniem wymagań dotyczących wystawiania poświadczeń zweryfikowanych przez użytkownika.  

## <a name="update-the-sample-app"></a>Aktualizowanie przykładowej aplikacji

Teraz wprowadzamy modyfikacje kodu wystawcy aplikacji przykładowej w celu zaktualizowania go przy użyciu adresu URL poświadczeń do zweryfikowania. Pozwala to na wydawanie zweryfikowanych poświadczeń przy użyciu własnej dzierżawy.

1. Przejdź do folderu, w którym zostały umieszczone pliki przykładowej aplikacji.
2. Otwórz folder wystawców, a następnie otwórz app.js w Visual Studio Code.
3. Zaktualizuj stałą "Credential" przy użyciu nowego adresu URL poświadczeń i ustaw stałątype CredentialType na wartość "MyFirstVC" i Zapisz plik.

    ![obraz kodu programu Visual Studio pokazujący odpowiednie obszary wyróżnione](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. Otwórz wiersz polecenia i Otwórz folder wystawcy.
5. Uruchom zaktualizowaną aplikację węzła.

    ```terminal
    node app.js
    ```

6. Korzystanie z innego wiersza polecenia ngrok w celu skonfigurowania adresu URL na 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Możesz również zauważyć, że ta aplikacja lub witryna sieci Web może być ryzykowna. Komunikat jest w tej chwili oczekiwany, ponieważ nie został jeszcze połączony z Twoją domeną. Postępuj zgodnie z instrukcjami dotyczącymi [powiązań DNS](how-to-dnsbind.md) , aby je skonfigurować.

    
7. Otwórz adres URL protokołu HTTPS wygenerowany przez ngrok.

    ![Punkty końcowe przekazywania NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. Wybierz pozycję **Pobierz POświadczenie**
9. W programie Authenticator Skanuj kod QR.
10. W **tej aplikacji lub witrynie sieci Web może być ryzykowny** komunikat z ostrzeżeniem wybierz pozycję **Zaawansowane**.

  ![Początkowe ostrzeżenie](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. W przypadku ostrzeżenia dotyczącego ryzykownej witryny sieci Web wybierz pozycję **nadal (niebezpieczne)**

  ![Drugie ostrzeżenie dotyczące wystawcy](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. Na ekranie **Dodawanie poświadczeń** Zwróć uwagę na kilka rzeczy: 
    1. W górnej części ekranu można zobaczyć czerwony komunikat **niezweryfikowany**
    1. Poświadczenie jest dostosowywane na podstawie zmian wprowadzonych w pliku wyświetlanym.
    1. Opcja **Zaloguj się do konta** wskazuje **didplayground.b2clogin.com**.
    
   ![ekran dodawania poświadczeń z ostrzeżeniem](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. Wybierz pozycję **Zaloguj się do swojego konta** i Uwierzytelnij się przy użyciu poświadczeń podanych w [samouczku wprowadzenie](get-started-verifiable-credentials.md).
14. Po pomyślnym uwierzytelnieniu przycisku **Dodaj** nie jest już wyszarzony. Wybierz pozycję **Dodaj**.

  ![Dodaj ekran poświadczeń po uwierzytelnieniu](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Teraz wydano zweryfikowane poświadczenia przy użyciu naszej dzierżawy w celu wygenerowania naszego VC, przy jednoczesnym użyciu naszej dzierżawy usługi B2C na potrzeby uwierzytelniania.

  ![VC wystawiony przez usługę Azure AD i uwierzytelniany przez nasze wystąpienie usługi Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Testowanie weryfikacji VC przy użyciu przykładowej aplikacji

Teraz, gdy wydano zweryfikowane poświadczenia z naszej dzierżawy, spróbujmy ją zweryfikować przy użyciu naszej przykładowej aplikacji.

>[!IMPORTANT]
> Podczas testowania użyj tego samego adresu e-mail i hasła, które zostały użyte w artykule [wprowadzenie](get-started-verifiable-credentials.md) . Gdy dzierżawca wystawia VC, dane wejściowe pochodzą z tokenu identyfikatora wystawionego przez dzierżawę Microsoft B2C. W samouczku dwa są przełączane wystawianie tokenów do dzierżawy

1. Otwórz **Ustawienia** w bloku poświadczenia zweryfikowane w Azure Portal. Skopiuj identyfikator zdecentralizowany (DID).

   ![Skopiuj](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. Teraz otwórz plik programu weryfikatorer z przykładowych plików aplikacji. Musimy zaktualizować plik app.js w przykładowym kodzie weryfikatora i wprowadzić następujące zmiany:

    - **poświadczenie**: zmiana adresu URL poświadczeń
    - **CredentialType**: "MyFirstVC"
    - **issuerDid**: Skopiuj tę wartość z Azure Portal>zweryfikowane poświadczenia (wersja zapoznawcza) >ustawienia>zdecentralizowanego identyfikatora (DID)
    
   ![Zaktualizuj stałą issuerDid, aby pasowała do identyfikatora wystawcy](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. Zatrzymaj uruchamianie usługi wystawcy ngrok.

    ```cmd
    control-c
    ```

4. Teraz uruchom ngrok z portem weryfikatora 8082.

    ```cmd
    ngrok http 8082
    ```

5. W innym oknie terminalu przejdź do aplikacji weryfikatorer i uruchom ją podobnie jak w przypadku uruchomienia aplikacji wystawcy.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. Otwórz adres URL ngrok w przeglądarce i Zeskanuj kod QR przy użyciu uwierzytelniania na urządzeniu przenośnym.
7. Na urządzeniu przenośnym wybierz pozycję **Zezwalaj** na **nowym ekranie żądania uprawnień** .

    >[!NOTE]
    > Podpisywanie tego obwodu wirtualnego jest nadal w programie Microsoft B2Ce. Weryfikator jest nadal z przykładowej dzierżawy aplikacji firmy Microsoft. Ze względu na to, że firma Microsoft została połączona ze swoją domeną, nie widzisz ostrzeżenia, jak mamy w trakcie przepływu wystawiania. Zostanie ona zaktualizowana w następnej sekcji.
    
   ![nowe żądanie uprawnienia](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Twoje poświadczenie nie zostało pomyślnie zweryfikowane.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz przykładowy kod, który wystawił obwód wirtualny od wystawcy, można przejść do następnej sekcji, w której jest używany własny dostawca tożsamości do uwierzytelniania użytkowników próbujących uzyskać dostęp do zweryfikowanych poświadczeń i używania przez Ciebie do podpisywania żądań prezentacji.

> [!div class="nextstepaction"]
> [Samouczek — problem i weryfikacja zweryfikowanych poświadczeń przy użyciu dzierżawy](issue-verify-verifiable-credentials-your-tenant.md)


