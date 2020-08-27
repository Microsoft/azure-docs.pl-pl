---
title: Wyświetlanie najważniejszych metryk przy użyciu pulpitów nawigacyjnych platformy Cloudyn na platformie Azure
description: W tym artykule opisano, jak można przeglądać kluczowe metryki za pomocą pulpitów nawigacyjnych platformy Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 03d5e3959b85a2b0bd9ae30744051895ff026b77
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691228"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Wyświetlanie najważniejszych metryk kosztów za pomocą pulpitów nawigacyjnych

Pulpity nawigacyjne platformy Cloudyn zapewniają ogólny widok raportów. Pulpity nawigacyjne umożliwiają wyświetlanie najważniejszych metryk kosztów w jednym widoku. Dostarczają one również informacje o najważniejszych trendach biznesowych, ułatwiając podejmowanie ważnych decyzji.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Pulpity nawigacyjne są również używane do tworzenia widoków dla osób mających różne obowiązki w organizacji, co może obejmować następujące funkcje:

- Kontroler finansowy
- Właściciel aplikacji lub projektu
- Inżynier DevOps
- Kierownictwo

Pulpity nawigacyjne składają się z widżetów, a każdy widżet jest zasadniczo miniaturą raportu. Kliknij widżet, aby otworzyć jego raport. Podczas dostosowywania raportów zapisuje się je w obszarze Moje raporty i są one dodawane do pulpitu nawigacyjnego.

