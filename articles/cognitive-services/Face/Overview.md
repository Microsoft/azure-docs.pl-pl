---
title: Co to jest usługa rozpoznawania twarzy platformy Azure?
titleSuffix: Azure Cognitive Services
description: Usługa rozpoznawania twarzy platformy Azure udostępnia algorytmy sztucznej inteligencji, które są używane do wykrywania, rozpoznawania i analizowania ludzkich twarzy na obrazach.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: rozpoznawanie twarzy, oprogramowanie do rozpoznawania twarzy, analiza twarzy, dopasowywanie twarzy, aplikacja do rozpoznawania twarzy, wyszukiwanie twarzy według obrazu, wyszukiwanie rozpoznawania twarzy
ms.openlocfilehash: 30707dd844769f730f135a451cd5306ca7e402cb
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726992"
---
# <a name="what-is-the-azure-face-service"></a>Co to jest usługa rozpoznawania twarzy platformy Azure?

> [!WARNING]
> 11 czerwca 2020 roku firma Microsoft ogłosiła, że nie będzie sprzedawać technologii rozpoznawania twarzy departamentom policji w Stanach Zjednoczonych, dopóki nie zostaną wdrożone ścisłe przepisy oparte na prawach człowieka. W związku z tym klienci nie mogą korzystać z funkcji rozpoznawania twarzy zawartych w usługach platformy Azure, takich jak twarz lub Video Indexer, jeśli klient jest lub zezwala na korzystanie z takich usług przez lub dla działu Stany Zjednoczone.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Usługa rozpoznawania twarzy platformy Azure udostępnia algorytmy sztucznej inteligencji, które wykrywają, rozpoznawać i analizować ludzkie twarze na obrazach. Oprogramowanie do rozpoznawania twarzy jest ważne w wielu różnych scenariuszach, takich jak zabezpieczenia, naturalny interfejs użytkownika, analiza zawartości obrazów i zarządzanie nimi, aplikacje mobilne i robotyka.

Usługa rozpoznawania twarzy udostępnia kilka różnych funkcji analizy twarzy, które opisano w poniższych sekcjach.

Ta dokumentacja zawiera następujące typy artykułów:
* Przewodnik [Szybki start to](./Quickstarts/client-libraries.md) instrukcje krok po kroku, które pozwalają na wywołania usługi i uzyskiwanie wyników w krótkim czasie. 
* Przewodniki [z instrukcjami zawierają](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md) instrukcje dotyczące korzystania z usługi w bardziej szczegółowe lub dostosowane sposoby.
* Artykuły [koncepcyjne](./concepts/face-detection.md) zawierają szczegółowe wyjaśnienia dotyczące funkcjonalności i funkcji usługi.
* Samouczki [są](./enrollment-overview.md) dłuższymi przewodnikami, które pokazują, jak używać tej usługi jako składnika w szerszych rozwiązaniach biznesowych.

## <a name="face-detection"></a>Wykrywanie twarzy

Interfejs API wykrywania wykrywa ludzkie twarze na obrazie i zwraca współrzędne prostokąta ich lokalizacji. Opcjonalnie funkcja wykrywania twarzy może wyodrębnić szereg atrybutów związanych z twarzą, takich jak ułożenie głowy, płeć, wiek, emocje, zarost i okulary. Te atrybuty są przewidywaniami ogólnymi, a nie rzeczywistymi klasyfikacjami. 

> [!NOTE]
> Funkcja wykrywania twarzy jest również dostępna za [pośrednictwem przetwarzanie obrazów usługi](../computer-vision/overview.md). Jeśli jednak chcesz wykonać dodatkowe operacje rozpoznawania twarzy, takie jak Identyfikowanie, Weryfikowanie, Znajdowanie podobnych lub Grupa, należy zamiast tego użyć tej usługi rozpoznawania twarzy.

![Obraz kobiety i mężczyzny z prostokątami narysowanych wokół twarzy oraz wyświetlonym wieku i płci](./Images/Face.detection.jpg)

