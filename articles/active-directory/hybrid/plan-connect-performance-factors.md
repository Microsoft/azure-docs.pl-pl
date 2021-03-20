---
title: Czynniki wpływające na wydajność programu Azure AD Connect
description: W tym dokumencie wyjaśniono, jak różne czynniki wpływają na aparat aprowizacji Azure AD Connect. Te czynniki ułatwią organizacjom Planowanie wdrożenia Azure AD Connect, aby upewnić się, że spełnia on wymagania dotyczące synchronizacji.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15bcb0f7ca30c343072da396abeac8d08dee03a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90087013"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Czynniki wpływające na wydajność programu Azure AD Connect

Azure AD Connect synchronizuje Active Directory z usługą Azure AD. Ten serwer jest krytycznym składnikiem przeniesienia tożsamości użytkowników do chmury. Podstawowe czynniki wpływające na wydajność Azure AD Connect są następujące:

| **Współczynnik projektu**| **Definicja** |
|:-|-|
| Topologia| Dystrybucja punktów końcowych i składników Azure AD Connect musi zarządzać w sieci. |
| Skalowanie| Liczba obiektów, takich jak użytkownicy, grupy i jednostki organizacyjne, które mają być zarządzane przez Azure AD Connect. |
| Sprzęt| Sprzęt (fizyczny lub wirtualny) dla Azure AD Connect i zależna pojemność wydajności każdego składnika sprzętowego, w tym Konfiguracja procesora CPU, pamięci, sieci i dysku twardego. |
| Konfigurowanie| Jak Azure AD Connect przetwarza katalogi i informacje. |
| Ładowanie| Częstotliwość zmian obiektów. Obciążenia mogą się różnić w ciągu godziny, dnia lub tygodnia. W zależności od składnika może być konieczne zaprojektowanie obciążenia szczytowego lub średniego obciążenia. |

Celem tego dokumentu jest opisywanie czynników wpływających na wydajność aparatu aprowizacji Azure AD Connect. Duże lub złożone organizacje (obsługa administracyjna więcej niż 100 000 obiektów) może korzystać z zaleceń w celu optymalizacji ich implementacji Azure AD Connect, jeśli występują jakiekolwiek problemy z wydajnością opisane tutaj. Inne składniki Azure AD Connect, takie jak [Azure AD Connect kondycja](how-to-connect-health-agent-install.md) i agenci, nie zostały omówione w tym miejscu.

> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania ani działania Azure AD Connect poza akcjami, które są formalnie udokumentowane. Każda z tych akcji może skutkować niespójnością lub nieobsługiwanym stanem synchronizacji Azure AD Connect. W związku z tym firma Microsoft nie może zapewnić pomocy technicznej dotyczącej takich wdrożeń.

## <a name="azure-ad-connect-component-factors"></a>Azure AD Connect czynniki składników

Na poniższym diagramie przedstawiono architekturę wysokiego poziomu dla aparatu aprowizacji łączącego się z pojedynczym lasem, chociaż obsługiwane są wiele lasów. Ta architektura pokazuje, jak różne składniki współdziałają ze sobą.

