---
title: Zarządzanie kosztami i użyciem platformy AWS w usłudze Azure Cost Management
description: Z tego artykułu dowiesz się, jak korzystać z analizy kosztów i budżetów w usłudze Cost Management w celu zarządzania kosztami i użyciem platformy AWS.
author: bandersmsft
ms.author: banders
ms.date: 10/16/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: 5fed70ccdbebbd178412c416f37c2e9001a81f38
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148970"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Zarządzanie kosztami i użyciem platformy AWS na platformie Azure

Po skonfigurowaniu i ustawieniu integracji raportów o kosztach i użyciu platformy AWS dla usługi Azure Cost Management można rozpocząć zarządzanie kosztami i użyciem platformy AWS. Z tego artykułu dowiesz się, jak korzystać z analizy kosztów i budżetów w usłudze Cost Management w celu zarządzania kosztami i użyciem platformy AWS.

Jeśli integracja nie została jeszcze skonfigurowana, zapoznaj się z tematem [Ustawianie i konfigurowanie integracji raportów o użyciu platformy AWS](aws-integration-set-up-configure.md).

_Przed rozpoczęciem_ : Jeśli nie znasz analizy kosztów, zobacz przewodnik Szybki start [Poznawanie i analizowanie kosztów za pomocą funkcji Analiza kosztów](quick-acm-cost-analysis.md). Jeśli nie znasz budżetów na platformie Azure, zobacz samouczek [Tworzenie budżetów platformy Azure i zarządzanie nimi](tutorial-acm-create-budgets.md).

## <a name="view-aws-costs-in-cost-analysis"></a>Wyświetlanie kosztów dotyczących platformy AWS w analizie kosztów

Koszty dotyczące platformy AWS są dostępne w analizie kosztów w następujących zakresach:

- Połączone konta platformy AWS w grupie zarządzania
- Koszty połączonego konta platformy AWS
- Koszty skonsolidowanego konta platformy AWS

W następnych sekcjach opisano sposób korzystania z zakresów, aby wyświetlać dane dotyczące kosztów i użycia dla każdego z nich.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Wyświetlanie połączonych kont platformy AWS w grupie zarządzania

Wyświetlanie kosztów przy użyciu zakresu grupy zarządzania jest jedynym sposobem wyświetlania zagregowanych kosztów pochodzących z różnych subskrypcji platformy Azure i połączonych kont platformy AWS. Korzystanie z grupy zarządzania zapewnia widok międzychmurowy, umożliwiający wyświetlanie kosztów platform Azure i AWS razem.

W analizie kosztów otwórz selektor zakresu i wybierz grupę zarządzania, która zawiera połączone konta platformy AWS. Oto przykładowy obraz w witrynie Azure Portal:

:::image type="content" source="./media/aws-integration-manage/select-scope01.png" alt-text="Przykład widoku wyboru zakresu z połączonymi kontami w ramach grupy zarządzania" :::

Oto przykład przedstawiający koszt grupy zarządzania w analizie kosztów, pogrupowany według dostawcy (platformy Azure i AWS).

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-azure.png" alt-text="Przykład widoku wyboru zakresu z połączonymi kontami w ramach grupy zarządzania" lightbox="./media/aws-integration-manage/cost-analysis-aws-azure.png" :::

> [!NOTE]
> Grupy zarządzania nie są obecnie obsługiwane w przypadku klientów umowy MCA. Klienci umowy MCA mogą utworzyć łącznik, aby wyświetlać swoje dane platformy AWS. Jednak klienci umowy MCA nie mogą wyświetlać kosztów platformy Azure i AWS razem w ramach grupy zarządzania.

### <a name="view-aws-linked-account-costs"></a>Wyświetlanie kosztów połączonego konta platformy AWS

Aby wyświetlić koszty połączonego konta platformy AWS, otwórz selektor zakresu i wybierz połączone konto platformy AWS. Pamiętaj, że połączone konta są skojarzone z grupą zarządzania, jak określono w łączniku platformy AWS.

Oto przykład przedstawiający wybór zakresu połączonego konta platformy AWS.

:::image type="content" source="./media/aws-integration-manage/select-scope02.png" alt-text="Przykład widoku wyboru zakresu z połączonymi kontami w ramach grupy zarządzania" :::

### <a name="view-aws-consolidated-account-costs"></a>Wyświetlanie kosztów skonsolidowanego konta platformy AWS

Aby wyświetlić koszty skonsolidowanego konta platformy AWS, otwórz selektor zakresu i wybierz skonsolidowane konto platformy AWS. Oto przykład przedstawiający wybór zakresu skonsolidowanego konta platformy AWS.

:::image type="content" source="./media/aws-integration-manage/select-scope03.png" alt-text="Przykład widoku wyboru zakresu z połączonymi kontami w ramach grupy zarządzania" :::

Ten zakres zapewnia zagregowany widok wszystkich połączonych kont platformy AWS skojarzonych ze skonsolidowanym kontem platformy AWS. Oto przykład przedstawiający koszty skonsolidowanego konta platformy AWS, pogrupowane według nazwy usługi.

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" alt-text="Przykład widoku wyboru zakresu z połączonymi kontami w ramach grupy zarządzania" lightbox="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" :::

