---
title: Jak naprawić zmodyfikowane reguły domyślne — Azure AD Connect | Microsoft Docs
description: Dowiedz się, jak naprawić zmodyfikowane domyślne reguły, które są dostępne w Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fc1bc3158e04c9b1f677af7ef2375ac3ed2ce7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91320051"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Naprawianie zmodyfikowanych reguł domyślnych w Azure AD Connect

Program Azure Active Directory (Azure AD) Connect używa domyślnych reguł do synchronizacji.  Niestety te reguły nie są powszechnie stosowane do wszystkich organizacji. Na podstawie Twoich wymagań może być konieczne ich zmodyfikowanie. W tym artykule omówiono dwa przykłady najbardziej typowych dostosowań i wyjaśniono poprawny sposób osiągnięcia tych dostosowań.

>[!NOTE] 
> Modyfikowanie istniejących reguł domyślnych w celu osiągnięcia wymaganego dostosowania nie jest obsługiwane. Jeśli to zrobisz, uniemożliwi to zaktualizowanie tych reguł do najnowszej wersji w przyszłych wersjach. Nie uzyskasz potrzebnych poprawek lub nowych funkcji. W tym dokumencie wyjaśniono, jak osiągnąć ten sam wynik bez modyfikowania istniejących reguł domyślnych. 

## <a name="how-to-identify-modified-default-rules"></a>Jak zidentyfikować zmodyfikowane reguły domyślne
Począwszy od wersji 1.3.7.0 Azure AD Connect, można łatwo zidentyfikować zmodyfikowaną regułę domyślną. Przejdź do pozycji **aplikacje na pulpicie**, a następnie wybierz pozycję **Edytor reguł synchronizacji**.

![Azure AD Connect, z wyróżnionym edytorem reguł synchronizacji](media/how-to-connect-fix-default-rules/default1.png)

W edytorze wszystkie zmodyfikowane reguły domyślne są wyświetlane z ikoną ostrzeżenia przed nazwą.

![Ikona ostrzeżenia](media/how-to-connect-fix-default-rules/default2.png)

 Zostanie wyświetlona reguła o tej samej nazwie obok niej (jest to standardowa reguła domyślna).

