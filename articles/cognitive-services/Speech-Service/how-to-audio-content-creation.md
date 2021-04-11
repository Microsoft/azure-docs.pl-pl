---
title: Tworzenie zawartości audio — usługa mowy
titleSuffix: Azure Cognitive Services
description: Tworzenie zawartości audio to narzędzie online, które pozwala dostosowywać i dostrajać dane wyjściowe zamiany tekstu na mowę firmy Microsoft dla aplikacji i produktów.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7e43c472c939049e5dfd4ec4df909a3178ef8e2d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553264"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Popraw syntezę przy użyciu narzędzia do tworzenia zawartości audio

[Tworzenie zawartości audio](https://aka.ms/audiocontentcreation) to łatwe w użyciu i zaawansowane narzędzie, które pozwala tworzyć wysoce naturalną zawartość audio dla różnych scenariuszy, takich jak Audiobooks, emisje wiadomości, narracje wideo i czat botów. Za pomocą tworzenia zawartości audio można precyzyjnie dostosować głosy zamiany tekstu na mowę i projektować dostosowane środowiska audio w efektywny i tani sposób.

Narzędzie jest oparte na [języku SSML (Speech syntezing Language)](speech-synthesis-markup.md). Pozwala to na dostosowanie atrybutów danych wyjściowych zamiany tekstu na mowę w czasie rzeczywistym lub syntezie partii, takich jak znaki głosowe, style głosu, szybkość mówienia, wymowa i Prosody.

Możesz mieć łatwy dostęp do ponad 150 wstępnie skompilowanych głosów w pobliżu 50 różnych języków, w tym do neuronowychych głosów TTS i niestandardowego głosu, jeśli został on skompilowany. 

Zobacz [Samouczek wideo](https://www.youtube.com/watch?v=O1wIJ7mts_w) dotyczący tworzenia zawartości audio.

## <a name="how-to-get-started"></a>Jak zacząć?

Tworzenie zawartości audio to bezpłatne narzędzie, ale płacisz za usługę Azure Speech. Aby móc korzystać z narzędzia, musisz zalogować się przy użyciu konta platformy Azure i utworzyć zasób mowy. Dla każdego konta platformy Azure masz miesięczne bezpłatne przydziały mowy, które obejmują 500 000 znaków dla głosów neuronowych TTS (miesięcznie), 5 000 000 znaków dla standardowych i niestandardowych głosów (miesięcznie) oraz 1 niestandardową usługę hostingu punktu końcowego (miesięcznie). Miesięczna przydzielona kwota jest zwykle wystarczająca dla niewielkiego zespołu zawartości dla około 3-5 osób. Poniżej przedstawiono procedurę tworzenia konta platformy Azure i uzyskiwania zasobu mowy. 

### <a name="step-1---create-an-azure-account"></a>Krok 1. Tworzenie konta platformy Azure

Aby móc korzystać z tworzenia zawartości audio, musisz mieć [konto Microsoft](https://account.microsoft.com/account) i [konto platformy Azure](https://azure.microsoft.com/free/ai/). Postępuj zgodnie z tymi instrukcjami, aby [skonfigurować konto](./overview.md#try-the-speech-service-for-free). 

[Azure Portal](https://portal.azure.com/) to centralne miejsce, w którym można zarządzać kontem platformy Azure. Można utworzyć zasób mowy, zarządzać dostępem do produktu oraz monitorować wszystko z prostych aplikacji sieci Web w celu złożonych wdrożeń w chmurze. 

### <a name="step-2---create-a-speech-resource"></a>Krok 2. Tworzenie zasobu mowy

Po zarejestrowaniu się do konta platformy Azure musisz utworzyć zasób mowy na koncie platformy Azure, aby uzyskać dostęp do usług mowy. Zapoznaj się z instrukcjami dotyczącymi [sposobu tworzenia zasobu mowy](./overview.md#create-the-azure-resource). 

Wdrożenie nowego zasobu mowy trwa kilka minut. Po zakończeniu wdrażania możesz rozpocząć podróż tworzenia zawartości audio. 

 >[!NOTE]
   > Jeśli planujesz używać głosów neuronowych, upewnij się, że tworzysz zasób w [regionie, który obsługuje głosy neuronowych](regions.md#neural-and-standard-voices).
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>Krok 3. Logowanie do tworzenia zawartości audio przy użyciu konta platformy Azure i zasobu mowy

1. Po uzyskaniu konta platformy Azure i zasobu mowy możesz zalogować się do [tworzenia zawartości audio](https://aka.ms/audiocontentcreation) , klikając pozycję **Rozpocznij pracę**.
2. Zostanie wyświetlona strona **zasobów mowy** . Wybierz zasób mowy, nad którym chcesz korzystać. Kliknij pozycję **Przejdź do Studio** , aby rozpocząć tworzenie dźwięku. Nowy zasób mowy można także utworzyć tutaj, klikając pozycję **Utwórz nowy**. Po następnym zalogowaniu się do narzędzia tworzenia zawartości audio zostanie utworzone połączenie bezpośrednio z plikami roboczymi audio w ramach bieżącego zasobu mowy. 
3. Możesz w dowolnym momencie zmodyfikować zasób mowy przy użyciu opcji **Ustawienia** , która znajduje się w górnym obszarze nawigacji.

## <a name="how-to-use-the-tool"></a>Jak używać narzędzia?

Na tym diagramie przedstawiono kroki, które należy wykonać, aby dostosować wyniki zamiany tekstu na mowę. Skorzystaj z poniższych linków, aby dowiedzieć się więcej o każdym z kroków.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Diagram kroków, które należy wykonać, aby dostosować wyniki zamiany tekstu na mowę":::


1. Wybierz zasób mowy, nad którym chcesz korzystać.
2. [Utwórz plik dostrajania audio](#create-an-audio-tuning-file) przy użyciu zwykłego tekstu lub skryptów SSML. Wpisz lub przekaż swoją zawartość do tworzenia zawartości audio.
3. Wybierz głos i język zawartości skryptu. Tworzenie zawartości audio obejmuje wszystkie [głosy zamiany tekstu na mowę firmy Microsoft](language-support.md#text-to-speech). Możesz użyć standardowego, neuronowych lub własnego niestandardowego głosu.
   >[!NOTE]
   > Dostęp warunkowy jest dostępny dla niestandardowych głosów neuronowych, które umożliwiają tworzenie głosów o wysokiej rozdzielczości, podobnie jak w przypadku mowy dźwięku naturalnego. Aby uzyskać więcej informacji, zobacz [kontroli Process](./text-to-speech.md).

4. Kliknij ikonę **odtwarzania** (trójkąt), aby wyświetlić podgląd domyślnego wyniku syntezy. Następnie Popraw dane wyjściowe przez dostosowanie wymowy, przerwania, skoku, stawki, intonation, stylu głosu i innych. Aby zapoznać się z pełną listą opcji, zobacz Language [syntezowania mowy](speech-synthesis-markup.md). Oto [film wideo](https://www.youtube.com/watch?v=O1wIJ7mts_w) , który pokazuje, jak dostosować dane wyjściowe mowy przy tworzeniu zawartości audio. 
5. Zapisz i [wyeksportuj dostosowany dźwięk](#export-tuned-audio). Po zapisaniu ścieżki dostrajania w systemie można kontynuować pracę i iterację danych wyjściowych. Gdy dane wyjściowe są zadowalające, można utworzyć zadanie tworzenia dźwięku za pomocą funkcji eksportowania. Można obserwować stan zadania eksportu i pobrać dane wyjściowe do użycia z aplikacjami i produktami.

## <a name="create-an-audio-tuning-file"></a>Utwórz plik strojenia audio

Istnieją dwa sposoby na uzyskanie zawartości do narzędzia tworzenia zawartości audio.

**Opcja 1:**

1. Kliknij ikonę **nowy plik** w prawym górnym rogu, aby utworzyć nowy plik strojenia audio.
2. Wpisz lub wklej zawartość do okna Edycja. Znaki dla każdego pliku są do 20 000. Jeśli Twój skrypt zawiera więcej niż 20 000 znaków, możesz użyć opcji 2, aby automatycznie podzielić zawartość na wiele plików. 
3. Nie zapomnij zapisać.

**Opcja 2:**

1. Kliknij przycisk **Przekaż** , aby zaimportować jeden lub więcej plików tekstowych. Obsługiwane są zarówno zwykły tekst, jak i SSML. Jeśli plik skryptu ma więcej niż 20 000 znaków, Podziel plik przez akapity, znak lub wyrażenia regularne. 
3. Podczas przekazywania plików tekstowych upewnij się, że plik spełnia te wymagania.

   | Właściwość | Wartość/uwagi |
   |----------|---------------|
   | Format pliku | Zwykły tekst (. txt)<br/> Tekst SSML (. txt)<br/> Pliki zip nie są obsługiwane |
   | Format kodowania | UTF-8 |
   | Nazwa pliku | Każdy plik musi mieć unikatową nazwę. Duplikaty nie są obsługiwane. |
   | Długość tekstu | Ograniczenie znaków w pliku tekstowym to 20 000. Jeśli pliki przekroczą ograniczenie, Podziel pliki na instrukcje w narzędziu. |
   | Ograniczenia SSML | Każdy plik SSML może zawierać tylko jedną część SSML. |

**Przykład zwykłego tekstu**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**Przykład tekstu SSML**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Eksportuj dostrojony dźwięk

Po przejrzeniu danych wyjściowych audio i spełnieniu ich dostrajania można wyeksportować dźwięk.

1. Kliknij przycisk **Eksportuj** , aby utworzyć zadanie tworzenia dźwięku. **Eksportowanie do biblioteki audio** jest zalecane, ponieważ obsługuje długie dane wyjściowe audio i pełne środowisko wyjścia audio. Możesz również bezpośrednio pobrać dźwięk na dysk lokalny, ale dostępne są tylko pierwsze 10 minut.
2. Wybierz format danych wyjściowych dla dostrajania audio. Poniżej znajduje się lista obsługiwanych formatów i częstotliwości próbkowania.
3. Stan zadania można wyświetlić na karcie **Eksportuj zadanie** . Jeśli zadanie nie powiedzie się, zobacz stronę ze szczegółowymi informacjami, aby uzyskać pełny raport.
4. Po zakończeniu zadania dźwięk jest dostępny do pobrania na karcie **biblioteka audio** .
5. Kliknij pozycję **Pobierz**. Teraz możesz przystąpić do używania niestandardowego, dostrojonego dźwięku w aplikacjach lub produktach.

**Obsługiwane formaty audio**

| Format | szybkość próbkowania 16 kHz | częstotliwość próbkowania 24 kHz |
|--------|--------------------|--------------------|
| pliki | RIFF-16khz-16bit-mono-PCM | RIFF-24khz-16bit-mono-PCM |
| formatu | audio-16khz-128kbitrate-mono-MP3 | audio-24khz-160kbitrate-mono-MP3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Jak dodać/usunąć użytkowników tworzenia zawartości audio?

Jeśli więcej niż jeden użytkownik chce użyć tworzenia zawartości audio, można przyznać użytkownikom dostęp do subskrypcji platformy Azure i zasobu mowy. Jeśli dodasz użytkownika do subskrypcji platformy Azure, użytkownik będzie mógł uzyskać dostęp do wszystkich zasobów w ramach subskrypcji platformy Azure. Ale jeśli dodasz tylko użytkownika do zasobu mowy, użytkownik będzie miał dostęp tylko do zasobu mowy i nie może uzyskać dostępu do innych zasobów w ramach tej subskrypcji platformy Azure. Użytkownik mający dostęp do zasobu mowy może korzystać z tworzenia zawartości audio.

### <a name="add-users-to-a-speech-resource"></a>Dodawanie użytkowników do zasobu mowy

Wykonaj następujące kroki, aby dodać użytkownika do zasobu mowy, aby mogli używać tworzenia zawartości audio.

1. Wyszukaj **usługi poznawcze** w [Azure Portal](https://portal.azure.com/)wybierz zasób mowy, do którego chcesz dodać użytkowników.
2. Kliknij pozycję **Kontrola dostępu (IAM)** . Kliknij kartę **Przypisania ról**, aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Karta Przypisanie roli":::
1. Kliknij pozycję **Dodaj** > **Dodaj przypisanie roli**, aby otworzyć okienko Dodawanie przypisania roli. Z listy rozwijanej rola wybierz rolę **użytkownika usługi poznawczej** . Jeśli chcesz nadać użytkownikowi własność tego zasobu mowy, możesz wybrać rolę **właściciela** .
1. Wybierz użytkownika z listy. Jeśli nie widzisz użytkownika na liście, możesz wpisać w polu Wybierz, aby wyszukać nazwy wyświetlane i adresy e-mail w katalogu. Jeśli użytkownik nie znajduje się w tym katalogu, można wprowadzić [konto Microsoft](https://account.microsoft.com/account) użytkownika (który jest traktowany jako zaufany przez usługę Azure Active Directory).
1. Kliknij przycisk **Zapisz**, aby przypisać rolę. Po kilku chwilach użytkownik ma przypisaną rolę użytkownika usługi poznawczej w zakresie zasobów mowy.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Okno dialogowe Dodawanie roli":::

1. Dodawani użytkownicy otrzymają wiadomość e-mail z zaproszeniem. Po kliknięciu przycisku **Akceptuj**  >  **zaakceptowanie zaproszenia do dołączenia do platformy Azure** można użyć [tworzenia zawartości audio](https://aka.ms/audiocontentcreation).

Użytkownicy, którzy znajdują się w tym samym zasobie mowy, zobaczą swoją służbową zawartość audio. Jeśli chcesz, aby każdy indywidualny użytkownik miał unikatowy i prywatny obszar roboczy w tworzeniu zawartości audio, [Utwórz nowy zasób mowy](#step-2---create-a-speech-resource) dla każdego użytkownika i nadaj każdemu użytkownikowi unikatowy dostęp do zasobu mowy. 

### <a name="remove-users-from-a-speech-resource"></a>Usuwanie użytkowników z zasobu mowy
1. Wyszukaj **usługi poznawcze** w Azure Portal wybierz zasób mowy, z którego chcesz usunąć użytkowników.
2. Kliknij pozycję **Kontrola dostępu (IAM)**. Kliknij kartę **przypisania ról** , aby wyświetlić wszystkie przypisania ról dla tego zasobu mowy.
3. Wybierz użytkowników, których chcesz usunąć, a następnie kliknij przycisk **Usuń**  >  **OK**.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Przycisk Usuń":::

### <a name="enable-users-to-grant-access"></a>Umożliwienie użytkownikom udzielenia dostępu
Jeśli chcesz, aby jeden z użytkowników miał dostęp do innych użytkowników, musisz nadać użytkownikowi rolę właściciela dla zasobu mowy i ustawić użytkownika jako czytnika katalogów platformy Azure. 
1. Dodaj użytkownika jako właściciela zasobu mowy. Zobacz [, jak dodać użytkowników do zasobu mowy](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Pole właściciela roli":::
1. Wybierz menu zwinięte w lewym górnym rogu. Kliknij przycisk **Azure Active Directory**, a następnie kliknij pozycję **Użytkownicy**.
1. Przeszukaj konto Microsoft użytkownika i przejdź do strony szczegółów użytkownika. Kliknij pozycję **przypisane role**.
1. Kliknij pozycję **Dodaj przydziały**  ->  **katalogów**.

## <a name="see-also"></a>Zobacz też

* [Długi interfejs API audio](./long-audio-api.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
