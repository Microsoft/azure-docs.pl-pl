---
title: Optymalizowanie czasu zapytania przy użyciu strategii magazynu wyskakujących tabel w Azure Database for PostgreSQL-pojedynczym serwerze
description: W tym artykule opisano, jak zoptymalizować czas zapytania z strategią magazynu wyskakujących tabel na jednym serwerze Azure Database for PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3f1fa0affb821b00d4f5529841533e854e634377
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86116186"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Optymalizowanie czasu zapytania przy użyciu strategii magazynu wyskakujących tabel 
W tym artykule opisano, jak zoptymalizować czasy zapytań za pomocą strategii magazynu tabel z niezwiększonymi atrybutami.

## <a name="toast-table-storage-strategies"></a>Strategie magazynu wyskakujących tabel
Cztery różne strategie są używane do przechowywania kolumn na dysku, które mogą korzystać z wyskakującego powiadomienia. Reprezentują różne kombinacje między kompresją i magazynem poza wierszem. Strategię można ustawić na poziomie typu danych i na poziomie kolumny.
- **Zwykły** sposób uniemożliwia kompresję lub przechowywanie poza wierszem. Wyłącza on użycie nagłówków jednobajtowych dla typów varlena. Zwykła jest jedyną możliwą strategią dla kolumn typów danych, które nie mogą być wyskakujące.
- **Extended** umożliwia kompresję i przechowywanie poza wierszem. Extended jest wartością domyślną dla większości typów danych, które mogą korzystać z wyskakującego powiadomienia. Najpierw podjęto próbę wykonania kompresji. Próba użycia magazynu poza wierszem jest podejmowana, jeśli wiersz jest wciąż zbyt duży.
- **Zewnętrzny** umożliwia przechowywanie poza wierszami, ale nie kompresji. Użycie zewnętrznych sprawia, że operacje podciągów w kolumnach o szerokim tekście i bajcie są szybsze. Ta szybkość obejmuje karę zwiększonego miejsca do magazynowania. Te operacje są zoptymalizowane pod kątem pobierania tylko wymaganych części wartości poza wierszem, gdy nie jest ona skompresowana.
- **Główny** umożliwia kompresję, ale nie poza wierszem. Magazyn poza wierszami jest nadal wykonywany dla takich kolumn, ale tylko jako Ostatnia. Występuje, gdy nie istnieje żaden inny sposób, aby wiersz był wystarczająco mały, aby zmieścił się na stronie.

## <a name="use-toast-table-storage-strategies"></a>Korzystanie z strategii magazynu wyskakujących tabel
Jeśli zapytania uzyskują dostęp do typów danych, które mogą korzystać z wyskakujących powiadomień, należy rozważyć użycie głównej strategii zamiast domyślnej opcji rozszerzonej w celu skrócenia czasów wykonywania zapytań. Głównym magazynem nie jest reguła magazynu poza wierszem. Jeśli zapytania nie mają dostępu do typów danych, które mogą korzystać z wyskakującego powiadomienia, może być korzystne, aby zachować rozszerzoną opcję. Większa część wierszy tabeli głównej mieści się w udostępnionej pamięci podręcznej buforów, co pomaga w wydajności.

Jeśli masz obciążenie wykorzystujące schemat z szerokimi tabelami i dużą liczbą znaków, rozważ użycie wyskakujących tabel PostgreSQL. Przykładowa tabela klienta ma więcej niż 350 kolumn z kilkoma kolumnami, które zawierają znaki 255. Po przeprowadzeniu konwersji na wyskakującą strategię tabeli, czas zapytania testów porównawczych został zmniejszony z 4203 sekund do 467 sekund. To jest poprawa o 89 procent.

## <a name="next-steps"></a>Następne kroki
Przejrzyj obciążenie dla powyższych cech. 

Zapoznaj się z następującą dokumentacją PostgreSQL: 
- [Rozdział 68, magazyn fizyczny bazy danych](https://www.postgresql.org/docs/current/storage-toast.html) 