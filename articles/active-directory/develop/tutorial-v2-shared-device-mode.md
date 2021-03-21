---
title: 'Samouczek: Używanie trybu udostępnionego urządzenia z biblioteką uwierzytelniania firmy Microsoft (MSAL) dla systemu Android | Azure'
titleSuffix: Microsoft identity platform
description: W ramach tego samouczka nauczysz się, jak przygotować urządzenie z systemem Android do działania w trybie współdzielonym i uruchomić aplikację z pierwszym wierszem.
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
ms.openlocfilehash: 6a173ed4dae9237d8aae991c943817ed70246eea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649059"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Samouczek: korzystanie z trybu udostępnionego urządzenia w aplikacji systemu Android

W tym samouczku Administratorzy dzierżawy systemu Android i Azure Active Directory (Azure AD) mogą uzyskać informacje o kodzie, aplikacji uwierzytelniania oraz ustawieniach dzierżawy wymaganych do włączenia trybu udostępnionego urządzenia dla aplikacji systemu Android.

W tym samouczku:

> [!div class="checklist"]
> * Pobierz przykład kodu
> * Włączanie i wykrywanie trybu udostępnionego urządzenia
> * Wykryj tryb pojedynczego lub wielu kont
> * Wykrywaj przełącznik użytkownika i włączaj logowanie globalne i wylogowanie
> * Skonfiguruj dzierżawcę i zarejestruj aplikację w Azure Portal
> * Konfigurowanie urządzenia z systemem Android w trybie Shared-Device
> * Uruchamianie przykładowej aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="developer-guide"></a>Przewodnik dla deweloperów

W tej części samouczka przedstawiono wskazówki dla deweloperów dotyczące implementowania trybu udostępnionego urządzenia w aplikacji systemu Android przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL). Zapoznaj się z [samouczkiem MSAL dla systemu Android](./tutorial-v2-android.md) , aby dowiedzieć się, jak zintegrować MSAL z aplikacją systemu Android, zalogować użytkownika, wywołać program Microsoft Graph i wylogować użytkownika.

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

Oto przykład auth_config.jsw pliku uwzględnionym w  > katalogu **głównym** > **zasobów** aplikacji >  przykładowej:

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
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ? "Shared" : "Non-Shared");
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

Jeśli piszesz aplikację, która będzie używana tylko przez pracowników w pierwszej kolejności na udostępnionym urządzeniu, zalecamy zapisanie aplikacji w celu zapewnienia obsługi tylko trybu jednego konta. Obejmuje to większość aplikacji, które są zadaniami, takimi jak aplikacje do rekordów medycznych, aplikacje fakturowania i większość aplikacji biznesowych. Upraszcza to programowanie, ponieważ nie będzie trzeba domieścić wielu funkcji zestawu SDK.

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

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-app.png" alt-text="Konfigurowanie strony aplikacji systemu Android w Azure Portal Szybki Start":::

Należy zaznaczyć pole wyboru **wprowadź tę zmianę dla mnie** , a następnie podać wartości, z których będzie pytał przewodnik szybkiego startu w Azure Portal. Gdy wszystko będzie gotowe, wygenerujemy wszystkie potrzebne pliki konfiguracji.

:::image type="content" source="media/tutorial-v2-shared-device-mode/config-info.png" alt-text="Konfigurowanie strony projektu w Azure Portal Szybki Start":::

## <a name="set-up-a-tenant"></a>Konfigurowanie dzierżawy

W celach testowych skonfiguruj następujące elementy w dzierżawie: co najmniej dwóch pracowników, jeden administrator urządzenia w chmurze i jeden administrator globalny. W Azure Portal Ustaw administratora urządzenia w chmurze, modyfikując role organizacyjne. W Azure Portal uzyskaj dostęp do ról organizacyjnych, wybierając pozycję **Azure Active Directory**  >  **role i Administratorzy**  >  **administrator urządzeń w chmurze**. Dodaj użytkowników, którzy mogą umieścić urządzenie w trybie udostępnionym.

## <a name="set-up-an-android-device-in-shared-mode"></a>Konfigurowanie urządzenia z systemem Android w trybie udostępniania

### <a name="download-the-authenticator-app"></a>Pobierz aplikację Authenticator

Pobierz aplikację Microsoft Authenticator ze sklepu Google Play. Jeśli aplikacja została już pobrana, upewnij się, że jest to Najnowsza wersja.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Ustawienia aplikacji uwierzytelniania & rejestrowanie urządzenia w chmurze

Uruchom aplikację Authenticator i przejdź do strony konta głównego. Po wyświetleniu strony **Dodawanie konta** możesz przystąpić do udostępnienia urządzenia.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-add-account.png" alt-text="Ekran Dodawanie konta uwierzytelniającego":::

Przejdź do okienka **Ustawienia** przy użyciu paska menu z prawej strony. Wybierz pozycję **Rejestracja urządzenia** w obszarze **służbowe konta szkolne &**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-settings.png" alt-text="Ekran ustawień uwierzytelniania":::

Po kliknięciu tego przycisku zostanie wyświetlony monit z prośbą o autoryzację dostępu do kontaktów urządzeń. Jest to spowodowane integracją konta systemu Android na urządzeniu. Wybierz pozycję **Zezwalaj**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png" alt-text="Ekran potwierdzania dostępu przez wystawcę uwierzytelnienia":::

Administrator urządzenia w chmurze powinien wprowadzić swoją organizacyjną wiadomość e-mail **lub zarejestrować ją jako urządzenie udostępnione**. Następnie kliknij przycisk **zarejestruj jako urządzenie udostępnione** , a następnie wprowadź swoje poświadczenia.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-device.png" alt-text="Ekran rejestracji urządzeń w aplikacji":::

:::image type="content" source="media/tutorial-v2-shared-device-mode/sign-in.png" alt-text="Zrzut ekranu aplikacji przedstawiający stronę logowania firmy Microsoft":::

Urządzenie jest teraz w trybie udostępnionym.

:::image type="content" source="media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png" alt-text="Ekran aplikacji z włączonym trybem udostępnionego urządzenia":::

 Wszelkie logowania i wylogowania na urządzeniu będą globalne, co oznacza, że mają zastosowanie do wszystkich aplikacji zintegrowanych z MSAL i Microsoft Authenticator na urządzeniu. Teraz można wdrażać aplikacje na urządzeniu, które korzysta z funkcji trybu udostępnionego urządzenia.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Wyświetlanie udostępnionego urządzenia w Azure Portal

Gdy urządzenie zostanie umieszczone w trybie udostępnionym, jest ono znane organizacji i śledzone w dzierżawie organizacyjnej. Możesz wyświetlić udostępnione urządzenia, przeglądając **Typ sprzężenia** w bloku Azure Active Directory Azure Portal.

:::image type="content" source="media/tutorial-v2-shared-device-mode/registered-device-screen.png" alt-text="Okienko wszystkie urządzenia pokazane w Azure Portal":::

## <a name="running-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Przykładowa aplikacja to prosta aplikacja, która wywoła interfejs API programu Graph organizacji. Przy pierwszym uruchomieniu zostanie wyświetlony monit o zgodę, ponieważ aplikacja jest nowa dla konta pracownika.

:::image type="content" source="media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png" alt-text="Ekran informacji o konfiguracji aplikacji":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o pracy z biblioteką uwierzytelniania firmy Microsoft i trybem udostępnionego urządzenia na urządzeniach z systemem Android:

> [!div class="nextstepaction"]
> [Tryb udostępnionego urządzenia dla urządzeń z systemem Android](msal-android-shared-devices.md)
