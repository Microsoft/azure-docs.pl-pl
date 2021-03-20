---
title: Informacje o wersji — Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Pobierz najnowsze informacje o nowych wersjach z zespołu Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: 3724a7d515197c1f969bb051fc201b82bee64c42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97813500"
---
# <a name="custom-vision-service-release-notes"></a>Informacje o wersji Custom Vision Service

## <a name="may-2-2019-and-may-10-2019"></a>2 maja 2019 i 10 maja 2019

- Udoskonalenia poprawek i zaplecza

## <a name="may-23-2019"></a>23 maja 2019

- Udoskonalone środowisko użytkownika portalu dotyczące subskrypcji platformy Azure, ułatwiające wybieranie katalogów platformy Azure.

## <a name="april-18-2019"></a>18 kwietnia 2019 r. 

- Dodano eksport wykrywania obiektów dla zestawu Vision AI dev Kit.
- Dostosowania interfejsu użytkownika, łącznie z wyszukiwaniem projektu.

## <a name="april-3-2019"></a>3 kwietnia 2019

- Zwiększono limit liczby pól ograniczenia na obraz do 200. 
- Poprawek, łącznie z istotną aktualizacją wydajności dla modeli wyeksportowanych do TensorFlow. 

## <a name="march-26-2019"></a>26 marca 2019

