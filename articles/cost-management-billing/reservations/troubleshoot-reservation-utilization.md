---
title: Rozwiązywanie problemów z wykorzystaniem rezerwacji platformy Azure
description: Ten artykuł ułatwia zrozumienie rezerwacji platformy Azure, dla których w witrynie Azure Portal pokazywany jest brak wykorzystania lub wykorzystanie na poziomie zero (0), oraz rozwiązywanie problemów z takim rezerwacjami. Wyjaśniono w nim również wykorzystanie, które nie jest zgodne.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: 9d5706843e8131110566ad8f955415b6db29f7ba
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207438"
---
# <a name="troubleshoot-reservation-utilization"></a>Rozwiązywanie problemów z wykorzystaniem rezerwacji

Ten artykuł ułatwia zrozumienie rezerwacji platformy Azure, dla których w witrynie Azure Portal pokazywany jest brak wykorzystania lub wykorzystanie na poziomie zero (0), oraz rozwiązywanie problemów z takim rezerwacjami. Wyjaśniono w nim również wykorzystanie, które nie jest zgodne.

## <a name="symptoms"></a>Objawy

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i przejdź do obszaru **Rezerwacje** .
1. Na liście rezerwacji sprawdź wykorzystanie rezerwacji w kolumnie **Wykorzystanie (%)** . Może ono wynosić 0%.
1. Wybierz rezerwację.
1. Procent użycia pokazany na grafie na stronie przeglądu rezerwacji może nie być zgodny z wartością widoczną na liście rezerwacji.

## <a name="cause"></a>Przyczyna

Kolumna **Wykorzystanie (%)** w witrynie Azure Portal pokazuje wartość dla bieżącego dnia. Wartość jest obliczana w miarę jak dane o użyciu są odbierane z miejsc, w których zasoby są uruchamiane. Platforma Azure oblicza procent wykorzystania na podstawie danych użycia.

Niektóre zasoby przesyłają raporty z danymi użycia wolniej niż inne. Ponadto niektóre typy produktów, takie jak bazy danych SQL, z opóźnieniem przesyłają raporty z danymi użycia.

Z powodu tego opóźnienia obliczone wykorzystanie może pokazywać wartość niższą niż rzeczywista. Różnica jest zauważalna na granicy dnia. W takich przypadkach, jeśli platforma Azure nie odbierze danych użycia z od czterech do ośmiu godzin, oblicza wartość 0%. Wartość 0% jest pokazywana, ponieważ dane użycia nie dotarły i wydaje się, że rezerwacja nie stosuje korzyści do żadnych zasobów.

W miarę jak dane docierają, wartość zmienia się i zbliża do prawidłowej wartości procentowej. Po zebraniu wszystkich danych użycia określana jest poprawna, dokładna wartość, która jest następnie wyświetlana na grafie.

## <a name="solution"></a>Rozwiązanie

Jeśli okaże się, że wartości wykorzystania nie są zgodne z oczekiwaniami, przejrzyj graf, aby uzyskać najbardziej dokładne dane na temat rzeczywistego wykorzystania. Wszystkie wartości punktów starsze niż dwa dni powinny być dokładne. Średnie z dłuższych okresów obejmujących od siedmiu do 30 dni powinny być dokładne.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zarządzania rezerwacjami, zobacz [Zarządzanie rezerwacjami zasobów platformy Azure](manage-reserved-vm-instance.md).