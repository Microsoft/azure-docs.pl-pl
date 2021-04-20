---
title: Parowanie i wyrównywanie zdań — Custom Translator
titleSuffix: Azure Cognitive Services
description: Podczas wykonywania szkolenia zdania obecne w dokumentach równoległych są sparowane lub wyrównane. Custom Translator uczy się tłumaczeń po jednym zdaniu na raz, czytając zdanie, tłumacząc to zdanie. Następnie wyrównuje wyrazy i frazy w tych dwóch zdaniach do siebie nawzajem.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 04/19/2021
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 43268afccbe66a21d2ce78709ba372a8a6682444
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727154"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Parowanie i wyrównywanie zdań w dokumentach równoległych

Po przesłaniu dokumentów zdania obecne w dokumentach równoległych są sparowane lub wyrównane. Custom Translator raportuje liczbę zdań, które można było sparować jako wyrównane zdania w każdym z zestawów danych.

## <a name="pairing-and-alignment-process"></a>Proces parowania i wyrównywania

Custom Translator uczy się tłumaczeń zdań po jednym zdaniu na raz. Odczytuje zdanie z tekstu źródłowego, a następnie tłumaczenie tego zdania z tekstu docelowego. Następnie wyrównuje wyrazy i frazy w tych dwóch zdaniach do siebie nawzajem. Ten proces umożliwia utworzenie mapy słów i fraz w jednym zdaniu na równoważne wyrazy i frazy w tłumaczeniu zdania. Wyrównanie stara się zapewnić, że system trenuje zdania, które są tłumaczeniami siebie nawzajem.

## <a name="pre-aligned-documents"></a>Wstępnie wyrównane dokumenty

Jeśli wiesz, że masz równoległe dokumenty, możesz przesłonić wyrównanie zdań, dostarczając wstępnie wyrównane pliki tekstowe. Możesz wyodrębnić wszystkie zdania z obu dokumentów do pliku tekstowego, uporządkować jedno zdanie w każdym wierszu i przekazać je za pomocą `.align` rozszerzenia. Rozszerzenie `.align` sygnalizuje Custom Translator, że powinno pominąć wyrównanie zdań.

Aby uzyskać najlepsze wyniki, upewnij się, że w plikach masz jedno zdanie na wiersz. Nie należy mieć znaków nowego linii w zdaniu, ponieważ spowoduje to słabe wyrównanie.

## <a name="suggested-minimum-number-of-sentences"></a>Sugerowana minimalna liczba zdań

Aby szkolenie zakończyło się pomyślnie, w poniższej tabeli przedstawiono minimalną liczbę zdań wymaganą w każdym typie dokumentu.To ograniczenie stanowi zabezpieczenia zapewniające, że zdania równoległe zawierają wystarczająco dużo unikatowego słownictwa, aby pomyślnie wytszkolić model tłumaczenia. W ogólnych wytycznych znajduje się więcej równoległych zdań w domenie jakości tłumaczenia ludzkich, które powinny tworzyć modele wyższej jakości.

| Typ dokumentu   | Sugerowana minimalna liczba zdań | Maksymalna liczba zdań |
|------------|--------------------------------------------|--------------------------------|
| Szkolenie   | 10 000                                     | Brak górnego limitu                 |
| Dostrajanie     | 500                                      | 2500       |
| Testowanie    | 500                                      | 2500  |
| Słownik | 0                                          | 250,000                 |

> [!NOTE]
>
> - Trenowania nie rozpocznie się i nie powiedzie się, jeśli minimalna liczba zdań w przypadku trenowania nie zostanie spełniony.
> - Dostrajanie i testowanie jest opcjonalne. Jeśli ich nie poimkniesz, system usunie odpowiednią wartość procentową z pola Trenuj, aby użyć jej do walidacji i testowania.
> - Model można trenować tylko przy użyciu danych słownika. Zapoznaj się z [tematem Co to jest słownik](./what-is-dictionary.md).
> - Jeśli słownik zawiera więcej niż 250 000 **[](https://docs.microsoft.com/azure/cognitive-services/translator/document-translation/overview)** zdań, prawdopodobnie lepszym wyborem będzie translator dokumentów.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [słownika w](what-is-dictionary.md) Custom Translator.
