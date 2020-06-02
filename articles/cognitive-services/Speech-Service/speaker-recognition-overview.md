---
title: Usługa rozpoznawanie osoby mówiącej platformy Azure
titleSuffix: Azure Cognitive Services
description: Usługa Azure Cognitive Services rozpoznawanie osoby mówiącej udostępnia algorytmy weryfikujące i identyfikujące głośniki przy użyciu ich unikatowych cech głosowych. Rozpoznawanie osoby mówiącej jest używany do odpowiedzi na pytanie "kto mówi?".
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
ms.openlocfilehash: 2d4ce6f274efbd4d8afe2ac48856b0fc312f0a09
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261787"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>Co to jest usługa Azure rozpoznawanie osoby mówiącej?

Usługa rozpoznawanie osoby mówiącej zapewnia algorytmy weryfikujące i identyfikujące głośniki przy użyciu ich unikatowych cech głosowych. Rozpoznawanie osoby mówiącej jest używany do odpowiedzi na pytanie "kto mówi?". Dane szkolenia audio można podawać dla jednego głośnika, który tworzy profil rejestracji na podstawie unikatowych cech głosu osoby mówiącej. Następnie można sprawdzić krzyżowo próbki głosu audio dla tego profilu, aby sprawdzić, czy prelegent jest tą samą osobą (Weryfikacja osoby mówiącej), czy też sprawdzić krzyżowo próbki głosu audio w *grupie* zarejestrowanych profilów głośników, aby sprawdzić, czy pasuje do dowolnego profilu w grupie (tożsamość osoby mówiącej). W odróżnieniu od [Diarizationu głośników](batch-transcription.md#speaker-separation-diarization) grupuje segmenty dźwięku przez prelegenta w operacji wsadowej.

## <a name="speaker-verification"></a>Weryfikacja osoby mówiącej

Weryfikacja osoby mówiącej usprawnia proces weryfikowania zarejestrowanej tożsamości osoby mówiącej za pomocą hasła lub dowolnego głosu. Może służyć do weryfikowania osób w celu bezpiecznego, bezproblemowego zaangażowania klientów w szeroką gamę rozwiązań, od weryfikacji tożsamości klientów w centrach wywołań w celu uzyskania dostępu do funkcji bez konieczności kontaktowania się z usługą.

### <a name="how-does-speaker-verification-work"></a>Jak działa weryfikacja osoby mówiącej?

![Jak działa weryfikacja głośników](media/speaker-recognition/speaker-rec.png)

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> * Zapoznaj się z [samouczkiem](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) dotyczącym weryfikacji niezależnych od tekstu.