### <a name="dimensions-available-for-filtering-and-grouping"></a>Wymiary dostępne do filtrowania i grupowania

W poniższej tabeli opisano wymiary dostępne do grupowania i filtrowania w analizie kosztów.

| Wymiar | Nagłówek raportu Amazon CUR | Zakresy | Komentarze |
| --- | --- | --- | --- |
| Strefa dostępności | lineitem/AvailabilityZone | Wszyscy |   |
| Lokalizacja | product/Region | Wszystko |   |
| Miernik |   | Wszyscy |   |
| Kategoria miernika | lineItem/ProductCode | Wszyscy |   |
| Podkategoria miernika | lineitem/UsageType | Wszyscy |   |
| Operacja | lineItem/Operation | Wszyscy |   |
| Zasób | lineItem/ResourceId | Wszyscy |   |
| Typ zasobu | product/instanceType | Wszystko | Jeśli element product/instanceType ma wartość null, używany jest element lineItem/UsageType. |
| ResourceGuid | Brak | Wszystko | Identyfikator GUID miernika platformy Azure. |
| Nazwa usługi | product/ProductName | Wszystko | Jeśli element product/ProductName ma wartość null, używany jest element lineItem/ProductCode. |
| Warstwa usługi |   |   |   |
| Identyfikator subskrypcji | lineItem/UsageAccountId | Skonsolidowane konto i grupa zarządzania |   |
| Nazwa subskrypcji | Brak | Skonsolidowane konto i grupa zarządzania | Nazwy kont są zbierane przy użyciu interfejsu AWS Organization API. |
| Tag | resourceTags | Wszystko | Prefiks _user:_ jest usuwany z tagów zdefiniowanych przez użytkownika, aby umożliwić używanie tagów międzychmurowych. Prefiks _aws:_ pozostaje nienaruszony. |
| Identyfikator konta rozliczeniowego | bill/PayerAccountId | Grupa zarządzania |   |
| Nazwa konta rozliczeniowego | Brak | Grupa zarządzania | Nazwy kont są zbierane przy użyciu interfejsu AWS Organization API. |
| Dostawca | Brak | Grupa zarządzania | Platforma AWS lub Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Ustawianie budżetów w zakresach platformy AWS

Używaj budżetów, aby aktywnie zarządzać kosztami i zwiększyć odpowiedzialność w organizacji. Budżety są ustawiane na zakresy skonsolidowanego konta platformy AWS i połączonego konta platformy AWS. Oto przykład budżetu dla skonsolidowanego konta platformy AWS, pokazany w usłudze Cost Management:

:::image type="content" source="./media/aws-integration-manage/budgets-aws-consolidated-account01.png" alt-text="Przykład widoku wyboru zakresu z połączonymi kontami w ramach grupy zarządzania" :::

## <a name="aws-data-collection-process"></a>Proces zbierania danych w usłudze AWS

Po skonfigurowaniu łącznika platformy AWS rozpocznie się proces zbierania i odnajdywania danych. Zebranie wszystkich danych użycia może potrwać kilka godzin. Czas trwania zależy od następujących czynników:

- Czas potrzebny do przetworzenia bieżących plików CUR, które znajdują się w zasobniku S3 platformy AWS.
- Czas potrzebny na utworzenie zakresów skonsolidowanego konta platformy AWS i połączonego konta platformy AWS.
- Czas i częstotliwość, z jaką usługa AWS zapisuje pliki raportów o kosztach i użyciu w zasobniku S3.

## <a name="aws-integration-pricing"></a>Ceny integracji platformy AWS

Z bezpłatnej wersji próbnej każdego łącznika platformy AWS można korzystać przez 90 dni.

Cena wynosi 1% miesięcznych kosztów platformy AWS. W każdym miesiącu opłata jest naliczana na podstawie kosztów zafakturowanych w poprzednim miesiącu.

Dostęp do interfejsów API platformy AWS może pociągnąć za sobą dodatkowe koszty na platformie AWS.

## <a name="aws-integration-limitations"></a>Ograniczenia integracji z platformą AWS

- Budżety w usłudze Cost Management nie obsługują grup zarządzania z wieloma walutami. W przypadku grup zarządzania z wieloma walutami ocena budżetu nie jest wyświetlana. Jeśli wybierzesz grupę zarządzania z wieloma walutami podczas tworzenia budżetu, zostanie wyświetlony komunikat o błędzie.
- Łączniki chmury nie obsługują usług AWS GovCloud (US), AWS Gov ani AWS China.
- W usłudze Cost Management są wyświetlane tylko _koszty użycia_ platformy AWS. Podatki, pomoc techniczna, zwroty, wystąpienia zarezerwowane, środki lub inne typy opłat nie są jeszcze obsługiwane.

## <a name="troubleshooting-aws-integration"></a>Rozwiązywanie problemów z integracją z platformą AWS

