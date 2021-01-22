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
ms.openlocfilehash: b59d9ebf55f7a4c02891a782b7271eec2f521576
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663285"
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

W przypadku zestawów danych en-US i zh-CN można dodatkowo pobrać raport, aby sprawdzić wyniki wymowy i poziom szumów dla każdego nagrania. Wynik wymowy z zakresu od 0 do 100. Wynik poniżej 70 zwykle wskazuje błąd mowy lub niezgodność skryptu. Ciężki akcent może zmniejszyć wynik wymowy i mieć wpływ na wygenerowany głos cyfrowy.

Wyższy współczynnik sygnału do szumu (SNR) wskazuje niższy hałas w dźwięku. Zwykle możesz dotrzeć do 50 SNR przez nagranie w profesjonalnej Studios. Dźwięk z SNRem poniżej 20 może spowodować oczywisty hałas w wygenerowanym głosie.

Rozważ ponowne nagranie wszelkich wyrażenia długości z wynikami niskiej wymowy lub słabego współczynnika sygnałów do szumów. Jeśli nie możesz zmienić rekordu, możesz wykluczyć te wyrażenia długości z zestawu danych.

## <a name="build-your-custom-voice-model"></a>Tworzenie niestandardowego modelu głosu

Po sprawdzeniu poprawności zestawu danych można go użyć do utworzenia niestandardowego modelu głosu.

1.  Przejdź do obszaru **Zamiana tekstu na mowę > niestandardowego głosu > [nazwa projektu] > szkolenie**.

2.  Kliknij pozycję **uczenie modelu**.

3.  Następnie wprowadź **nazwę** i **Opis** , aby ułatwić identyfikację tego modelu.

    Należy uważnie wybrać nazwę. Wprowadzona nazwa będzie nazwą użytą do określenia głosu w żądaniu dla syntezy mowy w ramach danych wejściowych SSML. Dozwolone są tylko litery, cyfry i kilka znaków interpunkcyjnych, takich jak-, \_ i (","). Użyj różnych nazw dla różnych modeli głosowych.

    Typowym zastosowaniem pola **Opis** jest zapisanie nazw zestawów danych, które zostały użyte do utworzenia modelu.

