---
title: Informacje o procesie przenoszenia w obszarze przenoszenia zasobów platformy Azure
description: Poznaj proces przenoszenia zasobów między regionami przy użyciu usługi Azure Resource przenoszącej
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.openlocfilehash: 4d520f51717aa11dba55697d63852b17e0ba9cf0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90604499"
---
# <a name="about-the-move-process"></a>Informacje o procesie przenoszenia

[Usługa Azure Resource](overview.md) przenoszona ułatwia przenoszenie zasobów platformy Azure między regionami platformy Azure. Ten artykuł podsumowuje składniki używane przez narzędzie do przenoszenia zasobów i opisuje proces przenoszenia. 


## <a name="components"></a>Składniki

Te składniki są używane podczas przenoszenia regionów.

**Składnik** | **Szczegóły**
--- | ---
**Przeniesienie zasobów** |  Współrzędne przenoszenia zasobów z [dostawcami zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types) , aby organizować przenoszenie zasobów między regionami. Przenoszenie zasobów analizuje zależności zasobów oraz utrzymuje i zarządza stanem zasobów podczas procesu przenoszenia. 
**Przenoszenie kolekcji** |  Kolekcja przenoszenia jest obiektem [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) .<br/><br/> Kolekcja Move jest tworzona podczas procesu przenoszenia regionu dla każdej sparowanej kombinacji regionów źródłowych i docelowych w ramach subskrypcji. Kolekcja zawiera metadane i informacje o konfiguracji zasobów, które mają zostać przeniesione.<br/><br/>Zasoby dodane do kolekcji przenoszenia muszą znajdować się w tej samej subskrypcji, ale mogą znajdować się w różnych grupach zasobów. 
**Przenieś zasób** | Po dodaniu zasobu do kolekcji przenoszenia jest on śledzony przez przeniesienie zasobów jako zasób przenoszenia.<br/><br/> Program przenoszenia zasobów przechowuje informacje dla wszystkich zasobów przenoszenia w kolekcji przenoszenia i utrzymuje relację jeden-do-jednego między zasobem źródłowym a docelowym. 
**Zależności** | Element przenoszenia zasobów sprawdza poprawność zasobów dodawanych do kolekcji i sprawdza, czy zasoby mają jakiekolwiek zależności, które nie znajdują się w kolekcji przenoszenia.<br/><br/> Po zidentyfikowaniu zależności dla zasobu można dodać te zależności do kolekcji przenoszenia i przenieść je zbyt lub wybrać alternatywne istniejące zasoby w regionie docelowym. Przed rozpoczęciem przenoszenia należy rozwiązać wszystkie zależności. 



## <a name="move-region-process"></a>Proces przenoszenia regionu 

![Diagram przedstawiający kroki przenoszenia](./media/about-move-process/move-steps.png)

Każdy przenoszony zasób przechodzi przez Podsumowanie kroków.

