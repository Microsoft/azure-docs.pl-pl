---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: ddematheu2
manager: chpalm
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: c6e8be5462e0caffec7a1c88dae54f3f818ec323
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498805"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-android-ios.md)]


Grupa usługi Azure Communication Services **wywołująca przykład Hero dla systemu Android** pokazuje, jak usługi komunikacyjne wywołujące Android SDK mogą być używane do tworzenia środowiska wywołującego grupę, która zawiera głos i wideo. W tym przykładowym samouczku szybki start dowiesz się, jak skonfigurować i uruchomić przykład. Przegląd przykładu jest dostępny dla kontekstu.

## <a name="download-code"></a>Pobieranie kodu

Znajdź projekt dla tego przykładu w witrynie [GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero). Wersja przykładu z [zespołem międzyoperacyjności](../../concepts/teams-interop.md) można znaleźć w oddzielnym [rozgałęzieniu](https://github.com/Azure-Samples/communication-services-android-calling-hero/tree/feature/teams_interop).

## <a name="overview"></a>Omówienie

Przykładem jest Natywna aplikacja dla systemu Android, która korzysta z zestawów SDK systemu Android dla usług Azure Communication Services w celu utworzenia interfejsu wywołującego, który umożliwia nawiązywanie połączeń głosowych i wideo. Aplikacja używa składnika po stronie serwera, aby udostępnić tokeny dostępu, które są następnie używane do inicjowania zestawu SDK usług Azure Communication Services. Aby skonfigurować ten składnik po stronie serwera, możesz korzystać z [usługi zaufanej za pomocą](../../tutorials/trusted-service-tutorial.md) samouczka Azure Functions.

Oto jak wygląda przykład:

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="Zrzut ekranu przedstawiający stronę docelową przykładowej aplikacji.":::

Po naciśnięciu przycisku "Rozpocznij nowe wywołanie" aplikacja dla systemu Android utworzy nowe wywołanie i przyłączy je. Aplikacja umożliwia również dołączenie istniejącego wywołania usług Azure Communications Services przez określenie identyfikatora istniejącego wywołania.

Po dołączeniu wywołania zostanie wyświetlony monit o przyznanie aplikacji uprawnienia dostępu do aparatu i mikrofonu. Zostanie również wyświetlony monit o podanie nazwy wyświetlanej.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="Zrzut ekranu przedstawiający ekran przed wywołaniem przykładowej aplikacji.":::

Po skonfigurowaniu wyświetlanej nazwy i urządzeń można przyłączyć się do połączenia. Zobaczysz główną kanwę wywołań, w której działa podstawowe środowisko wywoływania.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="Zrzut ekranu przedstawiający ekran główny przykładowej aplikacji.":::

Składniki głównego ekranu wywołującego:

- **Galeria multimediów**: Główny etap pokazujący uczestników. Jeśli Uczestnik ma włączony aparat fotograficzny, w tym miejscu są wyświetlane ich kanały wideo. Każdy uczestnik ma pojedynczy kafelek, który pokazuje ich nazwę wyświetlaną i strumień wideo (gdy istnieje). Galeria obsługuje wielu uczestników i jest aktualizowana, gdy uczestnicy są dodawani lub usunięci do wywołania.
- **Pasek akcji**: to miejsce, w którym znajdują się podstawowe kontrolki wywołań. Te kontrolki umożliwiają włączenie/wyłączenie filmu wideo i mikrofonu, udostępnienie ekranu i pozostawienie wywołania.

Poniżej znajdziesz więcej informacji na temat wymagań wstępnych i kroków związanych z konfigurowaniem przykładu.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) uruchomiony na komputerze
- Zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../../quickstarts/create-communication-resource.md).
- Funkcja platformy Azure z uruchomionym [punktem końcowym uwierzytelniania](../../tutorials/trusted-service-tutorial.md) , aby pobrać tokeny dostępu.

