---
title: Korzystanie z pulpitu nawigacyjnego dla laboratorium zajęć w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak używać pulpitu nawigacyjnego dla laboratorium zajęć w Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 7a4c4adcd83e4a4781767cd3c34216dca2b4d510
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897996"
---
# <a name="dashboard-for-classroom-labs"></a>Pulpit nawigacyjny dla laboratoriów zajęć
W tym artykule opisano widok pulpitu nawigacyjnego laboratorium zajęć w Azure Lab Services. 

![Pulpit nawigacyjny](./media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Kafelek kosztów i rozliczeń
Ten kafelek zawiera następujące szczegóły oszacowania kosztów:

| Ustawienie | Wartość | 
| ------- | ----- | 
| Godziny przydziału | Maksymalna liczba godzin, przez jaką użytkownik może używać maszyny wirtualnej poza zaplanowanymi godzinami. |
| Zaplanowane godziny | Godziny, które będą naliczane na podstawie harmonogramu ustawionego w laboratorium. Ta wartość jest dostępna tylko wtedy, gdy dla wszystkich zdarzeń harmonogramu jest ustawiony dzień od/do. |
| Godz./użytkownika | Suma godzin przydziału i zaplanowanych godzin. |
| Maksymalna liczba użytkowników | Maksymalna liczba użytkowników w laboratorium na podstawie wszystkich maszyn wirtualnych, które mają zostać przejęte. |
| Godz. x użytkowników | Godziny/użytkownik pomnożony przez liczbę użytkowników. |
| Przydziały skorygowane | Suma godzin przydziału dodanych do określonych użytkowników. |
| Łączna liczba godzin * $/godz. | Koszt za godzinę na podstawie wybranego rozmiaru maszyny wirtualnej. Jest to uzależnione od zwykłej opłaty zgodnie z rzeczywistym użyciem. |
| Łączny szacowany koszt | Jest to maksymalna cena dla tego laboratorium w oparciu o bieżące ustawienia. |

## <a name="template-tile"></a>Kafelek szablonu
Na tym kafelku są widoczne następujące informacje:

- Data utworzenia szablonu 
- Data ostatniej publikacji szablonu 

Zawiera również link umożliwiający przejście do strony **szablonu** , na której można [zarządzać maszyną wirtualną szablonu](how-to-create-manage-template.md) dla klasy. 

## <a name="virtual-machine-pool-tile"></a>Kafelek puli maszyn wirtualnych

Na tym kafelku są widoczne następujące informacje:

- Liczba maszyn wirtualnych przypisanych do uczniów (użytkowników)
- Liczba maszyn wirtualnych, które nie zostały jeszcze przypisane do uczniów

Zawiera również link umożliwiający przejście do strony **puli maszyn wirtualnych** , na której można [zarządzać pulą maszyn wirtualnych](how-to-set-virtual-machine-passwords.md) w laboratorium. 

## <a name="users-tile"></a>Kafelek użytkownicy

Na tym kafelku są widoczne następujące informacje:

- Liczba użytkowników zarejestrowanych w klasie
- Liczba użytkowników, którzy są dodawani do laboratorium, ale nie są zarejestrowani do klasy 

Zawiera również link umożliwiający przejście do strony **Użytkownicy** , na której można [zarządzać użytkownikami](how-to-configure-student-usage.md) w laboratorium. 

## <a name="schedules-tile"></a>Kafelek harmonogramów
Bieżące zaplanowane zdarzenia dla laboratorium są wyświetlane na kafelku. Zawiera również link umożliwiający przejście do strony **harmonogram** , w której można [tworzyć harmonogramy i zarządzać nimi](how-to-create-schedules.md). Kafelek zawiera szczegółowe informacje dotyczące tylko dwóch zaplanowanych zdarzeń oraz liczbę pozostałych zdarzeń zaplanowanych dla laboratorium. 

![Zaplanowane zdarzenia](./media/use-dashboard/scheduled-events.png)