![Na diagramie przedstawiono sposób działania podłączonych katalogów i Azure AD Connect aparatu aprowizacji, w tym przestrzeń łącznika i składniki Metaverse w SQL Database. ](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Aparat aprowizacji nawiązuje połączenie z każdym lasem Active Directory i usługą Azure AD. Proces odczytywania informacji z każdego katalogu nazywa się importem. Eksport dotyczy aktualizacji katalogów z aparatu aprowizacji. Synchronizacja szacuje reguły przepływu obiektów w aparacie aprowizacji. Aby uzyskać bardziej szczegółowy szczegółowe, możesz odnieść się do [Azure AD Connect Sync: zrozumienie architektury](./concept-azure-ad-connect-sync-architecture.md).

Azure AD Connect używa następujących obszarów tymczasowych, reguł i procesów, aby umożliwić synchronizację z Active Directory do usługi Azure AD:

* **Przestrzeń łącznika (CS)** — obiekty z każdego połączonego katalogu (CD), rzeczywiste katalogi są przygotowane w tym miejscu jako pierwsze przed przetworzeniem przez aparat aprowizacji. Usługa Azure AD ma swoje własne CS i każdy Las, z którym nawiązuje połączenie, ma własne CS.
* **Metaverse (MV)** — obiekty, które muszą być synchronizowane, są tworzone w tym miejscu na podstawie reguł synchronizacji. Obiekty muszą znajdować się w MV, zanim będą mogły wypełniać obiekty i atrybuty do innych podłączonych katalogów. Istnieje tylko jedna MV.
* **Reguły synchronizacji** — decydują, które obiekty zostaną utworzone (rzutowane) lub połączone (przyłączone) do obiektów w mV. Reguły synchronizacji decydują również o tym, które wartości atrybutów będą kopiowane lub przekształcane z i z katalogów.
* **Profile uruchamiania** — pakietuje etapy procesu kopiowania obiektów i ich wartości atrybutów zgodnie z regułami synchronizacji między obszarami przejściowymi i połączonymi katalogami.

Istnieją różne profile przebiegów, które umożliwiają optymalizację wydajności aparatu aprowizacji. Większość organizacji będzie używać domyślnych harmonogramów i profilów uruchamiania dla normalnych operacji, ale niektóre organizacje mogą mieć możliwość [zmiany harmonogramu](./how-to-connect-sync-feature-scheduler.md) lub wyzwolenia innych profili uruchamiania, aby powiększyć sytuacje. Dostępne są następujące profile uruchamiania:

### <a name="initial-sync-profile"></a>Profil synchronizacji początkowej

Początkowy profil synchronizacji to proces odczytywania podłączonych katalogów, takich jak Las Active Directory po raz pierwszy. Następnie wykonuje analizę wszystkich wpisów w bazie danych aparatu synchronizacji. Cykl początkowy spowoduje utworzenie nowych obiektów w usłudze Azure AD, jeśli Active Directory lasy są duże. Synchronizacja początkowa obejmuje następujące kroki:

1. Pełny import na wszystkich łącznikach
2. Pełna synchronizacja wszystkich łączników
3. Eksportuj na wszystkich łącznikach

### <a name="delta-sync-profile"></a>Profil synchronizacji różnicowej

Aby zoptymalizować proces synchronizacji, ten profil przebiegu przetwarza tylko zmiany (tworzy, usuwa i aktualizuje) obiektów w podłączonych katalogach, od momentu ostatniego procesu synchronizacji. Domyślnie profil synchronizacji różnicowej jest uruchamiany co 30 minut. Organizacje powinny dążyć do utrzymania czasu, który zajmuje mniej niż 30 minut, aby upewnić się, że usługa Azure AD jest aktualna. Aby monitorować kondycję Azure AD Connect, użyj [agenta monitorowania kondycji](how-to-connect-health-sync.md) , aby wyświetlić wszelkie problemy związane z procesem. Profil synchronizacji różnicowej obejmuje następujące kroki:

1. Import Delta na wszystkich łącznikach
2. Synchronizacja różnicowa na wszystkich łącznikach
3. Eksportuj na wszystkich łącznikach

Typowy scenariusz synchronizacji różnicowej organizacji korporacyjnej to:

- ~ 1% obiektów jest usuniętych
- ~ 1% obiektów jest tworzonych
- ~ 5% obiektów jest modyfikowanych

Częstotliwość zmian może się różnić w zależności od tego, jak często organizacja aktualizuje użytkowników w Active Directory. Na przykład wyższe stawki zmiany mogą wystąpić podczas zatrudniania i redukowania siły roboczej.

### <a name="full-sync-profile"></a>Profil pełnej synchronizacji

Jeśli zostały wprowadzone następujące zmiany konfiguracji, wymagany jest pełny cykl synchronizacji:



- Zwiększono zakres obiektów lub atrybutów do zaimportowania z podłączonych katalogów. Na przykład po dodaniu domeny lub jednostki organizacyjnej do zakresu importu.
- Wprowadzono zmiany w regułach synchronizacji. Na przykład podczas tworzenia nowej reguły w celu wypełnienia tytułu użytkownika w usłudze Azure AD na podstawie extension_attribute3 w Active Directory. Ta aktualizacja wymaga, aby aparat aprowizacji ponownie badał wszystkich istniejących użytkowników w celu zaktualizowania ich tytułów w celu zastosowania zmiany w przyszłości.

W cyklu pełnej synchronizacji uwzględniono następujące operacje:

1. Pełny import na wszystkich łącznikach
2. Synchronizacja pełna/różnicowa na wszystkich łącznikach
3. Eksportuj na wszystkich łącznikach

> [!NOTE]
> Należy zachować ostrożność podczas wykonywania aktualizacji zbiorczych do wielu obiektów w Active Directory lub usłudze Azure AD. Aktualizacje zbiorcze spowodują, że proces synchronizacji różnicowej będzie trwać dłużej podczas importowania, ponieważ wiele obiektów uległo zmianie. Długie Importy mogą wystąpić, nawet jeśli aktualizacja zbiorcza nie ma wpływu na proces synchronizacji. Na przykład przypisanie licencji do wielu użytkowników w usłudze Azure AD spowoduje długi cykl importowania z usługi Azure AD, ale nie spowoduje żadnych zmian atrybutów w Active Directory.

### <a name="synchronization"></a>Synchronizacja

Środowisko uruchomieniowe procesu synchronizacji ma następujące cechy wydajności:

* Synchronizacja jest jednowątkowa, co oznacza, że aparat aprowizacji nie wykonuje równoległego przetwarzania profilów uruchamiania podłączonych katalogów, obiektów lub atrybutów.
* Czas importu rośnie liniowo wraz z liczbą synchronizowanych obiektów. Na przykład jeśli Importowanie obiektów 10 000 trwa 10 minut 20 000, to na tym samym serwerze zostaną potrwać około 20 minut.
* Eksport jest również liniowy.
* Synchronizacja będzie rośnie wykładniczo na podstawie liczby obiektów z odwołaniami do innych obiektów. Członkostwa w grupach i grup zagnieżdżonych mają główny wpływ na wydajność, ponieważ ich członkowie odwołują się do obiektów użytkownika lub innych grup. Te odwołania muszą zostać znalezione i odwołują się do rzeczywistych obiektów w MV, aby ukończyć cykl synchronizacji.

### <a name="filtering"></a>Filtrowanie

Rozmiar topologii Active Directory, która ma zostać zaimportowana, to liczba jeden czynnik wpływający na wydajność i całkowity czas wykonywania wewnętrznych składników aparatu aprowizacji.

[Filtrowanie](./how-to-connect-sync-configure-filtering.md) należy zastosować, aby zmniejszyć liczbę obiektów do zsynchronizowania. Uniemożliwi to przetwarzanie i eksportowanie niepotrzebnych obiektów do usługi Azure AD. W kolejności preferencji dostępne są następujące techniki filtrowania:



- **Filtrowanie oparte na domenie** — ta opcja umożliwia wybranie określonych domen w celu synchronizacji z usługą Azure AD. Należy dodać i usunąć domeny z konfiguracji aparatu synchronizacji po wprowadzeniu zmian w infrastrukturze lokalnej po zainstalowaniu synchronizacji Azure AD Connect.
- **Filtrowanie jednostki organizacyjnej (OU)** — używa jednostek organizacyjnych do określonych obiektów w Active Directory domenach na potrzeby aprowizacji do usługi Azure AD. Filtrowanie jednostek organizacyjnych jest drugim zalecanym mechanizmem filtrowania, ponieważ używa prostych zapytań o zakres LDAP do importowania mniejszego podzestawu obiektów z Active Directory.
- **Filtrowanie atrybutów dla obiektów** — używa wartości atrybutów dla obiektów, aby zdecydować, czy konkretny obiekt w Active Directory jest inicjowany w usłudze Azure AD. Filtrowanie atrybutów doskonale nadaje się do precyzyjnego dostrajania filtrów, gdy filtrowanie domen i jednostek organizacyjnych nie spełnia określonych wymagań filtrowania. Filtrowanie atrybutów nie skraca czasu importowania, ale może skrócić czas synchronizacji i eksportowania.
- **Filtrowanie oparte na grupach** — umożliwia określenie, czy obiekty mają być inicjowane w usłudze Azure AD przy użyciu członkostwa w grupie. Filtrowanie na podstawie grupy jest przystosowane tylko do sytuacji testowych i nie jest zalecane w środowisku produkcyjnym ze względu na dodatkowe obciążenie wymagane do sprawdzenia członkostwa w grupie w trakcie cyklu synchronizacji.

Wiele trwałych [obiektów odłączeń](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) w Active Directory cs może wydłużyć czas synchronizacji, ponieważ aparat aprowizacji musi ponownie oszacować każdy obiekt odłączania, aby możliwe było połączenie w cyklu synchronizacji. Aby rozwiązać ten problem, należy wziąć pod uwagę jedną z następujących zaleceń:



- Umieść obiekty rozłączne poza zakresem do zaimportowania przy użyciu filtrowania domeny lub jednostki organizacyjnej.
- Zaprojektuj/Dołącz obiekty do MV i ustaw atrybut [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) równe true, aby uniemożliwić Inicjowanie obsługi tych obiektów w usłudze Azure AD CS.

> [!NOTE]
> Użytkownicy mogą uzyskać pomylić lub mogą wystąpić problemy z uprawnieniami aplikacji, gdy jest zbyt wiele odfiltrowanych obiektów. Na przykład w implementacji hybrydowej usługi Exchange Online użytkownicy z lokalnymi skrzynkami pocztowymi będą widzieć więcej użytkowników na globalnej liście adresów niż użytkownicy z skrzynkami pocztowymi w usłudze Exchange Online. W innych przypadkach użytkownik może chcieć udzielić dostępu w aplikacji w chmurze innemu użytkownikowi, który nie jest częścią zakresu filtrowanego zestawu obiektów.

### <a name="attribute-flows"></a>Przepływy atrybutów

Przepływy atrybutów to proces kopiowania lub przekształcania wartości atrybutów obiektów z jednego podłączonego katalogu do innego połączonego katalogu. Są one definiowane jako część reguł synchronizacji. Na przykład po zmianie numeru telefonu użytkownika w Active Directory numer telefonu w usłudze Azure AD zostanie zaktualizowany. Organizacje mogą [modyfikować przepływy atrybutów w ramach](./how-to-connect-sync-change-the-configuration.md) różnych wymagań. Zalecane jest skopiowanie istniejących przepływów atrybutów przed ich zmianą.

Proste przekierowania, takie jak przepływanie wartości atrybutu do innego atrybutu, nie mają wpływu na wydajność. Przykład przekierowania przepływa w ramach numeru telefonu komórkowego w Active Directory do numeru telefonów biurowych w usłudze Azure AD.

Transformowanie wartości atrybutów może mieć wpływ na wydajność procesu synchronizacji. Transformowanie wartości atrybutów obejmuje modyfikowanie, ponowne formatowanie, łączenie lub odejmowanie wartości atrybutów.

Organizacje mogą uniemożliwiać przepływ niektórych atrybutów do usługi Azure AD, ale nie wpływają na wydajność aparatu aprowizacji.

> [!NOTE]
> Nie usuwaj niepożądanych przepływów atrybutów w regułach synchronizacji. Zaleca się ich wyłączenie, ponieważ usunięte reguły są ponownie tworzone podczas uaktualnień Azure AD Connect.

## <a name="azure-ad-connect-dependency-factors"></a>Azure AD Connect czynniki zależności

Wydajność Azure AD Connect zależy od wydajności podłączonych katalogów, do których importuje i eksportuje. Na przykład rozmiar Active Directory wymaganego do zaimportowania lub opóźnienia sieci w usłudze Azure AD. Baza danych SQL, której używa aparat aprowizacji, ma także wpływ na ogólną wydajność cyklu synchronizacji.

### <a name="active-directory-factors"></a>Active Directory czynniki

Jak wspomniano wcześniej, liczba obiektów do zaimportowania ma znaczny wpływ na wydajność. [Sprzęt i wymagania wstępne dotyczące Azure AD Connect](how-to-connect-install-prerequisites.md) poszczególnych warstw sprzętu na podstawie rozmiaru wdrożenia. Azure AD Connect obsługują tylko określone topologie, jak opisano w [topologii dla Azure AD Connect](plan-connect-topologies.md). Brak optymalizacji wydajności i zaleceń dotyczących nieobsługiwanych topologii.

Upewnij się, że serwer Azure AD Connect spełnia wymagania sprzętowe w zależności od rozmiaru Active Directory, który chcesz zaimportować. Złe lub wolne połączenie sieciowe między serwerem Azure AD Connect i Active Directory kontrolerów domeny może spowolnić import.

### <a name="azure-ad-factors"></a>Czynniki usługi Azure AD

Usługa Azure AD używa ograniczania przepustowości, aby chronić usługę w chmurze przed atakami typu "odmowa usługi" (DoS). Obecnie usługa Azure AD ma limit ograniczania wynoszący 7 000 operacji zapisu na 5 minut (84 000 za godzinę). Na przykład można ograniczyć następujące operacje:



- Azure AD Connect wyeksportować do usługi Azure AD.
- Skrypty programu PowerShell lub aplikacje aktualizujące usługę Azure AD bezpośrednio nawet w tle, takie jak członkostwa w grupie dynamicznej.
- Użytkownicy aktualizują własne rekordy tożsamości, takie jak rejestrowanie usługi MFA lub SSPR (Samoobsługowe resetowanie hasła).
- Operacje w graficznym interfejsie użytkownika.

Zaplanuj zadania wdrażania i konserwacji, aby upewnić się, że Azure AD Connect cykl synchronizacji nie ma wpływu na limity ograniczania. Na przykład jeśli masz dużą ilość wynajmu, w której można tworzyć tysiące tożsamości użytkowników, może to spowodować, że aktualizacje członkostwa w grupie dynamicznej, przypisania licencjonowania i samoobsługowe rejestracje resetowania haseł. Lepiej jest rozłożyć te zapisy na kilka godzin lub kilka dni.

### <a name="sql-database-factors"></a>Czynniki bazy danych SQL

Rozmiar topologii Active Directory źródłowej będzie miał wpływ na wydajność bazy danych SQL. Postępuj zgodnie z [wymaganiami sprzętowymi](how-to-connect-install-prerequisites.md) bazy danych programu SQL Server i weź pod uwagę następujące zalecenia:



- Organizacje mające ponad 100 000 użytkowników mogą ograniczyć opóźnienia sieci dzięki umieszczeniu bazy danych SQL i aparatu aprowizacji na tym samym serwerze.
- Ze względu na wymagania dotyczące dużej ilości danych wejściowych i wyjściowych (we/wy) procesu synchronizacji należy użyć dysków półprzewodnikowych (SSD) dla bazy danych SQL aparatu aprowizacji, aby uzyskać optymalne wyniki, jeśli nie jest to możliwe, należy rozważyć konfiguracje RAID 0 lub RAID 1.
- Nie wykonuj pełnej synchronizacji zapobiegawczo; powoduje to niepotrzebne zmiany i wolniejsze czasy odpowiedzi.

## <a name="conclusion"></a>Podsumowanie

Aby zoptymalizować wydajność implementacji Azure AD Connect, należy wziąć pod uwagę następujące zalecenia:



- Użyj [zalecanej konfiguracji sprzętowej](how-to-connect-install-prerequisites.md) na podstawie rozmiaru implementacji serwera Azure AD Connect.
- Podczas uaktualniania Azure AD Connect w przypadku wdrożeń na dużą skalę należy rozważyć użycie [metody migracji wahadłowej](./how-to-upgrade-previous-version.md#swing-migration), aby upewnić się, że masz co najmniej przestoje i najlepszą niezawodność. 
- Użyj SSD dla bazy danych SQL, aby uzyskać najlepszą wydajność.
- Przefiltruj zakres Active Directory tak, aby obejmował tylko obiekty, które muszą być obsługiwane w usłudze Azure AD przy użyciu funkcji filtrowania domeny, jednostki organizacyjnej lub atrybutu.
- Jeśli trzeba zmienić domyślne reguły przepływu atrybutów, należy najpierw skopiować regułę, a następnie zmienić kopię i wyłączyć oryginalną regułę. Pamiętaj, aby ponownie uruchomić pełną synchronizację.
- Zaplanuj odpowiedni czas dla początkowego profilu przebiegu pełnej synchronizacji.
- Staraj się ukończyć cykl synchronizacji różnicowej w ciągu 30 minut. Jeśli profil synchronizacji różnicowej nie zostanie ukończony w ciągu 30 minut, należy zmodyfikować domyślną częstotliwość synchronizacji tak, aby obejmowała pełny cykl synchronizacji różnicowej.
- Monitoruj [kondycję Azure AD Connect synchronizacji](how-to-connect-health-agent-install.md) w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).