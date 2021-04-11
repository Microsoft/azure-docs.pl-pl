---
title: Co to jest usługa Azure Front?
titleSuffix: Azure Cognitive Services
description: Usługa Azure twarzy zapewnia algorytmy AI używane do wykrywania, rozpoznawania i analizowania ludzkich twarzy na obrazach.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: rozpoznawanie twarzy, oprogramowanie do rozpoznawania twarzy, analiza twarzy, dopasowanie twarzy, aplikacja do rozpoznawania twarzy, wyszukiwanie twarzy według obrazu, wyszukiwanie twarzy
ms.openlocfilehash: 26076289d8c6659abdd55fa805c27b13690feccd
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258745"
---
# <a name="what-is-the-azure-face-service"></a>Co to jest usługa Azure Front?

> [!WARNING]
> 11 czerwca 2020 roku firma Microsoft ogłosiła, że nie będzie sprzedawać technologii rozpoznawania twarzy departamentom policji w Stanach Zjednoczonych, dopóki nie zostaną wdrożone ścisłe przepisy oparte na prawach człowieka. W związku z tym klienci nie mogą korzystać z funkcji rozpoznawania twarzy ani funkcji dostępnych w ramach usług platformy Azure, takich jak twarzy lub Video Indexer, jeśli klient jest lub zezwala na korzystanie z takich usług przez lub w odniesieniu do działu policji w Stany Zjednoczone.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Usługa Azure twarzy oferuje algorytmy AI, które wykrywają, rozpoznajeją i analizują ludzkie twarze na obrazach. Oprogramowanie do rozpoznawania twarzy jest ważne w wielu różnych scenariuszach, takich jak zabezpieczenia, interfejs użytkownika naturalnego, analiza zawartości obrazów i zarządzanie nimi, aplikacje mobilne i programy Robotic.

Usługa twarzy oferuje kilka różnych funkcji analizy twarzy, które są opisane w poniższych sekcjach.

Ta dokumentacja zawiera następujące typy artykułów:
* [Przewodniki Szybki Start](./Quickstarts/client-libraries.md) to instrukcje krok po kroku, które umożliwiają wykonywanie wywołań do usługi i uzyskiwanie wyników w krótkim czasie. 
* [Przewodniki z](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md) instrukcjami zawiera instrukcje dotyczące korzystania z usługi w bardziej specyficzny lub dostosowany sposób.
* [Artykuły koncepcyjne](./concepts/face-detection.md) zawierają szczegółowe omówienie funkcji i funkcji usługi.
* [Samouczki](./Tutorials/FaceAPIinCSharpTutorial.md) są więcej przewodnikami, które pokazują, jak używać tej usługi jako składnika w szerszym zakresie rozwiązań dla biznesu.

## <a name="face-detection"></a>Wykrywanie twarzy

Interfejs API wykrywania wykrywa ludzkie twarze na obrazie i zwraca współrzędne ich lokalizacji. Opcjonalnie wykrywanie twarzy może wyodrębnić serię atrybutów związanych z twarzą, takich jak ułożenia głowy, płeć, wiek, rozpoznawania emocji, twarzy i szklanki. Te atrybuty to ogólne przewidywania, a nie rzeczywiste klasyfikacje. 

> [!NOTE]
> Funkcja wykrywania kroju jest również dostępna za pomocą [usługi przetwarzanie obrazów](../computer-vision/overview.md). Jeśli jednak chcesz wykonywać dalsze operacje na funkcjach, takich jak identyfikowanie, weryfikowanie, Znajdowanie podobnych lub grupowanie, należy używać tej usługi w zamian.

![Obraz kobieta i Man, z prostokątami rysowanymi wokół ich powierzchni i wieku oraz wyświetlanych płci](./Images/Face.detection.jpg)