- Usługa Custom Vision Service była ogólnie dostępna na platformie Azure!
- Dodano funkcję szkolenia zaawansowanego z nowym zapleczem uczenia maszynowego w celu zwiększenia wydajności, szczególnie w przypadku wymagających zestawów danych i klasyfikacji szczegółowej. Dzięki zaawansowanemu szkoleniu możesz określić budżet czasu obliczeń dla szkolenia, a Custom Vision będzie doświadczalnie identyfikować najlepsze ustawienia szkolenia i rozszerzania. W przypadku szybkich iteracji można nadal korzystać z istniejącego szybkiego szkolenia.
- Wprowadzono 3,0 interfejsów API. Ogłoszono wycofanie interfejsów API poprzedzających 3,0, 1 października 2019. Zapoznaj się z dokumentacją [przewodników szybki start](./quickstarts/image-classification.md) , aby zapoznać się z przykładami dotyczącymi rozpoczynania pracy.
- Zamieniono "domyślne iteracje" z publikowanie/cofa publikowanie w interfejsach API 3,0.
- Dodano nowe cele eksportu modelu. Eksport pliku dockerfile został uaktualniony do obsługi ARM dla Raspberry Pi 3. Obsługa eksportowania została dodana do [zestawu Vision AI Kit.](https://visionaidevkit.com/)..
- Zwiększono limit tagów na projekt do 500 dla warstwy S0. Zwiększono limit obrazów na projekt do 100 000 dla warstwy S0.
- Usunięto domenę dorosłą. Zamiast tego zaleca się używanie domeny ogólnej.
- Zapowiedziane [cenniki](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) dotyczące ogólnej dostępności.  

## <a name="february-25-2019"></a>25 lutego 2019

- Ogłoszono koniec ograniczonego okresu próbnego (projekty nieskojarzone z zasobem platformy Azure), ponieważ Custom Vision niemal zakończeniu przejścia do publicznej wersji zapoznawczej platformy Azure. Od 25 marca 2019 witryna CustomVision.ai będzie obsługiwać przeglądanie projektów skojarzonych z zasobem platformy Azure, takich jak bezpłatny Custom Vision zasobów. Do 1 października 2019 nadal będzie można uzyskiwać dostęp do istniejących projektów z ograniczoną wersją próbną za pośrednictwem interfejsów API Custom Vision. Umożliwi to czas na aktualizację kluczy interfejsu API dla wszystkich aplikacji, które zostały nagrane Custom Vision. Po 1 października 2019 wszystkie ograniczone projekty w wersji próbnej, które nie zostały przeniesione na platformę Azure, zostaną usunięte.

## <a name="january-22-2019"></a>22 stycznia 2019 r.

- Dodano wsparcie dla nowych regionów świadczenia usługi Azure: zachodnie stany USA 2, Wschodnie stany USA, Wschodnie stany USA 2, Europa Zachodnia, Europa Północna, Azja Południowo-Wschodnia, Japonia Wschodnia, Indie Środkowe, Południowe Zjednoczone Królestwo, Japonia Wschodnia i Północno-środkowe stany USA. Pomoc techniczna jest kontynuowana dla Południowo-środkowe stany USA.

## <a name="december-12-2018"></a>12 grudnia 2018

- Obsługa eksportowania modeli wykrywania obiektów (wprowadzono kompaktową domenę wykrywania obiektów).
- Rozwiązano szereg problemów z ułatwieniami dostępu dla ulepszonego czytnika ekranu i obsługi nawigacji klawiaturowej.
- Aktualizacje środowiska UX dla przeglądarki obrazów i ulepszone środowisko tagowania wykrywania obiektów umożliwiające szybsze znakowanie.  
- Zaktualizowano model podstawowy dla domeny wykrywania obiektów w celu lepszego wykrywania obiektów jakości.
- Poprawki błędów.

## <a name="november-6-2018"></a>6 listopada 2018

- Dodano obsługę domeny logo podczas wykrywania obiektu.

## <a name="october-9-2018"></a>9 października 2018 r.

- Wykrywanie obiektów wprowadza płatną wersję zapoznawczą. Teraz można tworzyć projekty wykrywania obiektów z zasobem platformy Azure.
- Dodano funkcję "Przenieś do platformy Azure" do witryny sieci Web, aby ułatwić uaktualnienie ograniczonego projektu wersji próbnej w celu połączenia z platformą Azure. projekt połączony z zasobem (F0 lub S0) Można to znaleźć na stronie Ustawienia dla danego produktu.  
- Dodano eksport do ONNX 1,2, aby obsługiwał system Windows 2018 z aktualizacją Update systemu Windows ML.
Poprawki błędów, w tym eksport ONNX z użyciem znaków specjalnych.

## <a name="august-14-2018"></a>14 sierpnia 2018

- Dodano widżet "wprowadzenie", aby customvision.ai lokację w celu obsługi użytkowników w ramach szkolenia projektu.
- Dalsze ulepszenia potoku uczenia maszynowego, aby korzystać z projektów wieloetykietowych (nowej warstwy strat).

## <a name="june-28-2018"></a>28 czerwca 2018

- Poprawki usterek & udoskonalenia zaplecza.
- Włączono klasyfikację wieloklasową dla projektów, w których obrazy mają dokładnie jedną etykietę. W przewidywaniach dla trybu wieloklasowego prawdopodobieństwa będzie sumą do jednego (wszystkie obrazy są klasyfikowane do określonych tagów).

## <a name="june-13-2018"></a>13 czerwca 2018 r.

- Odświeżanie środowiska użytkownika, ukierunkowane na łatwość użycia i ułatwienia dostępu.
- Ulepszenia potoku uczenia maszynowego, aby korzystać z projektów wieloetykietowych z dużą liczbą tagów.
- Naprawiono usterkę w eksporcie TensorFlow. Włączono przechowywanie wersji modelu, dlatego iteracje można wyeksportować więcej niż raz.

## <a name="may-7-2018"></a>7 maja 2018 r.

- Wprowadzono funkcję wykrywania obiektów w wersji zapoznawczej dla projektów w ograniczonej wersji próbnej.
- Uaktualnienie do interfejsów API 2.0
- Warstwa S0 rozwinięta do 250 tagów i 50 000 obrazów.
- Znaczne ulepszenia zaplecza w potoku uczenia maszynowego na potrzeby projektów klasyfikacji obrazów. Projekty szkolone po 27 kwietnia 2018 r. skorzystają z tych aktualizacji.
- Dodano eksportowanie modeli do ONNX na potrzeby użycia z uczeniem maszynowym systemu Windows.
- Dodano eksportowanie modeli do pliku Dockerfile. Dzięki temu można pobierać artefakty, aby tworzyć własne kontenery systemu Windows lub Linux, w tym DockerFile, model TensorFlow i kod usługi.
- W przypadku nowo przeszkolonych modeli wyeksportowanych do TensorFlow w domenach General (Compact) i dzielnic (Compact) [średnie wartości są teraz (0, 0, 0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), aby zapewnić spójność we wszystkich projektach.

## <a name="march-1-2018"></a>1 marca 2018 r.

- Wprowadzono płatną wersję zapoznawczą i dołączono ją do Azure Portal. Projekty można teraz dołączać do zasobów Azure przy użyciu warstwy F0 (Bezpłatna) lub S0 (Standardowa). Wprowadzono projekty warstwy S0, które umożliwiają użycie do 100 tagów i 25 000 obrazów.
- Zmiany w zapleczu potoku uczenia maszynowego/parametru normalizacji. Dzięki temu klienci zyskują lepszą kontrolę nad kompromisami precyzji-przywołania podczas dostosowywania progu prawdopodobieństwa. W ramach tych zmian domyślny próg prawdopodobieństwa w portalu CustomVision.ai został ustawiony na 50%.

## <a name="december-19-2017"></a>19 grudnia 2017 r.

- Eksport do systemu Android (TensorFlow) dodany, oprócz wcześniej wydanego eksportu do systemu iOS (CoreML). Pozwala to na eksportowanie przeszkolonego modelu kompaktowego do uruchomienia w trybie offline w aplikacji.
- Dodano „kompaktowe” domeny Retail i Landmark umożliwiające eksportowanie modeli do tych domen.
- Wydano wersję [1.2 interfejsu API szkolenia](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) oraz [1.1 interfejsu API przewidywania](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Zaktualizowane interfejsy API obsługują eksportowanie modeli, nową operację przewidywania, która nie zapisuje obrazów w katalogu „Prognozy”, a także wprowadzono operacje wsadowe w interfejsie API szkolenia.
- Ulepszenia środowiska użytkownika, w tym możliwość wyświetlania domen, które zostały użyte do szkolenia iteracji.
- Zaktualizowano [zestaw SDK języka C# i dane przykładowe](https://github.com/Microsoft/Cognitive-CustomVision-Windows).