## <a name="running-sample-locally"></a>Uruchamianie przykładu lokalnie

Przykład wywołania grupy można uruchomić lokalnie przy użyciu Android Studio. Deweloperzy mogą wykorzystać swoje urządzenia fizyczne lub emulator do testowania aplikacji.

### <a name="before-running-the-sample-for-the-first-time"></a>Przed uruchomieniem przykładu po raz pierwszy

1. Otwórz Android Studio i wybierz pozycję `Open an Existing Project`
2. Otwórz `AzureCalling` folder w pobranej wersji dla przykładu.
3. Rozwiń węzeł aplikacje/zasoby do zaktualizowania `appSettings.properties` . Ustaw wartość klucza `communicationTokenFetchUrl` jako adres URL dla punktu końcowego uwierzytelniania skonfigurowanego jako warunek wstępny.

### <a name="run-sample"></a>Uruchom przykład

Kompiluj i uruchamiaj przykład w Android Studio.

## <a name="optional-securing-an-authentication-endpoint"></a>Obowiązkowe Zabezpieczanie punktu końcowego uwierzytelniania

W celach demonstracyjnych ten przykład używa publicznie dostępnego punktu końcowego, aby pobrać token usług Azure Communications Services. W przypadku scenariuszy produkcyjnych zalecamy użycie własnego bezpiecznego punktu końcowego do aprowizacji własnych tokenów.

W przypadku dodatkowej konfiguracji ten przykład obsługuje łączenie się z chronionym punktem końcowym usługi **Azure Active Directory** (Azure AD), aby umożliwić aplikacji pobranie tokenu usługi Azure Communications Services. Zobacz poniższe kroki:

1. Włącz uwierzytelnianie Azure Active Directory w aplikacji.  
   - [Zarejestruj aplikację w obszarze Azure Active Directory (przy użyciu ustawień platformy systemu Android)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Skonfiguruj App Service lub aplikację Azure Functions do korzystania z logowania za pomocą usługi Azure AD](../../../app-service/configure-authentication-provider-aad.md)

2. Przejdź do strony przeglądu zarejestrowanej aplikacji w obszarze Azure Active Directory rejestracje aplikacji. Zanotuj `Package name` , `Signature hash` , `MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Azure Active Directory konfigurację Azure Portal.":::

3. Edytuj `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` i ustaw, `isAADAuthEnabled` aby włączyć Azure Active Directory
4. Edytuj `AndroidManifest.xml` i ustaw `android:path` wartość skrótu sygnatury magazynu kluczy. Obowiązkowe. Bieżąca wartość używa skrótu z powiązanego debugowania. Magazyn kluczy. Jeśli jest używany inny magazyn kluczy, należy go zaktualizować.
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Skopiuj konfigurację MSAL systemu Android z Azure Portal i wklej do programu `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` . Dołącz "account_mode": "SINGLE"
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. Edytuj `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` i ustaw wartość klucza jako `communicationTokenFetchUrl` adres URL dla punktu końcowego bezpiecznego uwierzytelniania.
7. Edytuj `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` i ustaw wartość klucza `aadScopes` z `Azure Active Directory` `Expose an API` zakresów

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

>[!div class="nextstepaction"]
>[Pobierz przykład z witryny GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero)

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z [użyciem zestawu SDK wywołującego](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Dowiedz się więcej o [sposobie wywoływania programu](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Materiały uzupełniające

- [Azure Communication GitHub](https://github.com/Azure/communication) — Znajdź więcej przykładów i informacji na oficjalnej stronie GitHub
- [Przykłady](./../overview.md) — więcej przykładów i przykładów znajdziesz na naszej stronie Przegląd przykładów.
- [Funkcje wywoływania komunikacji na platformie Azure](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) — aby dowiedzieć się więcej o WYWOŁYWANIU zestawu SDK wywołania zestawu Android SDK —[Azure Communication dla systemu Android](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)
