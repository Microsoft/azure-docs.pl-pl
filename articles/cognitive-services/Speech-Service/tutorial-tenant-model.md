---
title: Tworzenie modelu dzierżawy (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Automatyczne generowanie bezpiecznego, zgodnego modelu dzierżawców (Custom Speech z danymi Microsoft 365), które korzysta z danych Microsoft 365 w celu zapewnienia optymalnego rozpoznawania mowy dla warunków specyficznych dla organizacji.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e861182ee57a0b49d3e62a858fc97dbf0890ea3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399707"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Samouczek: Tworzenie modelu dzierżawy (wersja zapoznawcza)

Model dzierżawy (Custom Speech z danymi Microsoft 365) to usługa opcjonalna dla klientów z systemem Microsoft 365 Enterprise, którzy automatycznie generują niestandardowy model rozpoznawania mowy na podstawie danych Microsoft 365 w organizacji. Model jest zoptymalizowany pod kątem warunków technicznych, żargon i nazwisk osób, a wszystko to w bezpieczny i zgodny sposób.

> [!IMPORTANT]
> Jeśli Twoja organizacja rejestruje się za pomocą usługi modelu dzierżawy, usługa mowy może uzyskać dostęp do modelu języka w organizacji. Model jest generowany na podstawie Microsoft 365 publicznych grup wiadomości e-mail i dokumentów, które mogą być widoczne dla wszystkich użytkowników w organizacji. Administrator organizacji może włączyć lub wyłączyć korzystanie z modelu języka w całej organizacji z poziomu portalu administracyjnego.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie w modelu dzierżawy za pomocą Centrum administracyjnego Microsoft 365
> * Pobierz klucz subskrypcji mowy
> * Tworzenie modelu dzierżawy
> * Wdrażanie modelu dzierżawy
> * Korzystanie z modelu dzierżawy za pomocą zestawu Speech SDK

## <a name="enroll-in-the-tenant-model-service"></a>Rejestrowanie w usłudze modelu dzierżawy

Aby można było wdrożyć model dzierżawy, należy zarejestrować się w usłudze modelu dzierżawy. Rejestracja została zakończona w centrum administracyjnym Microsoft 365 i może być wykonana tylko przez administratora.

1. Zaloguj się do [centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com).

1. W lewym okienku wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Ustawienia** z menu zagnieżdżonego, a następnie wybierz pozycję **Azure Speech Services** z okna głównego.

   ![Okienko "usługi & Dodatki"](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Zaznacz pole wyboru **Zezwalaj na model języka w całej organizacji** , a następnie wybierz pozycję **Zapisz zmiany**.

   ![Okienko usługi Azure Speech Services](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Aby wyłączyć wystąpienie modelu dzierżawy:
1. Powtórz powyższe kroki 1 i 2.
1. Wyczyść pole wyboru **Zezwalaj na model języka w całej organizacji** , a następnie wybierz pozycję **Zapisz zmiany**.

## <a name="get-a-speech-subscription-key"></a>Pobierz klucz subskrypcji mowy

Aby korzystać z modelu dzierżawy z zestawem SDK mowy, potrzebujesz zasobu mowy i skojarzonego z nim klucza subskrypcji.

1. Zaloguj się w witrynie [Azure Portal](https://aka.ms/azureportal).
1. Wybierz pozycję **Utwórz zasób**.
1. W polu **wyszukiwania** wpisz **Speech**.
1. Na liście wyników wybierz pozycję **Speech**, a następnie wybierz pozycję **Utwórz**.
1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby utworzyć zasób. Upewnij się, że:
   * **Lokalizacja** jest ustawiona na **Wschodnie** lub **zachodnie**.
   * **Warstwa cenowa** jest ustawiona na **S0**.
1. Wybierz przycisk **Utwórz**.

   Po kilku minutach zasób zostanie utworzony. Klucz subskrypcji jest dostępny w sekcji **Przegląd** dla zasobu.

## <a name="create-a-language-model"></a>Tworzenie modelu języka

Gdy administrator włączył model dzierżawy dla Twojej organizacji, możesz utworzyć model języka oparty na danych Microsoft 365.

1. Zaloguj się do programu [Speech Studio](https://speech.microsoft.com/).
1. W prawym górnym rogu wybierz pozycję **Ustawienia** (ikona koła zębatego), a następnie wybierz pozycję **Ustawienia modelu dzierżawy**.

   ![Łącze "ustawienia modelu dzierżawy"](media/tenant-language-model/tenant-language-settings.png)

   Speech Studio wyświetla komunikat informujący o tym, czy masz kwalifikacje do utworzenia modelu dzierżawy.

   > [!NOTE]
   > Klienci korporacyjni w Ameryka Północna mogą tworzyć model dzierżawy (angielski). Jeśli jesteś Skrytka klientam, kluczem klienta lub klientem programu Office 365 dla instytucji rządowych, ta funkcja jest niedostępna. Aby określić, czy jesteś klientem z kluczem Skrytka klienta lub klientem, zobacz:
   > * [Skrytka klienta](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Klucz klienta](/microsoft-365/compliance/customer-key-overview)
   > * [Pakiet Office 365 dla instytucji rządowych](https://www.microsoft.com/microsoft-365/government)

1. Wybierz pozycję **Wyraź zgodę**.

   Gdy model dzierżawy jest gotowy, otrzymasz wiadomość e-mail z potwierdzeniem z dodatkowymi instrukcjami.

## <a name="deploy-your-tenant-model"></a>Wdróż model dzierżawy

Gdy wystąpienie modelu dzierżawy jest gotowe, wdróż je, wykonując następujące czynności:

1. W wiadomości e-mail z potwierdzeniem wybierz przycisk **Wyświetl model** . Lub Zaloguj się do programu [Speech Studio](https://speech.microsoft.com/).
1. W prawym górnym rogu wybierz pozycję **Ustawienia** (ikona koła zębatego), a następnie wybierz pozycję **Ustawienia modelu dzierżawy**.

   ![Łącze "ustawienia modelu dzierżawy"](media/tenant-language-model/tenant-language-settings.png)

1. Wybierz pozycję **Deploy** (Wdróż).

   Po wdrożeniu modelu stan zmieni się na *wdrożone*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Korzystanie z modelu dzierżawy za pomocą zestawu Speech SDK

Teraz, gdy już wdrożono model, możesz go używać z zestawem Speech SDK. W tej sekcji użyto przykładowego kodu do wywołania usługi mowy przy użyciu uwierzytelniania Azure Active Directory (Azure AD).

Przyjrzyjmy się kodowi, który będzie używany do wywoływania zestawu Speech SDK w języku C#. W tym przykładzie wykonujesz rozpoznawanie mowy przy użyciu modelu dzierżawy. W tym przewodniku zakłada się, że platforma została już skonfigurowana. Jeśli potrzebujesz pomocy dotyczącej konfiguracji, zobacz [Szybki Start: Rozpoznawanie mowy, C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Skopiuj ten kod do projektu:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Następnie należy ponownie skompilować i uruchomić projekt z wiersza polecenia. Przed uruchomieniem polecenia zaktualizuj kilka parametrów, wykonując następujące czynności:

1. Zamień `<Username>` i na `<Password>` wartości dla prawidłowego użytkownika dzierżawy.
1. Zamień na `<Subscription-Key>` klucz subskrypcji dla zasobu mowy. Ta wartość jest dostępna w sekcji **Przegląd** dla zasobu mowy w [Azure Portal](https://aka.ms/azureportal).
1. Zamień `<Endpoint-Uri>` na następujący punkt końcowy. Upewnij się, że zastąpisz do regionu, w `{your region}` którym został utworzony zasób mowy. Te regiony są obsługiwane: `westus` , `westus2` i `eastus` . Informacje o regionie są dostępne w sekcji **Przegląd** Twojego zasobu mowy w [Azure Portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Uruchom następujące polecenie:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

W tym samouczku pokazano, jak za pomocą Microsoft 365 danych utworzyć niestandardowy model rozpoznawania mowy, wdrożyć go i korzystać z zestawu Speech SDK.

## <a name="next-steps"></a>Następne kroki

* [Speech Studio](https://speech.microsoft.com/)
* [Zestaw SDK rozpoznawania mowy](speech-sdk.md)
