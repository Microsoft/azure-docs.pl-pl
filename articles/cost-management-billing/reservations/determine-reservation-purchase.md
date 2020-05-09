---
title: Określanie rezerwacji platformy Azure, którą należy kupić
description: Ten artykuł pomaga określić, którą rezerwację należy zakupić.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: feee7475dcadc6d06693d9e60020097f8dc9149c
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628609"
---
# <a name="determine-what-reservation-to-purchase"></a>Określanie odpowiedniej rezerwacji do kupienia

Wszystkie rezerwacje, z wyjątkiem usługi Azure Databricks, są stosowane godzinowo. Rezerwacje należy zakupić na podstawie spójnego użycia podstawowego. Istnieje wiele sposobów określania elementów do zakupu. Ten artykuł pomaga określić, które rezerwacje należy kupić.

Zakup większej pojemności niż historyczne użycie powoduje, że rezerwacja jest niewystarczająca. Jeśli to możliwe, należy unikać niewystarczających rezerwacji. Niewykorzystana pojemność zarezerwowana nie jest przenoszona na następną godzinę. Użycie przekraczające zarezerwowaną ilość jest obciążane droższymi stawkami płatności zgodnie z rzeczywistym użyciem.

## <a name="analyze-usage-data"></a>Analiza danych użycia

W poniższych sekcjach znajdują się informacje ułatwiające analizowanie codziennych danych użycia w celu określenia użycia bazowego i rezerwacji do kupienia.

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>Analizowanie użycia pod kątem zakupu wystąpienia zarezerwowanego maszyny wirtualnej

Określ odpowiedni rozmiar maszyny wirtualnej do kupienia. Na przykład rezerwacja zakupiona dla maszyn wirtualnych serii ES nie ma zastosowania do maszyn wirtualnych serii E i odwrotnie.

Maszyny wirtualne serii promocyjnej nie uzyskują rabatu na rezerwacje, dlatego należy je usunąć z analizy.

Aby ograniczyć dane do użycia kwalifikujących się maszyn wirtualnych, zastosuj następujące filtry do danych użycia:

- W elemencie **MeterCategory** odfiltruj elementy **Maszyny wirtualne**.
- Pobierz informacje **ServiceType** z elementu **AdditionalInfo**. Informacje sugerują prawidłowy rozmiar maszyny wirtualnej. Przykład: Standardowa E32.
- Użyj pola **ResourceLocation**, aby określić centrum danych użycia.

Ignoruj zasoby, które mają mniej niż 24 godziny użycia w ciągu dnia.

