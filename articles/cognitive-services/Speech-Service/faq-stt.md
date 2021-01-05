---
title: Zamiana mowy na tekst często zadawanych pytań
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi zamiany mowy na tekst.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: a78e18de1f495feb6234fa5bfd97162d8b80de4c
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857328"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Zamiana mowy na tekst często zadawanych pytań

Jeśli nie możesz znaleźć odpowiedzi na pytania w tym często zadawane pytania, zapoznaj się z [innymi opcjami pomocy technicznej](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Ogólne

**P: Jaka jest różnica między modelem linii bazowej i niestandardową zamianą mowy na model tekstu?**

Odp **.: model** linii bazowej został przeszkolony przy użyciu danych należących do firmy Microsoft i jest już wdrożony w chmurze. Możesz użyć niestandardowego modelu, aby dostosować model do lepszego dopasowania do określonego środowiska, które ma określony szum lub język otoczenia. Piętra fabryki, samochody i zakłócenia! wymagają dostosowania modelu akustycznego. Tematy, takie jak biologia, fizyka, Radiologia, nazwy produktów i niestandardowe akronimy, wymagają przystosowanego modelu języka. W przypadku uczenia modelu niestandardowego należy zacząć od powiązanego tekstu, aby poprawić rozpoznawanie specjalnych terminów i fraz.

**P: co mogę zrobić, jeśli chcę użyć modelu linii bazowej?**

Odp **.: najpierw** Pobierz [klucz subskrypcji](overview.md#try-the-speech-service-for-free). Jeśli chcesz, aby wywołania REST były nawiązywane w ramach prewdrażanych modeli bazowych, zobacz [interfejsy API REST](./overview.md#reference-docs). Jeśli chcesz używać obiektów WebSockets, [Pobierz zestaw SDK](speech-sdk.md).

**P: Czy zawsze muszę utworzyć niestandardowy model mowy?**

Odp **.: nie**. Jeśli aplikacja używa ogólnego, codziennego języka, nie musisz dostosowywać modelu. Jeśli aplikacja jest używana w środowisku, w którym nie ma żadnego szumu w tle, nie musisz dostosowywać modelu.

Możesz wdrożyć linie bazowe i dostosowane modele w portalu, a następnie uruchomić na nich testy dokładności. Ta funkcja służy do mierzenia dokładności modelu linii bazowej w porównaniu z modelem niestandardowym.

**P: Jak mogę się dowiedzieć, gdy trwa przetwarzanie dla mojego zestawu danych lub modelu?**

Odp **.: obecnie** jest to jedyny sposób, w jaki stan modelu lub zestawu danych w tabeli. Po zakończeniu przetwarzania stan zostanie **zakończony pomyślnie**.

**P: Czy mogę utworzyć więcej niż jeden model?**

Odp **.: nie** ma żadnego limitu liczby modeli, które mogą znajdować się w kolekcji.

**P: zgłoszono pomyłkę. Jak mogę anulować Importowanie danych lub tworzenie modelu, który jest w toku?**

Odp **.: obecnie** nie można wycofać procesu adaptacji akustycznej lub językowej. Zaimportowane dane i modele można usunąć, gdy są one w stanie terminalu.

**P: otrzymuję kilka wyników dla każdej frazy ze szczegółowym formatem danych wyjściowych. Którego z nich użyć?**

Odp **.: zawsze** Pobieraj pierwszy wynik, nawet jeśli inny wynik ("N-Najlepsza") może mieć wyższą wartość ufności. Usługa mowy traktuje pierwszy wynik jako najlepszy. Może być również pusty ciąg, jeśli nie rozpoznano mowy.

Inne wyniki są prawdopodobnie gorsze i mogą nie mieć pełnej wielkości liter i znaków interpunkcyjnych. Te wyniki są najbardziej przydatne w scenariuszach specjalnych, takich jak umożliwienie użytkownikom wyboru poprawek z listy lub obsługi nieprawidłowo rozpoznanych poleceń.

**P: Dlaczego istnieją różne modele podstawowe?**

Odp **.: możesz** wybrać spośród więcej niż jednego modelu podstawowego w usłudze Speech. Każda nazwa modelu zawiera datę dodania. Po rozpoczęciu uczenia modelu niestandardowego Użyj najnowszego modelu, aby uzyskać najlepszą dokładność. Starsze modele podstawowe są nadal dostępne przez pewien czas po udostępnieniu nowego modelu. Możesz nadal korzystać z modelu, do którego pracujesz, dopóki nie zostanie wycofany (zobacz [cykl życia modelu](custom-speech-overview.md#model-lifecycle)). Nadal zaleca się przełączenie do najnowszego modelu podstawowego w celu uzyskania lepszej dokładności.

**P: Czy mogę zaktualizować istniejący model (stos modelu)?**

Odp **.: nie** można zaktualizować istniejącego modelu. Jako rozwiązanie Połącz stary zestaw danych z nowym zestawem danych i dostosuj go.

Stary zestaw danych i nowy zestaw danych muszą być połączone w pojedynczym pliku zip (dla danych akustycznych) lub w pliku txt (dla danych języka). Po zakończeniu adaptacji nowy, zaktualizowany model musi zostać ponownie wdrożony w celu uzyskania nowego punktu końcowego

**P: gdy dostępna jest nowa wersja modelu podstawowego, czy moje wdrożenie jest automatycznie aktualizowane?**

Odp **.: wdrożenia** nie będą automatycznie aktualizowane.

Jeśli dostosowano i wdrożono model, wdrożenie pozostanie bez zmian. Wdrożony model można zlikwidować, ponownie dostosować przy użyciu nowszej wersji modelu podstawowego i wdrożyć go w celu uzyskania lepszej dokładności.

Modele podstawowe i modele niestandardowe zostaną wycofane po pewnym czasie (zobacz [cykl życia modelu](custom-speech-overview.md#model-lifecycle)).

**P: Czy można pobrać model i uruchomić go lokalnie?**

Odp **.: model** niestandardowy można uruchomić lokalnie w [kontenerze platformy Docker](speech-container-howto.md?tabs=cstt).

**P: Czy można kopiować i przenosić moje zestawy danych, modele i wdrożenia do innego regionu lub subskrypcji?**

Odp **.: można** użyć [interfejsu API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) , aby skopiować model niestandardowy do innego regionu lub subskrypcji. Zestawy danych lub wdrożenia nie mogą być kopiowane. Zestaw danych można zaimportować ponownie w innej subskrypcji i utworzyć punkty końcowe przy użyciu kopii modelu.

**P: Czy moje żądania są zarejestrowane?**

Odp **.: domyślnie** żądania nie są rejestrowane (ani audio ani transkrypcja). W razie potrzeby można wybrać opcję *Rejestruj zawartość z tej opcji punktu końcowego* podczas [tworzenia niestandardowego punktu końcowego](./how-to-custom-speech-train-model.md). Możesz również włączyć rejestrowanie audio w [zestawie mowy SDK](speech-sdk.md) dla każdego żądania, bez tworzenia niestandardowego punktu końcowego. W obu przypadkach wyniki audio i rozpoznawania żądań będą przechowywane w bezpiecznym magazynie. Subskrypcje korzystające z magazynu należącego do firmy Microsoft będą dostępne przez 30 dni.

Zarejestrowane pliki można wyeksportować na stronie wdrożenia w programie Speech Studio, jeśli używasz niestandardowego punktu końcowego z *zawartością dziennika z tego punktu końcowego* . Jeśli rejestrowanie audio jest włączone za pośrednictwem zestawu SDK, należy wywołać [interfejs API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModelLogs) , aby uzyskać dostęp do plików.

**P: Czy moje żądania są ograniczone?**

Odp **.: Zobacz** [przydziały i limity usługi Speech Services](speech-services-quotas-and-limits.md).

**P: jak opłaty są naliczane za dwa kanały audio?**

Odp **.: Jeśli** przesyłasz każdy kanał osobno (każdy kanał w osobnym pliku), zostanie naliczona opłata za cały plik. Jeśli przesyłasz pojedynczy plik z każdym kanałem, który zostanie przyładowany, zostanie naliczona opłata za czas trwania pojedynczego pliku. Szczegółowe informacje o cenach można znaleźć na [stronie cennika usługi Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Jeśli masz jeszcze inne problemy z ochroną prywatności, które uniemożliwiają korzystanie z usługi Custom Speech Service, skontaktuj się z jednym z kanałów pomocy technicznej.

## <a name="increasing-concurrency"></a>Zwiększanie współbieżności
Zobacz [przydziały i limity usługi Speech Services](speech-services-quotas-and-limits.md).


## <a name="importing-data"></a>Importowanie danych

**P: jaki jest limit rozmiaru zestawu danych i dlaczego jest to limit?**

Odp **.: limit** jest spowodowany przez ograniczenie rozmiaru pliku na potrzeby przekazywania http. Zapoznaj się z limitem [przydziału i limitów usługi mowy](speech-services-quotas-and-limits.md) dla rzeczywistego limitu. Dane można podzielić na wiele zestawów danych i wybrać wszystkie z nich do uczenia modelu.

**P: Czy mogę zip moje pliki tekstowe, aby można było przekazać większy plik tekstowy?**

Odp **.: nie**. Obecnie dozwolone są tylko nieskompresowane pliki tekstowe.

**P: Raport dotyczący danych wskazuje, że wyrażenia długości nie powiodło się. Jaki jest problem?**

Odp **.:** niepowodzenie przekazania 100 procent wyrażenia długości w pliku nie jest problemem. Jeśli większość wyrażenia długości w zestawie danych akustycznych lub w języku (na przykład więcej niż 95 procent) została pomyślnie zaimportowana, zestaw danych może być użyteczny. Zaleca się jednak, aby poznać przyczynę niepowodzenia wyrażenia długości i rozwiązać problemy. Najczęstsze problemy, takie jak błędy formatowania, są łatwe do naprawienia.

## <a name="creating-an-acoustic-model"></a>Tworzenie modelu akustycznego

**P: jak dużo danych akustycznych jest potrzebnych?**

Odp **.: zalecamy** rozpoczęcie od 30 minut i godzinę akustyczną danych.

**P: jakie dane należy zebrać?**

Odp **.: Zbierz** dane, które są blisko scenariusza aplikacji i przypadku użycia, jak to możliwe. Zbieranie danych powinno być zgodne z aplikacją docelową i użytkownikami w odniesieniu do urządzeń lub urządzeń, środowisk i typów głośników. Ogólnie rzecz biorąc, należy zbierać dane z szerokiego zakresu głośników, jak to możliwe.

**P: jak zbierać dane akustyczne?**

Odp.: możesz utworzyć autonomiczną **aplikację do zbierania** danych lub użyć oprogramowania do nagrywania dźwięku na półkę. Możesz również utworzyć wersję aplikacji, która rejestruje dane audio, a następnie używa tych danych.

**P: Czy muszę transkrypcja dane adaptacyjne?**

Odp **.: tak**. Możesz transkrypcja go samodzielnie lub korzystać z profesjonalnej usługi transkrypcji. Niektórzy użytkownicy preferują profesjonalne transcribers i inne korzystają z crowdsourcing lub same transkrypcji.

**P: jak długo trwa uczenie danych audio modelu niestandardowego?**

Odp **.:** uczenie modelu z danymi audio to długotrwały proces. W zależności od ilości danych Tworzenie niestandardowego modelu może potrwać kilka dni. Jeśli nie można jej zakończyć w ciągu jednego tygodnia, usługa może przerwać operację uczenia i zgłosić model jako niepowodzenie. W celu uzyskania szybszych wyników użyj jednego z [regionów](custom-speech-overview.md#set-up-your-azure-account) , w których dedykowany sprzęt jest dostępny do uczenia się. Możesz skopiować w pełni przeszkolony model do innego regionu przy użyciu [interfejsu API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription). Szkolenie z zaledwie tekstem jest znacznie szybsze i zwykle kończy się w ciągu kilku minut.

Niektóre modele podstawowe nie mogą być dostosowane do danych audio. W przypadku korzystania z nich, usługa będzie po prostu używać tekstu transkrypcji do uczenia i odrzucania danych audio. Szkolenie zostanie wykonane znacznie szybciej, a wyniki będą takie same jak w przypadku szkoleń w postaci zwykłego tekstu.

## <a name="accuracy-testing"></a>Testowanie dokładności

**P: co to jest współczynnik błędów wyrazów (WER) i jak jest obliczana?**

Odp **.: funkcja** raportowanie błędów systemu Windows to metryka oceny dla funkcji rozpoznawania mowy. Raportowanie błędów systemu Windows jest zliczane jako łączna liczba błędów, które obejmują wstawienia, usunięcia i podstawienia, podzieloną przez łączną liczbę słów w transkrypcji odwołania. Aby uzyskać więcej informacji, zobacz [ocenę dokładności Custom Speech](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy).

**P: Jak mogę określić czy wyniki testu dokładności są dobre?**

Odp **.: wyniki** zawierają porównanie między modelem linii bazowej i dostosowanym modelem. Należy dążyć do naniesienia poziomu modelu linii bazowej, aby wartościowa dostosowania.

**P: Jak mogę ustalić wartości błędów dla modelu podstawowego, aby zobaczyć, czy nastąpiła poprawa?**

Odp **.: wyniki** testu w trybie offline pokazują dokładność linii bazowej modelu niestandardowego i ulepszenie względem linii bazowej.

## <a name="creating-a-language-model"></a>Tworzenie modelu języka

**P: ile danych tekstowych jest potrzebnych do przekazania?**

Odp **.: to** zależy od tego, jak różne słownictwo i frazy używane w aplikacji pochodzą z modeli języka początkowego. W przypadku wszystkich nowych słów warto podać dowolną liczbę przykładów użycia tych słów. W przypadku typowych fraz, które są używane w aplikacji, w tym również zwroty w danych języka, są przydatne, ponieważ instruuje system, aby nasłuchiwać tych warunków. Często istnieje co najmniej 100 i zwykle kilka wyrażenia długości w zestawie danych języka. Ponadto, jeśli oczekuje się, że niektóre typy zapytań będą bardziej typowe niż inne, można wstawić wiele kopii typowych zapytań w zestawie danych.

**P: Czy mogę po prostu przekazać listę słów?**

Odp **.:** przekazanie listy wyrazów spowoduje dodanie wyrazów do słownika, ale nie uczy się, w jaki sposób wyrazy są zwykle używane. Dostarczając pełne lub częściowe wyrażenia długości (zdania lub frazy, które użytkownicy mogą powiedzieć), model języka może poznać nowe wyrazy i sposób ich użycia. Niestandardowy model języka jest dobry nie tylko w przypadku dodawania nowych słów do systemu, ale również do dostosowywania prawdopodobieństwa znanych wyrazów dla aplikacji. Zapewnienie pełnej wyrażenia długości pomaga lepiej poznać system.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Model dzierżawy (Custom Speech z danymi Microsoft 365)

**P: jakie informacje znajdują się w modelu dzierżawców i jak zostało utworzone?**

Odp **.:** Model dzierżawy jest tworzony przy użyciu [publicznych grup](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) wiadomości e-mail i dokumentów, które mogą być widoczne dla wszystkich użytkowników w organizacji.

**P: jakie środowiska mowy zostały ulepszone przez model dzierżawców?**

Odp **.:** Gdy model dzierżawy jest włączony, tworzony i publikowany, służy do ulepszania rozpoznawania dla dowolnych aplikacji przedsiębiorstwa utworzonych przy użyciu usługi Speech. to również przekazywanie użytkownikowi tokenu usługi Azure AD wskazującego na członkostwo w przedsiębiorstwie.

Funkcje mowy wbudowane w Microsoft 365, takie jak dyktowanie i podpisy programu PowerPoint, nie ulegają zmianie podczas tworzenia modelu dzierżawców dla aplikacji usługi Speech.

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów](troubleshooting.md)
- [Informacje o wersji](releasenotes.md)