Skorzystaj z poniższych informacji dotyczących rozwiązywania problemów, aby rozwiązać typowe problemy.

### <a name="no-permission-to-aws-linked-accounts"></a>Brak uprawnień do połączonych kont platformy AWS

**Kod błędu:** _Unauthorized_

Uprawnienia dostępu do połączonych kont platformy AWS można zdobyć na dwa sposoby:

- Uzyskanie dostępu do grupy zarządzania, która ma połączone konta platformy AWS.
- Udzielenie użytkownikowi przez inną osobę uprawnień do połączonego konta platformy AWS.

Domyślnie twórca łącznika platformy AWS jest właścicielem wszystkich obiektów utworzonych przez łącznik. Dotyczy to również skonsolidowanego konta platformy AWS i połączonego konta platformy AWS.

Aby możliwe było zweryfikowanie ustawień łączników, musisz mieć co najmniej rolę współautora. Czytelnik nie może weryfikować ustawień łącznika.

### <a name="collection-failed-with-assumerole"></a>Zbieranie nie powiodło się z powodu elementu AssumeRole

**Kod błędu:** _FailedToAssumeRole_

Ten błąd oznacza, że usługa Cost Management nie może wywołać interfejsu API elementu AssumeRole platformy AWS. Przyczyną tego może być problem z definicją roli. Upewnij się, że są spełnione następujące warunki:

- Identyfikator zewnętrzny jest taki sam jak w definicji roli i definicji łącznika.
- Typ roli jest ustawiony na **Another AWS account Belonging to you or 3rd party** (Inne konto AWS należące do Ciebie lub innej firmy).
- Pole **Require MFA** (Wymaganie usługi MFA) jest wyczyszczone.
- Zaufane konto platformy AWS w roli AWS to _432263259397_ .

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Zbieranie nie powiodło się z powodu odmowy dostępu do definicji raportu CUR

**Kod błędu:** _AccessDeniedReportDefinitions_

Ten błąd oznacza, że usługa Cost Management nie może wyświetlić definicji raportów kosztów i użycia. To uprawnienie służy do sprawdzania, czy raport CUR jest zdefiniowany zgodnie z oczekiwaniami usługi Azure Cost Management. Zobacz [Tworzenie raportu o kosztach i użyciu platformy AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Zbieranie nie powiodło się z powodu odmowy dostępu do list raportów

**Kod błędu:** _AccessDeniedListReports_

Ten błąd oznacza, że usługa Cost Management nie może wyświetlić listy obiektów w zasobniku S3, w których znajduje się raport CUR. Zasady zarządzania dostępem i tożsamościami na platformie AWS wymagają uprawnienia do zasobnika oraz obiektów w zasobniku. Zobacz [Tworzenie roli i zasad w usłudze AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Zbieranie nie powiodło się z powodu odmowy dostępu do pobierania raportu

**Kod błędu:** _AccessDeniedDownloadReport_

Ten błąd oznacza, że usługa Cost Management nie jest w stanie uzyskać dostępu do wszystkich plików CUR przechowywanych w zasobniku usługi Amazon S3 i pobrać ich. Upewnij się, że kod JSON zasad platformy AWS dołączony do roli przypomina przykład przedstawiony w dolnej części sekcji [Tworzenie roli i zasad w usłudze AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Zbieranie nie powiodło się, ponieważ nie znaleziono raportu o kosztach i użyciu

**Kod błędu:** _FailedToFindReport_

Ten błąd oznacza, że usługa Cost Management nie może znaleźć raportu o kosztach i użyciu zdefiniowanego w łączniku. Upewnij się, że nie został usunięty i że kod JSON zasad platformy AWS dołączony do roli przypomina przykład przedstawiony w dolnej części sekcji [Tworzenie roli i zasad w usłudze AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Nie można utworzyć lub zweryfikować łącznika ze względu na niezgodność definicji raportów o kosztach i użyciu

**Kod błędu:** _ReportIsNotValid_

Ten błąd jest związany z definicją raportu o kosztach i użyciu platformy AWS. Wymagamy określonych ustawień dla tego raportu. Zobacz wymagania w sekcji [Tworzenie raportu o kosztach i użyciu platformy AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="internal-error-when-creating-connector"></a>Błąd wewnętrzny podczas tworzenia łącznika

**Kod błędu:** _Tworzenie łącznika — nie można utworzyć łącznika &lt;NazwaŁącznika&gt;. Powód: Błąd wewnętrzny. Upewnij się, że podano poprawne właściwości platformy AWS._

Ten błąd może wystąpić, gdy subskrypcja i łącznik platformy AWS znajdują się w różnych grupach zarządzania. Subskrypcja i łącznik platformy AWS muszą znajdować się w tej samej grupie zarządzania.

## <a name="next-steps"></a>Następne kroki

- Jeśli środowisko platformy Azure nie zostało jeszcze skonfigurowane w grupach zarządzania, zobacz [Konfiguracja początkowa grup zarządzania](../../governance/management-groups/overview.md#initial-setup-of-management-groups).
