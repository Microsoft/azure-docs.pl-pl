---
title: Przegląd rozpoznawanie osoby mówiącej — usługa mowy
titleSuffix: Azure Cognitive Services
description: Rozpoznawanie osoby mówiącej zapewnia algorytmy, które weryfikują i identyfikują głośniki według ich unikatowych cech głosowych przy użyciu usługi Voice biometry. Rozpoznawanie osoby mówiącej jest używany do odpowiedzi na pytanie "kto mówi?". Ten artykuł zawiera omówienie zalet i możliwości usługi rozpoznawanie osoby mówiącej.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: Rozpoznawanie osoby mówiącej, biometry głosu
ms.openlocfilehash: 2c5b73b93c22ef27d7b68455f5e1e5108f25c984
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397263"
---
# <a name="what-is-speaker-recognition"></a>Co to jest rozpoznawanie osoby mówiącej?

Usługa rozpoznawanie osoby mówiącej zapewnia algorytmy, które weryfikują i identyfikują głośniki według ich unikatowych cech głosowych przy użyciu usługi Voice biometry. Rozpoznawanie osoby mówiącej jest używany do odpowiedzi na pytanie "kto mówi?". Dane szkolenia audio można podawać dla jednego głośnika, który tworzy profil rejestracji na podstawie unikatowych cech głosu osoby mówiącej. Następnie można sprawdzić krzyżowo próbki głosu audio dla tego profilu, aby sprawdzić, czy prelegent jest tą samą osobą (Weryfikacja osoby mówiącej), czy też sprawdzić krzyżowo próbki głosu audio w *grupie* zarejestrowanych profilów głośników, aby sprawdzić, czy pasuje do dowolnego profilu w grupie (tożsamość osoby mówiącej). W odróżnieniu od [Diarizationu głośników](batch-transcription.md#speaker-separation-diarization) grupuje segmenty dźwięku przez prelegenta w operacji wsadowej.

## <a name="speaker-verification"></a>Weryfikacja osoby mówiącej

Weryfikacja osoby mówiącej usprawnia proces weryfikowania zarejestrowanej tożsamości osoby mówiącej za pomocą hasła lub dowolnego głosu. Może służyć do weryfikowania osób w celu bezpiecznego, bezproblemowego zaangażowania klientów w szeroką gamę rozwiązań, od weryfikacji tożsamości klientów w centrach wywołań w celu uzyskania dostępu do funkcji bez konieczności kontaktowania się z usługą.

### <a name="how-does-speaker-verification-work"></a>Jak działa weryfikacja osoby mówiącej?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Weryfikacja osoby mówiącej Flowchart.":::

Weryfikacja osoby mówiącej może być zależna od tekstu lub niezależna od tekstu. Weryfikacja **zależna od tekstu** oznacza, że głośniki muszą wybrać takie samo hasło, które ma być używane podczas faz rejestracji i weryfikacji. Weryfikacja **niezależna od tekstu** oznacza, że głośniki mogą mówić w codziennym języku w zwrotach rejestracyjnych i weryfikacyjnych.

W przypadku weryfikacji **zależnej od tekstu** głos osoby mówiącej jest rejestrowany przez wymawianie hasła z zestawu wstępnie zdefiniowanych fraz. Funkcje głosu są wyodrębniane z nagrania audio w celu utworzenia unikatowej sygnatury głosowej, podczas gdy wybrane hasło jest również rozpoznawane. Razem, podpis głosu i hasło są używane do weryfikowania prelegenta. 

Weryfikacja **niezależna od tekstu** nie ma żadnych ograniczeń dotyczących tego, co informuje prelegenta podczas rejestracji, lub próbki audio do zweryfikowania, ponieważ wyodrębnia tylko funkcje głosu do oceny podobieństwa. 

Interfejsy API nie mają na celu określenie, czy dźwięk pochodzi od osoby działającej na żywo, czy przez imitację/nagranie zarejestrowanego głośnika. 

## <a name="speaker-identification"></a>Identyfikacja osoby mówiącej

Identyfikacja osoby mówiącej służy do określenia nieznanej tożsamości osoby mówiącej w grupie zarejestrowanych głośników. Identyfikacja osoby mówiącej pozwala na podzielanie mowy na poszczególne głośniki i odblokowywanie wartości ze scenariuszy z wieloma głośnikami, takich jak:

* Obsługa rozwiązań związanych z produktywnością spotkań zdalnych 
* Tworzenie personalizacji urządzenia z obsługą kilku użytkowników

### <a name="how-does-speaker-identification-work"></a>Jak działa identyfikacja osoby mówiącej?

Rejestracja w celu identyfikacji osoby mówiącej jest **niezależna od tekstu**, co oznacza, że nie ma żadnych ograniczeń dotyczących tego, co mówi w dźwięku. Podobnie jak w przypadku weryfikacja osoby mówiącej, w fazie rejestracji głos osoby mówiącej jest rejestrowany, a funkcje głosu są wyodrębniane w celu utworzenia unikatowej sygnatury głosowej. W fazie identyfikacji wejściowa próbka głosu jest porównywana z określoną listą zarejestrowanych głosów (do 50 w poszczególnych żądaniach).

## <a name="data-security-and-privacy"></a>Bezpieczeństwo i prywatność danych

Dane rejestracyjne osoby mówiącej są przechowywane w zabezpieczonym systemie, w tym dźwięk mowy do rejestracji i funkcje podpisów głosowych. Dźwięk mowy do rejestracji jest używany tylko wtedy, gdy algorytm jest uaktualniany, a funkcje muszą zostać wyodrębnione ponownie. Usługa nie zachowuje nagrania mowy ani wyodrębnionych funkcji głosowych wysyłanych do usługi podczas fazy rozpoznawania. 

Określasz, jak długo mają być przechowywane dane. Można tworzyć, aktualizować i usuwać dane rejestracji dla poszczególnych głośników za poorednictwem wywołań interfejsu API. Usunięcie subskrypcji spowoduje również usunięcie wszystkich danych dotyczących rejestracji głośników skojarzonych z subskrypcją. 

Podobnie jak w przypadku wszystkich zasobów Cognitive Services, deweloperzy korzystający z usługi rozpoznawanie osoby mówiącej muszą mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Upewnij się, że zostały odebrane odpowiednie uprawnienia od użytkowników do rozpoznawanie osoby mówiącej. Aby uzyskać więcej informacji, zobacz [stronę Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   w centrum zaufania firmy Microsoft. 

## <a name="common-questions-and-solutions"></a>Często zadawane pytania i rozwiązania

| Pytanie | Rozwiązanie |
|---------|----------|
| Jakie scenariusze mogą rozpoznawanie osoby mówiącej być używane? | Wywołaj centrum weryfikacja klienta, ewidencjonowanie głosu oparte na głosach, transkrypcja spotkań, Personalizacja urządzenia przez wiele użytkowników|
| Jaka jest różnica między identyfikacją i weryfikacją? | Identyfikacja jest procesem wykrywania, który element członkowski z grupy głośników mówi. Weryfikacja jest czynnością potwierdzającą, że prelegent pasuje do znanego lub **zarejestrowanego** głosu.|
| Jaka jest różnica między weryfikacją tekstową i niezależną od tekstu? | Weryfikacja zależna od tekstu wymaga określonego zdania do rejestracji i rozpoznawania. Weryfikacja niezależna od tekstu wymaga dłuższego przykładu głosu do rejestracji, ale wszystkie elementy mogą być wymawiane, w tym podczas rozpoznawania.|
| Jakie języki są obsługiwane? | Angielski, francuski, hiszpański, chiński, niemiecki, włoski, japoński i portugalski |
| Jakie regiony platformy Azure są obsługiwane? | Rozpoznawanie osoby mówiącej to usługa w wersji zapoznawczej, która jest obecnie dostępna tylko w regionie zachodnie stany USA.|
| Jakie formaty dźwięku są obsługiwane? | Mono 16-bitowe, 16kHz w formacie WAV |
| **Akceptowanie** i **odrzucanie** odpowiedzi nie jest dokładne, jak dostosować próg? | Ze względu na to, że optymalny próg różni się w zależności od scenariuszy, interfejs API decyduje o tym, czy "Akceptuję" czy "odrzucić", w oparciu o domyślny próg 0,5. Zaawansowani użytkownicy powinni zastąpić domyślną decyzję, a następnie dostosować wynik na podstawie własnego scenariusza. |
| Czy można wielokrotnie rejestrować jednego prelegenta? | Tak, w przypadku weryfikacji zależnej od tekstu możesz zarejestrować prelegenta do 50 razy. W przypadku weryfikacji niezależnej od tekstu lub identyfikacji osoby mówiącej możesz zarejestrować się nawet przez 300 sekund audio. |
| Jakie dane są przechowywane na platformie Azure? | Dźwięk rejestracyjny jest przechowywany w usłudze do momentu [usunięcia](speaker-recognition-basics.md#deleting-voice-profile-enrollments)profilu głosowego. Próbki audio rozpoznawania nie są zachowywane ani przechowywane. |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> * Zapoznaj się z [artykułem podstawowe](speaker-recognition-basics.md) rozpoznawanie osoby mówiącej, aby zapoznać się z typowymi wzorcami projektowymi, których można używać w aplikacjach.
> * Zapoznaj się z [samouczkiem](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) dotyczącym weryfikacji niezależnych od tekstu.
