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
ms.openlocfilehash: 600ca48cc19ee8723b423e484ec96736a55ae7fc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95532260"
---
# <a name="what-is-the-azure-face-service"></a>Co to jest usługa Azure Front?

> [!WARNING]
> 11 czerwca 2020 roku firma Microsoft ogłosiła, że nie będzie sprzedawać technologii rozpoznawania twarzy departamentom policji w Stanach Zjednoczonych, dopóki nie zostaną wdrożone ścisłe przepisy oparte na prawach człowieka. W związku z tym klienci nie mogą korzystać z funkcji rozpoznawania twarzy ani funkcji dostępnych w ramach usług platformy Azure, takich jak twarzy lub Video Indexer, jeśli klient jest lub zezwala na korzystanie z takich usług przez lub w odniesieniu do działu policji w Stany Zjednoczone.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Usługa Azure twarzy oferuje algorytmy AI, które wykrywają, rozpoznajeją i analizują ludzkie twarze na obrazach. Oprogramowanie do rozpoznawania twarzy jest ważne w wielu różnych scenariuszach, takich jak zabezpieczenia, interfejs użytkownika naturalnego, analiza zawartości obrazów i zarządzanie nimi, aplikacje mobilne i programy Robotic.

Usługa twarzy oferuje kilka różnych funkcji analizy twarzy, które są opisane w poniższych sekcjach.

## <a name="face-detection"></a>Wykrywanie twarzy

Usługa twarzy wykrywa ludzkie twarze na obrazie i zwraca współrzędne ich lokalizacji. Opcjonalnie wykrywanie twarzy może wyodrębnić serię atrybutów związanych z twarzą, takich jak ułożenia głowy, płeć, wiek, rozpoznawania emocji, twarzy i szklanki.

> [!NOTE]
> Funkcja wykrywania kroju jest również dostępna za pomocą [usługi przetwarzanie obrazów](../computer-vision/overview.md). Jeśli jednak chcesz wykonać dalsze operacje przy użyciu danych dotyczących kroju, Użyj tej usługi zamiast tego.

![Obraz kobieta i Man, z prostokątami rysowanymi wokół ich powierzchni i wieku oraz wyświetlanych płci](./Images/Face.detection.jpg)

Aby uzyskać więcej informacji o wykrywaniu czołowym, zobacz artykuł dotyczący pojęć dotyczących [wykrywania](concepts/face-detection.md) . Zapoznaj się również z dokumentacją dotyczącą [wykrywania interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

## <a name="face-verification"></a>Weryfikacja twarzy

Sprawdź, czy interfejs API wykonuje uwierzytelnianie w odniesieniu do dwóch wykrytych twarzy lub od jednej wykrytych funkcji do jednego obiektu osoby. W praktyce ocenia on, czy dwie twarze należą do tej samej osoby. Ta funkcja jest potencjalnie przydatna w scenariuszach zabezpieczeń. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący koncepcji [rozpoznawania twarzy](concepts/face-recognition.md) lub dokumentacja dotycząca [weryfikowania interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) .

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

## <a name="person-identification"></a>Identyfikacja osoby

Identyfikuj interfejs API służy do identyfikowania wykrytej twarzy względem bazy danych osób (wyszukiwanie rozpoznawania twarzy). Ta funkcja może być przydatna w przypadku automatycznego tagowania obrazów w oprogramowaniu do zarządzania fotografiami. Baza danych jest tworzona z góry i można ją edytować w miarę upływu czasu.

Na poniższej ilustracji przedstawiono przykład bazy danych o nazwie `"myfriends"` . Każda grupa może zawierać maksymalnie 1 000 000 różnych obiektów osób. Dla każdego obiektu osoby może być zarejestrowanych maksymalnie 248 twarzy.

![Siatka z trzema kolumnami dla różnych osób, z których każdy ma trzy wiersze obrazów](./Images/person.group.clare.jpg)

Po utworzeniu i przeprowadzeniu uczenia bazy danych można przeprowadzić identyfikację grupy z nową wykrytą stroną. Jeśli twarz zostanie zidentyfikowana jako należąca do osoby w grupie, zostanie zwrócony obiekt osoby.

Aby uzyskać więcej informacji na temat identyfikacji osób, zobacz Przewodnik dotyczący pojęć dotyczących [rozpoznawania twarzy](concepts/face-recognition.md) lub dokumentację dotyczącą [identyfikacji interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="deploy-on-premises-using-docker-containers"></a>Wdrażanie lokalnie przy użyciu kontenerów platformy Docker

[Użyj kontenera funkcji Front (wersja zapoznawcza),](face-how-to-install-containers.md) aby wdrożyć funkcje interfejsu API lokalnie. Ten kontener platformy Docker umożliwia przełączenie usługi do danych pod kątem zgodności, zabezpieczeń lub innych przyczyn operacyjnych.

## <a name="sample-apps"></a>Przykładowe aplikacje

Następujące przykładowe aplikacje pokazują kilka sposobów korzystania z usługi kroju:

- [Interfejs API rozpoznawania twarzy: Biblioteka kliencka systemu Windows i przykład](https://github.com/Microsoft/Cognitive-Face-Windows) to aplikacja WPF, która ilustruje kilka scenariuszy wykrywania, analizy i identyfikacji na platformie.
- [FAMILYNOTES platformy UWP App](https://github.com/Microsoft/Windows-appsample-familynotes) to aplikacja platforma uniwersalna systemu Windows (platformy UWP), która korzysta z identyfikacji czołowej oraz mowy, Cortany, atramentu i kamery w scenariuszu udostępniania notatek z rodziny.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich zasobów Cognitive Services, deweloperzy korzystający z usługi kroju muszą mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Aby uzyskać więcej informacji, zobacz [stronę Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w centrum zaufania firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Skorzystaj z przewodnika Szybki Start, aby zakodować podstawowe składniki aplikacji rozpoznawania kroju w wybranym języku.

- [Biblioteka kliencka — szybki start](quickstarts/client-libraries.md).