---
title: Tworzenie niestandardowej usługi mowy głosowej
titleSuffix: Azure Cognitive Services
description: Gdy wszystko będzie gotowe do przekazania danych, przejdź do niestandardowego portalu głosowego. Utwórz lub wybierz niestandardowy projekt głosowy. Projekt musi udostępniać odpowiednie właściwości języka/ustawień regionalnych oraz płci jako dane, które mają być używane dla szkolenia głosowego.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 262a96c0c316987d0245ed29836f6a013c4339d1
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573160"
---
# <a name="create-a-custom-voice"></a>Tworzenie niestandardowego głosu

W obszarze [Przygotowywanie danych dla niestandardowego głosu](how-to-custom-voice-prepare-data.md)opisano różne typy danych, których można użyć do uczenia niestandardowego głosu i różne wymagania dotyczące formatu. Po przygotowaniu danych możesz rozpocząć przekazywanie ich do [niestandardowego portalu głosowego](https://aka.ms/custom-voice-portal)lub za pomocą interfejsu API szkolenia niestandardowego dla połączeń głosowych. Tutaj opisano kroki szkolenia niestandardowego głosu za pomocą portalu.

> [!NOTE]
> Na tej stronie założono, że użytkownik ma uprawnienia do odczytu [z niestandardowego głosu](how-to-custom-voice.md) i [przygotowania danych do niestandardowego głosu](how-to-custom-voice-prepare-data.md)i utworzył niestandardowy projekt głosowy.

Sprawdź języki obsługiwane dla niestandardowego głosu: [język do dostosowania](language-support.md#customization).

## <a name="upload-your-datasets"></a>Przekazywanie zestawów danych

Gdy wszystko będzie gotowe do przekazania danych, przejdź do [niestandardowego portalu głosowego](https://aka.ms/custom-voice-portal). Utwórz lub wybierz niestandardowy projekt głosowy. Projekt musi udostępniać odpowiednie właściwości języka/ustawień regionalnych oraz płci jako dane, które mają być używane dla szkolenia głosowego. Na przykład wybierz, `en-GB` czy nagranie audio zostało wykonane w języku angielskim z akcentem Zjednoczonego Królestwa.

Przejdź do karty **dane** , a następnie kliknij pozycję **Przekaż dane**. W kreatorze wybierz odpowiedni typ danych, który jest zgodny z przygotowanymi danymi.

Każdy przekazywany zestaw danych musi spełniać wymagania dotyczące wybranego typu danych. Ważne jest, aby poprawnie sformatować dane przed ich przekazaniem. Gwarantuje to, że dane będą prawidłowo przetwarzane przez usługę niestandardowej usługi głosowej. Przejdź do pozycji [Przygotuj dane dla niestandardowego głosu](how-to-custom-voice-prepare-data.md) i upewnij się, że dane zostały odpowiednio sformatowane.

> [!NOTE]
> Użytkownicy z bezpłatną subskrypcją (F0) mogą jednocześnie przekazywać dwa zestawy danych. Użytkownicy Standard Subscription (S0) mogą przekazywać jednocześnie pięć zestawów danych. Jeśli osiągnięto limit, poczekaj na zakończenie importowania co najmniej jednego zestawu danych. Następnie spróbuj ponownie.

> [!NOTE]
> Maksymalna liczba zestawów danych, które mogą zostać zaimportowane na subskrypcję, to 10. zip plików dla użytkowników bezpłatnej subskrypcji (F0) i użytkowników 500 for Standard Subscription (S0).

Zestawy danych są automatycznie sprawdzane po naciśnięciu przycisku Przekaż. Sprawdzanie poprawności danych obejmuje serie kontroli plików audio, aby sprawdzić ich format, rozmiar i częstotliwość próbkowania. Usuń błędy i prześlij je ponownie. Po pomyślnym zainicjowaniu żądania importowania danych powinien zostać wyświetlony wpis w tabeli danych, który odnosi się do przekazanego zestawu danych.

W poniższej tabeli przedstawiono Stany przetwarzania zaimportowanych zestawów danych:

| Stan | Znaczenie |
| ----- | ------- |
| Przetwarzanie | Zestaw danych został odebrany i jest przetwarzany. |
| Powodzenie | Zestaw danych został sprawdzony i może być teraz używany do kompilowania modelu głosowego. |
| Niepowodzenie | Przetwarzanie zestawu danych nie powiodło się z powodu wielu przyczyn, na przykład błędy plików, problemy z danymi lub problemy z siecią. |

Po zakończeniu walidacji można zobaczyć łączną liczbę dopasowanych wyrażenia długości dla każdego z zestawów danych w kolumnie **wyrażenia długości** . Jeśli wybrany typ danych wymaga segmentacji w postaci długiego dźwięku, ta kolumna odzwierciedla tylko wyrażenia długości z segmentacją na podstawie transkrypcji lub za pośrednictwem usługi transkrypcji mowy. Można dokładniej pobrać zestaw danych zweryfikowany, aby wyświetlić szczegółowe wyniki pomyślnie zaimportowanego wyrażenia długości i ich transkrypcji mapowania. Wskazówka: przetwarzanie danych za długi-audio może trwać dłużej niż godzinę.

W widoku Szczegóły danych można dokładniej sprawdzić wyniki wymowy i poziom szumów dla każdego zestawu danych. Wynik wymowy z zakresu od 0 do 100. Wynik poniżej 70 zwykle wskazuje błąd mowy lub niezgodność skryptu. Ciężki akcent może zmniejszyć wynik wymowy i mieć wpływ na wygenerowany głos cyfrowy.

Wyższy współczynnik sygnału do szumu (SNR) wskazuje niższy hałas w dźwięku. Zwykle możesz dotrzeć do 50 SNR przez nagranie w profesjonalnej Studios. Dźwięk z SNRem poniżej 20 może spowodować oczywisty hałas w wygenerowanym głosie.

Rozważ ponowne nagranie wszelkich wyrażenia długości z wynikami niskiej wymowy lub słabego współczynnika sygnałów do szumów. Jeśli nie możesz zmienić rekordu, możesz wykluczyć te wyrażenia długości z zestawu danych.

> [!NOTE]
> W przypadku korzystania z niestandardowego głosu neuronowych należy zarejestrować talent głosu na karcie **Voice talent** . Podczas przygotowywania skryptu nagrywania upewnij się, że dołączysz poniższe zdanie, aby uzyskać potwierdzenie Talenti głosu przy użyciu danych głosowych, aby utworzyć model głosu TTS i wygenerować syntetyczną mowę. "I [stan imię i nazwisko] wie, że nagrania mojego głosu będą używane przez użytkownika [Nadaj nazwę firmie] do tworzenia i używania syntetycznej wersji mojego głosu".
To zdanie zostanie użyte do sprawdzenia, czy nagrania w zestawach danych szkoleniowych są wykonywane przez tę samą osobę, która wyraża zgodę. [Dowiedz się więcej na temat sposobu przetwarzania danych i sposobu weryfikacji talent głosu](https://aka.ms/CNV-data-privacy). 

## <a name="build-your-custom-voice-model"></a>Tworzenie niestandardowego modelu głosu

Po sprawdzeniu poprawności zestawu danych można go użyć do utworzenia niestandardowego modelu głosu.

1.  Przejdź do obszaru **Zamiana tekstu na mowę > niestandardowego głosu > [nazwa projektu] > model**.

2.  Kliknij pozycję **uczenie modelu**.

3.  Następnie wprowadź **nazwę** i **Opis** , aby ułatwić identyfikację tego modelu.

    Należy uważnie wybrać nazwę. Wprowadzona nazwa będzie nazwą użytą do określenia głosu w żądaniu dla syntezy mowy w ramach danych wejściowych SSML. Dozwolone są tylko litery, cyfry i kilka znaków interpunkcyjnych, takich jak-, \_ i (","). Użyj różnych nazw dla różnych modeli głosowych.

    Typowym zastosowaniem pola **Opis** jest zapisanie nazw zestawów danych, które zostały użyte do utworzenia modelu.

4.  Na stronie **Wybierz dane szkoleniowe** wybierz co najmniej jeden zestaw danych, który ma być używany do szkoleń. Przed przesłaniem Sprawdź liczbę wyrażenia długości. Można zacząć od dowolnej liczby wyrażenia długości dla modeli głosu pl-US i zh-CN przy użyciu metody szkolenia "adaptacyjne". W przypadku innych ustawień regionalnych należy wybrać więcej niż 2 000 wyrażenia długości, aby umożliwić uczenie głosu przy użyciu warstwy Standardowa, w tym metod szkoleniowych "statystycznych parametrów" i "łączenie" oraz więcej niż 300 wyrażenia długości do uczenia niestandardowego głosu neuronowych. 

    > [!NOTE]
    > Zduplikowane nazwy audio zostaną usunięte z szkolenia. Upewnij się, że wybrane zestawy danych nie zawierają tych samych nazw audio w wielu plikach ZIP.

    > [!TIP]
    > Użycie zestawów danych z tego samego prezentera jest wymagane w celu uzyskania jakości wyników. Różne metody szkoleniowe wymagają różnego rozmiaru danych szkoleniowych. Aby przeprowadzić uczenie modelu z metodą "statystyczną parametryczną", wymagane jest co najmniej 2 000 odrębnych wyrażenia długości. Dla metody "złączne" jest 6 000 wyrażenia długości, a w przypadku wartości "neuronowych" minimalne wymaganie dotyczące rozmiaru danych to 300 wyrażenia długości.

5. Wybierz **metodę uczenia** w następnym kroku. 

    > [!NOTE]
    > Jeśli chcesz przeprowadzić uczenie głosu neuronowych, musisz określić profil talenta głosowego z plikiem zgody na dźwięk dostarczonym w przypadku głosu talent, aby użyć jego danych mowy do uczenia niestandardowego modelu głosowego. Niestandardowy głos neuronowych jest dostępny z ograniczonym dostępem. Upewnij się, że rozumiesz odpowiednie [wymagania AI](https://aka.ms/gating-overview) , i [Zastosuj tutaj dostęp](https://aka.ms/customneural). 
    
    Na tej stronie możesz również wybrać opcję przekazania skryptu do testowania. Skrypt testowy musi być plikiem txt, mniejszym niż 1 MB. Obsługiwany format kodowania obejmuje ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE lub UTF-16. W każdym akapicie wypowiedź zostanie oddzielny dźwięk. Jeśli chcesz połączyć wszystkie zdania w jeden dźwięk, wprowadź je w jednym akapicie. 

6. Kliknij pozycję **uczenie** , aby rozpocząć tworzenie modelu głosu.

W tabeli szkoleń zostanie wyświetlony nowy wpis, który odnosi się do nowo utworzonego modelu. W tabeli jest również wyświetlany stan: przetwarzanie, zakończone powodzeniem, zakończone niepowodzeniem.

Wyświetlany stan odzwierciedla proces konwersji zestawu danych na model głosu, jak pokazano poniżej.

| Stan | Znaczenie |
| ----- | ------- |
| Przetwarzanie | Trwa tworzenie modelu głosu. |
| Powodzenie | Twój model głosu został utworzony i można go wdrożyć. |
| Niepowodzenie | Twój model głosowy nie powiódł się z powodu z wielu powodów, na przykład niewidocznych problemów z danymi lub problemów z siecią. |

Czas uczenia zależy od ilości przetworzonych danych audio i wybranej metody szkoleniowej. Może ona mieć zakres od 30 minut do 40 godzin. Po pomyślnym przeprowadzeniu szkolenia modelu można zacząć go przetestować. 

> [!NOTE]
> Użytkownicy z bezpłatną subskrypcją (F0) mogą jednocześnie przeszkolić jedną czcionkę głosową. Użytkownicy Standard Subscription (S0) mogą szkolić trzy głosy jednocześnie. Jeśli osiągnięto limit, poczekaj na zakończenie szkolenia co najmniej jednej z Twoich czcionek głosowych, a następnie spróbuj ponownie.

> [!NOTE]
> Szkolenie niestandardowych głosów neuronowych nie jest bezpłatne. Sprawdź [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) tutaj. 

> [!NOTE]
> Maksymalna liczba modeli głosu, które mogą być przeszkolone na subskrypcję, to 10 modeli dla użytkowników bezpłatnych subskrypcji (F0) i 100 dla użytkowników w warstwie Standardowa (S0).

W przypadku korzystania z funkcji szkolenia głosu neuronowych można wybrać, aby szkolić model zoptymalizowany pod kątem scenariuszy przesyłania strumieniowego w czasie rzeczywistym lub model HD neuronowych zoptymalizowany pod kątem asynchronicznej [syntezy typu Long-audio](long-audio-api.md).  

## <a name="test-your-voice-model"></a>Testowanie modelu głosu

Każde szkolenie spowoduje automatyczne wygenerowanie 100 próbek audio w celu ułatwienia przetestowania modelu. Po pomyślnym skompilowaniu modelu głosu można go przetestować przed wdrożeniem do użycia.

1.  Przejdź do obszaru **Zamiana tekstu na mowę > niestandardowego głosu > [nazwa projektu] > model**.

2.  Kliknij nazwę modelu, który chcesz przetestować.

3.  Na stronie Szczegóły modelu można znaleźć przykładowe audio na karcie **testowanie** . 

Jakość głosu zależy od wielu czynników, w tym rozmiaru danych szkoleniowych, jakości nagrania, dokładności pliku transkrypcji, jak dobrze zarejestrowany głos w danych szkoleniowych pasuje do osobowości zamierzonego głosu na potrzeby planowanego użycia i nie tylko. Zapoznaj się [z tym tutaj, aby dowiedzieć się więcej o możliwościach i ograniczeniach naszej technologii oraz najlepszych sposobach poprawy jakości modelu](https://aka.ms/CNV-limits). 

## <a name="create-and-use-a-custom-voice-endpoint"></a>Tworzenie niestandardowego punktu końcowego głosu i korzystanie z niego

Po pomyślnym utworzeniu i przetestowaniu modelu głosu należy wdrożyć go w niestandardowym punkcie końcowym zamiany tekstu na mowę. Następnie należy użyć tego punktu końcowego zamiast zwykłego punktu końcowego podczas wykonywania żądań zamiany tekstu na mowę za pomocą interfejsu API REST. Niestandardowy punkt końcowy może być wywoływany tylko przez subskrypcję, która została użyta do wdrożenia czcionki.

Aby utworzyć nowy niestandardowy punkt końcowy, przejdź do pozycji **Zamiana tekstu na mowę > niestandardowego głosu > punkt końcowy**. Wybierz pozycję **Dodaj punkt końcowy** i wprowadź **nazwę** i **Opis** niestandardowego punktu końcowego. Następnie wybierz niestandardowy model głosu, który chcesz skojarzyć z tym punktem końcowym.

Po kliknięciu przycisku **Dodaj** w tabeli punkt końcowy zobaczysz wpis dla nowego punktu końcowego. Utworzenie wystąpienia nowego punktu końcowego może potrwać kilka minut. Gdy stan wdrożenia zostanie **zakończony pomyślnie**, punkt końcowy jest gotowy do użycia.

Można **wstrzymywać** i **wznawiać** punkt końcowy, jeśli nie jest on używany przez cały czas. Gdy punkt końcowy jest ponownie uaktywniany po zawieszeniu, adres URL punktu końcowego będzie przechowywany tak samo, więc nie trzeba zmieniać kodu w aplikacjach. 

Możesz również zaktualizować punkt końcowy do nowego modelu. Aby zmienić model, upewnij się, że nowy model ma taką samą nazwę, jak ten, który ma zostać zaktualizowany. 

> [!NOTE]
> Użytkownicy z bezpłatną subskrypcją (F0) mogą mieć wdrożony tylko jeden model. Użytkownicy Standard Subscription (S0) mogą tworzyć do 50 punktów końcowych, z których każdy ma własny niestandardowy głos.

> [!NOTE]
> Aby użyć niestandardowego głosu, należy określić nazwę modelu głosu, użyć niestandardowego identyfikatora URI bezpośrednio w żądaniu HTTP i użyć tej samej subskrypcji do przekazywania uwierzytelniania usługi TTS.

Po wdrożeniu punktu końcowego nazwa punktu końcowego jest wyświetlana jako link. Kliknij link, aby wyświetlić informacje specyficzne dla danego punktu końcowego, takie jak klucz punktu końcowego, adres URL punktu końcowego i przykładowy kod.

Testy online punktu końcowego są również dostępne za pośrednictwem niestandardowego portalu głosowego. Aby przetestować punkt końcowy, wybierz pozycję **Sprawdź punkt końcowy** na stronie **szczegółów punktu końcowego** . Zostanie wyświetlona strona testowanie punktu końcowego. Wprowadź tekst do wymawiania (w formacie zwykłego tekstu lub [SSML](speech-synthesis-markup.md) w polu tekstowym). Aby usłyszeć tekst mówiony przez niestandardową czcionkę głosową, wybierz opcję **Odtwórz**. Ta funkcja testowania będzie obciążana opłatą za niestandardowe użycie syntezy mowy.

Niestandardowy punkt końcowy jest funkcjonalnie identyczny ze standardowym punktem końcowym używanym do obsługi żądań zamiany tekstu na mowę. Aby uzyskać więcej informacji, zobacz [interfejs API REST](rest-text-to-speech.md) .

## <a name="next-steps"></a>Następne kroki

* [Przewodnik: zapisywanie przykładów głosu](record-custom-voice-samples.md)
* [Odwołanie do tekstu do Speech API](rest-text-to-speech.md)
* [Długi interfejs API audio](long-audio-api.md)
