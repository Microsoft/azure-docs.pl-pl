---
title: Samouczek — przypisywanie dostępu przy użyciu rozwiązania Cloudyn na platformie Azure
description: Ten samouczek zawiera informacje na temat przypisywania dostępu do danych rozwiązania Cloudyn przy użyciu kont użytkowników w celu zdefiniowania poziomów dostępu do jednostek.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: a3c10d95a57ea81f269af8f8f1cad49fc1c1b845
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131262"
---
# <a name="tutorial-assign-access-to-cloudyn-data"></a>Samouczek: Przypisywanie dostępu do danych rozwiązania Cloudyn

Prawa dostępu do danych rozwiązania Cloudyn są przyznawane za pośrednictwem funkcji zarządzania użytkownikami lub jednostkami. Konta użytkowników platformy Cloudyn określają dostęp do *jednostek* i funkcji administracyjnych. Istnieją dwa typy dostępu: administratora i użytkownika. O ile nie zmodyfikowano ich dla poszczególnych użytkowników, prawa dostępu administratora umożliwiają użytkownikowi nieograniczone korzystanie ze wszystkich funkcji w portalu Cloudyn, w tym z funkcji, takich jak: zarządzanie użytkownikami, zarządzanie listami adresatów oraz dostęp jednostki głównej do danych wszystkich jednostek. Dostęp użytkownika jest przeznaczony dla użytkowników końcowych i umożliwia im wyświetlanie raportów oraz tworzenie raportów przy użyciu dostępu do danych jednostki.

Jednostki są używane do odzwierciedlania struktury hierarchicznej organizacji biznesowej. Identyfikują one działy, oddziały i zespoły Twojej organizacji w rozwiązaniu Cloudyn. Hierarchia jednostek pomaga dokładnie śledzić wydatki dla poszczególnych jednostek.

Zarejestrowanie konta lub umowy dotyczącej platformy Azure spowodowało utworzenie w rozwiązaniu Cloudyn konta z uprawnieniami administratora, więc możesz wykonać wszystkie czynności opisane w tym samouczku. Ten samouczek dotyczy dostępu do danych rozwiązania Cloudyn, w tym danych zarządzania użytkownikami i zarządzania jednostkami. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie użytkownika z prawami dostępu administratora
> * Tworzenie użytkownika z prawami dostępu użytkownika
> * Usuwanie użytkownika
> * Usuwanie lub eksportowanie danych osobowych
> * Tworzenie jednostek i zarządzanie nimi


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć konto platformy Azure.
- Musisz mieć rejestrację próbną lub płatną subskrypcję usługi Cloudyn.

## <a name="create-a-user-with-admin-access"></a>Tworzenie użytkownika z prawami dostępu administratora

Mimo że Ty masz już prawa dostępu administratora, współpracownicy z organizacji mogą również potrzebować takich praw. W portalu Cloudyn kliknij symbol koła zębatego w prawym górnym rogu i wybierz pozycję **Zarządzanie użytkownikami**. Kliknij pozycję **Dodaj nowego użytkownika**, aby dodać nowego użytkownika.

Wprowadź wymagane informacje o użytkowniku. **Identyfikator logowania** musi być prawidłowym adresem e-mail. Wybierz uprawnienia, aby zezwolić na zarządzanie użytkownikami, co pozwoli użytkownikowi na tworzenie i modyfikowanie innych użytkowników. Zarządzanie listami adresatów umożliwia użytkownikowi edytowanie list adresatów. Link z informacjami logowania jest wysyłany do użytkownika pocztą e-mail z platformy Cloudyn po wybraniu pozycji **Powiadom użytkownika pocztą e-mail**. Podczas pierwszego logowania użytkownik ustawia hasło.