4.  Na stronie **Wybierz dane szkoleniowe** wybierz co najmniej jeden zestaw danych, który ma być używany do szkoleń. Przed przesłaniem Sprawdź liczbę wyrażenia długości. Można zacząć od dowolnej liczby wyrażenia długości dla modeli głosu pl-US i zh-CN. W przypadku innych ustawień regionalnych należy wybrać więcej niż 2 000 wyrażenia długości, aby umożliwić uczenie głosu.

    > [!NOTE]
    > Zduplikowane nazwy audio zostaną usunięte z szkolenia. Upewnij się, że wybrane zestawy danych nie zawierają tych samych nazw audio w wielu plikach ZIP.

    > [!TIP]
    > Użycie zestawów danych z tego samego prezentera jest wymagane w celu uzyskania jakości wyników. Gdy zbiory danych, które zostały przesłane do szkolenia, zawierają łączną liczbę 6 000 odrębnych wyrażenia długości, nauczysz swój model głosowy za pomocą statystycznej techniki syntezy. W przypadku, gdy dane szkoleniowe przekraczają łączną liczbę 6 000 odrębnych wyrażenia długości, rozpocznie się proces szkolenia przy użyciu techniki syntezy łączenia. Zwykle technologia łączenia może skutkować bardziej naturalnymi wynikami głosu i wyższym dokładnością. [Skontaktuj się z niestandardowym zespołem mowy](https://go.microsoft.com/fwlink/?linkid=2108737) , jeśli chcesz nauczyć model z najnowszą technologią neuronowych TTS, która może generować cyfrowy głos równoważny do publicznie dostępnych [głosów neuronowych](language-support.md#neural-voices).

5.  Kliknij pozycję **uczenie** , aby rozpocząć tworzenie modelu głosu.

W tabeli szkoleń zostanie wyświetlony nowy wpis, który odnosi się do nowo utworzonego modelu. W tabeli jest również wyświetlany stan: przetwarzanie, zakończone powodzeniem, zakończone niepowodzeniem.

Wyświetlany stan odzwierciedla proces konwersji zestawu danych na model głosu, jak pokazano poniżej.

| Stan | Znaczenie |
| ----- | ------- |
| Przetwarzanie | Trwa tworzenie modelu głosu. |
| Powodzenie | Twój model głosu został utworzony i można go wdrożyć. |
| Niepowodzenie | Twój model głosowy nie powiódł się z powodu z wielu powodów, na przykład niewidocznych problemów z danymi lub problemów z siecią. |

Czas uczenia zależy od ilości przetworzonych danych audio. Typowy przedziały czasu od około 30 minut dla setek wyrażenia długości do 40 godzin dla 20 000 wyrażenia długości. Po pomyślnym przeprowadzeniu szkolenia modelu można zacząć go przetestować.

> [!NOTE]
> Użytkownicy z bezpłatną subskrypcją (F0) mogą jednocześnie przeszkolić jedną czcionkę głosową. Użytkownicy Standard Subscription (S0) mogą szkolić trzy głosy jednocześnie. Jeśli osiągnięto limit, poczekaj na zakończenie szkolenia co najmniej jednej z Twoich czcionek głosowych, a następnie spróbuj ponownie.

> [!NOTE]
> Maksymalna liczba modeli głosu, które mogą być przeszkolone na subskrypcję, to 10 modeli dla użytkowników bezpłatnych subskrypcji (F0) i 100 dla użytkowników w warstwie Standardowa (S0).

W przypadku korzystania z funkcji szkolenia głosu neuronowych można wybrać, aby szkolić model zoptymalizowany pod kątem scenariuszy przesyłania strumieniowego w czasie rzeczywistym lub model HD neuronowych zoptymalizowany pod kątem asynchronicznej [syntezy typu Long-audio](long-audio-api.md).  

## <a name="test-your-voice-model"></a>Testowanie modelu głosu

Po pomyślnym skompilowaniu czcionki głosowej możesz ją przetestować przed wdrożeniem jej do użycia.

1.  Przejdź do obszaru **zamiany tekstu na mowę > niestandardowego głosu > [nazwa projektu] > testowanie**.

2.  Kliknij przycisk **Dodaj test**.

3.  Wybierz jeden lub wiele modeli, które chcesz przetestować.

4.  Podaj tekst, który ma być wypowiadany przez głos. Jeśli wybrano opcję jednoczesnego testowania wielu modeli, ten sam tekst będzie używany do testowania różnych modeli.

    > [!NOTE]
    > Język tekstu musi być taki sam jak język czcionki głosowej. Testy można testować tylko dla pomyślnie przeszkolonych modeli. W tym kroku jest obsługiwany tylko zwykły tekst.

5.  Kliknij pozycję **Utwórz**.

Po przesłaniu żądania testowego nastąpi powrót do strony testowej. Tabela zawiera teraz wpis odpowiadający nowemu żądaniu i kolumnie Stan. Wypróbowanie mowy może potrwać kilka minut. Gdy kolumna stanu **powiedzie się**, można odtworzyć dźwięk lub pobrać dane wejściowe tekstu (plik. txt) i dane wyjściowe audio (plik. wav), a następnie Audition je w celu zapewnienia jakości.

Wyniki testów można również znaleźć na stronie szczegółów poszczególnych modeli wybranych do testowania. Przejdź do karty **szkolenie** i kliknij nazwę modelu, aby wprowadzić stronę szczegółów modelu.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Tworzenie niestandardowego punktu końcowego głosu i korzystanie z niego

Po pomyślnym utworzeniu i przetestowaniu modelu głosu należy wdrożyć go w niestandardowym punkcie końcowym zamiany tekstu na mowę. Następnie należy użyć tego punktu końcowego zamiast zwykłego punktu końcowego podczas wykonywania żądań zamiany tekstu na mowę za pomocą interfejsu API REST. Niestandardowy punkt końcowy może być wywoływany tylko przez subskrypcję, która została użyta do wdrożenia czcionki.

Aby utworzyć nowy niestandardowy punkt końcowy, przejdź do pozycji **Zamiana tekstu na mowę > niestandardowego głosu > wdrożenia**. Wybierz pozycję **Dodaj punkt końcowy** i wprowadź **nazwę** i **Opis** niestandardowego punktu końcowego. Następnie wybierz niestandardowy model głosu, który chcesz skojarzyć z tym punktem końcowym.

Po kliknięciu przycisku **Dodaj** w tabeli punkt końcowy zobaczysz wpis dla nowego punktu końcowego. Utworzenie wystąpienia nowego punktu końcowego może potrwać kilka minut. Gdy stan wdrożenia zostanie **zakończony pomyślnie**, punkt końcowy jest gotowy do użycia.

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