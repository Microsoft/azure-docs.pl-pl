---
title: Używanie trybu udostępnionego urządzenia z systemem MSAL Android | Azure
description: Dowiedz się, jak przygotować urządzenie z systemem Android do działania w trybie udostępnionym i uruchomić aplikację Firstline Worker.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4bbcf73654d7f588c63a9bf81ab6a689360ec978
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91355061"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Samouczek: korzystanie z trybu udostępnionego urządzenia w aplikacji systemu Android

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="developer-guide"></a>Przewodnik dla deweloperów

Ten przewodnik zawiera wskazówki dla deweloperów dotyczące wdrażania trybu udostępnionego urządzenia w aplikacji systemu Android przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL). Zapoznaj się z [samouczkiem MSAL dla systemu Android](./tutorial-v2-android.md) , aby dowiedzieć się, jak zintegrować MSAL z aplikacją systemu Android, zalogować użytkownika, wywołać program Microsoft Graph i wylogować użytkownika.

### <a name="download-the-sample"></a>Pobieranie przykładu

Sklonuj [przykładową aplikację](https://github.com/Azure-Samples/ms-identity-android-java/) z usługi GitHub. Przykład ma możliwość pracy w [trybie jednego lub wielu kont](./single-multi-account.md).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Dodawanie zestawu SDK MSAL do lokalnego repozytorium Maven

Jeśli nie korzystasz z przykładowej aplikacji, dodaj bibliotekę MSAL jako zależność w pliku Build. Gradle, na przykład:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Skonfiguruj aplikację do korzystania z trybu udostępnionego urządzenia

Aby uzyskać więcej informacji na temat konfigurowania pliku konfiguracji, zapoznaj się z [dokumentacją konfiguracyjną](./msal-configuration.md) .

Ustaw wartość na `"shared_device_mode_supported"` `true` w pliku konfiguracji MSAL.

Nie można zaplanować obsługi trybu wielu kont. Może to być możliwe, jeśli nie używasz urządzenia udostępnionego, a użytkownik może zalogować się do aplikacji z więcej niż jednym kontem w tym samym czasie. Jeśli tak, ustaw `"account_mode"` wartość `"SINGLE"` . Gwarantuje to, że aplikacja będzie zawsze otrzymywać `ISingleAccountPublicClientApplication` i znacząco upraszcza integrację z MSAL. Wartość domyślna `"account_mode"` to `"MULTIPLE"` , dlatego ważne jest, aby zmienić tę wartość w pliku konfiguracji, jeśli używasz `"single account"` trybu.

Oto przykład auth_config.jsw pliku uwzględnionym w **app** > katalogu**głównym** > **zasobów**aplikacji > **raw** przykładowej:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Wykrywanie trybu udostępnionego urządzenia

Tryb Shared-Device umożliwia skonfigurowanie urządzeń z systemem Android, które mają być współużytkowane przez wielu pracowników, a jednocześnie zapewnia zarządzanie tożsamością urządzenia przez firmę Microsoft. Pracownicy mogą logować się na swoich urządzeniach i szybko uzyskiwać dostęp do informacji o klientach. Po ich przeniesieniu lub zadaniu będzie można wylogować się ze wszystkich aplikacji na udostępnionym urządzeniu przy użyciu jednego kliknięcia, a urządzenie zostanie natychmiast przygotowane do użycia przez następnego pracownika.

Użyj `isSharedDevice()` , aby określić, czy aplikacja jest uruchomiona na urządzeniu znajdującym się w trybie Shared-urządzenie. Twoja aplikacja może użyć tej flagi, aby określić, czy należy odpowiednio zmodyfikować środowisko użytkownika.

Oto fragment kodu, który pokazuje, jak można korzystać z programu `isSharedDevice()` .  Jest ona z `SingleAccountModeFragment` klasy w przykładowej aplikacji:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inicjowanie obiektu PublicClientApplication

W przypadku ustawienia `"account_mode":"SINGLE"` w pliku konfiguracji MSAL można bezpiecznie rzutować zwracany obiekt aplikacji jako `ISingleAccountPublicCLientApplication` .

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Wykryj tryb Single a Multiple Account

Jeśli piszesz aplikację, która będzie używana tylko dla procesów roboczych Firstline na urządzeniu udostępnionym, zalecamy zapisanie aplikacji w celu zapewnienia obsługi tylko trybu jednego konta. Obejmuje to większość aplikacji, które są zadaniami, takimi jak aplikacje do rekordów medycznych, aplikacje fakturowania i większość aplikacji biznesowych. Upraszcza to programowanie, ponieważ nie będzie trzeba domieścić wielu funkcji zestawu SDK.

Jeśli aplikacja obsługuje wiele kont, a także tryb udostępnionego urządzenia, należy wykonać sprawdzanie typu i rzutować na odpowiedni interfejs, jak pokazano poniżej.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Uzyskaj zalogowanego użytkownika i ustal, czy użytkownik zmienił się na urządzeniu

`loadAccount`Metoda pobiera konto zalogowanego użytkownika. `onAccountChanged`Metoda określa, czy zalogowany użytkownik zmienił się, a jeśli tak, wyczyść:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Globalne Logowanie użytkownika

Następujące znaki użytkownika na urządzeniu są używane przez inne aplikacje, które używają MSAL z aplikacją Authenticator:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Globalne wylogowywanie użytkownika

Następujące usunięcie konta zalogowanego i wyczyszczenie buforowanych tokenów z nie tylko aplikacji, ale również z urządzenia znajdującego się w trybie udostępnionego urządzenia:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Podręcznik administratora

Poniższe kroki opisują skonfigurowanie aplikacji w Azure Portal i przełączenie urządzenia do trybu Shared-urządzenie.

### <a name="register-your-application-in-azure-active-directory"></a>Zarejestruj swoją aplikację w Azure Active Directory

Najpierw Zarejestruj swoją aplikację w dzierżawie organizacyjnej. Następnie podaj poniższe wartości w auth_config.js, aby aplikacja działała poprawnie.

Aby uzyskać więcej informacji na ten temat, zobacz [Rejestrowanie aplikacji](./tutorial-v2-android.md#register-your-application).

> [!NOTE]
> Po zarejestrowaniu aplikacji Skorzystaj z przewodnika Szybki Start po lewej stronie, a następnie wybierz pozycję **Android**. Spowoduje to wyświetlenie strony, na której zostanie wyświetlony monit o podanie **nazwy pakietu** i **skrótu podpisu** dla aplikacji. Są one bardzo ważne, aby mieć pewność, że konfiguracja aplikacji będzie działać. Następnie otrzymasz obiekt konfiguracji, którego możesz użyć dla aplikacji, która zostanie wycięta i wklejona do auth_config.jspliku.

![Ekran rejestracji aplikacji należy ](media/tutorial-v2-shared-device-mode/register-app.png) wybrać opcję **wprowadź tę zmianę dla mnie** , a następnie podać wartości z monitem szybki start dla Azure Portal. Gdy wszystko będzie gotowe, wygenerujemy wszystkie potrzebne pliki konfiguracji.

![Ekran informacji o konfiguracji aplikacji](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Konfigurowanie dzierżawy

W celach testowych skonfiguruj następujące elementy w dzierżawie: co najmniej dwóch pracowników, jeden administrator urządzenia w chmurze i jeden administrator globalny. W Azure Portal Ustaw administratora urządzenia w chmurze, modyfikując role organizacyjne. W Azure Portal uzyskaj dostęp do ról organizacyjnych, wybierając pozycję **Azure Active Directory**  >  **role i Administratorzy**  >  **administrator urządzeń w chmurze**. Dodaj użytkowników, którzy mogą umieścić urządzenie w trybie udostępnionym.

## <a name="set-up-an-android-device-in-shared-mode"></a>Konfigurowanie urządzenia z systemem Android w trybie udostępniania

### <a name="download-the-authenticator-app"></a>Pobierz aplikację Authenticator

Pobierz aplikację Microsoft Authenticator ze sklepu Google Play. Jeśli aplikacja została już pobrana, upewnij się, że jest to Najnowsza wersja.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Ustawienia aplikacji uwierzytelniania & rejestrowanie urządzenia w chmurze

Uruchom aplikację Authenticator i przejdź do strony konta głównego. Po wyświetleniu strony **Dodawanie konta** możesz przystąpić do udostępnienia urządzenia.

![Ekran Dodawanie konta uwierzytelniającego](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Przejdź do okienka **Ustawienia** przy użyciu paska menu z prawej strony. Wybierz pozycję **Rejestracja urządzenia** w obszarze **służbowe konta szkolne &**.

 ![Ekran Dodawanie konta uwierzytelniającego](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Po kliknięciu tego przycisku zostanie wyświetlony monit z prośbą o autoryzację dostępu do kontaktów urządzeń. Jest to spowodowane integracją konta systemu Android na urządzeniu. Wybierz pozycję **Zezwalaj**.

 ![Ekran Dodawanie konta uwierzytelniającego](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

Administrator urządzenia w chmurze powinien wprowadzić swoją organizacyjną wiadomość e-mail **lub zarejestrować ją jako urządzenie udostępnione**. Następnie kliknij przycisk **zarejestruj jako urządzenie udostępnione** , a następnie wprowadź swoje poświadczenia.

![Rejestrowanie — ekran urządzenia](media/tutorial-v2-shared-device-mode/register-device.png)

![Zrzut ekranu przedstawia stronę logowania.](media/tutorial-v2-shared-device-mode/sign-in.png)

Urządzenie jest teraz w trybie udostępnionym.

![Rejestrowanie — ekran urządzenia](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Wszelkie logowania i wylogowania na urządzeniu będą globalne, co oznacza, że mają zastosowanie do wszystkich aplikacji zintegrowanych z MSAL i Microsoft Authenticator na urządzeniu. Teraz można wdrażać aplikacje na urządzeniu, które korzysta z funkcji trybu udostępnionego urządzenia.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Wyświetlanie udostępnionego urządzenia w Azure Portal

Gdy urządzenie zostanie umieszczone w trybie udostępnionym, jest ono znane organizacji i śledzone w dzierżawie organizacyjnej. Możesz wyświetlić udostępnione urządzenia, przeglądając **Typ sprzężenia** w bloku Azure Active Directory Azure Portal.

![Blok wszystkie urządzenia w Azure Portal](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Przykładowa aplikacja to prosta aplikacja, która wywoła interfejs API programu Graph organizacji. Przy pierwszym uruchomieniu zostanie wyświetlony monit o zgodę, ponieważ aplikacja jest nowa dla konta pracownika.

![Ekran informacji o konfiguracji aplikacji](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o trybie udostępniania w [trybie udostępnionego urządzenia dla urządzeń z systemem Android](msal-android-shared-devices.md)