---
title: Co to jest usługa rozpoznawania twarzy?
titleSuffix: Azure Cognitive Services
description: Usługa Azure Cognitive Services Face udostępnia algorytmy, które są używane do wykrywania, rozpoznawania i analizowania ludzkich twarzy na obrazach.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 6714a0c4b967d80ad683ef023b5811423bdcb022
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403467"
---
# <a name="what-is-the-azure-face-service"></a>Co to jest usługa Azure Face?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Usługa Azure Cognitive Services Face udostępnia algorytmy, które wykrywają, rozpoznają i analizują ludzkie twarze na obrazach. Możliwość przetwarzania informacji o ludzkiej twarzy jest ważna w wielu różnych scenariuszach oprogramowania. Przykładowe scenariusze obejmują zabezpieczenia, naturalny interfejs użytkownika, analizę zawartości obrazu i zarządzanie nimi, aplikacje mobilne i robotykę.

Usługa Face udostępnia kilka różnych funkcji, z których każda przedstawiona jest w poniższych sekcjach.

## <a name="face-detection"></a>Wykrywanie twarzy

Usługa Twarz wykrywa ludzkie twarze na obrazie i zwraca współrzędne prostokąta ich lokalizacji. Opcjonalnie wykrywanie twarzy można wyodrębnić szereg atrybutów związanych z twarzą. Przykładami są pozy głowy, płeć, wiek, emocje, zarost i okulary.

> [!NOTE]
> Funkcja wykrywania twarzy jest również dostępna za pośrednictwem [interfejsu API przetwarzania obrazów.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) Jeśli chcesz wykonać dalsze operacje z danymi twarzy, użyj usługi Face, która jest usługą omówione w tym artykule.

![Wyświetlany jest wizerunek kobiety i mężczyzny, z prostokątami narysowanymi wokół ich twarzy oraz wieku i płci](./Images/Face.detection.jpg)

Aby uzyskać więcej informacji na temat wykrywania twarzy, zobacz pojęcia [dotyczące wykrywania twarzy.](concepts/face-detection.md) Zobacz też dokumentację odwołania [do interfejsu API wykrywania.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

## <a name="face-verification"></a>Weryfikacja twarzy

Verify API wykonuje uwierzytelnianie względem dwóch wykrytych twarzy lub z jednej wykrytej twarzy do obiektu jednej osoby. W praktyce ocenia on, czy dwie twarze należą do tej samej osoby. Ta funkcja jest potencjalnie przydatna w scenariuszach zabezpieczeń. Aby uzyskać więcej informacji, zobacz przewodnik po pojęciach [rozpoznawania twarzy](concepts/face-recognition.md) lub dokumentację odwołania do interfejsu [API weryfikuj.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Interfejs API Znajdź podobne porównuje twarz docelową z zestawem twarzy kandydatów, aby znaleźć mniejszy zestaw ścian, które wyglądają podobnie do ściany docelowej. Obsługiwane są dwa tryby pracy, matchPerson i matchFace. Tryb matchPerson zwraca podobne twarze po filtrowaniu dla tej samej osoby przy użyciu [interfejsu Api Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Tryb matchFace ignoruje filtr tej samej osoby. Zwraca listę podobnych twarzy kandydatów, które mogą lub nie mogą należeć do tej samej osoby.

Poniższy przykład przedstawia twarz docelową:

![Uśmiechnięta kobieta](./Images/FaceFindSimilar.QueryFace.jpg)

I te obrazy są twarze kandydata:

![Pięć obrazów uśmiechniętych osób. Obrazy a i b pokazują tę samą osobę.](./Images/FaceFindSimilar.Candidates.jpg)

Aby znaleźć cztery podobne ściany, matchPerson tryb zwraca a i b, które pokazują tę samą osobę jako twarz docelową. Tryb matchFace zwraca a, b,&mdash;c i d dokładnie czterech kandydatów, nawet jeśli niektórzy nie są tą samą osobą co cel lub mają niskie podobieństwo. Aby uzyskać więcej informacji, zobacz przewodnik pojęć [rozpoznawania twarzy](concepts/face-recognition.md) lub znajdź podobną dokumentację [interfejsu API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)

## <a name="face-grouping"></a>Grupowanie twarzy

Interfejs API grupowania dzieli zestaw nieznanych twarzy na kilka grup na podstawie podobieństwa. Każda grupa jest odpowiednim rozłącznym podzestawem oryginalnego zestawu twarzy. Wszystkie twarze w grupie mogą należeć do tej samej osoby. Może istnieć kilka różnych grup dla jednej osoby. Grupy są zróżnicowane przez inny czynnik, takich jak wyrażenie, na przykład. Aby uzyskać więcej informacji, zobacz przewodnik po pojęciach [rozpoznawania twarzy](concepts/face-recognition.md) lub dokumentację referencyjną interfejsu [API grupy.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)

## <a name="person-identification"></a>Identyfikacja osoby

Interfejs API identyfikacji służy do identyfikowania wykrytej twarzy w bazie danych osób. Ta funkcja może być przydatna do automatycznego oznaczania obrazów w oprogramowaniu do zarządzania zdjęciami. Baza danych jest tworzę łajda z wyprzedzeniem i można ją edytować w czasie.

Na poniższej ilustracji przedstawiono `"myfriends"`przykład bazy danych o nazwie . Każda grupa może zawierać maksymalnie 1 milion obiektów innej osoby. Dla każdego obiektu osoby może być zarejestrowanych maksymalnie 248 twarzy.

![Siatka z trzema kolumnami dla różnych osób, każda z trzema rzędami obrazów twarzy](./Images/person.group.clare.jpg)

Po utworzeniu i przeszkoleniu bazy danych można wykonać identyfikację grupy za pomocą nowej wykrytej twarzy. Jeśli twarz zostanie zidentyfikowana jako należąca do osoby w grupie, zostanie zwrócony obiekt osoby.

Aby uzyskać więcej informacji na temat identyfikacji osoby, zobacz przewodnik pojęć [rozpoznawania twarzy](concepts/face-recognition.md) lub dokumentację referencyjną [identyfikowanie interfejsu API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="containers"></a>Containers

[Użyj kontenera Twarz,](face-how-to-install-containers.md) aby wykrywać, rozpoznawać i identyfikować twarze, instalując standardowy kontener platformy Docker bliżej danych.

## <a name="sample-apps"></a>Przykładowe aplikacje

Następujące przykładowe aplikacje przedstawiają kilka sposobów korzystania z usługi Face:

- [Interfejs API rozpoznawania twarzy: Biblioteka klienta systemu Windows i przykład jest](https://github.com/Microsoft/Cognitive-Face-Windows) aplikacją WPF, która pokazuje kilka scenariuszy wykrywania twarzy, analizy i identyfikacji.
- [Aplikacja FamilyNotes UWP](https://github.com/Microsoft/Windows-appsample-familynotes) to aplikacja platformy uniwersalnej systemu Windows (UWP), która używa identyfikacji twarzy wraz z mową, Cortaną, atramentem i kamerą w scenariuszu udostępniania notatek rodzinnych.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich zasobów usług Cognitive Services, deweloperzy korzystający z usługi Face muszą być świadomi zasad firmy Microsoft dotyczących danych klientów. Aby uzyskać więcej informacji, zobacz [stronę Usługi Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z przewodnikiem Szybki start, aby zaimplementować scenariusz wykrywania twarzy w kodzie:

- [Szybki start: wykrywanie twarzy na obrazie przy użyciu pliku .NET SDK z c#](quickstarts/csharp.md). Dostępne są inne języki.
