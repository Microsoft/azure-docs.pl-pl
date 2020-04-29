---
title: Co to jest interfejs API rozpoznawania osoby mówiącej?
titleSuffix: Azure Cognitive Services
description: Weryfikacja głośników i tożsamość osoby mówiącej interfejs API rozpoznawania osoby mówiącej w Cognitive Services.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73464919"
---
# <a name="speaker-recognition-api---preview"></a>Interfejs API rozpoznawania osoby mówiącej — wersja zapoznawcza

Interfejsy API rozpoznawanie osoby mówiącej są interfejsami API opartymi na chmurze, które udostępniają zaawansowane algorytmy dla weryfikacji głośników i identyfikacji osoby mówiącej. Rozpoznawanie osoby mówiącej jest podzielony na dwie kategorie: Weryfikacja głośników i tożsamość osoby mówiącej.

## <a name="speaker-verification"></a>Weryfikacja osoby mówiącej

Głos ma unikalne cechy, które mogą być skojarzone z osobą.  Aplikacje mogą używać głosu jako dodatkowego czynnika do weryfikacji w scenariuszach takich jak wywołania i usługi sieci Web.

Weryfikacja osoby mówiącej interfejsów API służy jako inteligentne narzędzie ułatwiające Weryfikowanie użytkowników przy użyciu ich hasła głosowego i mowy.

### <a name="enrollment"></a>Rejestrowanie

Rejestracja w celu weryfikacji prelegenta jest zależna od tekstu, co oznacza, że głośniki muszą wybrać określone hasło do użycia podczas faz rejestracji i weryfikacji.

W fazie rejestracji głośników głos osoby mówiącej jest rejestrowany w postaci określonej frazy. Funkcje głosu są wyodrębniane w celu utworzenia unikatowej sygnatury głosowej podczas rozpoznawania wybranej frazy. Ze sobą dane rejestracyjne prelegenta zostaną użyte do zweryfikowania prelegenta. Dane rejestracyjne prelegenta są przechowywane w zabezpieczonym systemie. Klient określa, jak długo należy zachować. Klienci mogą tworzyć, aktualizować i usuwać dane rejestracyjne dla poszczególnych głośników za poorednictwem wywołań interfejsu API.  Usunięcie subskrypcji spowoduje również usunięcie wszystkich danych dotyczących rejestracji głośników skojarzonych z subskrypcją.

Klienci powinni upewnić się, że otrzymali odpowiednie uprawnienia od użytkowników w celu weryfikacji.

### <a name="verification"></a>Weryfikacja

W fazie weryfikacji klient powinien wywołać interfejs API weryfikacji prezentera z IDENTYFIKATORem skojarzonym z osobą do zweryfikowania.  Usługa wyodrębnia funkcje głosu i hasło z wejściowego nagrania mowy. Następnie porównuje funkcje z odpowiednimi elementami danych rejestracji głośników dla prelegenta, że klient szuka i określi wszystkie dopasowania.  Odpowiedź zwróci wartość "Akceptuj" lub "Odrzuć" z różnymi poziomami ufności.  Następnie klient określi, jak używać wyników, aby pomóc w ustaleniu, czy ta osoba jest zarejestrowanym głośnikem.

Poziom ufności progu należy ustawić na podstawie scenariusza i innych używanych czynników weryfikacji. Zalecamy przeprowadzenie eksperymentu z poziomem zaufania i rozważenie odpowiedniego ustawienia dla każdej aplikacji. Interfejsy API nie mają na celu określenie, czy dźwięk pochodzi od osoby działającej na żywo, czy przez imitację lub nagranie zarejestrowanego głośnika.

Usługa nie zachowuje nagrania mowy ani wyodrębnionych funkcji głosowych wysyłanych do usługi podczas fazy weryfikacji.

Aby uzyskać więcej informacji na temat weryfikacji osoby mówiącej, zapoznaj się z artykułem dotyczącym interfejsu API [weryfikacji osoby mówiącej](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identyfikacja osoby mówiącej

Aplikacje mogą używać głosu do identyfikowania osoby mówiącej o tym, że nadano grupę zarejestrowanych głośników. Identyfikacja osoby mówiącej interfejsów API można używać w scenariuszach takich jak produktywność spotkań, Personalizacja i transkrypcja centrum połączeń.

### <a name="enrollment"></a>Rejestrowanie

Rejestracja w celu identyfikacji osoby mówiącej jest niezależna od tekstu, co oznacza, że nie ma żadnych ograniczeń w zakresie słów wypowiadanych przez osobę mówiącą na nagraniu dźwiękowym. Hasło nie jest wymagane.

W fazie rejestracji głos osoby mówiącej jest rejestrowany, a funkcje głosu są wyodrębniane w celu utworzenia unikatowej sygnatury głosowej. Wyodrębnione audio i funkcje mowy są przechowywane w zabezpieczonym systemie. Klient kontroluje czas zachowywania. Klienci mogą tworzyć, aktualizować i usuwać dane dotyczące rejestracji głośników dla poszczególnych głośników za poorednictwem wywołań interfejsu API. Usunięcie subskrypcji spowoduje również usunięcie wszystkich danych dotyczących rejestracji głośników skojarzonych z subskrypcją.

Klienci powinni upewnić się, że otrzymali odpowiednie uprawnienia od użytkowników w celu identyfikacji osoby mówiącej.

### <a name="identification"></a>Identyfikacja

W fazie identyfikacji usługa identyfikacji głośników wyodrębnia funkcje głosu z wejściowego nagrania mowy. Następnie porównuje funkcje z danymi rejestracji określonej listy głośników. Gdy zostanie znalezione dopasowanie z zarejestrowanym głośnikem, odpowiedź zwróci identyfikator osoby mówiącej o poziom ufności.  W przeciwnym razie odpowiedź zwróci wartość "Odrzuć", gdy żaden głośnik nie jest odpowiednikiem zarejestrowanego głośnika.

Poziom ufności progu należy ustawić na podstawie scenariusza. Zalecamy przeprowadzenie eksperymentu z poziomem zaufania i rozważenie odpowiedniego ustawienia dla każdej aplikacji. Interfejsy API nie mają na celu określenie, czy dźwięk pochodzi od osoby działającej na żywo, czy przez imitację lub nagranie zarejestrowanego głośnika.

Usługa nie zachowuje nagrania mowy ani wyodrębnionych funkcji głosowych, które są wysyłane do usługi w ramach fazy identyfikacji.

Aby uzyskać więcej informacji na temat identyfikacji osoby mówiącej, zapoznaj się z artykułem dotyczącym interfejsu API  [identyfikacji osoby mówiącej](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
