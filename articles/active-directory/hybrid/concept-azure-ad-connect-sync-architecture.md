---
title: 'Azure AD Connect Sync: zrozumienie architektury — Azure'
description: W tym temacie opisano architekturę Azure AD Connect Sync i wyjaśniono używane terminy.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27055ce84bbb073045b69b942fd13f4fde4e3b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563866"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Synchronizacja Azure AD Connect: zrozumienie architektury
W tym temacie omówiono podstawową architekturę synchronizacji Azure AD Connect. W wielu aspektach jest podobny do jego poprzedników MIIS 2003, ILM 2007 i FIM 2010. Azure AD Connect synchronizacja jest ewolucją tych technologii. Jeśli znasz już każdą z tych wcześniejszych technologii, zawartość tego tematu będzie również znana. Jeśli dopiero zaczynasz synchronizację, ten temat jest dla Ciebie. Nie jest jednak wymagane, aby poznać szczegóły tego tematu, aby można było pomyślnie dostosowywać Azure AD Connect synchronizacji (nazywany aparatem synchronizacji w tym temacie).

## <a name="architecture"></a>Architektura
Aparat synchronizacji tworzy Zintegrowany widok obiektów, które są przechowywane w wielu połączonych źródłach danych i zarządza informacjami o tożsamościach w tych źródłach danych. Ten widok zintegrowany jest określany przez informacje o tożsamości pobrane z połączonych źródeł danych oraz zestaw reguł, które określają sposób przetwarzania tych informacji.

### <a name="connected-data-sources-and-connectors"></a>Połączone źródła danych i łączniki
Aparat synchronizacji przetwarza informacje o tożsamościach z różnych repozytoriów danych, takich jak Active Directory lub SQL Server Database. Każde repozytorium danych, które organizuje dane w formacie podobnym do bazy danych i zapewnia standardowe metody dostępu do danych, jest potencjalnym kandydatem źródła danych dla aparatu synchronizacji. Repozytoria danych, które są synchronizowane przez aparat synchronizacji, są nazywane **połączonymi źródłami danych** lub **katalogami podłączonymi** (CD).

Aparat synchronizacji hermetyzuje interakcje z połączonym źródłem danych w module nazywanym **łącznikiem**. Każdy typ połączonego źródła danych ma określony łącznik. Łącznik tłumaczy wymaganą operację na format zrozumiały dla połączonego źródła danych.

Łączniki tworzą wywołania interfejsu API do wymiany informacji o tożsamości (zarówno do odczytu i zapisu) z połączonym źródłem danych. Istnieje również możliwość dodania łącznika niestandardowego za pomocą rozszerzalnej struktury łączności. Na poniższej ilustracji pokazano, jak łącznik nawiązuje połączenie z połączonym źródłem danych z aparatem synchronizacji.

