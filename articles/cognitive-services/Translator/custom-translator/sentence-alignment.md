---
title: Parowanie i wyrównywanie zdań — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: W trakcie wykonywania szkolenia zdania obecne w dokumentach równoległych są sparowane lub wyrównane. Translator niestandardowy uzyskuje tłumaczenie jednego zdania jednocześnie, odczytując zdanie, tłumaczenie tego zdania. Następnie wyrównuje słowa i frazy w tych dwóch zdaniach.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 0c33d766bfd3dff47ddb151e8ce4ea7b25c37548
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897955"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Parowanie i wyrównanie zdań w dokumentach równoległych

Po przekazaniu dokumentów zdania obecne w dokumentach równoległych są sparowane lub wyrównane. Translator niestandardowy zgłasza liczbę zdań, które było w stanie sparować jako wyrównane zdania w każdym z zestawów danych.

## <a name="pairing-and-alignment-process"></a>Proces parowania i wyrównywania

Translator niestandardowy uzyskuje translację zdań z jednego zdania w danym momencie. Odczytuje zdanie z tekstu źródłowego, a następnie tłumaczenie tego zdania z tekstu docelowego. Następnie wyrównuje słowa i frazy w tych dwóch zdaniach. Ten proces umożliwia działowi IT utworzenie mapy słów i fraz w jednym zdaniu do odpowiedników słów i fraz w tłumaczeniu. Wyrównania próbuje upewnić się, że system pociąga za sobą tłumaczenie.

## <a name="pre-aligned-documents"></a>Wstępnie wyrównane dokumenty

Jeśli wiesz, że masz dokumenty równoległe, możesz zastąpić wyrównanie zdania, dostarczając wstępnie wyrównane pliki tekstowe. Można wyodrębnić wszystkie zdania z dokumentów do pliku tekstowego, zorganizować jedno zdanie na wiersz i przekazać je z `.align` rozszerzeniem. `.align`Rozszerzenie sygnalizuje translatorowi niestandardowemu, że powinien pominąć wyrównanie zdania.

Aby uzyskać najlepsze wyniki, należy się upewnić, że w plikach znajduje się jedno zdanie na wiersz. Nie mają znaków nowego wiersza w obrębie zdania, ponieważ spowoduje to słabą wyrównania.

## <a name="suggested-minimum-number-of-sentences"></a>Sugerowana minimalna liczba zdań

Aby szkolenie powiodło się, w poniższej tabeli przedstawiono minimalną liczbę zdań wymaganych w każdym typie dokumentu.To ograniczenie jest środkiem bezpieczeństwa, aby upewnić się, że zdania równoległe zawierają wystarczającą liczbę unikatowych słownictwa, aby pomyślnie szkolić model tłumaczenia. Ogólne wytyczne mają więcej niż równoległe zdania z jakością tłumaczenia ludzkiego powinny generować wyższe modele jakości.

| Typ dokumentu   | Sugerowana minimalna liczba zdań | Maksymalna liczba zdań |
|------------|--------------------------------------------|--------------------------------|
| Szkolenia   | 10 000                                     | Brak górnego limitu                 |
| Dostrajanie     | 500                                      | 2500       |
| Testowanie    | 500                                      | 2500  |
| Słownik | 0                                          | Brak górnego limitu                 |

> [!NOTE]
> - Szkolenie nie zostanie uruchomione i zakończy się niepowodzeniem, jeśli nie spełniono minimalnej liczby zdań w 10 000 dla szkolenia. 
> - Dostrajanie i testowanie są opcjonalne. Jeśli ich nie podasz, system usunie odpowiedni odsetek z szkolenia do użycia w celu weryfikacji i testowania. 
> - Możesz nauczyć model przy użyciu tylko danych słownika. Zapoznaj się z informacjami o [słowniku](./what-is-dictionary.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [słownika](what-is-dictionary.md) w translatoru niestandardowym.
