---
title: Potwierdzenia — aparat rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje związane z analizą odbioru przy użyciu interfejsu API rozpoznawania i limitów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 7a14b3c93a6c545648faf28c991764e990e487b1
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724985"
---
# <a name="receipt-concepts"></a>Pojęcia dotyczące odbioru

Aparat rozpoznawania AzureForm może analizować potwierdzenia przy użyciu jednego ze wstępnie skompilowanych modeli. Interfejs API paragonu wyodrębnia najważniejsze informacje z przyjęć sprzedaży w języku angielskim, takie jak nazwa handlowa, Data transakcji, Suma transakcji, elementy wiersza itd. 

## <a name="understanding-receipts"></a>Informacje o potwierdzeniach 

Wiele firm i osób nadal opiera się na ręcznym wyodrębnianiu danych z ich przyjęć sprzedaży, niezależnie od tego, czy są to raporty z wydatków biznesowych, zwrotne, cele podatkowe, budżetowanie czy inne. Często w tych scenariuszach obrazy fizycznego paragonu są wymagane do celów weryfikacji.  

Automatyczne wyodrębnianie danych z tych przyjęć może być skomplikowane. Odbiory mogą być crumplede i trudne do odczytania, a obrazy smartphone mogą mieć niską jakość. Ponadto szablony i pola paragonów mogą się różnić w zależności od rynku, regionu i handlowca. Te wyzwania w przypadku wyodrębniania danych i wykrywania pól sprawiają, że podczas przetwarzania przez funkcję odbioru występuje unikatowy problem.  

Korzystając z funkcji optycznego rozpoznawania znaków (OCR) i naszego prekompilowanego modelu paragonów, interfejs API paragonów włącza te scenariusze przetwarzania paragonów. Ze względu na to, że model jest wstępnie szkolony na naszych danych, można łatwo przeanalizować swoje wpłaty w jednym kroku &mdash; bez konieczności uczenia ani etykietowania modeli.

![Przykładowe potwierdzenie](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>Do czego służy interfejs API paragonów? 

Interfejs API prekompilowanego paragonu wyodrębnia zawartość przyjęć sprzedaży &mdash; typu paragonu, który często uzyskuje się w restauracjach, detalicznych lub w sklepie spożywczym.

### <a name="fields-extracted"></a>Wyodrębnione pola

* Nazwa handlowa 
* Adres handlowca 
* Numer telefonu handlowca 
* Data transakcji 
* Czas transakcji 
* Suma częściowa 
* Podatek 
* Łącznie 
* Porada 
* Wyodrębnianie elementu wiersza (na przykład ilość elementów, Cena elementu, nazwa elementu)

### <a name="additional-features"></a>Dodatkowe funkcje

Interfejs API paragonu zwraca również następujące informacje:

* Typ paragonu (na przykład element, karta kredytowa itd.)
* Poziom ufności pól (każde pole zwraca skojarzoną wartość zaufania)
* Tekst nieprzetworzony OCR (wyodrębniany tekst wyjściowy tekstu dla całego potwierdzenia)

## <a name="input-requirements"></a>Wymagania wejściowe

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Obsługiwane ustawienia regionalne 

* **Wstępnie skompilowane przyjęcie w wersji 2.0** (ga) obsługuje przyjęcia sprzedaży w ustawieniach regionalnych en-us
* **Wstępnie utworzone przyjęcie — wersja zapoznawcza. 1** (publiczna wersja zapoznawcza) dodaje dodatkową pomoc techniczną dla następujących ustawień regionalnych usługi: 
  * EN-AU 
  * EN-CA 
  * PL GB 
  * DODATEK EN-IN 

  > [!NOTE]
  > Dane wejściowe języka 
  >
  > Wstępnie utworzone przyjęcie — wersja zapoznawcza. 1 ma opcjonalny parametr żądania, aby określić ustawienia regionalne odbioru z dodatkowych rynków w języku angielskim. W przypadku przyjęć sprzedaży w języku angielskim z Australii (EN-AU), Kanada (EN-CA), Wielka Brytania (en-GB) i Indie (EN-IN) można określić ustawienia regionalne, aby uzyskać ulepszone wyniki. Jeśli nie określono ustawień regionalnych w wersji 2.1-Preview. 1, model będzie domyślnie modelem EN-US.

## <a name="customer-scenarios"></a>Scenariusze klientów  

Dane wyodrębnione za pomocą interfejsu API paragonów mogą służyć do wykonywania różnych zadań. Poniżej przedstawiono kilka przykładów naszych klientów korzystających z interfejsu API odbierania. 

### <a name="business-expense-reporting"></a>Raportowanie wydatków w firmie  

Często wydatki biznesowe obejmują czas poświęcany na ręczne wprowadzanie danych z obrazów przyjęć. Korzystając z interfejsu API paragonów, można użyć wyodrębnionych pól, aby częściowo zautomatyzować ten proces i szybko analizować swoje wpłaty.  

Ponieważ interfejs API paragonów zawiera proste dane wyjściowe JSON, można użyć wyodrębnionych wartości pól na wiele sposobów. Integruj z wewnętrznymi aplikacjami do wydatków, aby wstępnie wypełnić raporty dotyczące wydatków. Aby uzyskać więcej informacji na ten temat, Przeczytaj o tym, jak Acumatica korzysta z interfejsu API odbioru w celu [raportowania wydatków na mniej bolesnym proces](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Inspekcja i ewidencjonowanie aktywności 

Dane wyjściowe interfejsu API paragonów mogą być również używane do analizowania dużej liczby wydatków w różnych punktach w ramach raportowania wydatków i procesu zwrotnego. Możesz przetwarzać potwierdzenia, aby klasyfikacja je do ręcznego przeprowadzania inspekcji lub szybkich zatwierdzeń.  

Dane wyjściowe paragonu są również przydatne do ogólnego utrzymywania się w ramach firmy lub użytku osobistego. Korzystając z interfejsu API paragonów, można przekształcić dowolne nieprzetworzone dane obrazu/pliku PDF na cyfrowe dane wyjściowe, które są funkcjonalne.

### <a name="consumer-behavior"></a>Zachowanie konsumentów 

Paragony zawierają przydatne dane, których można użyć do analizowania zachowań klientów i trendów zakupów.