![Diagram przedstawia połączone źródło danych oraz aparat synchronizacji skojarzony z wierszem o nazwie łącznik.](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Dane mogą przepływać w dowolnym kierunku, ale nie mogą przepływać jednocześnie w obu kierunkach. Innymi słowy, łącznik można skonfigurować w taki sposób, aby dane mogły być przesyłane z połączonego źródła danych do aparatu synchronizacji lub z aparatu synchronizacji do połączonego źródła danych, ale tylko jedna z tych operacji może wystąpić w jednym czasie dla jednego obiektu i atrybutu. Kierunek może być różny dla różnych obiektów i dla różnych atrybutów.

Aby skonfigurować łącznik, należy określić typy obiektów, które mają zostać zsynchronizowane. Określenie typów obiektów definiuje zakres obiektów uwzględnionych w procesie synchronizacji. Następnym krokiem jest wybranie atrybutów do zsynchronizowania, które są znane jako lista dołączania do atrybutów. Te ustawienia można zmienić w dowolnym momencie w reakcji na zmiany reguł firmy. W przypadku korzystania z Kreatora instalacji Azure AD Connect te ustawienia są konfigurowane.

Aby wyeksportować obiekty do połączonego źródła danych, lista dołączania atrybutów musi zawierać co najmniej minimalne atrybuty wymagane do utworzenia określonego typu obiektu w połączonym źródle danych. Na przykład atrybut **sAMAccountName** musi być uwzględniony na liście dołączania atrybutów do eksportowania obiektu użytkownika do Active Directory, ponieważ wszystkie obiekty użytkownika w Active Directory muszą mieć zdefiniowany atrybut **sAMAccountName** . Ponownie Kreator instalacji wykonuje tę konfigurację.

Jeśli połączone źródło danych używa składników strukturalnych, takich jak partycje lub kontenery do organizowania obiektów, można ograniczyć obszary w połączonym źródle danych, które są używane dla danego rozwiązania.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Wewnętrzna struktura przestrzeni nazw aparatu synchronizacji
Cała przestrzeń nazw aparatu synchronizacji składa się z dwóch przestrzeni nazw, w których przechowywane są informacje o tożsamości. Dwie przestrzenie nazw:

* Przestrzeń łącznika (CS)
* Metaverse (MV)

**Przestrzeń łącznika** jest obszarem przejściowym, który zawiera reprezentacje wyznaczonych obiektów z połączonego źródła danych i atrybutów określonych na liście dołączania atrybutów. Aparat synchronizacji używa przestrzeni łącznika, aby określić, co zostało zmienione w połączonym źródle danych i przemieścić zmiany przychodzące. Aparat synchronizacji używa również przestrzeni łącznika, aby przemieścić wychodzące zmiany eksportu do połączonego źródła danych. Aparat synchronizacji utrzymuje odrębne miejsce łącznika jako obszar przejściowy dla każdego łącznika.

Za pomocą obszaru przejściowego aparat synchronizacji pozostaje niezależny od połączonych źródeł danych i nie ma wpływ na ich dostępność i dostęp. W związku z tym można w dowolnym momencie przetworzyć informacje o tożsamości przy użyciu danych w obszarze przejściowym. Aparat synchronizacji może zażądać tylko zmian wprowadzonych w połączonym źródle danych od momentu zakończenia ostatniej sesji komunikacji lub wypchnąć tylko zmiany informacji o tożsamości, które nie otrzymały połączonego źródła danych, co zmniejsza ruch sieciowy między aparatem synchronizacji i połączonym źródłem danych.

Ponadto aparat synchronizacji przechowuje informacje o stanie wszystkich obiektów, które etapuje w przestrzeni łącznika. Po odebraniu nowych danych aparat synchronizacji zawsze sprawdza, czy dane zostały już zsynchronizowane.

**Metaverse** to obszar przechowywania zawierający zagregowane informacje o tożsamości z wielu połączonych źródeł danych, co zapewnia jeden globalny, Zintegrowany widok wszystkich połączonych obiektów. Obiekty Metaverse są tworzone na podstawie informacji o tożsamości, które są pobierane z połączonych źródeł danych, oraz zestawu reguł, które umożliwiają dostosowanie procesu synchronizacji.

Na poniższej ilustracji przedstawiono przestrzeń nazw obszaru łącznika i przestrzeń nazw Metaverse w aparacie synchronizacji.

![Diagram przedstawia połączone źródło danych i aparat synchronizacji, który jest rozdzielony na przestrzeń łącznika i przestrzenie nazw Metaverse, skojarzone z wierszem o nazwie łącznik.](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Obiekty tożsamości aparatu synchronizacji
Obiekty w aparacie synchronizacji są reprezentacją obu obiektów w połączonym źródle danych lub widoku zintegrowanego, który aparat synchronizacji ma dla tych obiektów. Każdy obiekt aparatu synchronizacji musi mieć unikatowy identyfikator globalny (GUID). Identyfikatory GUID zapewniają integralność danych i bezpośrednie relacje między obiektami.

### <a name="connector-space-objects"></a>Obiekty przestrzeni łącznika
Gdy aparat synchronizacji komunikuje się z połączonym źródłem danych, odczytuje informacje o tożsamości w połączonym źródle danych i używa tych informacji do utworzenia reprezentacji obiektu Identity w przestrzeni łącznika. Nie można tworzyć ani usuwać tych obiektów pojedynczo. Można jednak ręcznie usunąć wszystkie obiekty w przestrzeni łącznika.

Wszystkie obiekty w obszarze łącznika mają dwa atrybuty:

* Unikatowy identyfikator globalny (GUID)
* Nazwa wyróżniająca (znana także jako nazwa DN)

Jeśli połączone źródło danych przypisze unikatowy atrybut do obiektu, obiekty w przestrzeni łącznika mogą także mieć atrybut zakotwiczenia. Atrybut zakotwiczenia jednoznacznie identyfikuje obiekt w połączonym źródle danych. Aparat synchronizacji używa kotwicy do lokalizowania odpowiedniej reprezentacji tego obiektu w połączonym źródle danych. Aparat synchronizacji zakłada, że zakotwiczenie obiektu nigdy nie ulega zmianie w okresie istnienia obiektu.

Wiele łączników używa znanego unikatowego identyfikatora do automatycznego generowania kotwic dla każdego obiektu podczas importowania. Na przykład łącznik Active Directory używa atrybutu **objectGUID** dla zakotwiczenia. W przypadku połączonych źródeł danych, które nie zapewniają jasno zdefiniowanego unikatowego identyfikatora, można określić generację zakotwiczenia w ramach konfiguracji łącznika.

W takim przypadku zakotwiczenie jest zbudowane z jednego lub więcej unikatowych atrybutów typu obiektu, a nie żadnych zmian i jednoznacznie identyfikuje obiekt w przestrzeni łącznika (na przykład numer pracownika lub identyfikator użytkownika).

Obiekt przestrzeni łącznika może mieć jedną z następujących wartości:

* Obiekt przemieszczania
* Symbol zastępczy

### <a name="staging-objects"></a>Obiekty przemieszczania
Obiekt przemieszczania reprezentuje wystąpienie wywskazanych typów obiektów z połączonego źródła danych. Oprócz identyfikatora GUID i nazwy wyróżniającej, obiekt przemieszczania zawsze ma wartość wskazującą typ obiektu.

Zaimportowane obiekty przemieszczania zawsze mają wartość dla atrybutu zakotwiczenia. Obiekty przemieszczania, które były nowo obsługiwane przez aparat synchronizacji i są w trakcie tworzenia w połączonym źródle danych, nie mają wartości atrybutu zakotwiczenia.

Obiekty przejściowe zawierają również bieżące wartości atrybutów firmy oraz informacje operacyjne, które są konieczne przez aparat synchronizacji do przeprowadzenia procesu synchronizacji. Informacje operacyjne obejmują flagi wskazujące typ aktualizacji, które są przemieszczane w obiekcie przemieszczania. Jeśli obiekt przemieszczania otrzymał nowe informacje o tożsamości od połączonego źródła danych, które nie zostało jeszcze przetworzone, obiekt zostanie oflagowany jako **oczekujące na import**. Jeśli obiekt przemieszczania zawiera nowe informacje o tożsamości, które nie zostały jeszcze wyeksportowane do połączonego źródła danych, są oflagowane jako **oczekujące na eksport**.

Obiekt przemieszczania może być obiektem importu lub obiektem eksportu. Aparat synchronizacji tworzy obiekt importu przy użyciu informacji o obiekcie odebranych z połączonego źródła danych. Gdy aparat synchronizacji otrzymuje informacje o istnieniu nowego obiektu, który jest zgodny z jednym z typów obiektów zaznaczonych w łączniku, tworzy obiekt importu w przestrzeni łącznika jako reprezentację obiektu w połączonym źródle danych.

Na poniższej ilustracji przedstawiono obiekt importu, który reprezentuje obiekt w połączonym źródle danych.

![Na diagramie przedstawiono obiekt importu przeniesiony z połączonego źródła danych do przestrzeni nazw obszaru łącznika w aparacie synchronizacji.](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

Aparat synchronizacji tworzy obiekt eksportu przy użyciu informacji o obiekcie w magazynie Metaverse. Eksportuj obiekty do połączonego źródła danych podczas następnej sesji komunikacji. Z perspektywy aparatu synchronizacji obiekty eksportu nie istnieją jeszcze w połączonym źródle danych. W związku z tym atrybut kotwicy dla obiektu eksportu nie jest dostępny. Po odebraniu obiektu z aparatu synchronizacji połączone źródło danych tworzy unikatową wartość dla atrybutu zakotwiczenia obiektu.

Na poniższej ilustracji przedstawiono sposób tworzenia obiektu eksportu przy użyciu informacji o tożsamości w obiekcie Metaverse.

![Na diagramie przedstawiono obiekt eksportu przeniesiony z elementu Metaverse do przestrzeni nazw łącznika, a następnie do połączonego źródła danych.](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

Aparat synchronizacji potwierdza eksport obiektu przez ponowne zaimportowanie obiektu z połączonego źródła danych. Eksportowanie obiektów staje się obiektami importu, gdy aparat synchronizacji otrzymuje je podczas następnego importowania z tego połączonego źródła danych.

### <a name="placeholders"></a>Symbole zastępcze
Aparat synchronizacji używa płaskiej przestrzeni nazw do przechowywania obiektów. Jednak niektóre połączone źródła danych, takie jak Active Directory, używają hierarchicznej przestrzeni nazw. Aby przekształcić informacje z hierarchicznej przestrzeni nazw w płaską przestrzeń nazw, aparat synchronizacji używa symboli zastępczych, aby zachować hierarchię.

Każdy symbol zastępczy reprezentuje składnik (na przykład jednostkę organizacyjną) nazwy hierarchicznej obiektu, która nie została zaimportowana do aparatu synchronizacji, ale jest wymagana do skonstruowania nazwy hierarchicznej. Wypełniają one przerwy utworzone przez odwołania w połączonym źródle danych do obiektów, które nie są obiektami tymczasowymi w przestrzeni łącznika.

Aparat synchronizacji używa również symboli zastępczych do przechowywania obiektów, do których istnieją odwołania, które nie zostały jeszcze zaimportowane. Na przykład jeśli synchronizacja została skonfigurowana tak, aby obejmowała atrybut Manager dla obiektu *Abbie Spencer* , a odebrana wartość jest obiektem, który nie został jeszcze zaimportowany, na przykład *CN = Lewandowski Sperry, CN = users, DC = Fabrikam, DC = com*, informacje o Menedżerze są przechowywane jako symbole zastępcze w obszarze łącznika. Jeśli obiekt Menedżera zostanie zaimportowany później, obiekt zastępczy zostanie zastąpiony przez obiekt przemieszczania, który reprezentuje Menedżera.

### <a name="metaverse-objects"></a>Obiekty Metaverse
Obiekt Metaverse zawiera widok zagregowany, który aparat synchronizacji ma obiekty przejściowe w przestrzeni łącznika. Aparat synchronizacji tworzy obiekty Metaverse przy użyciu informacji w obszarze Importuj obiekty. Kilka obiektów przestrzeni łącznika można połączyć z pojedynczym obiektem Metaverse, ale obiekt przestrzeni łącznika nie może być połączony z więcej niż jednym obiektem Metaverse.

Nie można ręcznie utworzyć lub usunąć obiektów Metaverse. Aparat synchronizacji automatycznie usuwa obiekty Metaverse, które nie mają linku do żadnego obiektu obszaru łącznika w obszarze łącznika.

Aby zmapować obiekty w ramach połączonego źródła danych na odpowiedni typ obiektu w ramach Metaverse, aparat synchronizacji zapewnia rozszerzalny schemat ze wstępnie zdefiniowanym zestawem typów obiektów i skojarzonymi atrybutami. Można tworzyć nowe typy obiektów i atrybuty dla obiektów Metaverse. Atrybuty mogą być jednowartościowe lub wielowartościowe, a typy atrybutów mogą być ciągami, odwołaniami, numerami i wartościami logicznymi.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relacje między obiektami przemieszczania i obiektami Metaverse
W przestrzeni nazw aparatu synchronizacji przepływ danych jest włączany przez relację linku między obiektami przemieszczania i obiektami Metaverse. Obiekt przemieszczania połączony z obiektem Metaverse jest nazywany **obiektem przyłączonym** (lub **obiektem łącznika**). Obiekt przemieszczania, który nie jest połączony z obiektem Metaverse, jest nazywany **odłączonym obiektem** (lub **obiektem odłączania**). Warunki przyłączone i odłączone są preferowane, aby nie mylić z łącznikami odpowiedzialnymi za Importowanie i eksportowanie danych z podłączonego katalogu.

Symbole zastępcze nigdy nie są połączone z obiektem Metaverse

Przyłączony obiekt składa się z obiektu przemieszczania i jego połączonej relacji z pojedynczym obiektem Metaverse. Przyłączone obiekty są używane do synchronizowania wartości atrybutów między obiektem przestrzeni łącznika a obiektem Metaverse.

Gdy obiekt przemieszczania stanie się obiektem przyłączonym podczas synchronizacji, atrybuty mogą przepływać między obiektem przemieszczania a obiektem Metaverse. Przepływ atrybutu jest dwukierunkowy i jest konfigurowany przy użyciu reguł atrybutów importu i eksportu reguł atrybutów.

Pojedynczy obiekt przestrzeni łącznika można połączyć tylko z jednym obiektem Metaverse. Jednak każdy obiekt Metaverse może być połączony z wieloma obiektami przestrzeni łącznika w tym samym lub w różnych miejscach łączników, jak pokazano na poniższej ilustracji.

![Diagram przedstawia dwa połączone obiekty danych skojarzone przez łączniki z aparatem synchronizacji, który ma przyłączone obiekty i odłączony obiekt.](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Połączona relacja między obiektem przemieszczania i obiektem Metaverse jest trwała i może zostać usunięta tylko przez określone zasady.

Odłączony obiekt jest obiektem przejściowym, który nie jest połączony z żadnym obiektem Metaverse. Wartości atrybutów odłączonego obiektu nie są przetwarzane w ramach elementu Metaverse. Wartości atrybutów odpowiedniego obiektu w połączonym źródle danych nie są aktualizowane przez aparat synchronizacji.

Za pomocą odłączonych obiektów można przechowywać informacje o tożsamościach w aparacie synchronizacji i przetwarzać je później. Przechowywanie obiektu przemieszczania jako obiektu rozłączonego w przestrzeni łącznika ma wiele zalet. Ponieważ System przygotował już wymagane informacje dotyczące tego obiektu, nie jest konieczne ponowne utworzenie reprezentacji tego obiektu podczas kolejnego importowania z połączonego źródła danych. W ten sposób aparat synchronizacji zawsze ma kompletną migawkę połączonego źródła danych, nawet jeśli nie istnieje bieżące połączenie z połączonym źródłem danych. Odłączone obiekty mogą być konwertowane na połączone obiekty i na odwrót, w zależności od określonych reguł.

Obiekt importu jest tworzony jako odłączony obiekt. Obiekt eksportu musi być obiektem przyłączonym. Logika systemu wymusza tę regułę i usuwa każdy obiekt eksportu, który nie jest przyłączonym obiektem.

## <a name="sync-engine-identity-management-process"></a>Proces zarządzania tożsamościami aparatu synchronizacji
Proces zarządzania tożsamościami kontroluje sposób aktualizowania informacji o tożsamości między różnymi połączonymi źródłami danych. Zarządzanie tożsamościami odbywa się w trzech procesach:

* Importuj
* Synchronizacja
* Eksportowanie

Podczas procesu importowania aparat synchronizacji szacuje informacje o tożsamości przychodzące z połączonego źródła danych. Po wykryciu zmian program tworzy nowe obiekty przemieszczania lub aktualizuje istniejące obiekty tymczasowe w obszarze łącznika w celu synchronizacji.

W trakcie procesu synchronizacji aparat synchronizacji aktualizuje funkcję Metaverse w celu odzwierciedlenia zmian, które wystąpiły w miejscu łącznika i aktualizuje przestrzeń łącznika w celu odzwierciedlenia zmian, które wystąpiły w magazynie Metaverse.

W trakcie procesu eksportowania aparat synchronizacji wypychanie zmian, które są przemieszczane w obiektach przemieszczania i są oflagowane jako oczekujące na eksport.

Na poniższej ilustracji przedstawiono, gdzie każdy proces występuje, gdy informacje o tożsamości są przesyłane z jednego połączonego źródła danych do innego.

![Diagram przedstawia przepływ informacji o tożsamości z połączonych danych do obszaru łącznika (import) do magazynu Metaverse do przestrzeni łącznika (synchronizację) na połączone dane (eksport).](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Importuj proces
Podczas procesu importowania aparat synchronizacji szacuje aktualizacje informacji o tożsamości. Aparat synchronizacji porównuje informacje o tożsamości otrzymane z połączonego źródła danych z informacjami o tożsamości dla obiektu przemieszczania i określa, czy obiekt przemieszczania wymaga aktualizacji. Jeśli konieczne jest zaktualizowanie obiektu przemieszczania przy użyciu nowych danych, obiekt przemieszczania jest oflagowany jako oczekujące na import.

Według obiektów przemieszczania w przestrzeni łącznika przed synchronizacją aparat synchronizacji może przetwarzać tylko informacje o tożsamości, które uległy zmianie. Ten proces zapewnia następujące korzyści:

* **Wydajna synchronizacja**. Ilość danych przetworzonych podczas synchronizacji jest zminimalizowana.
* **Wydajna ponowna synchronizacja**. Można zmienić sposób, w jaki aparat synchronizacji przetwarza informacje o tożsamości bez ponownego łączenia aparatu synchronizacji ze źródłem danych.
* **Możliwość synchronizacji wersji zapoznawczej**. Możesz wyświetlić podgląd synchronizacji, aby sprawdzić, czy założono, że założenia procesu zarządzania tożsamościami są poprawne.

Dla każdego obiektu określonego w łączniku, aparat synchronizacji najpierw próbuje zlokalizować reprezentację obiektu w przestrzeni łącznika łącznika. Aparat synchronizacji bada wszystkie obiekty tymczasowe w przestrzeni łącznika i próbuje znaleźć odpowiedni obiekt przemieszczania, który ma pasujący atrybut kotwicy. Jeśli żaden istniejący obiekt tymczasowy nie ma pasującego atrybutu kotwicy, aparat synchronizacji próbuje znaleźć odpowiedni obiekt tymczasowy o tej samej nazwie wyróżniającej.

Gdy aparat synchronizacji odnajdzie obiekt przemieszczania, który pasuje do nazwy wyróżniającej, ale nie przez zakotwiczenie, wystąpi następujące specjalne zachowanie:

* Jeśli obiekt znajdujący się w obszarze łącznika nie ma zakotwiczenia, aparat synchronizacji usuwa ten obiekt z obszaru łącznika i oznaczy obiekt Metaverse, z którym jest on połączony, przy **następnym uruchomieniu synchronizacji**. Następnie tworzy nowy obiekt importu.
* Jeśli obiekt znajdujący się w obszarze łącznika ma zakotwiczenie, aparat synchronizacji założono, że w połączonym katalogu zmieniono nazwę tego obiektu lub został on usunięty. Przypisuje tymczasową, nową nazwę wyróżniającą dla obiektu obszaru łącznika, aby można było przygotować obiekt przychodzący. Stary obiekt stanie się **chwilą**, oczekiwanie na zaimportowanie lub usunięcie tego łącznika w celu rozwiązania problemu.

Jeśli aparat synchronizacji lokalizuje obiekt przemieszczania, który odnosi się do obiektu określonego w łączniku, określa, jakiego rodzaju zmiany należy zastosować. Na przykład aparat synchronizacji może zmienić nazwę lub usunąć obiekt w połączonym źródle danych lub zaktualizować wartości atrybutów obiektu.

Obiekty przejściowe ze zaktualizowanymi danymi są oznaczane jako oczekujące na import. Dostępne są różne typy oczekujących operacji importowania. W zależności od wyniku procesu importowania, obiekt przemieszczania w przestrzeni łącznika ma jeden z następujących oczekujących typów importu:

* **Brak**. Nie są dostępne żadne zmiany w żadnym z atrybutów obiektu przemieszczania. Aparat synchronizacji nie Oflaguj tego typu jako oczekującego importu.
* **Dodaj**. Obiekt przemieszczania jest nowym obiektem importu w przestrzeni łącznika. Aparat synchronizacji flaguje ten typ jako oczekujący na dodatkowe przetwarzanie w magazynie Metaverse.
* **Aktualizacja**. Aparat synchronizacji odnajduje odpowiedni obiekt tymczasowy w obszarze łącznika i flaguje ten typ jako oczekujące na import, aby aktualizacje atrybutów mogły być przetwarzane w magazynie Metaverse. Aktualizacje obejmują zmianę nazwy obiektu.
* **Delete**. Aparat synchronizacji odnajduje odpowiedni obiekt tymczasowy w obszarze łącznika i flaguje ten typ jako oczekujące na import, aby można było usunąć przyłączony obiekt.
* **Usuń/Dodaj**. Aparat synchronizacji odnajduje odpowiedni obiekt tymczasowy w przestrzeni łącznika, ale typy obiektów nie są zgodne. W takim przypadku przygotowana jest modyfikacja usuwania dodawania. Modyfikacja usuwania dodawania wskazuje aparatowi synchronizacji, że musi wystąpić pełna ponowna synchronizacja tego obiektu, ponieważ różne zestawy reguł stosują się do tego obiektu, gdy zmieniany jest typ obiektu.

Ustawiając stan oczekującego importu obiektu przemieszczania, można zmniejszyć znacznie ilość danych przetworzonych podczas synchronizacji, ponieważ dzięki temu system może przetwarzać tylko te obiekty, które mają zaktualizowane dane.

### <a name="synchronization-process"></a>Proces synchronizacji
Synchronizacja składa się z dwóch powiązanych procesów:

* Synchronizacja ruchu przychodzącego, gdy zawartość programu Metaverse jest aktualizowana przy użyciu danych w obszarze łącznika.
* Synchronizacja wychodząca, gdy zawartość obszaru łącznika jest aktualizowana przy użyciu danych w magazynie Metaverse.

Przy użyciu informacji przemieszczonych w obszarze łącznika proces synchronizacji ruchu przychodzącego tworzy w obiekcie Metaverse Zintegrowany widok danych przechowywanych w połączonych źródłach danych. Wszystkie obiekty tymczasowe lub tylko te z oczekującymi informacjami o imporcie są agregowane w zależności od sposobu konfiguracji reguł.

Proces synchronizacji danych wychodzących aktualizuje obiekty eksportu po zmianie obiektów Metaverse.

Synchronizacja ruchu przychodzącego powoduje utworzenie zintegrowanego widoku w magazynie Metaverse informacji o tożsamości, które są odbierane z połączonych źródeł danych. Aparat synchronizacji może w dowolnym momencie przetwarzać informacje o tożsamości przy użyciu najnowszych informacji o tożsamości, które pochodzą z połączonego źródła danych.

**Synchronizacja ruchu przychodzącego**

Synchronizacja ruchu przychodzącego obejmuje następujące procesy:

* **Inicjowanie** obsługi administracyjnej (nazywanej również **projekcją** , jeśli ważne jest odróżnienie tego procesu od aprowizacji synchronizacji wychodzącej). Aparat synchronizacji tworzy nowy obiekt Metaverse oparty na obiekcie przemieszczania i łączy je. Inicjowanie obsługi administracyjnej jest operacją na poziomie obiektu.
* **Dołącz**. Aparat synchronizacji łączy obiekt przemieszczania z istniejącym obiektem Metaverse. Sprzężenie jest operacją na poziomie obiektu.
* **Importuj przepływ atrybutów**. Aparat synchronizacji aktualizuje wartości atrybutów, nazywane przepływem atrybutu, obiektu w obiekcie Metaverse. Importowanie przepływu atrybutów jest operacją na poziomie atrybutu, która wymaga połączenia między obiektem przemieszczania i obiektem Metaverse.

Inicjowanie obsługi jest jedynym procesem, który tworzy obiekty w obiekcie Metaverse. Inicjowanie obsługi dotyczy tylko obiektów importu, które są odłączone. Podczas aprowizacji aparat synchronizacji tworzy obiekt Metaverse, który odnosi się do typu obiektu obiektu importu i ustanawia połączenie między obydwoma obiektami, tworząc w ten sposób przyłączony obiekt.

Proces dołączania ustanawia także łącze między obiektami importu i obiektem Metaverse. Różnica między przyłączaniem i udostępnianiem polega na tym, że proces przyłączania wymaga, aby obiekt importu był połączony z istniejącym obiektem Metaverse, gdzie proces udostępniania tworzy nowy obiekt Metaverse.

Aparat synchronizacji próbuje dołączyć obiekt importu do obiektu metaverse przy użyciu kryteriów określonych w konfiguracji reguły synchronizacji.

W trakcie procesów aprowizacji i przyłączania aparat synchronizacji łączy rozłączny obiekt z obiektem Metaverse, a następnie dołącza do nich. Po zakończeniu tych operacji na poziomie obiektów aparat synchronizacji może zaktualizować wartości atrybutów skojarzonego obiektu Metaverse. Ten proces jest nazywany przepływem atrybutu importu.

Przepływ atrybutów importowania występuje na wszystkich obiektach importu, które zawierają nowe dane i są połączone z obiektem Metaverse.

**Synchronizacja wychodząca**

Synchronizacja wychodząca aktualizacji eksportuje obiekty, gdy obiekt Metaverse zostanie zmieniony, ale nie został usunięty. Celem synchronizacji danych wychodzących jest oszacowanie, czy zmiany w obiektach Metaverse wymagają aktualizacji obiektów przejściowych w miejscach łączników. W niektórych przypadkach zmiany mogą wymagać aktualizacji obiektów przemieszczania we wszystkich miejscach łączników. Obiekty przemieszczania, które są zmieniane, są oflagowane jako oczekujące na wywóz, co umożliwia ich eksportowanie obiektów. Te obiekty eksportu są później przekazywane do połączonego źródła danych podczas procesu eksportowania.

Synchronizacja wychodząca ma trzy procesy:

* **Inicjowanie obsługi**
* **Anulowania obsługi**
* **Eksportuj przepływ atrybutów**

Inicjowanie obsługi administracyjnej i cofanie aprowizacji to operacje na poziomie obiektów. Anulowanie aprowizacji zależy od zainicjowania obsługi, ponieważ może ją inicjować tylko inicjowanie obsługi administracyjnej. Anulowanie aprowizacji jest wyzwalane po usunięciu powiązania między obiektem Metaverse a obiektem eksportu.

Inicjowanie obsługi jest zawsze wyzwalane, gdy zmiany są stosowane do obiektów w obiekcie Metaverse. Po wprowadzeniu zmian w obiektach Metaverse aparat synchronizacji może wykonać dowolne z następujących zadań w ramach procesu aprowizacji:

* Utwórz przyłączone obiekty, gdzie obiekt Metaverse jest połączony z nowo utworzonym obiektem eksportu.
* Zmień nazwę przyłączonego obiektu.
* Rozłącz linki między obiektem Metaverse i obiektami przemieszczania, tworząc rozłączny obiekt.

Jeśli Inicjowanie obsługi wymaga aparatu synchronizacji do utworzenia nowego obiektu łącznika, obiekt przemieszczania, do którego jest połączony obiekt Metaverse, jest zawsze obiektem eksportu, ponieważ obiekt nie istnieje jeszcze w połączonym źródle danych.

Jeśli obsługa administracyjna wymaga aparatu synchronizacji do odłączenia przyłączonego obiektu, utworzenie odłączonego obiektu powoduje wyzwolenie anulowania aprowizacji. Proces anulowania aprowizacji usuwa obiekt.

Podczas anulowania aprowizacji usuwanie obiektu eksportu nie powoduje fizycznego usunięcia obiektu. Obiekt jest oflagowany jako **usunięty**, co oznacza, że operacja usuwania jest przygotowana do obiektu.

Przepływ atrybutu eksportu występuje również w procesie synchronizacji danych wychodzących, podobnie jak w przypadku synchronizacji ruchu przychodzącego przez przepływ atrybutów importu. Przepływ atrybutu eksportu występuje tylko między obiektami Metaverse i eksportu, które są sprzężone.

### <a name="export-process"></a>Proces eksportowania
W procesie eksportu aparat synchronizacji bada wszystkie obiekty eksportu, które są oflagowane jako oczekujące na eksport w obszarze łącznika, a następnie wysyła aktualizacje do połączonego źródła danych.

Aparat synchronizacji może ustalić pomyślne eksporty, ale nie może ustalić, czy proces zarządzania tożsamościami został ukończony. Obiekty w połączonym źródle danych zawsze mogą być zmieniane przez inne procesy. Ponieważ aparat synchronizacji nie ma trwałego połączenia z połączonym źródłem danych, nie wystarcza do wprowadzania założeń o właściwościach obiektu w połączonym źródle danych, które są oparte tylko na pomyślnym wyeksportowaniu powiadomienia.

Na przykład proces w połączonym źródle danych może zmienić atrybuty obiektu z powrotem do ich oryginalnych wartości (to oznacza, że połączone źródło danych może zastąpić wartości bezpośrednio po wypchnięciu danych przez aparat synchronizacji i pomyślnie zastosowanym w połączonym źródle danych).

Aparat synchronizacji przechowuje informacje o stanie eksportu i importu dotyczące każdego obiektu przemieszczania. Jeśli wartości atrybutów, które są określone na liście dołączania atrybutów od ostatniego eksportu, magazyn importu i eksportu umożliwiają aparatowi synchronizacji odpowiednio reagować. Aparat synchronizacji używa procesu importowania do potwierdzania wartości atrybutów, które zostały wyeksportowane do połączonego źródła danych. Porównanie zaimportowanych i eksportowanych informacji, jak pokazano na poniższej ilustracji, umożliwia aparatowi synchronizacji ustalenie, czy eksport zakończył się powodzeniem, czy też musi być powtórzony.

![Diagram przedstawia synchronizację obiektu między miejscem łącznika i połączonymi danymi za pośrednictwem łącznika.](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Na przykład jeśli aparat synchronizacji eksportuje atrybut C, który ma wartość 5, do połączonego źródła danych, przechowuje C = 5 w pamięci stanu eksportu. Każdy dodatkowy eksport tego obiektu spowoduje ponowne wypróbowanie eksportu C = 5 do połączonego źródła danych, ponieważ aparat synchronizacji zakłada, że ta wartość nie została trwale zastosowana do obiektu (to jest, chyba że inna wartość została ostatnio zaimportowana z połączonego źródła danych). Pamięć eksportu jest czyszczona po odebraniu C = 5 podczas operacji importowania obiektu.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji Azure AD Connect](how-to-connect-sync-whatis.md) .

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

