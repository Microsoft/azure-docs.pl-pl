---
title: Korzystanie z laboratoriów zajęć na potrzeby szkoleń — Azure Lab Services
description: W tym artykule opisano, jak używać Azure DevTest Labs do tworzenia laboratoriów na platformie Azure na potrzeby scenariuszy szkoleniowych.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 582508968609ba53de14b95b71ee75b5fec9ba93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445257"
---
# <a name="use-classroom-labs-for-trainings"></a>Korzystanie z laboratoriów zajęć na potrzeby szkoleń
Usługi Azure Labs umożliwiają nauczycielom (nauczycielom, wykładowcyom, instruktorom lub asystentom nauczania itp.) szybkie i łatwe tworzenie laboratorium online do aprowizacji wstępnie skonfigurowanych środowisk szkoleniowych dla tego instruktora. Każdy z nich może korzystać z tych samych i izolowanych środowisk dla szkolenia. Zasady mogą być stosowane w celu upewnienia się, że środowiska szkoleniowe są dostępne dla każdego z nich tylko wtedy, gdy ich potrzebują i zawierają wystarczające zasoby, takie jak maszyny wirtualne — wymagane do szkolenia. 

![Laboratorium zajęć](./media/classroom-labs-scenarios/classroom.png)

Laboratorium zajęć spełnia następujące wymagania, które są wymagane do przeprowadzenia szkolenia w dowolnym środowisku wirtualnym: 

- Osoba szkolony może szybko zainicjować swoje środowiska szkoleniowe
- Każda maszyna szkoleniowa powinna być taka sama
- Program szkolony nie widzi maszyn wirtualnych utworzonych przez innego program szkolony
- Kontroluj koszty, upewniając się, że osoba odpowiedzialna nie może uzyskać więcej maszyn wirtualnych niż potrzeba do szkolenia, a także zamknąć maszyny wirtualne, gdy ich nie używają
- Łatwo udostępniaj laboratorium szkoleniowe każdemu z nich
- Ponownie Użyj laboratorium szkoleniowego i ponownie

W tym artykule omówiono różne funkcje Azure Lab Services, których można użyć w celu spełnienia opisanych wcześniej wymagań szkoleniowych i szczegółowych czynności, które można wykonać, aby skonfigurować laboratorium do szkolenia.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Utwórz konto laboratorium jako administrator konta laboratorium
Pierwszym krokiem w programie przy użyciu Azure Lab Services jest utworzenie konta laboratorium w Azure Portal. Po utworzeniu konta laboratorium przez administratora konta laboratorium administrator dodaje użytkowników, którzy chcą utworzyć laboratorium, do roli **twórca laboratorium** . Nauczyciele tworzą laboratoria z maszynami wirtualnymi przeznaczonymi dla studentów, którzy wykonują ćwiczenia w zakresie nauczania. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta laboratorium i zarządzanie nim](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Tworzenie laboratoriów na potrzeby zajęć i zarządzanie nimi
Nauczycieli, który jest członkiem roli twórca laboratorium na koncie laboratorium, może utworzyć jedną lub więcej laboratoriów na koncie laboratorium. Utwórz i skonfiguruj maszynę wirtualną szablonu przy użyciu wszystkich programów wymaganych do wykonywania ćwiczeń w danym kursie. Możesz wybrać gotowy obraz z dostępnych obrazów do utworzenia laboratorium zajęć, a następnie dostosować go, instalując oprogramowanie wymagane do laboratorium. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie laboratoriów zajęć i zarządzanie nimi](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Konfigurowanie ustawień i zasad użytkowania
Twórca laboratorium może dodawać i usuwać użytkowników z laboratorium, otrzymywać linki do rejestracji w celu wysyłania do użytkowników laboratorium, konfigurować zasady, takie jak Ustawianie poszczególnych przydziałów dla użytkownika, aktualizowanie liczby maszyn wirtualnych dostępnych w laboratorium i innych. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie ustawień i zasad użytkowania](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Tworzenie harmonogramów i zarządzanie nimi
Harmonogramy umożliwiają skonfigurowanie laboratorium klasy w taki sposób, aby maszyny wirtualne w laboratorium były automatycznie uruchamiane i zamykane w określonym czasie. Można zdefiniować harmonogram jednorazowy lub cykliczny. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie harmonogramów dla laboratoriów zajęć i zarządzanie nimi](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Konfigurowanie i publikowanie maszyny wirtualnej szablonu
Szablon w laboratorium to podstawowy obraz maszyny wirtualnej, który służy do tworzenia maszyn wirtualnych wszystkich użytkowników. Skonfiguruj maszynę wirtualną z szablonem, tak aby była skonfigurowana dokładnie dla uczestników szkoleń. Możesz podać nazwę i opis szablonu, które będą widoczne dla użytkowników laboratorium. Następnie należy opublikować szablon w celu udostępnienia dla użytkowników laboratorium wystąpień z szablonową maszyną wirtualną. Gdy opublikujesz szablon, usługa Azure Lab Services utworzy maszyny wirtualne w laboratorium przy użyciu tego szablonu. Liczba maszyn wirtualnych utworzonych w ramach tego procesu jest taka sama jak maksymalna liczba użytkowników, którzy mogą korzystać z laboratorium. Tę liczbę można ustawić w zasadach użytkowania laboratorium. Wszystkie maszyny wirtualne mają taką samą konfigurację jak szablon. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie i publikowanie maszyn wirtualnych z szablonem](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Korzystanie z maszyn wirtualnych w laboratorium zajęć
Uczestnik ucznia lub szkolenia rejestruje się w laboratorium i łączy się z maszyną wirtualną, aby wykonać ćwiczenia. Aby uzyskać szczegółowe informacje, zobacz [jak uzyskać dostęp do laboratorium zajęć](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Następne kroki
Zacznij od utworzenia konta laboratorium w laboratoriach zajęć, postępując zgodnie z instrukcjami w artykule: [Samouczek: Konfigurowanie konta laboratorium przy użyciu Azure Lab Services](tutorial-setup-lab-account.md).