Wersje pulpitów nawigacyjnych są różne dla użytkowników platformy Cloudyn w wersji Management (MSP), Enterprise i Premium. Różnice są określane według poziomów dostępu jednostki. Aby uzyskać więcej informacji na temat poziomów dostępu, zobacz [Poziomy dostępu jednostki](tutorial-user-access.md#entity-access-levels).

Dostępność pulpitu nawigacyjnego zależy od typu konta dostawcy usług w chmurze, które jest używane podczas przeglądania pulpitów nawigacyjnych. Typ informacji dostępnych i zbieranych przez platformę Cloudyn ma wpływ na raporty w pulpitach nawigacyjnych. Na przykład jeśli nie masz konta usługi AWS, pulpit nawigacyjny S3 Tracker nie zostanie wyświetlony. Podobnie, jeśli nie włączysz dostępu usługi Azure Resource Manager do platformy Cloudyn, nie będą wyświetlane żadne informacje specyficzne dla platformy Azure w widżetach pulpitu nawigacyjnego Optimizer.

Możesz użyć dowolnego z gotowych pulpitów nawigacyjnych lub utworzyć własny pulpit nawigacyjny z dostosowanymi raportami. Jeśli nie znasz raportów platformy Cloudyn, zobacz [Raporty platformy Cloudyn](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Tworzenie niestandardowego pulpitu nawigacyjnego

Aby szybko rozpocząć pracę z niestandardowym pulpitem nawigacyjnym, możesz zduplikować istniejący, aby użyć jego właściwości. Następnie możesz go zmodyfikować zgodnie z potrzebami. Na pulpicie nawigacyjnym, który chcesz skopiować, kliknij przycisk **Save As** (Zapisz jako). Duplikować można tylko niestandardowe pulpity nawigacyjne — nie można duplikować pulpitów nawigacyjnych zawartych na platformie Cloudyn.

Aby utworzyć niestandardowy pulpit nawigacyjny:

1. Na stronie głównej kliknij pozycję **Add New +** (Dodaj nowy). Zostanie wyświetlona strona My Dashboard (Mój pulpit nawigacyjny).  
    ![Strona My Dashboard (Mój pulpit nawigacyjny), na której dodaje się nowe raporty](./media/dashboards/my-dashboard.png)
2. Kliknij pozycję **Add New Report** (Dodaj nowy raport). Zostanie wyświetlone okno dodawania raportu.
3. Wybierz raport, który chcesz dodać do widżetu pulpitu nawigacyjnego. Widżet zostanie dodany do pulpitu nawigacyjnego.
4. Powtarzaj powyższe kroki, aż pulpit nawigacyjny będzie gotowy.
5. Aby zmienić nazwę pulpitu nawigacyjnego, kliknij ją na stronie głównej pulpitu nawigacyjnego i wpisz nową nazwę.

## <a name="modify-a-custom-dashboard"></a>Modyfikowanie niestandardowego pulpitu nawigacyjnego

Podobnie jak w przypadku tworzenia niestandardowego pulpitu nawigacyjnego, nie można modyfikować pulpitów nawigacyjnych zawartych na platformie Cloudyn. Aby zmodyfikować niestandardowy raport pulpitu nawigacyjnego:

1. Na pulpicie nawigacyjnym znajdź raport, który chcesz zmodyfikować, a następnie kliknij przycisk **Edit** (Edytuj). Zostanie wyświetlony raport.
2. Wprowadź wszelkie żądane zmiany do raportu, a następnie kliknij pozycję **Save** (Zapisz). Raport zostanie zaktualizowany i zostaną wyświetlone zmiany.

## <a name="share-a-custom-dashboard"></a>Udostępnianie niestandardowego pulpitu nawigacyjnego

Niestandardowy pulpit nawigacyjny możesz udostępnić innym osobom _publicznie_ lub osobom z _Twojej jednostki_. Gdy udostępnisz pulpit nawigacyjny publicznie, wszyscy użytkownicy będą mogli go wyświetlić. Gdy udostępnisz pulpit nawigacyjny swojej jednostce, tylko użytkownicy mający dostęp będą mogli go wyświetlić. Kroki umożliwiające udostępnianie niestandardowego pulpitu nawigacyjnego publicznie i użytkownikom swojej jednostki są podobne.

Aby udostępnić niestandardowy pulpit nawigacyjny publicznie:

1. Na pulpicie nawigacyjnym kliknij pozycję **Dashboard Settings** (Ustawienia pulpitu nawigacyjnego). Zostanie wyświetlone okno ustawień pulpitu nawigacyjnego.  
    ![Ustawienia pulpitu nawigacyjnego dla niestandardowego pulpitu nawigacyjnego](./media/dashboards/dashboard-options.png)
2. W oknie ustawień pulpitu nawigacyjnego kliknij symbol strzałki, a następnie kliknij pozycję **Public** (Publiczny). Zostanie wyświetlone okno dialogowe potwierdzania publicznego udostępnienia pulpitu nawigacyjnego.
3. Kliknij przycisk **Yes** (Tak). Pulpit nawigacyjny jest teraz dostępny dla innych użytkowników.

## <a name="delete-a-custom-dashboard-report"></a>Usuwanie niestandardowego raportu pulpitu nawigacyjnego

Składnik raportu niestandardowego można usunąć z pulpitu nawigacyjnego. Usunięcie raportu z pulpitu nawigacyjnego nie powoduje usunięcia raportu z listy raportów. Zamiast tego usunięcie raportu powoduje usunięcie go tylko z pulpitu nawigacyjnego.

Aby usunąć składnik pulpitu nawigacyjnego, na składniku pulpitu nawigacyjnego kliknij pozycję **Delete** (Usuń). Kliknięcie przycisku **Delete** (Usuń) powoduje natychmiastowe usunięcie składnika pulpitu nawigacyjnego.

## <a name="share-a-dashboard-enterprise"></a>Udostępnianie pulpitu nawigacyjnego (Enterprise)

Niestandardowe pulpity nawigacyjne można udostępniać wszystkim użytkownikom w organizacji lub użytkownikom bieżącej jednostki. Udostępnianie pulpitu nawigacyjnego może dać innym osobom szybki ogólny widok wskaźnika KPI. Udostępnienie pulpitu nawigacyjnego powoduje automatyczne replikowanie pulpitu nawigacyjnego do wszystkich jednostek/klientów platformy Cloudyn. Zmiany w udostępnionym pulpicie nawigacyjnym są automatycznie aktualizowane.

Aby udostępnić pulpit nawigacyjny wszystkim użytkownikom, w tym podjednostkom:

1. Na stronie głównej pulpitu nawigacyjnego kliknij pozycję **Edit** (Edytuj).
2. Kliknij pozycję **Share** (Udostępnij), a następnie wybierz opcję **Public** (Publiczny).
3. Zostanie wyświetlone okno dialogowe potwierdzania globalnego publicznego udostępnienia pulpitu nawigacyjnego.
4. Kliknij przycisk **Yes** (Tak), aby ustawić pulpit nawigacyjny jako globalny publiczny pulpit nawigacyjny.

Aby udostępnić pulpit nawigacyjny wszystkim użytkownikom bieżącej jednostki:

1. Na stronie głównej pulpitu nawigacyjnego kliknij pozycję **Edit** (Edytuj).
2. Kliknij pozycję **Share** (Udostępnij), a następnie wybierz pozycję **My Entity** (Moja jednostka).
3. Kliknij przycisk **Yes** (Tak), aby ustawić pulpit nawigacyjny jako publiczny pulpit nawigacyjny.

## <a name="duplicate-a-custom-dashboard"></a>Duplikowanie niestandardowego pulpitu nawigacyjnego

Podczas tworzenia nowego pulpitu nawigacyjnego warto użyć podobnych właściwości z istniejącego pulpitu nawigacyjnego. Aby utworzyć nowy pulpit nawigacyjny, możesz zduplikować już istniejący.

Duplikować można tylko niestandardowe pulpity nawigacyjne. Nie można duplikować standardowych pulpitów nawigacyjnych.

Duplikowanie (klonowanie) niestandardowego pulpitu nawigacyjnego:

1. Na pulpicie nawigacyjnym, który ma zostać zduplikowany, kliknij przycisk **Save As** (Zapisz jako). Zostanie otwarty nowy pulpit nawigacyjny o tej samej nazwie i numerze.
2. Zmień nazwę zduplikowanego pulpitu nawigacyjnego i zmodyfikuj go tak, jak chcesz.

— Lub —

1. W obszarze Dashboard Settings (Ustawienia pulpitu nawigacyjnego) kliknij pozycję **Save As** (Zapisz jako) w wierszu pulpitu nawigacyjnego, który chcesz zduplikować.
2. Zostanie otwarty zduplikowany pulpit nawigacyjny.
3. Zmień nazwę pulpitu nawigacyjnego i zmodyfikuj go tak, jak chcesz.

## <a name="set-a-default-dashboard"></a>Ustawianie domyślnego pulpitu nawigacyjnego

Pulpit nawigacyjny można ustawić jako domyślny. Ustawienie go jako domyślnego powoduje, że jest on wyświetlany jako karta z lewej strony na liście kart pulpitu nawigacyjnego. Domyślny pulpit nawigacyjny jest wyświetlany po otwarciu platformy Cloudyn.

- Kliknij kartę pulpitu nawigacyjnego, który chcesz ustawić jako domyślny, a następnie kliknij przycisk **Default** (Domyślny) po prawej stronie.

— Lub —

1. Kliknij pozycję **Dashboard Settings** (Ustawienia pulpitu nawigacyjnego), aby wyświetlić listę dostępnych pulpitów nawigacyjnych, i wybierz pulpit nawigacyjny, który ma zostać ustawiony jako domyślny.  
    ![Opcje pulpitu nawigacyjnego dla domyślnego pulpitu nawigacyjnego](./media/dashboards/dashboard-options.png)
2. Kliknij pozycję **Default** (Domyślny) w wierszu pulpitu nawigacyjnego. Zostanie wyświetlone okno dialogowe potwierdzania ustawienia pulpitu nawigacyjnego jako domyślnego.
3. Kliknij przycisk **Yes** (Tak). Pulpit nawigacyjny został ustawiony jako domyślny.

## <a name="management-dashboard"></a>Pulpit nawigacyjny zarządzania
Pulpit nawigacyjny zarządzania (lub pulpit nawigacyjny MSP dla użytkowników MSP) zawiera najważniejsze typy raportów głównych.  
![Pulpit nawigacyjny zarządzania przedstawiający różne raporty](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Podsumowanie jednostki kosztu (tylko wersja Enterprise)
Ten widżet podsumowuje zarządzane jednostki kosztów, w tym liczbę jednostek i liczbę kont.
- Kliknij widżet, aby otworzyć raport Szczegóły firmy.

### <a name="cost-over-time"></a>Koszt w czasie
Ten widżet może pomóc w odkrywaniu trendów kosztów. Wyróżnia on koszt dla ostatniego dnia na podstawie trendu z ostatnich 30 dni.
- Kliknij widżet, aby otworzyć raport Koszty rzeczywiste w czasie i wyświetlić oraz filtrować dodatkowe szczegóły.

### <a name="asset-controller"></a>Kontroler zasobów
Ten widżet wyróżnia liczbę uruchomionych wystąpień z poprzedniego dnia powyżej trendu użycia z ostatnich 30 dni.
- Kliknij widżet, aby otworzyć pulpit nawigacyjny Kontroler zasobów.

### <a name="unused-ri-detector"></a>Wykrywacz nieużywanych wystąpień zarezerwowanych
Ten widżet wyróżnia liczbę nieużywanych rezerwacji usługi Amazon EC2.
- Kliknij widżet, aby otworzyć raport Aktualnie nieużywane rezerwacje, w którym można zobaczyć nieużywane rezerwacje możliwe do zmodyfikowania.

### <a name="cost-by-service"></a>Koszt według usługi
Ten widżet wyróżnia koszty amortyzowane według usługi z ostatnich 30 dni. Zatrzymaj wskaźnik myszy na wykresie kołowym, aby zobaczyć koszty na usługę.
- Kliknij widżet, aby otworzyć raport Analiza kosztu rzeczywistego.

### <a name="potential-savings"></a>Potencjalne oszczędności
Ten widżet zawiera zalecenia dotyczące cennika typu wystąpienia dla usług Amazon EC2 i Amazon RDS.
- Kliknij widżet, aby otworzyć raport Analiza oszczędności. Wyświetla on listę kosztów według typów wystąpień z potencjalnymi oszczędnościami.

### <a name="compute-instances---daily-trend"></a>Wystąpienia obliczeniowe — trend dzienny
Ten widżet wyświetla aktywne wystąpienia według typu z ostatnich 30 dni.
- Kliknij widżet, aby otworzyć raport Wystąpienia w czasie, w którym można wyświetlić podział wszystkich wystąpień uruchomionych w ciągu ostatnich 30 dni.

### <a name="storage-by-department"></a>Magazyn według działu
Ten widżet przedstawia usługi magazynu używane przez działy. Zatrzymaj wskaźnik myszy na wykresie kołowym, aby zobaczyć użycie magazynu przez dział.
- Kliknij widżet, aby otworzyć pulpit nawigacyjny Śledzenie usługi S3.

## <a name="cost-controller-dashboard"></a>Pulpit nawigacyjny Kontroler kosztów
Pulpit nawigacyjny Kontroler kosztów zawiera wstępnie ustawione najważniejsze alokacje kosztów.  
![Pulpit nawigacyjny Kontroler kosztów przedstawiający różne raporty](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Koszt w czasie
Ten widżet pomaga w odkrywaniu trendów kosztów. Wyróżnia on koszt dla ostatniego dnia na podstawie trendu z ostatnich 30 dni.
- Kliknij widżet, aby otworzyć raport Koszty rzeczywiste w czasie i wyświetlić oraz filtrować dodatkowe szczegóły.

### <a name="monthly-cost-trends"></a>Trendy kosztów miesięcznych
Ten widżet wyróżnia przewidywane koszty amortyzowane i koszty rzeczywiste od początku miesiąca.
- Kliknij widżet, aby otworzyć raport Przewidywany koszt dla bieżącego miesiąca, który zawiera podsumowanie kosztów od początku miesiąca.

Ten raport przedstawia koszt od początku miesiąca, koszt z poprzedniego miesiąca oraz przewidywany koszt w bieżącym miesiącu. Przewidywany koszt w bieżącym miesiącu jest obliczany przez dodanie aktualnych kosztów miesięcznych i przewidywań. Przewidywanie bazuje na kosztach monitorowanych w ciągu ostatnich 30 dni.

### <a name="12-month-planner"></a>Plan 12-miesięczny
Ten widżet wyróżnia przewidywane koszty w ciągu następnych 12 miesięcy i potencjalne oszczędności.
- Kliknij widżet, aby otworzyć raport Przewidywany koszt roczny.

### <a name="cost-by-service"></a>Koszt według usługi
Ten widżet wyróżnia koszty amortyzowane według usługi z ostatnich 30 dni.
- Zatrzymaj wskaźnik myszy na wykresie kołowym, aby zobaczyć koszty na usługę.
- Kliknij widżet, aby otworzyć raport Analiza kosztu rzeczywistego.

### <a name="cost-by-account"></a>Koszt według konta
Ten widżet wyróżnia koszty amortyzowane według konta w ciągu ostatnich 30 dni.
- Zatrzymaj wskaźnik myszy na wykresie kołowym, aby zobaczyć koszty według konta.
- Kliknij widżet, aby otworzyć raport Analiza kosztu rzeczywistego.

### <a name="cost-trend-by-day"></a>Trend kosztów według dnia
Ten widżet wyróżnia wydatki w ciągu ostatnich 30 dni.
- Zatrzymaj wskaźnik myszy na wykresie słupkowym, aby zobaczyć koszty na dzień.
- Kliknij widżet, aby otworzyć raport Koszty rzeczywiste w czasie.

### <a name="cost-trend-by-month---last-6-months"></a>Trend kosztów według miesiąca — ostatnie 6 miesięcy

Ten widżet wyróżnia wydatki w ciągu ostatnich sześciu miesięcy.
- Zatrzymaj wskaźnik myszy na wykresie słupkowym, aby zobaczyć koszty na miesiąc.
- Kliknij widżet, aby otworzyć raport Koszty rzeczywiste w czasie.

## <a name="asset-controller-dashboard"></a>Pulpit nawigacyjny Kontroler zasobów

Ten pulpit nawigacyjny przedstawia liczbę uruchomionych wystąpień, dostępnych i używanych dysków, dystrybucji typów wystąpień oraz informacji o magazynie.  
![Pulpit nawigacyjny Kontroler zasobów przedstawiający różne raporty](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Wystąpienia obliczeniowe
Ten widżet przedstawia liczbę uruchomionych wystąpień na podstawie trendu użycia w ciągu ostatnich 30 dni.
- Kliknij widżet, aby otworzyć raport Wystąpienia w czasie.

### <a name="disks"></a>Dyski
Ten widżet wyróżnia łączną liczbę i wielkość dysków, które są używane i dostępne.
- Kliknij widżet, aby otworzyć raport Aktywne dyski.

### <a name="instance-type-distribution"></a>Dystrybucja typu wystąpienia
Ten widżet wyróżnia typy wystąpień na wykresie kołowym.
- Kliknij widżet, aby otworzyć raport Dystrybucja wystąpień, który przedstawia podział aktywnych wystąpień przez wybraną agregację.

### <a name="compute-instances---daily-trend"></a>Wystąpienia obliczeniowe — trend dzienny
Ten widżet wyróżnia wystąpienia obliczeniowe (na miejscu, zarezerwowane i na żądanie) dziennie w ciągu ostatnich 30 dni.
- Zatrzymaj wskaźnik myszy na wykresie, aby wyświetlić liczbę wystąpień obliczeniowych według typu dziennie.
- Kliknij widżet, aby otworzyć raport Wystąpienia w czasie.

### <a name="all-buckets-s3"></a>Wszystkie zasobniki (usługa S3)
Ten widżet wyróżnia łączny magazyn usługi S3 i liczbę przechowywanych obiektów.
- Kliknij widżet, aby otworzyć pulpit nawigacyjny Śledzenie usługi S3. Pulpit nawigacyjny ułatwia znajdowanie, analizowanie i wyświetlanie bieżącego użycia magazynu oraz trendów.

### <a name="sql-db-instances-rds"></a>Wystąpienia bazy danych SQL (RDS)
Ten widżet wyróżnia liczbę uruchomionych wystąpień usługi Amazon RDS na podstawie trendu w ciągu ostatnich 30 dni.
- Kliknij widżet, aby otworzyć raport Wystąpienie usług pulpitu zdalnego w czasie.

## <a name="optimizer-dashboard"></a>Pulpit nawigacyjny Optymalizator
Ten pulpit nawigacyjny przedstawia rekomendacje dotyczące zmniejszania rozmiaru, nieużywane zasoby i potencjalne oszczędności.  
![Pulpit nawigacyjny Optymalizator przedstawiający różne raporty](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>Kalkulator wystąpień zarezerwowanych
Ten widżet wyróżnia rekomendacje dotyczące kupowania wystąpień zarezerwowanych i pokazuje potencjalne oszczędności roczne.
- Kliknij widżet, aby otworzyć Kalkulator wystąpień zarezerwowanych, w którym można określić, kiedy mają być stosowane plany cenowe na żądanie, a kiedy zastrzeżone.

### <a name="sizing"></a>Ustalanie rozmiaru
Ten widżet wyróżnia zalecane rozmiary i ewentualne oszczędności, jeśli zostały wdrożone.
- Kliknij widżet, aby otworzyć raport zawierający zalecenia dotyczące ekonomicznych rozmiarów usługi EC2.

### <a name="unused-ri-detector"></a>Wykrywacz nieużywanych wystąpień zarezerwowanych
Ten widżet wyróżnia liczbę nieużywanych rezerwacji usługi Amazon EC2.
- Kliknij widżet, aby otworzyć raport Aktualnie nieużywane rezerwacje, w którym można zobaczyć nieużywane rezerwacje, które można modyfikować.

###  <a name="available-disks"></a>Dostępne dyski
Ten widżet wyróżnia liczbę niedołączonych dysków we wdrożeniu.
- Kliknij widżet, aby otworzyć raport Niedołączone dyski.

### <a name="rds-ri-calculator"></a>Kalkulator wystąpień zarezerwowanych usługi RDS
Ten widżet wyróżnia zalecenia dotyczące rezerwacji dla wystąpień usługi Amazon RDS oraz potencjalne oszczędności.
- Kliknij widżet, aby otworzyć raport Rekomendacje dotyczące zakupu wystąpień zarezerwowanych usługi RDS, w którym można zobaczyć zalecenia platformy Cloudyn w celu używania wystąpień zarezerwowanych zamiast wystąpień na żądanie.

### <a name="rds-sizing"></a>Ustalanie rozmiarów usługi RDS
Ten widżet pokazuje zalecenia dotyczące rozmiarów i potencjalne oszczędności.
- Kliknij widżet, aby otworzyć raport Rekomendacje dotyczące określania wielkości dla usługi RDS, który zawiera szczegółowe zalecenia dotyczące określania wielkości usługi Amazon RDS.

Rekomendacje dotyczące optymalizacji są oparte na danych użycia i wydajności monitorowanych w ostatnim miesiącu.

## <a name="s3-tracker-dashboard"></a>Pulpit nawigacyjny Śledzenie usługi S3
Pulpit nawigacyjny Śledzenie usługi S3 ułatwia znajdowanie, analizowanie i wyświetlanie bieżącego użycia magazynu oraz trendów.  
![Pulpit nawigacyjny Śledzenie usługi S3 przedstawiający różne raporty](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Wszystkie zasobniki
Ten widżet wyróżnia łączny rozmiar wszystkich zasobników w GB i całkowitą liczbę obiektów w zasobniku.
- Kliknij widżet, aby otworzyć raport Rozkład rozmiaru usługi S3. Raport ułatwia analizowanie rozmiaru usługi S3 według zasobnika, folderu najwyższego poziomu, klasy magazynu i stanu przechowywania wersji.

### <a name="bucket-properties"></a>Właściwości zasobnika
Ten widżet wyróżnia łączną liczbę zasobników magazynu.
- Kliknij widżet, aby wyświetlić raport Właściwości zasobnika usługi S3.

### <a name="scan-status"></a>Stan skanowania
Ten widżet pokazuje, kiedy zostało wykonane ostatnie skanowanie usługi S3 i kiedy rozpocznie się następne.
- Kliknij widżet, aby wyświetlić raport Stan skanowania usługi S3.

### <a name="storage-by-bucket"></a>Magazyn według zasobnika
Ten widżet wyróżnia wartość procentową, z której korzysta każda klasa magazynu zasobników.
- Kliknij widżet, aby otworzyć raport Rozkład rozmiaru usługi S3. Raport ułatwia analizowanie rozmiaru usługi S3 według zasobnika, folderu najwyższego poziomu, klasy magazynu i stanu przechowywania wersji.

### <a name="number-of-objects-by-bucket"></a>Liczba obiektów według zasobnika
Ten widżet wyróżnia liczbę obiektów w zasobniku w rzeczywistej liczbie i wartości procentowej. Zatrzymaj wskaźnik myszy na zasobniku, aby wyświetlić łączną liczbę obiektów.
- Kliknij widżet, aby otworzyć raport Rozkład rozmiaru usługi S3 (na podstawie skanowania).

## <a name="cloud-comparison-dashboard"></a>Pulpit nawigacyjny Porównanie z chmurą
Pulpit nawigacyjny Porównanie z chmurą ułatwia porównanie kosztów od różnych dostawców chmury na podstawie cennika, typu procesora i rozmiaru pamięci RAM.  
![Pulpit nawigacyjny Porównanie z chmurą przedstawiający różne raporty](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>Koszt usługi EC2 na platformie Azure według typu wystąpienia
Ten widżet wyróżnia ostatnie 30 dni użycia w stawkach na żądanie. Porównuje koszt z bieżącym kosztem usługi Amazon EC2 i potencjalnym kosztem na platformie Azure.
- Zatrzymaj wskaźnik myszy na paskach, aby porównać koszty według typu wystąpienia.
- Kliknij widżet, aby otworzyć raport Eksportowanie wdrożenia — Analiza kosztów.

### <a name="ec2-cost-in-azure"></a>Koszt usługi EC2 na platformie Azure
Ten widżet pokazuje bieżące koszty usługi Amazon EC2 i porównuje je z platformą Azure. Porównanie odbywa się na podstawie ostatnich 30 dni użycia w ramach stawek na żądanie.
- Kliknij widżet, aby otworzyć raport Eksportowanie wdrożenia — Analiza kosztów.

### <a name="ec2azure-instance-type-mapping"></a>Mapowanie typu wystąpienia platformy Azure/usługi EC2
Ten widżet wyróżnia najlepsze mapowanie elastycznych jednostek obliczeniowych między usługą Amazon EC2 i platformą Azure.
- Kliknij widżet, aby otworzyć raport Mapowanie typów wystąpień.

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej o raportach, zapoznaj się z artykułem [Raporty platformy Cloudyn](use-reports.md).
