---
title: Zbadaj Azure Active Directory Identity Protection ryzyka
description: Dowiedz się, jak zbadać ryzykowne użytkownicy, wykrywania i logowania w Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: be72c2152bdb8e1155d2dd29547f93ba3605d462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95251097"
---
# <a name="how-to-investigate-risk"></a>Instrukcje: Badanie ryzyka

Ochrona tożsamości zapewnia organizacjom trzy raporty, których mogą używać do badania zagrożeń związanych z tożsamościami w ich środowisku. Te raporty to **ryzykowni użytkownicy**, **ryzykowne logowania** i **wykrycia ryzyka**. Badanie zdarzeń jest kluczem do lepszego zrozumienia i zidentyfikowania wszystkich słabych punktów w strategii zabezpieczeń.

Wszystkie trzy raporty umożliwiają pobieranie zdarzeń w programie. Format CSV do dalszej analizy poza Azure Portal. Raporty ryzykownych użytkowników i ryzykownych logowań umożliwiają pobranie najnowszych wpisów 2500, podczas gdy raport wykrywania ryzyka umożliwia pobranie najnowszych rekordów 5000.

Organizacje mogą wykorzystać integracje interfejsów API Microsoft Graph do agregowania danych z innymi źródłami, do których mogą mieć dostęp jako organizacja.

Trzy raporty są dostępne w **Azure Portal**  >  **Azure Active Directory**  >  **zabezpieczenia**.

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

> [!NOTE] 
> Program Identity Protection szacuje ryzyko dla wszystkich przepływów uwierzytelniania, niezależnie od tego, czy jest on interaktywny, czy nieinteraktywny. Jednak raport logowania zawiera tylko logowania interaktywne. Mogą pojawić się ryzykowne logowania, które wystąpiły w przypadku logowania nieinterakcyjnego, ale logowanie nie będzie widoczne w raporcie logowania usługi Azure AD.

## <a name="risk-detections"></a>Wykrycia ryzyka

Raport wykrywania ryzyka zawiera dane z możliwością filtrowania do ostatnich 90 dni (3 miesiące).

Korzystając z informacji podanych w raporcie wykrywania ryzyka, Administratorzy mogą znaleźć następujące informacje:

- Informacje o każdym wykrywaniu ryzyka włącznie z typem.
- Inne zagrożenia wyzwalane w tym samym czasie
- Lokalizacja próby logowania
- Połącz się z bardziej szczegółowymi informacjami z Microsoft Cloud App Security (MCAS).

Administratorzy mogą następnie powrócić do raportu o zagrożeniu lub zalogowaniu użytkownika, aby podjąć działania na podstawie zebranych informacji.

> [!NOTE] 
> Nasz system może wykryć, że zdarzenie ryzyka, które przyczynia się do oceny ryzyka dla użytkownika, było fałszywie dodatnie lub ryzyko związane z użytkownikiem zostało skorygowane przy użyciu wymuszania zasad, takich jak zakończenie monitu usługi MFA lub bezpieczne Zmienianie hasła. W związku z tym, nasz system spowoduje odłączenie stanu ryzyka i szczegółowe informacje o ryzyku "zatwierdzono logowanie w bezpiecznym miejscu". 


## <a name="next-steps"></a>Następne kroki

- [Zasady dostępne w celu ograniczenia ryzyka](concept-identity-protection-policies.md)

- [Włącz zasady logowania i ryzyka dla użytkowników](howto-identity-protection-configure-risk-policies.md)
