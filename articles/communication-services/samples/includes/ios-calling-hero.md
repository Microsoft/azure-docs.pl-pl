---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 7d39decaa6376c614e48b65ad2fc1b3043aa0a3c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101682443"
---
[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Grupa usługi Azure Communication Services **wywołująca przykład Hero dla systemu iOS** pokazuje, w jaki sposób usługi komunikacyjne wywołujące bibliotekę klienta systemu iOS mogą być używane do tworzenia środowiska wywołującego grupę, które zawiera głos i wideo. W tym przykładowym samouczku szybki start dowiesz się, jak skonfigurować i uruchomić przykład. Przegląd przykładu jest dostępny dla kontekstu.

## <a name="overview"></a>Omówienie

Przykładem jest Natywna aplikacja dla systemu iOS, która korzysta z bibliotek klienckich systemu iOS w usłudze Azure Communications Services do tworzenia interfejsu wywołującego, który umożliwia nawiązywanie połączeń głosowych i wideo. Aplikacja używa składnika serwerowego do udostępniania tokenów dostępu, które są następnie używane do inicjowania biblioteki klienta usług Azure Communications Services. Aby skonfigurować ten składnik po stronie serwera, możesz korzystać z [usługi zaufanej za pomocą](../../tutorials/trusted-service-tutorial.md) samouczka Azure Functions.

Oto jak wygląda przykład:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="Zrzut ekranu przedstawiający stronę docelową przykładowej aplikacji.":::

Po naciśnięciu przycisku "Rozpocznij nowe wywołanie" aplikacja systemu iOS utworzy nowe wywołanie i przyłączy je. Aplikacja umożliwia również dołączenie istniejącego wywołania usług Azure Communications Services przez określenie identyfikatora istniejącego wywołania.

Po dołączeniu wywołania zostanie wyświetlony monit o przyznanie aplikacji uprawnienia dostępu do aparatu i mikrofonu. Zostanie również wyświetlony monit o podanie nazwy wyświetlanej.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="Zrzut ekranu przedstawiający ekran przed wywołaniem przykładowej aplikacji.":::

Po skonfigurowaniu wyświetlanej nazwy i urządzeń można przyłączyć się do połączenia. Zobaczysz główną kanwę wywołań, w której działa podstawowe środowisko wywoływania.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="Zrzut ekranu przedstawiający ekran główny przykładowej aplikacji.":::

Składniki głównego ekranu wywołującego:

- **Galeria multimediów**: Główny etap pokazujący uczestników. Jeśli Uczestnik ma włączony aparat fotograficzny, w tym miejscu są wyświetlane ich kanały wideo. Każdy uczestnik ma pojedynczy kafelek, który pokazuje ich nazwę wyświetlaną i strumień wideo (gdy istnieje). Galeria obsługuje wielu uczestników i jest aktualizowana, gdy uczestnicy są dodawani lub usunięci do wywołania.
- **Pasek akcji**: to miejsce, w którym znajdują się podstawowe kontrolki wywołań. Te kontrolki umożliwiają włączenie/wyłączenie filmu wideo i mikrofonu, udostępnienie ekranu i pozostawienie wywołania.

Poniżej znajdziesz więcej informacji na temat wymagań wstępnych i kroków związanych z konfigurowaniem przykładu.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Na komputerze Mac z systemem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), wraz z prawidłowym certyfikatem dewelopera zainstalowanym w łańcuchu kluczy.
- Zasób usług Azure Communications Services. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu komunikacyjnego platformy Azure](../../quickstarts/create-communication-resource.md).
- Funkcja platformy Azure, która uruchamia [zaufaną logikę usługi](../../tutorials/trusted-service-tutorial.md) , aby pobrać tokeny dostępu.

## <a name="running-sample-locally"></a>Uruchamianie przykładu lokalnie

Przykład wywołania grupy można uruchomić lokalnie przy użyciu XCode. Deweloperzy mogą wykorzystać swoje urządzenia fizyczne lub emulator do testowania aplikacji.

### <a name="before-running-the-sample-for-the-first-time"></a>Przed uruchomieniem przykładu po raz pierwszy

1. Zainstaluj zależności, uruchamiając program `pod install` .
2. Otwórz `ACSCall.xcworkspace` w Xcode.
3. Aktualizacja `AppSettings.plist` . Ustaw wartość `acsTokenFetchUrl` klucza jako adres URL punktu końcowego uwierzytelniania.

### <a name="run-sample"></a>Uruchom przykład

Kompiluj i uruchamiaj przykład w XCode.

## <a name="optional-securing-an-authentication-endpoint"></a>Obowiązkowe Zabezpieczanie punktu końcowego uwierzytelniania

W celach demonstracyjnych ten przykład używa publicznie dostępnego punktu końcowego, aby pobrać token usług Azure Communications Services. W przypadku scenariuszy produkcyjnych zalecamy użycie własnego bezpiecznego punktu końcowego do aprowizacji własnych tokenów.

W przypadku dodatkowej konfiguracji ten przykład obsługuje łączenie się z chronionym punktem końcowym usługi **Azure Active Directory** (Azure AD), aby umożliwić aplikacji pobranie tokenu usługi Azure Communications Services. Zobacz poniższe kroki:

1. Włącz uwierzytelnianie Azure Active Directory w aplikacji.  
   - [Zarejestruj aplikację w obszarze Azure Active Directory (przy użyciu ustawień platformy iOS/macOS)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [Skonfiguruj App Service lub aplikację Azure Functions do korzystania z logowania za pomocą usługi Azure AD](../../../app-service/configure-authentication-provider-aad.md)
2. Przejdź do strony przeglądu zarejestrowanej aplikacji w obszarze Azure Active Directory rejestracje aplikacji. Zanotuj `Application (client) ID` , `Directory (tenant) ID` , `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure Active Directory konfigurację Azure Portal.":::

3. Otwórz `AppSettings.plist` w Xcode, Dodaj następujące wartości klucza:
   - `acsTokenFetchUrl`: Adres URL żądania tokenu usług Azure Communications Services 
   - `isAADAuthEnabled`: Wartość logiczna określająca, czy wymagane jest uwierzytelnianie tokenu usługi Azure Communications Services
   - `aadClientId`: Identyfikator aplikacji (klienta)
   - `aadTenantId`: Identyfikator Twojego katalogu (dzierżawy)
   - `aadRedirectURI`: Identyfikator URI przekierowania powinien mieć następujący format: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: Tablica zakresów uprawnień żądana przez użytkowników na potrzeby autoryzacji. Dodaj `<Application ID URI>/user_impersonation` do tablicy, aby udzielić dostępu do punktu końcowego uwierzytelniania

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z [pomocą biblioteki klienta wywołującego](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Dowiedz się więcej o [sposobie wywoływania programu](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Materiały uzupełniające

- [Azure Communication GitHub](https://github.com/Azure/communication) — Znajdź więcej przykładów i informacji na oficjalnej stronie GitHub