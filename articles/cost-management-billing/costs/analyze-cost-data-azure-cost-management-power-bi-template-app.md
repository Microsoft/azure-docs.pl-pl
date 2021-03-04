---
title: Analizowanie kosztów platformy Azure za pomocą aplikacji usługi Power BI
description: W tym artykule wyjaśniono, jak zainstalować aplikację Azure Cost Management usługi Power BI i korzystać z niej.
author: bandersmsft
ms.author: banders
ms.date: 02/19/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: b08ff57f964ef7bc3712c930c222a10ed0f89ef4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045102"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Analizowanie kosztów za pomocą aplikacji Azure Cost Management usługi Power BI dla umów Enterprise Agreement (EA)

W tym artykule wyjaśniono, jak zainstalować aplikację Azure Cost Management usługi Power BI i korzystać z niej. Aplikacja ułatwia analizowanie kosztów platformy Azure i zarządzanie nimi w usłudze Power BI. Za pomocą aplikacji możesz monitorować koszty i trendy użycia oraz znajdować możliwości optymalizacji kosztów w celu zmniejszenia wydatków.

Aplikacja Power BI Azure Cost Management obecnie obsługuje tylko klientów z [Enterprise Agreementem](https://azure.microsoft.com/pricing/enterprise-agreement/).

Aplikacja ogranicza szerszym. Jeśli chcesz zmodyfikować i zwiększyć domyślne filtry, widoki i wizualizacje, aby dostosować je do swoich potrzeb, należy zamiast tego użyć [łącznika Azure Cost Management w Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management) . Za pomocą łącznika Azure Cost Management można dołączać dodatkowe dane z innych źródeł, aby tworzyć niestandardowe raporty, aby uzyskać pełne widoki ogólnego kosztu biznesowego. Łącznik obsługuje również umowy klienta firmy Microsoft.

> [!NOTE]
> Aplikacje szablonów usługi Power BI nie obsługują pobierania pliku PBIX.

## <a name="prerequisites"></a>Wymagania wstępne

- [Licencja na usługę Power BI Pro](/power-bi/service-self-service-signup-for-power-bi) jest wymagana do zainstalowania i używania aplikacji.
- Aby łączyć się z danymi, musisz używać konta [administratora przedsiębiorstwa](../manage/understand-ea-roles.md). Rola administratora przedsiębiorstwa (tylko do odczytu) jest obsługiwana.

## <a name="installation-steps"></a>Kroki instalacji

Aby zainstalować aplikację:

1. Otwórz [aplikację Azure Cost Management usługi Power BI](https://aka.ms/costmgmt/ACMApp).
1. Na stronie usługi Power BI AppSource wybierz pozycję **Pobierz teraz**.
1. Wybierz pozycję **Kontynuuj**, aby wyrazić zgodę na warunki użytkowania i zasady ochrony prywatności.
1. W polu **Czy zainstalować tę aplikację usługi Power BI?** wybierz pozycję **Zainstaluj**.
1. W razie konieczności utwórz obszar roboczy i wybierz pozycję **Kontynuuj**.
1. Po zakończeniu instalacji zostanie wyświetlone powiadomienie z informacją, że nowa aplikacja jest gotowa.
1. Wybierz zainstalowaną aplikację.
1. Na stronie pierwsze kroki wybierz pozycję **Połącz swoje dane**.
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-your-data.png" alt-text="Zrzut ekranu z wyróżnionym linkiem Połącz dane." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-your-data.png" :::
1. W wyświetlonym oknie dialogowym wprowadź swój numer rejestracji EA dla wartości **BillingProfileIdOrEnrollmentNumber**. Określ liczbę miesięcy, z których mają zostać pobrane dane. Pozostaw domyślną wartość pola **Zakres** (**Numer rejestracji**), a następnie wybierz pozycję **Dalej**.  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png" alt-text="Zrzut ekranu przedstawiający miejsce wprowadzenia informacji o rejestracji E." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png" :::
1. Następne okno dialogowe łączy się z platformą Azure i pobiera dane. *Pozostaw wartości domyślne zgodnie z konfiguracją* , a następnie wybierz pozycję **Zaloguj i Kontynuuj**.  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe łączenie z aplikacją Azure Cost Management z wartościami domyślnymi." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png" :::
1. W ostatnim kroku instalacji następuje nawiązanie połączenia z rejestracją EA. Jest do tego wymagane konto [administratora przedsiębiorstwa](../manage/understand-ea-roles.md). Pozostaw wszystkie wartości domyślne. Wybierz pozycję **Zaloguj się i Połącz**.  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe łączenie z aplikacją Azure Cost Management z wartościami domyślnymi, z którymi można nawiązać połączenie." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png" :::
1. Zostanie wyświetlony monit o uwierzytelnienie przy użyciu rejestracji w ramach umowy EA. Uwierzytelnianie za pomocą Power BI. Po uwierzytelnieniu rozpocznie się Power BI odświeżanie danych.
    > [!NOTE]
    > Proces odświeżania danych może zająć trochę czasu. Jego długość zależy od określonej liczby miesięcy i ilości danych, które trzeba zsynchronizować.

Po zakończeniu odświeżania danych wybierz aplikację Azure Cost Management, aby wyświetlić wstępnie utworzone raporty.

## <a name="reports-available-with-the-app"></a>Raporty dostępne z aplikacją

W aplikacji są dostępne następujące raporty.

**Wprowadzenie** — oferuje przydatne linki do dokumentacji i linki do przesyłania opinii.

**Przegląd konta** — ten raport przedstawia miesięczne podsumowanie informacji, w tym:

- Opłaty i środki
- Nowe zakupy
- Opłaty za witrynę Azure Marketplace
- Opłaty nadwyżkowe i łączne

**Użycie według subskrypcji i grup zasobów** — udostępnia widok kosztów w czasie i wykresy pokazujące koszt według subskrypcji i grupy zasobów.

**Użycie według usług** — udostępnia widok czasu użytkowania według kategorii miernika. Można śledzić dane użycia i przechodzić do szczegółów wszelkich anomalii w celu zrozumienia nagłych wzrostów i spadków użycia.

**5 najważniejszych czynników napędzających użycie** — raport przedstawia przefiltrowane podsumowanie kosztów według 5 najpopularniejszych kategorii mierników i odpowiadających im nazw mierników.

**Użycie Korzyści użycia hybrydowego platformy Azure dla systemu Windows Server** — raport przedstawia maszyny wirtualne z włączoną Korzyścią użycia hybrydowego platformy Azure. Pokazuje również liczbę rdzeni/procesorów wirtualnych używanych przez maszyny wirtualne.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png" alt-text="Zrzut ekranu przedstawiający pełny raport korzyści z używania hybrydowego platformy Azure." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png" :::

Raport wskazuje maszyny wirtualne z systemem Windows, na których Korzyść użycia hybrydowego została **włączona**, ale które mają _mniej niż_ 8 procesorów wirtualnych. Pokazuje również maszyny, na których Korzyść użycia hybrydowego **nie została włączona** i które mają 8 _lub więcej_ procesorów wirtualnych. Te informacje ułatwiają pełne wykorzystanie Korzyści użycia hybrydowego. Zastosuj korzyść do najbardziej kosztownych maszyn wirtualnych, aby zmaksymalizować potencjalne oszczędności.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png" alt-text="Zrzut ekranu przedstawiający obszar mniej niż 8 procesorów wirtualnych vCPU i procesorów wirtualnych vCPU w raporcie korzyści użycia hybrydowego platformy Azure." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png" :::

**Obciążenie zwrotne za wystąpienia zarezerwowane** — raport pomaga zrozumieć, gdzie zastosowano korzyść wystąpień zarezerwowanych i w jakiej wysokości, z podziałem na regiony, subskrypcje, grupy zasobów lub zasoby. Aby wyświetlić widok, raport używa danych amortyzowanego użycia.

Aby wyświetlić dane niedostatecznego wykorzystania wystąpień zarezerwowanych, można przefiltrować dane według _typu obciążenia_.

Aby uzyskać więcej informacji na temat danych amortyzacji, zobacz [Pobieranie kosztów i użycia rezerwacji w ramach umowy Enterprise Agreement](../reservations/understand-reserved-instance-usage-ea.md).

**Oszczędności na wystąpieniach zarezerwowanych** — raport przedstawia oszczędności uzyskane na rezerwacjach dla subskrypcji, grup zasobów i poziomów zasobów. Są w nim pokazywane następujące informacje:

- Koszt z rezerwacją
- Szacowany koszt na żądanie, jeśli rezerwacja nie została zastosowana do użycia
- Oszczędności na kosztach uzyskane w wyniku rezerwacji

 W raporcie od całkowitych oszczędności odejmowane są straty wynikające z niedostatecznego wykorzystania rezerwacji. Starty te nie zostałyby poniesione, gdyby rezerwacji nie było.

Dane amortyzowanego użycia można wykorzystać do budowania na ich podstawie.

<a name="shared-recommendation"></a>
**Pokrycie wystąpieniami zarezerwowanymi maszyn wirtualnych (rekomendacja udostępniona)** — raport jest podzielony między użycie maszyn wirtualnych na żądanie i użycie wystąpień zarezerwowanych maszyn wirtualnych w wybranym okresie. Zawiera rekomendacje dotyczące zakupów wystąpień zarezerwowanych maszyn wirtualnych w zakresie udostępnionym.

Aby użyć raportu, wybierz filtr przechodzenia do szczegółów.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png" alt-text="Zrzut ekranu przedstawiający opcję Wybierz przechodzenie do szczegółów w raporcie o zapotrzebowaniu na wystąpienie zarezerwowane dla maszyny wirtualnej." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png" :::

Wybierz region, który chcesz przeanalizować. Następnie wybierz grupę elastyczności rozmiarów wystąpienia i tak dalej.

Dla każdego poziomu przechodzenia do szczegółów do raportu są stosowane następujące filtry:

- Dane pokrycia po prawej stronie to filtr pokazujący ilość użycia, za które naliczane są opłaty po stawce użycia na żądanie w porównaniu z ilością objętą rezerwacją.
- Rekomendacje są również filtrowane.

Tabela rekomendacji zawiera rekomendacje dotyczące zakupu rezerwacji w oparciu o używane rozmiary maszyn wirtualnych.

Wartości _Rozmiar znormalizowany_ i _Znormalizowana ilość zalecana_ ułatwiają normalizację zakupu do najmniejszego rozmiaru dla grupy elastyczności rozmiaru wystąpienia. Te informacje są przydatne, jeśli planujesz kupić tylko jedną rezerwację dla wszystkich rozmiarów w grupie elastyczności rozmiaru wystąpienia.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recommendations.png" alt-text="Zrzut ekranu przedstawiający raport dotyczący zaleceń z wystąpienia zarezerwowanego." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recommendations.png" :::

**Pokrycie wystąpieniami zarezerwowanymi maszyn wirtualnych (pojedyncza rekomendacja)** — raport jest podzielony między użycie maszyn wirtualnych na żądanie i użycie wystąpień zarezerwowanych maszyn wirtualnych w wybranym okresie. Zawiera rekomendacje dotyczące zakupów wystąpień zarezerwowanych maszyn wirtualnych w zakresie subskrypcji.

Aby uzyskać szczegółowe informacje dotyczące sposobu korzystania z tego raportu, zobacz sekcję [Pokrycie wystąpieniami zarezerwowanymi maszyn wirtualnych (rekomendacja udostępniona)](#shared-recommendation).

**Zakupy wystąpień zarezerwowanych** — raport przedstawia zakupy wystąpień zarezerwowanych w określonym okresie.

**Arkusz cen** — raport przedstawia szczegółową listę cen specyficznych dla konta rozliczeniowego lub rejestracji EA.

## <a name="troubleshoot-problems"></a>Rozwiązywanie problemów

Jeśli występują problemy z aplikacją Power BI, skorzystaj z poniższych informacji dotyczących rozwiązywania problemów, które mogą pomóc.

### <a name="error-processing-the-data-in-the-dataset"></a>Błąd podczas przetwarzania danych w zestawie danych

Może pojawić się komunikat o błędzie z informacją:

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

Zamiast `<TableName>` zostanie wyświetlona nazwa tabeli.

#### <a name="cause"></a>Przyczyna

Domyślna wartość elementu **Zakres** (`Enrollment Number`) została zmieniona w połączeniu z usługą Cost Management.

#### <a name="solution"></a>Rozwiązanie

Połącz się ponownie z usługą Cost Management i ustaw wartość elementu **Zakres** na `Enrollment Number`. Nie wprowadzaj numeru rejestracyjnego organizacji. Zamiast tego wpisz `Enrollment Number` dokładnie tak, jak pojawia się na poniższej ilustracji.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number-troubleshoot.png" alt-text="Zrzut ekranu, na którym widać, że domyślny tekst numeru rejestracji nie może ulec zmianie." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number-troubleshoot.png" :::

### <a name="budgetamount-error"></a>Błąd BudgetAmount

Może pojawić się komunikat o błędzie z informacją:

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje z powodu błędu dotyczącego podstawowych metadanych. Problem występuje z powodu braku dostępnego budżetu w obszarze **Zarządzanie kosztami > Budżet** w witrynie Azure Portal. Poprawka błędu jest w trakcie wdrażania do programu Power BI Desktop i usługi Power BI.

#### <a name="solution"></a>Rozwiązanie

- Dopóki błąd nie zostanie naprawiony, można obejść ten problem przez dodanie budżetu testowego w witrynie Azure Portal na poziomie konta rozliczeniowego/rejestracji umowy EA. Budżet testowy odblokowuje połączenie z usługą Power BI. Aby uzyskać więcej informacji na temat tworzenia budżetu, zobacz [Samouczek: Tworzenie budżetów platformy Azure i zarządzanie nimi](tutorial-acm-create-budgets.md).

### <a name="invalid-credentials-for-azureblob-error"></a>Nieprawidłowe poświadczenia dla błędu AzureBlob

Może pojawić się komunikat o błędzie z informacją:

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, jeśli zmienisz metodę uwierzytelniania dla połączenia ze źródłem danych.

#### <a name="solution"></a>Rozwiązanie

1. Nawiązywanie połączenia z danymi.
1. Po wprowadzeniu rejestracji w ramach umowy EA i liczby miesięcy upewnij się, że dla metody uwierzytelniania jest ustawiona wartość domyślna **Anonymous**, a dla poziomu prywatności wartość **None**.  
  :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png" alt-text="Zrzut ekranu przedstawia okno dialogowe Nawiązywanie połączenia z aplikacją Azure Cost Management z wprowadzonymi wartościami Anonymous i None." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png" :::
1. Na następnej stronie ustaw wartość **OAuth2** dla metody uwierzytelniania i wartość **None** dla poziomu prywatności. Następnie zaloguj się, aby uwierzytelnić się w rejestracji. Ten krok uruchamia również odświeżanie danych w usłudze Power BI.

## <a name="data-reference"></a>Opis danych

Poniższe informacje podsumowują dane dostępne za pośrednictwem aplikacji. Zawarto w nich także linki do opisów interfejsów API ze szczegółowymi informacjami o polach danych i wartościach.

| **Odwołanie do tabeli** | **Opis** |
| --- | --- |
| **AutoFitComboMeter** | Dane zawarte w aplikacji służące do normalizacji rekomendacji dotyczących wystąpień zarezerwowanych i użycia do najmniejszego rozmiaru w grupie rodzin wystąpień. |
| [**Podsumowanie salda**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | Podsumowanie salda dla umów Enterprise Agreement. |
| [**Budżety**](/rest/api/consumption/budgets/get#definitions) | Szczegóły budżetu umożliwiające porównanie rzeczywistych kosztów lub użycia z istniejącymi celami budżetu. |
| [**Arkusze cen**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | Mające zastosowanie stawki mierników dla podanego profilu rozliczeniowego lub rejestracji EA. |
| [**Opłaty za wystąpienia zarezerwowane**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | Opłaty związane z wystąpieniami zarezerwowanymi z ostatnich 24 miesięcy. |
| [**Rekomendacje dotyczące wystąpień zarezerwowanych (udostępnione)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | Rekomendacje dotyczące zakupu wystąpień zarezerwowanych w oparciu o trendy użycia wszystkich Twoich subskrypcji w ciągu ostatnich 7 dni. |
| [**Rekomendacje dotyczące wystąpień zarezerwowanych (pojedyncze)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | Rekomendacje dotyczące zakupu wystąpień zarezerwowanych w oparciu o trendy użycia Twojej pojedynczej subskrypcji w ciągu ostatnich 7 dni. |
| [**Szczegóły użycia wystąpienia zarezerwowanego**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | Szczegóły zużycia dla istniejących wystąpień zarezerwowanych w ciągu ostatniego miesiąca. |
| [**Podsumowanie użycia wystąpienia zarezerwowanego**](/rest/api/consumption/reservationssummaries/list) | Średni dzienny procent użycia rezerwacji na platformie Azure. |
| [**Szczegóły użycia**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Podział zużytych ilości i szacowane opłaty dla danego profilu rozliczeniowego w ramach rejestracji EA. |
| [**Amortyzowane szczegóły użycia**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Podział zużytych ilości i szacowane opłaty amortyzowane dla danego profilu rozliczeniowego w ramach rejestracji EA. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konfigurowania danych, odświeżania, udostępniania raportów i dodatkowych dostosowań raportów, zobacz następujące artykuły:

- [Konfigurowanie odświeżania zaplanowanego](/power-bi/refresh-scheduled-refresh)
- [Udostępnianie pulpitów nawigacyjnych i raportów usługi Power BI współpracownikom oraz innym osobom](/power-bi/service-share-dashboards)
- [Subskrybowanie dla siebie i innych raportów oraz pulpitów nawigacyjnych w usłudze Power BI](/power-bi/service-report-subscribe)
- [Pobieranie raportu z usługi Power BI do programu Power BI Desktop](/power-bi/service-export-to-pbix)
- [Zapisywanie raportu w usłudze Power BI i programie Power BI Desktop](/power-bi/service-report-save)
- [Tworzenie raportu w usłudze Power BI przez zaimportowanie zestawu danych](/power-bi/service-report-create-new)