W obszarze **Użytkownik ma dostęp administratora** wybrano jednostkę główną organizacji. Pozostaw zaznaczoną jednostkę główną i zapisz informacje o użytkowniku. Wybranie jednostki głównej pozwoli użytkownikowi na posiadanie uprawnień administratora nie tylko do jednostki głównej w drzewie, ale także do wszystkich jednostek, które znajdują się pod nią.  
  ![Przykład przedstawiający dostęp administratora w polu Dodaj nowego użytkownika](./media/tutorial-user-access/new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Tworzenie użytkownika z prawami dostępu użytkownika
Zazwyczaj użytkownicy, którzy potrzebują dostępu do danych rozwiązania Cloudyn, takich jak pulpity nawigacyjne i raporty, powinni mieć prawa użytkownika do ich wyświetlania. Utwórz nowego użytkownika z dostępem użytkownika podobnego do utworzonego przy użyciu dostępu administratora, pamiętając o następujących różnicach:

- Usuń zaznaczenie pól **Zezwól na zarządzanie użytkownikami** i **Zezwól na zarządzanie listami adresatów**, a następnie usuń wszystkie pozycje z listy **Użytkownik ma dostęp administratora**.
- Zaznacz jednostki, do których użytkownik musi mieć dostęp, na liście **Użytkownik ma dostęp użytkownika**.
- W razie potrzeby możesz również zezwolić administratorowi na dostęp do określonych jednostek.

![Przykład przedstawiający dostęp użytkownika w polu Dodaj nowego użytkownika](./media/tutorial-user-access/new-user-access.png)

Aby obejrzeć film wideo z samouczkiem dotyczącym dodawania użytkowników, zobacz [Adding Users to Cloudyn](https://youtu.be/Nzn7GLahx30) (Dodawanie użytkowników do rozwiązania Cloudyn).

## <a name="delete-a-user"></a>Usuwanie użytkownika

Po usunięciu użytkownika wszelkie jednostki, do których użytkownik miał dostęp, pozostaną bez zmian. W przypadku usunięcia użytkownika zostaną usunięte zapisane raporty *osobiste*. Zapisane raporty *publiczne* utworzone przez użytkownika nie są usuwane.

Nie możesz usunąć siebie jako użytkownika.

> [!WARNING]
> Po usunięciu użytkownika nie można go przywrócić.

1.    W portalu Cloudyn kliknij symbol koła zębatego w prawym górnym rogu i wybierz pozycję **Zarządzanie użytkownikami**.
2.    Na liście użytkowników wybierz użytkownika, którego chcesz usunąć, a następnie kliknij pozycję **Usuń użytkownika** (symbol kosza).
3.    W oknie Usuń użytkownika kliknij przycisk **Tak**, a następnie kliknij przycisk **OK**.


## <a name="delete-or-export-personal-data"></a>Usuwanie lub eksportowanie danych osobowych

Jeśli chcesz usunąć lub wyeksportować dane osobowe z usługi Cloudyn, musisz utworzyć bilet pomocy technicznej. Po utworzeniu biletu pomocy technicznej będzie on działać jak formalne żądanie — żądanie podmiotu danych. Firma Microsoft następnie podejmie natychmiastowe działania, aby usunąć konto i wszelkie dane klienta lub dane osobowe. Aby dowiedzieć się więcej o sposobie przesyłania żądań usunięcia lub wyeksportowania danych, zobacz [Żądania podmiotów danych dotyczące danych w usłudze Cloudyn](https://www.cloudyn.com/cloudyn-gdpr-requests).

## <a name="create-and-manage-entities"></a>Tworzenie jednostek i zarządzanie nimi

W przypadku definiowania hierarchii jednostek kosztów najlepszym rozwiązaniem jest zidentyfikowanie struktury organizacji. Jednostki umożliwiają dzielenie wydatków na poszczególne konta lub subskrypcje. Jednostki kosztów tworzy się w celu utworzenia grup logicznych na potrzeby zarządzania wydatkami i śledzenia ich. Podczas tworzenia drzewa zastanów się, jak musisz lub chcesz wyświetlać swoje koszty posegregowane według jednostek biznesowych, centrów kosztów, środowisk i działów sprzedaży. Drzewo jednostek w rozwiązaniu Cloudyn jest elastyczne z powodu dziedziczenia jednostek.

Poszczególne subskrypcje dla kont w chmurze są połączone z określonymi jednostkami. Jednostkę można skojarzyć z kontem dostawcy usług w chmurze lub subskrypcją. Dlatego jednostki są wielodostępne. Dostęp poszczególnych użytkowników można przypisywać do tylko do ich segmentu firmy przy użyciu jednostek. Dzięki temu dane są izolowane, nawet w dużych częściach firm, takich jak przedstawicielstwa. Izolacja danych wspomaga także ład.  

Po zarejestrowaniu konta lub umowy związanej z platformą Azure na platformie Cloudyn dane zasobów platformy Azure, w tym dane użycia, wydajności, rozliczeń i tagów, z subskrypcji zostały skopiowane do konta platformy Cloudyn. Drzewo jednostek trzeba jednak utworzyć ręcznie. W przypadku pominięcia rejestracji usługi Azure Resource Manager tylko dane rozliczeń i pewne raporty zasobów są dostępne w portalu Cloudyn.

W portalu Cloudyn kliknij symbol koła zębatego w prawym górnym rogu, a następnie wybierz pozycję **Konta w chmurze**. Rozpoczynasz od pojedynczej jednostki (głównej) i tworzysz drzewo jednostek pod tą jednostką główną. Oto przykład hierarchii jednostek, która po ukończeniu drzewa może przypominać wiele organizacji IT:

![Przykład drzewa jednostek wyświetlany na stronie Zarządzanie kontami](./media/tutorial-user-access/entity-tree.png)

Obok pozycji **Jednostki** kliknij pozycję **Dodaj jednostkę**. Wprowadź informacje o osobie lub dziale do dodania. Wartości pól **Imię i nazwisko** i **Adres e-mail** nie muszą być zgodne z danymi istniejących użytkowników. Jeśli chcesz wyświetlić listę poziomów dostępu, wyszukaj w pomocy zagadnienia dotyczące *dodawania jednostki*.

![Przykład przedstawiający nazwę jednostki i poziomy dostępu w polu Dodaj jednostki](./media/tutorial-user-access/add-entity.png)

Gdy wszystko będzie gotowe, **zapisz** jednostkę.

### <a name="entity-access-levels"></a>Poziomy dostępu jednostki

Poziomy dostępu jednostki w połączeniu z dostępem użytkownika umożliwiają zdefiniowanie typu akcji dostępnych w portalu Cloudyn.

- **Przedsiębiorstwo** — umożliwia tworzenie podrzędnych jednostek kosztów i zarządzanie nimi.
- **Przedsiębiorstwo i alokacja kosztów** — umożliwia tworzenie podrzędnych jednostek kosztów, w tym alokacji kosztów dla kont skonsolidowanych, i zarządzanie nimi.
- **Przedsiębiorstwo, koszty oparte na alokacji kosztów elementu nadrzędnego** — umożliwia tworzenie podrzędnych jednostek kosztów i zarządzanie nimi. Koszty dla konta są oparte na modelu alokacji kosztów elementu nadrzędnego.
- **Tylko niestandardowe pulpity nawigacyjne** — użytkownik może wyświetlić tylko wstępnie zdefiniowane niestandardowe pulpity nawigacyjne.
- **Tylko pulpity nawigacyjne** — użytkownik może wyświetlić tylko pulpity nawigacyjne.

### <a name="create-a-cost-entity-hierarchy"></a>Tworzenie hierarchii jednostek kosztów

Aby utworzyć hierarchię jednostek kosztów, musisz mieć konto na poziomie Przedsiębiorstwo lub Przedsiębiorstwo i alokacja kosztów.

W portalu Cloudyn kliknij symbol koła zębatego w prawym górnym rogu, a następnie wybierz pozycję **Cloud Accounts (Konta w chmurze)** . Drzewo **Entities (Jednostki)** zostanie wyświetlone w okienku po lewej stronie. W razie potrzeby rozwiń drzewo jednostek, aby wyświetlić jednostkę, która ma zostać skojarzona z kontem.  Twoje konta dostawcy usług w chmurze są wyświetlane na kartach w okienku po prawej stronie. Wybierz kartę, kliknij i przeciągnij konto lub subskrypcję do jednostki, a następnie upuść przeciągany element. Pole **Przeniesienie** informuje, że konto zostało przeniesione pomyślnie. Kliknij przycisk **OK**.

Istnieje także możliwość skojarzenia wielu kont z jednostką. Wybierz konta, a następnie kliknij polecenie **Przenieś**. W polu Przenieś konta wybierz jednostkę, do której ma zostać przeniesione konto, a następnie kliknij polecenie **Zapisz**. Pole Przenieś konta żąda potwierdzenia przeniesienia kont. Kliknij pozycję **Tak**, a następnie kliknij pozycję **OK**.

Aby obejrzeć film wideo z samouczkiem dotyczącym tworzenia hierarchii jednostek kosztów, zobacz [Creating a Cost Entity Hierarchy in Cloudyn](https://youtu.be/dAd9G7u0FmU) (Tworzenie hierarchii jednostek kosztów w rozwiązaniu Cloudyn).

Jeśli jesteś użytkownikiem platformy Azure z umową Enterprise Agreement, obejrzyj film z samouczkiem dotyczącym kojarzenia kont i subskrypcji z jednostkami: [Connecting to Azure Resource Manager with Cloudyn](https://youtu.be/oCIwvfBB6kk) (Łączenie z usługą Azure Resource Manager przy użyciu rozwiązania Cloudyn).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie użytkownika z prawami dostępu administratora
> * Tworzenie użytkownika z prawami dostępu użytkownika
> * Usuwanie użytkownika
> * Usuwanie lub eksportowanie danych osobowych
> * Tworzenie jednostek i zarządzanie nimi


Jeśli dostęp do interfejsu API usługi Azure Resource Manager nie został jeszcze włączony dla Twoich kont, przejdź do następującego artykułu.

> [!div class="nextstepaction"]
> [Aktywowanie subskrypcji i kont platformy Azure](./activate-subs-accounts.md)