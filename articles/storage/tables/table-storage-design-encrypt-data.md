---
title: Szyfrowanie danych tabeli usługi Azure Storage | Microsoft Docs
description: Informacje o szyfrowaniu danych tabeli w usłudze Azure Storage. Biblioteka klienta usługi Azure Storage dla platformy .NET umożliwia szyfrowanie jednostek ciągów dla operacji wstawiania i zamiany.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: fec213b9d7429714beb948f061445fd37d698624
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037494"
---
# <a name="encrypt-table-data"></a>Szyfruj dane tabeli
Biblioteka klienta usługi Azure Storage dla platformy .NET obsługuje szyfrowanie właściwości jednostki ciągów dla operacji wstawiania i zamieniania. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarne i są konwertowane z powrotem do ciągów po odszyfrowaniu.    

W przypadku tabel oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić przez określenie atrybutu [EncryptProperty] (dla jednostek POCO, które pochodzą z klasy tableentity) lub mechanizmu rozwiązywania konfliktów w opcjach żądania. Program rozpoznawania szyfrowania jest delegatem, który pobiera klucz partycji, klucz wiersza i nazwę właściwości i zwraca wartość logiczną wskazującą, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania, Biblioteka klienta używa tych informacji, aby zdecydować, czy szyfrować właściwość podczas zapisu do sieci. Delegat udostępnia również możliwość logiki dotyczącej sposobu szyfrowania właściwości. (Na przykład, jeśli X, wówczas Zaszyfruj Właściwość A; w przeciwnym razie Szyfruj właściwości a i B). Nie trzeba podawać tych informacji podczas odczytywania lub wykonywania zapytań dotyczących jednostek.

## <a name="merge-support"></a>Obsługa scalania

Scalanie nie jest obecnie obsługiwane. Ze względu na to, że podzbiór właściwości mógł zostać zaszyfrowany wcześniej przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych powoduje utratę danych. Scalanie wymaga wykonania dodatkowych wywołań usługi do odczytu istniejącej jednostki z usługi lub użycia nowego klucza dla każdej właściwości, co nie jest odpowiednie ze względu na wydajność.     

Aby uzyskać informacje na temat szyfrowania danych tabeli, zobacz [szyfrowanie po stronie klienta i Azure Key Vault Microsoft Azure Storage](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowe tabeli](table-storage-design-patterns.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