**Krok** | **Szczegóły** | **Stan/problemy**
--- | --- | --- 
**Krok 1. Wybieranie zasobów** | Wybierz zasób. Zasób zostanie dodany do kolekcji Move. | Stan zasobu jest przenoszony w celu *przygotowania do czasu oczekiwania*.<br/><br/> Jeśli zasób ma zależności, *Walidacja zależności* wskazuje, że należy dodać zasoby zależne do kolekcji przenoszenia.
**Krok 2. Weryfikowanie zależności** | Rozpocznij proces weryfikacji.<br/><br/> Jeśli walidacja wskazuje, że zasoby zależne są oczekujące, Dodaj je do kolekcji Move. <br/><br/> Dodaj wszystkie zasoby zależne, nawet jeśli nie chcesz ich przenosić. Później możesz określić, że przenoszone zasoby powinny używać różnych zasobów w regionie docelowym.<br/><br/> Należy ponownie sprawdzić poprawność, dopóki nie zostaną żadne oczekujące zależności. | Po dodaniu wszystkich zależności i walidacji zakończy się pomyślnie, stan zasobów zostanie przesunięty w celu *przygotowania do czasu*, bez jakichkolwiek problemów.
**Krok 3: przygotowanie** | Rozpocznij proces przygotowywania. Kroki przygotowania zależą od przenoszonych zasobów:<br/><br/> - **Zasoby bezstanowe**: zasoby bezstanowe mają tylko informacje o konfiguracji. Te zasoby nie potrzebują ciągłej replikacji danych w celu ich przeniesienia. Przykłady obejmują sieci wirtualne platformy Azure (sieci wirtualnych), karty sieciowe, moduły równoważenia obciążenia i sieciowe grupy zabezpieczeń. W przypadku tego typu zasobu proces przygotowywania generuje szablon Azure Resource Manager.<br/><br/> - **Zasoby stanowe**: zasoby stanowe mają zarówno informacje o konfiguracji, jak i dane, które muszą zostać przeniesione. Przykłady obejmują maszyny wirtualne platformy Azure i bazy danych Azure SQL Database. Proces przygotowywania różni się dla każdego zasobu. Może on obejmować replikację zasobu źródłowego do regionu docelowego. | Rozpoczęcie przenoszenia stanu zasobów *w celu przygotowania*go do postępu.<br/><br/> Po zakończeniu przygotowania stan zasobu zostanie przeniesiony, aby *zainicjować oczekujące przeniesienie*, bez problemów.<br/><br/> Niepowodzenie proces przenoszenia stanu do *przygotowania nie powiodło się*.
**Krok 4. inicjowanie przenoszenia** | Rozpocznij proces przenoszenia. Metoda Move zależy od typu zasobu:<br/><br/> - **Bezstanowe**: zazwyczaj w przypadku bezstanowych zasobów proces przenoszenia wdraża zaimportowany szablon w regionie docelowym. Szablon jest oparty na ustawieniach zasobów źródłowych oraz wszelkich ręcznych zmianach wprowadzonych w ustawieniach docelowych.<br/><br/> - **Stanowa**: w przypadku zasobów stanowych proces przenoszenia może polegać na utworzeniu zasobu lub włączeniu kopii w regionie docelowym.<br/><br/>  W przypadku tylko zasobów stanowych inicjowanie przenoszenia może spowodować przestoje zasobów źródłowych. Na przykład maszyny wirtualne i SQL. | Rozpoczęcie przenoszenia powoduje przeniesienie stanu, aby *zainicjować przenoszenie w toku*.<br/><br/> Pomyślne zainicjowanie przenoszenia powoduje przeniesienie stanu zasobu do *oczekujących przejść*, bez problemów. <br/><br/> Proces przenoszenia zakończony niepowodzeniem powoduje przeniesienie stanu, aby *zainicjować przenoszenie nie powiodło się*.
**Krok 5 — opcja 1: odrzuć przeniesienie** | Po początkowym przeniesieniu możesz zdecydować, czy chcesz kontynuować pracę z pełnym przenoszeniem. Jeśli nie, możesz odrzucić przeniesienie, a obiekt przenoszenia zasobów usuwa zasoby utworzone w celu. Proces replikacji dla zasobów stanowych jest kontynuowany po procesie odrzucenia. Ta opcja jest przydatna do testowania. | Odrzucanie zasobów przenosi stan *w toku*.<br/><br/> Pomyślnie Odrzuć stan przeniesienia, aby *zainicjować oczekujące przeniesienie*, bez problemów.<br/><br/> Nie można odrzucić stanu przenoszenia do *odrzucenia przeniesienia*. 
**Krok 5 opcja 2: Zatwierdź przeniesienie** | Po początkowym przeniesieniu, jeśli chcesz przejść do trybu pełnego przenoszenia, sprawdzasz zasoby w regionie docelowym, a gdy wszystko będzie gotowe, zatwierdzisz przeniesienie.<br/><br/> W przypadku zasobów stanowych zatwierdzenie może spowodować, że zasoby źródłowe, takie jak maszyny wirtualne lub SQL stają się niedostępne. | Jeśli zatwierdzisz przeniesienie, stan zasobu zostanie przeniesiony do * zatwierdzanie przenoszenia w toku * *.<br/><br/> Po pomyślnym zatwierdzeniu stan zasobu pokazuje, że *przeniesienie zostało zakończone*, bez problemów.<br/><br/> Nie *można przenieść*stanu przeniesienia do zatwierdzenia.
**Krok 6. Usuwanie źródła** | Po zatwierdzeniu przenoszenia i sprawdzeniu zasobów w regionie docelowym można usunąć zasób źródłowy. | Po zatwierdzeniu przenoszenia stan zasobu jest przenoszony do *oczekującego usunięcia źródła*.


## <a name="move-region-states"></a>Stany regionu przenoszenia

Proces przenoszenia ma wiele stanów i problemy, które mogą wystąpić w poszczególnych Stanach. Są one podsumowywane w schemacie blokowym.

![Schemat blokowy dla możliwych stanów i problemów](./media/about-move-process/process.png)


### <a name="dependency-analysis"></a>Analiza zależności

W trakcie procesu przenoszenia może pojawić się monit o zweryfikowanie zależności, jeśli:
- Zasób używa zasobów zależnych, które nie znajdują się w kolekcji przenoszenia.
- Zależny zasób w kolekcji Move ma własne zależności, które nie znajdują się w kolekcji Move.
- Zmodyfikowano ustawienia docelowe dla zasobu i należy ponownie sprawdzić poprawność zależności.


### <a name="remove-resources"></a>Usuwanie zasobów

Jeśli nie chcesz przenosić zasobu, możesz go usunąć z kolekcji Przenieś. Ogólnie rzecz biorąc, zasób jest usuwany z kolekcji wraz ze wszystkimi skojarzonymi akcjami lub obiektami, takimi jak replikacja lub przechowywane szablony. Dokładnie to, co się dzieje po usunięciu zasobu, zależy od typu zasobu i stanu zasobu po jego usunięciu. [Dowiedz się więcej](remove-move-resources.md).

## <a name="move-impact"></a>Wpływ przenoszenia

W tabeli podsumowano, co ma wpływ w przypadku przechodzenia między regionami.

**Zachowanie** | **Między regionami**
--- | --- | --- 
**Dane** | Dane zasobów i metadane są przenoszone.<br/><br/> Metadane są przechowywane tymczasowo do śledzenia stanu zależności zasobów i operacji.
**Zasób** | Zasób źródłowy pozostaje nienaruszony, aby upewnić się, że aplikacje będą działać i opcjonalnie można je usunąć po przeniesieniu.<br/><br/> Zasób jest tworzony w regionie docelowym.
**Przenieś proces** | Proces wieloetapowy wymagający ręcznej interwencji i monitorowania.
**Testowanie** | Testowanie przenoszenia jest ważne, ponieważ aplikacje powinny kontynuować pracę zgodnie z oczekiwaniami w regionie docelowym, po przeniesieniu.
**Downtime (Przestoje)** |  Nie oczekiwano utraty danych, ale niektóre przestoje dotyczące przenoszenia zasobów.



## <a name="next-steps"></a>Następne kroki

[Przenieś](tutorial-move-region-virtual-machines.md) Maszyny wirtualne platformy Azure do innego regionu.
[Przenieś](tutorial-move-region-sql.md) Zasoby usługi Azure SQL w innym regionie.