Aby uzyskać więcej informacji o wykrywaniu czołowym, zobacz artykuł dotyczący pojęć dotyczących [wykrywania](concepts/face-detection.md) . Zapoznaj się również z dokumentacją dotyczącą [wykrywania interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

## <a name="face-verification"></a>Weryfikacja twarzy

Sprawdź, czy interfejs API kompiluje się przy wykrywaniu i rozwiązuje pytanie "czy te dwa obrazy należą do tej samej osoby?". Weryfikacja jest również nazywana dopasowaniem "jeden do jednego", ponieważ obraz sondy jest porównywany tylko z jednym zarejestrowanym szablonem. Weryfikacja może być używana w scenariuszach weryfikacji tożsamości lub kontroli dostępu, aby sprawdzić, czy obraz pasuje do wcześniej przechwyconego obrazu (na przykład ze zdjęcia z karty identyfikatora wystawionego przez rząd). Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący koncepcji [rozpoznawania twarzy](concepts/face-recognition.md) lub dokumentacja dotycząca [weryfikowania interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) .

## <a name="face-identification"></a>Identyfikacja twarzy

Identyfikuj interfejs API również zaczyna się od wykrywania i odpowiada na pytanie "czy ta wykryta funkcja może być dopasowana do dowolnej zarejestrowanej platformy w bazie danych?". Ponieważ jest to podobne do wyszukiwania rozpoznawania, jest również nazywane dopasowywaniem "jeden do wielu". Dopasowania kandydatów są zwracane w zależności od tego, jak blisko szablon sondy z wykrytą stroną jest zgodny z każdym zarejestrowanym szablonem.

Na poniższej ilustracji przedstawiono przykład bazy danych o nazwie `"myfriends"` . Każda grupa może zawierać maksymalnie 1 000 000 różnych obiektów osób. Dla każdego obiektu osoby może być zarejestrowanych maksymalnie 248 twarzy.

![Siatka z trzema kolumnami dla różnych osób, z których każdy ma trzy wiersze obrazów](./Images/person.group.clare.jpg)

Po utworzeniu i przeprowadzeniu uczenia bazy danych można przeprowadzić identyfikację grupy z nową wykrytą stroną. Jeśli twarz zostanie zidentyfikowana jako należąca do osoby w grupie, zostanie zwrócony obiekt osoby.

Aby uzyskać więcej informacji na temat identyfikacji osób, zobacz Przewodnik dotyczący pojęć dotyczących [rozpoznawania twarzy](concepts/face-recognition.md) lub dokumentację dotyczącą [identyfikacji interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Interfejs API Znajdź podobny dopasowuje dopasowanie między powierzchnią docelową a zestawem kandydatów, co umożliwia znalezienie mniejszego zestawu twarzy, który wygląda podobnie do powierzchni docelowej. Jest to przydatne do przeszukiwania kroju na podstawie obrazu. 

Obsługiwane są dwa tryby pracy, **matchPerson** i **matchFace**. Tryb **matchPerson** zwraca podobne twarze po filtrowaniu dla tej samej osoby za pomocą narzędzia [verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Tryb **matchFace** ignoruje ten sam filtr osoby. Zwraca listę podobnych powierzchni kandydujących, które mogą lub nie należą do tej samej osoby.

W poniższym przykładzie pokazano tarczę docelową:

![Uśmiechnięta kobieta](./Images/FaceFindSimilar.QueryFace.jpg)

Obrazy te są następujące:

![Pięć obrazów uśmiechniętych osób. Obrazy a i b przedstawiają tę samą osobę.](./Images/FaceFindSimilar.Candidates.jpg)

Aby znaleźć cztery podobne twarze, tryb **matchPerson** zwraca a i b, które przedstawiają tę samą osobę co tarczę docelową. Tryb **matchFace** zwraca cztery kandydatów, b, c i d, &mdash; nawet jeśli niektórzy nie są tej samej osoby, co obiekt docelowy lub mają niską podobieństwo. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący koncepcji [rozpoznawania twarzy](concepts/face-recognition.md) lub dokumentacja [Znajdź podobne interfejsy API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) .

## <a name="face-grouping"></a>Grupowanie twarzy

Interfejs API grupowania dzieli zestaw nieznanych twarzy na kilka grup na podstawie podobieństwa. Każda grupa jest odpowiednim rozłącznym podzestawem oryginalnego zestawu twarzy. Wszystkie powierzchnie w grupie mogą należeć do tej samej osoby. Dla jednej osoby może istnieć kilka różnych grup. Grupy są odróżniane według innego czynnika, takiego jak wyrażenie, na przykład. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący pojęć dotyczących [rozpoznawania twarzy](concepts/face-recognition.md) lub dokumentacja [interfejsu API grupy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) .


## <a name="sample-apps"></a>Przykładowe aplikacje

Następujące przykładowe aplikacje pokazują kilka sposobów korzystania z usługi kroju:

- [Face API: Biblioteka kliencka systemu Windows i przykład](https://github.com/Microsoft/Cognitive-Face-Windows) to aplikacja WPF, która ilustruje kilka scenariuszy wykrywania, analizy i identyfikacji na platformie.
- [FAMILYNOTES platformy UWP App](https://github.com/Microsoft/Windows-appsample-familynotes) to aplikacja platforma uniwersalna systemu Windows (platformy UWP), która korzysta z identyfikacji czołowej oraz mowy, Cortany, atramentu i kamery w scenariuszu udostępniania notatek z rodziny.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich zasobów Cognitive Services, deweloperzy korzystający z usługi kroju muszą mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Aby uzyskać więcej informacji, zobacz [stronę Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w centrum zaufania firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Skorzystaj z przewodnika Szybki Start, aby zakodować podstawowe składniki aplikacji rozpoznawania kroju w wybranym języku.

- [Biblioteka kliencka — szybki start](quickstarts/client-libraries.md).