![Edytor reguł synchronizacji, wyświetlanie standardowej reguły domyślnej i zmodyfikowanej reguły domyślnej](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Wspólne dostosowania
Poniżej przedstawiono typowe dostosowania reguł domyślnych:

- Zmień przepływ atrybutów
- Zmień filtr zakresu
- Zmień warunek sprzężenia

Przed zmianą jakichkolwiek reguł:

- Wyłączenie harmonogramu synchronizacji. Harmonogram jest domyślnie uruchamiany co 30 minut. Upewnij się, że nie jest on uruchamiany podczas wprowadzania zmian i rozwiązywania problemów z nowymi regułami. Aby tymczasowo wyłączyć harmonogram, uruchom program PowerShell i uruchom polecenie `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 ![Polecenia programu PowerShell umożliwiające wyłączenie harmonogramu synchronizacji](media/how-to-connect-fix-default-rules/default3.png)

- Zmiana w filtrze zakresu może spowodować usunięcie obiektów w katalogu docelowym. Należy zachować ostrożność przed wprowadzeniem jakichkolwiek zmian w zakresie określania zakresu obiektów. Przed wprowadzeniem zmian na aktywnym serwerze zalecamy wprowadzenie zmian do serwera przejściowego.
- Uruchom podgląd na pojedynczym obiekcie, jak wspomniano w sekcji [Weryfikowanie reguły synchronizacji](#validate-sync-rule) , po dodaniu nowej reguły.
- Uruchom pełną synchronizację po dodaniu nowej reguły lub zmodyfikowaniu dowolnej niestandardowej reguły synchronizacji. Ta synchronizacja stosuje nowe reguły do wszystkich obiektów.

## <a name="change-attribute-flow"></a>Zmień przepływ atrybutów
Istnieją trzy różne scenariusze zmiany przepływu atrybutów:
- Dodawanie nowego atrybutu.
- Zastępowanie wartości istniejącego atrybutu.
- Nie można zsynchronizować istniejącego atrybutu.

Można to zrobić bez zmiany standardowych reguł domyślnych.

### <a name="add-a-new-attribute"></a>Dodaj nowy atrybut
Jeśli okaże się, że atrybut nie przepływa z katalogu źródłowego do katalogu docelowego, użyj [Azure AD Connect synchronizacji: rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md) , aby rozwiązać ten problem.

Jeśli rozszerzenia nie działają, spróbuj dodać dwie nowe reguły synchronizacji opisane w poniższych sekcjach.


#### <a name="add-an-inbound-sync-rule"></a>Dodawanie reguły synchronizacji ruchu przychodzącego
Reguła synchronizacji ruchu przychodzącego oznacza Źródło dla atrybutu, który jest obszarem łącznika, a obiektem docelowym jest Metaverse. Na przykład, aby utworzyć nowy przepływ atrybutów z Active Directory lokalnego do Azure Active Directory, Utwórz nową regułę synchronizacji ruchu przychodzącego. Uruchom **Edytor reguł synchronizacji**, wybierz pozycję **przychodzące** jako kierunek i wybierz pozycję **Dodaj nową regułę**. 

 ![Zrzut ekranu pokazujący "Edytor reguł synchronizacji" z wybraną opcją "przychodzące" i "Dodaj nową regułę".](media/how-to-connect-fix-default-rules/default3a.png)

Postępuj zgodnie z własną konwencją nazewnictwa, aby nazwać regułę. W tym miejscu używamy **niestandardowej usługi AD-User**. Oznacza to, że reguła jest regułą niestandardową i jest regułą ruchu przychodzącego z obszaru łącznika Active Directory do Metaverse.   

 ![Utwórz regułę synchronizacji ruchu przychodzącego](media/how-to-connect-fix-default-rules/default3b.png)

Podaj własny opis reguły, aby umożliwić łatwą konserwację zasady. Na przykład opis może opierać się na tym, jaki jest cel reguły, i dlaczego jest to potrzebne.

Wybierz odpowiednie opcje dla **połączonego systemu**, **Typ połączonego obiektu systemu** i pola **typu obiektu metaverse** .

Określ wartość priorytetu od 0 do 99 (niższy numer, wyższy priorytet). Dla **tagów**, **Włącz synchronizację haseł** i **wyłączone** pola, użyj domyślnych ustawień.

Pozostaw pusty **Filtr zakresu** . Oznacza to, że reguła ma zastosowanie do wszystkich obiektów przyłączonych między Active Directory połączonym i Metaverse.

Zachowaj puste **reguły sprzężenia** . Oznacza to, że ta reguła używa warunku sprzężenia zdefiniowanego w standardowej regule domyślnej. Jest to kolejny powód, aby nie wyłączać ani usuwać standardowej reguły domyślnej. Jeśli nie ma warunku sprzężenia, atrybut nie będzie przepływać. 

Dodaj odpowiednie przekształcenia dla atrybutu. Możesz przypisać stałą, aby zapewnić ciągły przepływ wartości do atrybutu docelowego. Można użyć bezpośredniego mapowania między atrybut źródłowy lub docelowy. Lub można użyć wyrażenia dla atrybutu. Poniżej przedstawiono różne [funkcje wyrażeń](./reference-connect-sync-functions-reference.md) , których można użyć.

#### <a name="add-an-outbound-sync-rule"></a>Dodawanie reguły synchronizacji danych wychodzących
Aby połączyć atrybut z katalogiem docelowym, należy utworzyć regułę wychodzącą. Oznacza to, że źródłem jest element Metaverse, a obiektem docelowym jest połączony system. Aby utworzyć regułę ruchu wychodzącego, uruchom **Edytor reguł synchronizacji**, Zmień **kierunek** na **wychodzący**, a następnie wybierz pozycję **Dodaj nową regułę**. 

![Edytor reguł synchronizacji](media/how-to-connect-fix-default-rules/default3c.png)

Podobnie jak w przypadku reguły ruchu przychodzącego, możesz użyć własnej konwencji nazewnictwa, aby nazwać regułę. Wybierz **podłączony system** jako dzierżawę usługi Azure AD, a następnie wybierz obiekt połączonego systemu, do którego chcesz ustawić wartość atrybutu. Ustaw pierwszeństwo od 0 do 99. 

![Utwórz regułę synchronizacji ruchu wychodzącego](media/how-to-connect-fix-default-rules/default3d.png)

Zachowaj puste ustawienia **filtru zakresu** i **sprzężenia** . Wypełnij transformację jako stałą, bezpośredni lub wyrażenie. 

Teraz wiesz, jak utworzyć nowy atrybut przepływu obiektu użytkownika z Active Directory, aby Azure Active Directory. Za pomocą tych kroków można mapować dowolny atrybut z dowolnego obiektu na źródłowy i docelowy. Aby uzyskać więcej informacji, zobacz [Tworzenie niestandardowych reguł synchronizacji](how-to-connect-create-custom-sync-rule.md) i [przygotowanie do aprowizacji użytkowników](/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Zastąp wartość istniejącego atrybutu
Możesz chcieć przesłonić wartość atrybutu, który został już zamapowany. Na przykład, jeśli zawsze chcesz ustawić wartość null dla atrybutu w usłudze Azure AD, wystarczy utworzyć tylko regułę ruchu przychodzącego. Wprowadź wartość stałą, a następnie `AuthoritativeNull` pozycję Flow do atrybutu Target. 

>[!NOTE] 
> Użyj `AuthoritativeNull` zamiast tego `Null` w tym przypadku. Wynika to z faktu, że wartość o wartości innej niż null zastępuje wartość null, nawet jeśli ma ona niższy priorytet (wyższą wartość liczbową w regule). `AuthoritativeNull`z drugiej strony nie jest zastępowana wartością inną niż null przez inne zasady. 

### <a name="dont-sync-existing-attribute"></a>Nie Synchronizuj istniejącego atrybutu
Jeśli chcesz wykluczyć atrybut z synchronizacji, użyj funkcji filtrowania atrybutów dostępnej w Azure AD Connect. Uruchom **Azure AD Connect** z ikony pulpitu, a następnie wybierz pozycję **Dostosuj opcje synchronizacji**.

![Azure AD Connect opcje dodatkowych zadań](media/how-to-connect-fix-default-rules/default4.png)

 Upewnij się, że jest zaznaczona opcja **filtrowanie aplikacji i atrybutów usługi Azure AD** , a następnie wybierz przycisk **dalej**.

![Azure AD Connect funkcje opcjonalne](media/how-to-connect-fix-default-rules/default5.png)

Wyczyść atrybuty, które mają zostać wykluczone z synchronizacji.

![Atrybuty Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Zmień filtr zakresu
Azure AD Sync zajmuje się większością obiektów. Można zmniejszyć zakres obiektów i zmniejszyć liczbę obiektów do wyeksportowania, bez zmiany standardowych domyślnych reguł synchronizacji. 

Użyj jednej z następujących metod, aby zmniejszyć zakres synchronizowanych obiektów:

- cloudFiltered — atrybut
- Filtrowanie jednostek organizacyjnych

W przypadku zmniejszenia zakresu synchronizowanych użytkowników synchronizacja skrótu hasła również zostaje zatrzymana dla użytkowników odfiltrowanych. Jeśli obiekty są już zsynchronizowane, po zmniejszeniu zakresu obiekty odfiltrowane są usuwane z katalogu docelowego. Z tego powodu należy upewnić się, że zakres jest bardzo uważnie.

>[!IMPORTANT] 
> Nie zaleca się zwiększania zakresu obiektów skonfigurowanych przez Azure AD Connect. Dzięki temu zespół pomocy technicznej firmy Microsoft może być trudny do zrozumienia dostosowań. Jeśli trzeba zwiększyć zakres obiektów, edytować istniejącą regułę, sklonować ją i wyłączyć oryginalną regułę. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered — atrybut
Nie można ustawić tego atrybutu w Active Directory. Ustaw wartość tego atrybutu przez dodanie nowej reguły ruchu przychodzącego. Następnie można użyć **transformacji** i **wyrażenia** , aby ustawić ten atrybut w obiekcie Metaverse. Poniższy przykład pokazuje, że nie chcesz synchronizować wszystkich użytkowników, których nazwa działu rozpoczyna się od **HRD** (bez uwzględniania wielkości liter):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Najpierw przeprowadzono konwersję działu ze źródła (Active Directory) na małe litery. Następnie korzystając z `Left` funkcji, mamy tylko trzy pierwsze znaki i porównano ją z `hrd` . Jeśli jest dopasowany, wartość jest ustawiona na `True` , w przeciwnym razie `NULL` . W przypadku ustawiania wartości null inna reguła o niższym priorytecie (wyższa wartość liczbowa) może zapisywać w tym samym stanie z innym warunkiem. Uruchom podgląd na jednym obiekcie, aby sprawdzić poprawność reguły synchronizacji, jak wspomniano w sekcji [Weryfikowanie reguły synchronizacji](#validate-sync-rule) .

![Utwórz Opcje reguły synchronizacji ruchu przychodzącego](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtrowanie jednostek organizacyjnych
Można utworzyć co najmniej jedną jednostkę organizacyjną (OU) i przenieść obiekty, które nie mają być synchronizowane z tymi jednostkami organizacyjnymi. Następnie należy skonfigurować filtrowanie jednostek organizacyjnych w Azure AD Connect. Uruchom **Azure AD Connect** z ikony pulpitu i wybierz poniższe opcje. Można również skonfigurować filtrowanie jednostek organizacyjnych w czasie instalacji Azure AD Connect. 

![Azure AD Connect dodatkowe zadania](media/how-to-connect-fix-default-rules/default8.png)

Postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze, a następnie wyczyść jednostki organizacyjne, które nie mają być synchronizowane.

![Azure AD Connect opcje filtrowania domeny i jednostki organizacyjnej](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Zmień warunek sprzężenia
Użyj domyślnych warunków sprzężenia skonfigurowanych przez Azure AD Connect. Zmiana domyślnych warunków przyłączania utrudnia pomoc techniczną firmy Microsoft w zrozumieniu dostosowań i obsłudze produktu.

## <a name="validate-sync-rule"></a>Weryfikuj regułę synchronizacji
Można sprawdzić poprawność nowo dodanej reguły synchronizacji przy użyciu funkcji Podgląd, bez uruchamiania pełnego cyklu synchronizacji. W Azure AD Connect wybierz pozycję **usługa synchronizacji**.

![Azure AD Connect, z wyróżnioną usługą synchronizacji](media/how-to-connect-fix-default-rules/default10.png)

Wybieranie **wyszukiwania Metaverse**. Wybierz obiekt zakresu jako **osobę**, wybierz pozycję **Dodaj klauzulę** i podaj kryteria wyszukiwania. Następnie wybierz pozycję **Wyszukaj**, a następnie kliknij dwukrotnie obiekt w wynikach wyszukiwania. Upewnij się, że dane w Azure AD Connect są aktualne dla tego obiektu, przez uruchomienie importowania i synchronizowania w lesie przed uruchomieniem tego kroku.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

We **właściwościach obiektu metaverse** wybierz pozycję **Łączniki**, wybierz obiekt w odpowiednim łączniku (Las), a następnie wybierz pozycję **właściwości.**

![Właściwości obiektu metaverse](media/how-to-connect-fix-default-rules/default12.png)

Wybierz **Podgląd...**

![Właściwości obiektu obszaru łącznika](media/how-to-connect-fix-default-rules/default13a.png)

W oknie Podgląd Wybierz pozycję **Generuj Podgląd** i **zaimportuj przepływ atrybutów** w okienku po lewej stronie.

![Zrzut ekranu pokazujący okno "Podgląd" z wybraną opcją "Importuj przepływ atrybutów" i "Generuj Podgląd".](media/how-to-connect-fix-default-rules/default14.png)
 
W tym miejscu należy zauważyć, że nowo dodana reguła jest uruchamiana na obiekcie i ma ustawiony `cloudFiltered` atrybut true.

![Wersja zapoznawcza](media/how-to-connect-fix-default-rules/default15a.png)
 
Aby porównać zmodyfikowaną regułę z regułą domyślną, należy wyeksportować obie reguły osobno, jako pliki tekstowe. Te reguły są eksportowane jako plik skryptu programu PowerShell. Można je porównać przy użyciu dowolnego narzędzia do porównywania plików (na przykład Windiff), aby zobaczyć zmiany. 
 
Zwróć uwagę, że w zmodyfikowanej regule `msExchMailboxGuid` atrybut jest zmieniany na typ **wyrażenia** , a nie **bezpośrednio**. Ponadto wartość zostanie zmieniona na wartość **null** i opcję **ExecuteOnce** . Można zignorować różnice zidentyfikowane i priorytetowe. 

![dane wyjściowe narzędzia Windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Aby naprawić reguły w celu zmiany ustawień domyślnych, Usuń zmodyfikowaną regułę i Włącz regułę domyślną. Upewnij się, że nie utracisz dostosowania, które chcesz uzyskać. Gdy wszystko będzie gotowe, uruchom **pełną synchronizację**.

## <a name="next-steps"></a>Następne kroki
- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Ustawienia dostosowane](how-to-connect-install-custom.md)