Jeśli chcesz analizować dane na poziomie rodziny rozmiaru wystąpienia, możesz uzyskać wartości elastyczności rozmiaru wystąpienia ze strony [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv). Połącz wartości z danymi, aby przeprowadzić analizę. Aby uzyskać więcej informacji o elastyczności rozmiaru wystąpienia, zobacz artykuł [Elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-usage-for-an-azure-synapse-analytics-reserved-instance-purchase"></a>Analizowanie użycia dla zakupu wystąpienia zarezerwowanego usługi Azure Synapse Analytics

Pojemność zarezerwowana dotyczy cen jednostek DWU usługi Azure Synapse Analytics. Nie dotyczy kosztów licencji usługi Azure Synapse Analytics ani kosztów innych niż obliczenia.

Aby zawęzić kwalifikujące się użycie, zastosuj następujące filtry do danych użycia:


- W elemencie **MeterCategory** odfiltruj elementy **SQL Database**.
- W elemencie **MeterName** odfiltruj elementy **Rdzeń wirtualny**.
- W elemencie **MeterSubCategory** odfiltruj wszystkie rekordy użycia, które mają w nazwie _Obliczenia_.

Z elementu **AdditionalInfo** pobierz wartość**Rdzenie wirtualne**. Zawiera on informacje o liczbie używanych rdzeni wirtualnych. Ilość to liczba **rdzeni wirtualnych** pomnożona przez liczbę godzin korzystania z bazy danych.

Dane informują o spójnym użyciu:

- kombinacja typu bazy danych. Na przykład wystąpienie zarządzane lub elastyczna pula na pojedynczą bazę danych.
- Warstwa usługi. Przykład: ogólnego przeznaczenia lub krytyczne dla działania firmy.
- Generacja. Przykład: generacja 5.
- Lokalizacja zasobu

### <a name="analysis-for-azure-synapse-analytics"></a>Analiza usługi Azure Synapse Analytics

Pojemność zarezerwowana ma zastosowanie do użycia jednostek DWU usługi Azure Synapse Analytics i jest kupowana w przyrostach co 100 jednostek DWU. Aby zawęzić kwalifikujące się użycie, zastosuj następujące filtry do danych użycia:

- W elemencie **MeterName** odfiltruj elementy **100 jednostek DWU**.
- W elemencie **Podkategoria miernika** odfiltruj elementy **Zoptymalizowane pod kątem obliczeń Gen2**.

Użyj pola **Lokalizacja zasobu**, aby określić użycie usługi Azure Synapse Analytics w regionie.

Użycie usługi Azure Synapse Analytics można skalować w górę i w dół w ciągu dnia. Porozmawiaj z zespołem, który zarządza wystąpieniem usługi Azure Synapse Analytics, aby uzyskać informacje o podstawowym użyciu.

Przejdź do rezerwacji w witrynie Azure Portal i kup pojemność zarezerwowaną usługi Azure Synapse Analytics w wielokrotnościach 100 jednostek DWU.

## <a name="reservation-purchase-recommendations"></a>Zalecenia dotyczące zakupów rezerwacji

Zalecenia dotyczące zakupu rezerwacji są obliczane przez przeanalizowanie danych użycia godzinowego w ciągu ostatnich 7, 30 i 60 dni. Platforma Azure oblicza prawdopodobne koszty w przypadku rezerwacji i porównuje je z rzeczywistymi kosztami w ramach płatności zgodnie z rzeczywistym użyciem w czasie trwania. Obliczenia są wykonywane dla każdej ilości, która była używana w przedziale czasowym. Zalecana jest ilość, która maksymalizuje oszczędności.

Na przykład możesz przez większość czasu używać 500 maszyn wirtualnych, ale czasami użycie rośnie nagle do 700 maszyn wirtualnych. W tym przykładzie platforma Azure oblicza oszczędności dla ilości 500 i 700 maszyn wirtualnych. Użycie na poziomie 700 maszyn wirtualnych jest sporadyczne, dlatego zalecane obliczenie określa, że oszczędności są maksymalizowane dla zakupu rezerwacji 500 maszyn wirtualnych, a zalecenie dotyczy 500 maszyn wirtualnych.

Pamiętaj o następujących kwestiach:

- Zalecenia dotyczące rezerwacji są obliczane przy użyciu stawek za użycie na żądanie, które Ciebie dotyczą.
- Zalecenia są obliczane dla poszczególnych rozmiarów, a nie dla rodziny rozmiarów wystąpienia.
- Zalecana ilość dla zakresu jest zmniejszana w dniu zakupu rezerwacji dla zakresu.
    - Jednak aktualizacja zaleceń dotyczących liczby rezerwacji między zakresami może trwać do 25 dni. Na przykład w przypadku zakupu na podstawie zaleceń dotyczących zakresu udostępnionego dostosowywanie zaleceń dotyczących zakresu pojedynczej subskrypcji może potrwać do 25 dni.

## <a name="recommendations-in-the-azure-portal"></a>Zalecenia w witrynie Azure Portal

Zakupy rezerwacji obliczane przez aparat zaleceń są wyświetlane na karcie **Zalecane** w witrynie [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs). Oto przykładowa ilustracja.

![Obraz przedstawiający zalecenia](./media/determine-reservation-purchase/select-product-ri.png)

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Zalecenia w aplikacji Power BI usługi Cost Management

Klienci z umowami Enterprise Agreement i umowami klienta firmy Microsoft mogą korzystać z raportów pokrycia wystąpieniami zarezerwowanymi dla maszyn wirtualnych i zaleceń zakupu. Raporty pokrycia przedstawiają całkowite użycie i użycie objęte wystąpieniami zarezerwowanymi.

1. Pobierz [aplikację Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
2. Przejdź do raportu pokrycia wystąpieniami zarezerwowanymi maszyny wirtualnej w zakresie udostępnionym lub pojedynczym, w zależności od zakresu, dla którego chcesz dokonać zakupu.
3. Wybierz region, rodzinę rozmiarów wystąpień, aby zobaczyć użycie, pokrycie wystąpieniami zarezerwowanymi i zalecenia dotyczące zakupu dla wybranego filtru.

## <a name="recommendations-in-azure-advisor"></a>Zalecenia w usłudze Azure Advisor

Zalecenia dotyczące zakupu rezerwacji są dostępne w usłudze [Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview).

- Usługa Advisor zapewnia zalecenia wyłącznie w zakresie jednej subskrypcji.
- Zalecenia usługi Advisor są obliczane dla ostatnich 30 dni. Przewidywane oszczędności dotyczą 3-letniego okresu rezerwacji.
- W przypadku zakupienia rezerwacji dotyczącej zakresu współdzielonego może minąć do 30 dni, zanim zalecenia zakupu rezerwacji usługi Advisor przestaną być wyświetlane.

## <a name="recommendations-using-apis"></a>Zalecenia dotyczące używania interfejsów API

Użyj interfejsu API REST [Rekomendacje dotyczące rezerwacji](/rest/api/consumption/reservationrecommendations/list) w celu programowego wyświetlania zaleceń.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
- [Understand reservation usage for your subscription with pay-as-you-go rates](understand-reserved-instance-usage.md) (Informacje na temat użycia rezerwacji w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
- [Koszty oprogramowania systemu Windows nieuwzględniane w przypadku wystąpień zarezerwowanych](reserved-instance-windows-software-costs.md)