---
title: Zbadaj Azure Active Directory Identity Protection ryzyka
description: Dowiedz się, jak zbadać ryzykowne użytkownicy, wykrywania i logowania w Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253483"
---
# <a name="how-to-investigate-risk"></a>Instrukcje: badanie ryzyka

Ochrona tożsamości zapewnia organizacjom trzy raporty, których mogą używać do badania zagrożeń związanych z tożsamościami w ich środowisku. Te raporty to **ryzykowni użytkownicy**, **ryzykowne logowania**i **wykrycia ryzyka**. Badanie zdarzeń jest kluczem do lepszego zrozumienia i zidentyfikowania wszystkich słabych punktów w strategii zabezpieczeń.

Wszystkie trzy raporty umożliwiają pobieranie zdarzeń w programie. Format CSV do dalszej analizy poza Azure Portal. Raporty ryzykownych użytkowników i ryzykownych logowań umożliwiają pobranie najnowszych wpisów 2500, podczas gdy raport wykrywania ryzyka umożliwia pobranie najnowszych rekordów 5000.

Organizacje mogą wykorzystać integracje interfejsów API Microsoft Graph do agregowania danych z innymi źródłami, do których mogą mieć dostęp jako organizacja.

Trzy raporty są dostępne w **Azure Portal** > **Azure Active Directory** > **zabezpieczenia**.

## <a name="navigating-the-reports"></a>Nawigowanie po raportach

Każdy raport zostanie uruchomiony z listą wszystkich wykrytych wykryć dla okresu wyświetlanego w górnej części raportu. Każdy raport umożliwia dodanie lub usunięcie kolumn na podstawie preferencji administratora. Administratorzy mogą wybrać pobranie danych z programu. Wolumin CSV lub. Format JSON. Raporty można filtrować przy użyciu filtrów w górnej części raportu.

Wybranie poszczególnych wpisów może spowodować włączenie dodatkowych wpisów w górnej części raportu, takich jak możliwość potwierdzenia, że logowanie zostało naruszone lub bezpieczne, potwierdzenie użytkownika jako złamane lub odrzucanie ryzyka związanego z użytkownikiem.

Wybranie poszczególnych wpisów rozszerza okno Szczegóły poniżej wykrycia. Widok szczegóły umożliwia administratorom badanie i wykonywanie akcji przy każdym wykryciu. 

![Przykładowy raport dotyczący ochrony tożsamości przedstawiający ryzykowne logowania i szczegóły](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Ryzykowni użytkownicy

Z informacjami udostępnianymi przez raport ryzykownych użytkowników Administratorzy mogą znaleźć następujące informacje:

- Którzy użytkownicy podlegają zagrożeniom, mają ryzyko skorygowane lub ryzyko odrzucone?
- Szczegóły dotyczące wykrywania
- Historia wszystkich ryzykownych logowań
- Historia ryzyka
 
Administratorzy mogą następnie podjąć odpowiednie działania dotyczące tych zdarzeń. Administratorzy mogą wybrać następujące opcje:

- Zresetuj hasło użytkownika
- Potwierdzanie naruszenia użytkowników
- Odrzuć ryzyko związane z użytkownikiem
- Zablokuj Logowanie użytkownika
- Zbadaj więcej przy użyciu usługi Azure ATP

## <a name="risky-sign-ins"></a>Ryzykowne logowania

Raport dotyczący ryzykownych logowań zawiera dane z możliwością filtrowania do 30 ostatnich dni (1 miesiąc).

Korzystając z informacji dostarczonych przez raport dotyczący ryzykownych logowań, Administratorzy mogą znaleźć następujące informacje:

- Zalogowania są klasyfikowane jako zagrożone, potwierdzone z naruszeniem, potwierdzone bezpiecznymi, odrzucone lub skorygowane.
- Zagregowane poziomy ryzyka związane z próbami logowania.
- Wyzwolone typy wykrywania
- Zastosowane zasady dostępu warunkowego
- Szczegóły usługi MFA
- Informacje o urządzeniu
- Informacje o aplikacji
- Informacje o lokalizacji

Administratorzy mogą następnie podjąć odpowiednie działania dotyczące tych zdarzeń. Administratorzy mogą wybrać następujące opcje:

- Potwierdź naruszenie logowania
- Potwierdź bezpieczne logowanie

## <a name="risk-detections"></a>Wykrycia ryzyka

Raport wykrywania ryzyka zawiera dane z możliwością filtrowania do ostatnich 90 dni (3 miesiące).

Korzystając z informacji podanych w raporcie wykrywania ryzyka, Administratorzy mogą znaleźć następujące informacje:

- Informacje o każdym wykrywaniu ryzyka włącznie z typem.
- Inne zagrożenia wyzwalane w tym samym czasie
- Lokalizacja próby logowania
- Połącz się z bardziej szczegółowymi informacjami z Microsoft Cloud App Security (MCAS).

Administratorzy mogą następnie powrócić do raportu o zagrożeniu lub zalogowaniu użytkownika, aby podjąć działania na podstawie zebranych informacji.

## <a name="next-steps"></a>Następne kroki

- [Zasady dostępne w celu ograniczenia ryzyka](concept-identity-protection-policies.md)

- [Włącz zasady logowania i ryzyka dla użytkowników](howto-identity-protection-configure-risk-policies.md)
