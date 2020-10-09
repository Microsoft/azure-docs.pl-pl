---
title: Koncepcje dotyczące zajęć w laboratoriach — Azure Lab Services | Microsoft Docs
description: Zapoznaj się z podstawowymi pojęciami dotyczącymi usług Lab Services oraz możliwością tworzenia laboratoriów oraz zarządzania nimi.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 38dd77df7a80ad252b553b6afa8b52d7fee753a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85443710"
---
# <a name="classroom-labs-concepts"></a>Pojęcia związane z laboratoriami na potrzeby zajęć

Poniższa lista zawiera pojęcia i definicje dotyczące najważniejszych usług laboratoryjnych:

## <a name="quota"></a>limit przydziału

Limit przydziału to czas (w godzinach), przez który nauczycieli może być ustawiony dla ucznia do korzystania z maszyny wirtualnej laboratorium. Można ustawić wartość 0 lub określoną liczbę godzin. Jeśli limit przydziału jest ustawiony na 0, student może użyć maszyny wirtualnej tylko wtedy, gdy harmonogram jest uruchomiony lub gdy nauczycieli ręcznie włączy maszynę wirtualną dla ucznia.  

Godziny przydziału są zliczane, gdy student uruchamia maszynę wirtualną laboratorium.  Jeśli nauczycieli ręcznie uruchamia maszynę wirtualną laboratorium dla ucznia, godziny przydziału nie będą używane dla tego ucznia.

## <a name="schedules"></a>Harmonogramy

Harmonogramy to przedziały czasu, które nauczycieli może utworzyć dla klasy, dzięki czemu maszyny wirtualne uczniów są dostępne dla czasu klasy.  Harmonogramy mogą być jednorazowe lub cykliczne.  Godziny przydziału nie są używane, gdy harmonogram jest uruchomiony.

Istnieją trzy typy harmonogramów: standardowe, tylko do uruchomienia i tylko Zatrzymaj.

- **Standardowa**.  Ten harmonogram umożliwia uruchomienie wszystkich maszyn wirtualnych uczniów o określonym czasie rozpoczęcia i zamknięcie wszystkich maszyn wirtualnych uczniów o określonym czasie zatrzymania.
- **Tylko początek**.   Ten harmonogram umożliwia uruchomienie wszystkich maszyn wirtualnych uczniów o określonej godzinie.  Maszyny wirtualne uczniów nie będą zatrzymywane do momentu zatrzymania przez studenta maszyny wirtualnej za pośrednictwem portalu Azure Lab Services lub zatrzymywania tylko harmonogramu.
- **Tylko Zatrzymaj**.  Ten harmonogram spowoduje zatrzymanie wszystkich maszyn wirtualnych uczniów w określonym czasie.  

## <a name="template-virtual-machine"></a>Szablonowa maszyna wirtualna

Szablon maszyny wirtualnej w laboratorium to podstawowy obraz maszyny wirtualnej, z którego są tworzone wszystkie maszyny wirtualne wszystkich użytkowników. Instruktorzy/twórcy mogą skonfigurować maszynę wirtualną szablonu i skonfigurować ją z oprogramowaniem, które chcą udostępnić uczestnikom szkoleń do laboratoriów. Podczas publikowania szablonu maszyny wirtualnej Azure Lab Services tworzy lub aktualizuje maszyny wirtualne laboratorium na podstawie maszyny wirtualnej szablonu.

## <a name="user-profiles"></a>Profile użytkowników

W tym artykule opisano różne profile użytkowników w usłudze Azure Lab Services.

### <a name="lab-account-owner"></a>Właściciel konta laboratorium

Zazwyczaj administrator IT zasobów w chmurze organizacji, który jest właścicielem subskrypcji platformy Azure, działa jako właściciel konta laboratorium i wykonuje następujące zadania:

- Konfiguruje konto laboratorium dla Twojej organizacji.
- Konfiguruje zasady dla wszystkich laboratoriów i zarządza nimi.
- Udziela osobom w organizacji uprawnień do tworzenia laboratorium w ramach konta laboratorium.

### <a name="educator"></a>Nauczyciel

Zazwyczaj użytkownicy, tacy jak nauczyciel lub instruktor w trybie online, tworzą laboratoria na potrzeby zajęć w ramach konta laboratorium. Nauczyciel wykonuje następujące zadania:

- Tworzy laboratorium na potrzeby zajęć.
- Tworzy maszyny wirtualne w laboratorium.
- Instaluje odpowiednie oprogramowanie na maszynach wirtualnych.
- Określa, kto ma dostęp do laboratorium.
- Udostępnia studentom link umożliwiający rejestrację w laboratorium.

### <a name="student"></a>Uczeń

Student wykonuje następujące zadania:

- Używa linku rejestracji, który otrzymuje od twórcy laboratorium w celu zarejestrowania się w laboratorium.
- Nawiązuje połączenie z maszyną wirtualną w laboratorium i używa jej do wykonywania prac, przydziałów i projektów na zajęciach.

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę od skonfigurowania konta laboratorium wymaganego do utworzenia laboratorium na potrzeby zajęć przy użyciu usługi Azure Lab Services:

- [Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)