Aby uzyskać więcej informacji na temat wykrywania twarzy, zobacz [artykuł Pojęcia związane z wykrywaniem](concepts/face-detection.md) twarzy. Zobacz również dokumentację [referencyjną wykrywania interfejsu API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

## <a name="face-verification"></a>Weryfikacja twarzy

Interfejs API weryfikacji opiera się na funkcji wykrywania i odpowiada na pytanie "Czy te dwa obrazy są tej samej osoby?". Weryfikacja jest również nazywana dopasowywaniem "jeden do jednego", ponieważ obraz sondy jest porównywany tylko z jednym zarejestrowanym szablonem. Weryfikacji można użyć w scenariuszach weryfikacji tożsamości lub kontroli dostępu, aby sprawdzić, czy obraz jest taki, jak wcześniej przechwycony obraz (na przykład ze zdjęcia wystawionego przez rządową kartę identyfikatora). Aby uzyskać więcej informacji, zobacz [przewodnik po pojęciach dotyczących rozpoznawania](concepts/face-recognition.md) twarzy lub [dokumentację referencyjną weryfikacji interfejsu API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)

## <a name="face-identification"></a>Identyfikacja twarzy

Interfejs API identyfikacji rozpoczyna się również od wykrywania i odpowiada na pytanie "Czy wykrytą twarz można dopasować do dowolnej zarejestrowanej twarzy w bazie danych?". Ponieważ jest ono podobne do wyszukiwania w funkcji rozpoznawania twarzy, jest również nazywane dopasowywaniem "jeden do wielu". Dopasowania kandydatów są zwracane na podstawie tego, jak blisko szablonu sondy z wykrytą twarzą pasuje do każdego z zarejestrowanych szablonów.

Na poniższej ilustracji przedstawiono przykład bazy danych o nazwie `"myfriends"` . Każda grupa może zawierać maksymalnie 1 milion różnych obiektów osoby. Dla każdego obiektu osoby może być zarejestrowanych maksymalnie 248 twarzy.

![Siatka z trzema kolumnami dla różnych osób, z których każda ma trzy wiersze obrazów twarzy](./Images/person.group.clare.jpg)

Po utworzeniu i wytębniu bazy danych można wykonać identyfikację grupy za pomocą nowej wykrytej twarzy. Jeśli twarz zostanie zidentyfikowana jako należąca do osoby w grupie, zostanie zwrócony obiekt osoby.

Aby uzyskać więcej informacji na temat identyfikacji osób, zobacz Przewodnik po pojęciach [dotyczących](concepts/face-recognition.md) rozpoznawania twarzy lub [dokumentację referencyjną interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) identyfikacji.

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Interfejs API wyszukiwania podobnych twarzy umożliwia dopasowywanie twarzy między docelową i zestawem twarzy do wyboru, znajdując mniejszy zestaw twarzy, które wyglądają podobnie do twarzy docelowej. Ta operacja jest przydatna do wyszukiwania twarzy według obrazu. 

Obsługiwane są dwa **tryby pracy: matchPerson** i **matchFace.** Tryb **matchPerson zwraca** podobne twarze po filtrowaniu dla tej samej osoby przy użyciu interfejsu [API weryfikacji](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). Tryb **matchFace** ignoruje filtr tej samej osoby. Zwraca listę podobnych twarzy, które mogą lub nie należeć do tej samej osoby.

W poniższym przykładzie przedstawiono twarz docelową:

![Uśmiechnięta kobieta](./Images/FaceFindSimilar.QueryFace.jpg)

A te obrazy są twarzami kandydacyjnie:

![Pięć obrazów uśmiechniętych osób. Obrazy a i b pokazują tę samą osobę.](./Images/FaceFindSimilar.Candidates.jpg)

Aby znaleźć cztery podobne twarze, tryb **matchPerson** zwraca wartości a i b, które pokazują tę samą osobę co twarz docelowa. Tryb **matchFace** zwraca dokładnie cztery kandydaci z wartościami a, b, c i d, nawet jeśli niektóre z nich nie są tym samym elementem docelowym lub mają &mdash; niewielkie podobieństwo. Aby uzyskać więcej informacji, zobacz [przewodnik po pojęciach dotyczących rozpoznawania](concepts/face-recognition.md) twarzy lub [dokumentację interfejsu API Znajdź podobne.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)

## <a name="face-grouping"></a>Grupowanie twarzy

Interfejs API grupowania dzieli zestaw nieznanych twarzy na kilka grup na podstawie podobieństwa. Każda grupa jest odpowiednim rozłącznym podzestawem oryginalnego zestawu twarzy. Wszystkie twarze w grupie prawdopodobnie będą należeć do tej samej osoby. Dla jednej osoby może być kilka różnych grup. Grupy różnią się innym czynnikiem, takim jak na przykład wyrażenie. Aby uzyskać więcej informacji, zobacz [przewodnik po pojęciach dotyczących rozpoznawania](concepts/face-recognition.md) twarzy lub dokumentację [interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) grupy.


## <a name="sample-apps"></a>Przykładowe aplikacje

Następujące przykładowe aplikacje pokazują kilka sposobów korzystania z usługi rozpoznawania twarzy:

- [Interfejs API rozpoznawania twarzy: Biblioteka](https://github.com/Microsoft/Cognitive-Face-Windows) klienta systemu Windows i przykład to aplikacja WPF, która demonstruje kilka scenariuszy wykrywania, analizy i identyfikacji twarzy.
- [Aplikacja FamilyNotes platformy uniwersalnej](https://github.com/Microsoft/Windows-appsample-familynotes) systemu Windows platforma uniwersalna systemu Windows (UWP), która korzysta z funkcji identyfikacji twarzy oraz mowy, Cortany, pisma odręcznego i aparatu w scenariuszu udostępniania notatek w rodzinie.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich Cognitive Services, deweloperzy, którzy korzystają z usługi rozpoznawania twarzy, muszą mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Aby uzyskać więcej informacji, zobacz [stronę Cognitive Services w](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) Centrum zaufania firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Wykonaj czynności z przewodnika Szybki start, aby zakodować podstawowe składniki aplikacji do rozpoznawania twarzy w wybranego przez Ciebie języku.

- [Przewodnik Szybki start dla biblioteki klienta .](quickstarts/client-libraries.md)
