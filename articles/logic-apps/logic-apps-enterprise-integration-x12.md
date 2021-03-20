---
title: Wysyłanie i odbieranie komunikatów X12 dla B2B
description: Komunikaty programu Exchange X12 dotyczące scenariuszy integracji B2B przedsiębiorstwa przy użyciu Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 61f3f2af61bc24f76d061de672a3eaacd54f7f0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015200"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Komunikaty programu Exchange X12 dla integracji z usługą B2B Enterprise w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw

Aby móc korzystać z komunikatów X12 w Azure Logic Apps, można użyć łącznika X12, który udostępnia wyzwalacze i akcje zarządzania komunikacją X12. Aby uzyskać informacje na temat komunikatów EDIFACT, zobacz temat [Exchange EDIFACT messages](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, z której ma być używany łącznik X12 i wyzwalacz, który uruchamia przepływ pracy aplikacji logiki. Łącznik X12 zawiera tylko akcje, a nie wyzwalacze. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) skojarzone z subskrypcją platformy Azure i połączone z aplikacją logiki, w której zamierzasz używać łącznika X12. Zarówno aplikacja logiki, jak i konto integracji muszą istnieć w tej samej lokalizacji lub regionie platformy Azure.

* Co najmniej dwóch [partnerów handlowych](../logic-apps/logic-apps-enterprise-integration-partners.md) , które zostały już zdefiniowane na koncie integracji, przy użyciu kwalifikatora tożsamości X12.

* [Schematy](../logic-apps/logic-apps-enterprise-integration-schemas.md) używane do sprawdzania poprawności kodu XML, które zostały już dodane do konta integracji. Jeśli pracujesz ze schematami dotyczącymi przenośności i działania odpowiedzialności (HIPAA), zobacz [schematy HIPAA](#hipaa-schemas).

* Aby można było korzystać z łącznika X12, należy utworzyć [umowę](../logic-apps/logic-apps-enterprise-integration-agreements.md) X12 między partnerami handlowymi i przechowywać tę umowę na koncie integracji. Jeśli pracujesz ze schematami dotyczącymi przenośności i działania odpowiedzialności (HIPAA), musisz dodać `schemaReferences` sekcję do umowy. Aby uzyskać więcej informacji, zobacz [schematy HIPAA](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Ustawienia odbierania

Po ustawieniu właściwości umowy można skonfigurować, w jaki sposób ta umowa identyfikuje i obsługuje komunikaty przychodzące otrzymane od partnera za pośrednictwem tej umowy.

1. W obszarze **Dodaj** wybierz pozycję **Odbierz ustawienia**.

1. Skonfiguruj te właściwości na podstawie umowy z partnerem, który wymienia z nim wiadomości. **Ustawienia odbierania** są zorganizowane w następujące sekcje:

   * [Identyfikatory](#inbound-identifiers)
   * [Potwierdzenia](#inbound-acknowledgement)
   * [Schematy](#inbound-schemas)
   * [Koperty](#inbound-envelopes)
   * [Numery kontrolne](#inbound-control-numbers)
   * [Weryfikacje](#inbound-validations)
   * [Ustawienia wewnętrzne](#inbound-internal-settings)

   Opisy właściwości znajdują się w tabelach w tej sekcji.

1. Gdy wszystko będzie gotowe, pamiętaj o zapisaniu ustawień, wybierając **przycisk OK**.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Ustawienia odbierania — identyfikatory

![Właściwości identyfikatora dla wiadomości przychodzących](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Właściwość | Opis |
|----------|-------------|
| **ISA1 (kwalifikator autoryzacji)** | Wartość kwalifikatora autoryzacji, która ma zostać użyta. Wartość domyślna to **00 — brak informacji o autoryzacji**. <p>**Uwaga**: w przypadku wybrania innych wartości Określ wartość właściwości **ISA2** . |
| **ISA2** | Wartość informacji o autoryzacji, która ma być używana, gdy właściwość **ISA1** nie jest **00 — brak informacji o autoryzacji**. Ta wartość właściwości musi zawierać co najmniej jeden znak alfanumeryczny i maksymalnie 10. |
| **ISA3 (kwalifikator zabezpieczeń)** | Wartość kwalifikatora zabezpieczeń, która ma zostać użyta. Wartość domyślna to **00 — brak informacji o zabezpieczeniach**. <p>**Uwaga**: w przypadku wybrania innych wartości Określ wartość właściwości **ISA4** . |
| **ISA4** | Wartość informacji o zabezpieczeniach, która ma być używana, gdy właściwość **ISA3** nie jest **00 — brak informacji o zabezpieczeniach**. Ta wartość właściwości musi zawierać co najmniej jeden znak alfanumeryczny i maksymalnie 10. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Ustawienia odbierania — potwierdzenie

![Potwierdzenie dla wiadomości przychodzących](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Właściwość | Opis |
|----------|-------------|
| **Oczekiwano TA1** | Zwróć potwierdzenie techniczne (TA1) do nadawcy wymiany. |
| **Przewidywany FA** | Zwróć potwierdzenie funkcjonalności (FA) do nadawcy wymiany. <p>Dla właściwości **wersja ŚT** na podstawie wersji schematu wybierz potwierdzenia 997 lub 999. <p>Aby włączyć generowanie pętli AK2 w potwierdzeniach funkcjonalnych dla akceptowanych zestawów transakcji, wybierz opcję **Dołącz pętlę AK2/IK2**. |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Ustawienia odbierania — schematy

![Schematy dla wiadomości przychodzących](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

W tej sekcji Wybierz [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) z [konta integracji](./logic-apps-enterprise-integration-create-integration-account.md) dla każdego typu transakcji (ST01) i aplikacji nadawcy (GS02). Potok odbierania EDI służy do rozbudowy komunikatu przychodzącego przez dopasowanie wartości i schematu ustawionych w tej sekcji wartościami dla ST01 i GS02 w komunikacie przychodzącym oraz ze schematem komunikatu przychodzącego. Po zakończeniu każdego wiersza zostanie wyświetlony nowy pusty wiersz.

| Właściwość | Opis |
|----------|-------------|
| **Wersja** | Wersja X12 schematu |
| **Typ transakcji (ST01)** | Typ transakcji |
| **Aplikacja nadawcy (GS02)** | Aplikacja nadawcy |
| **Schemat** | Plik schematu, którego chcesz użyć |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Ustawienia odbierania — koperty

![Separatory do użycia w zestawach transakcji dla wiadomości przychodzących](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Właściwość | Opis |
|----------|-------------|
| **ISA11 użycie** | Separator, który ma być używany w zestawie transakcji: <p>- **Identyfikator standardowy**: Użyj kropki (.) dla notacji dziesiętnej, a nie notacji dziesiętnej dokumentu przychodzącego w potoku odbierania EDI. <p>- **Separator powtórzeń**: Określ separator powtórzonych wystąpień prostego elementu danych lub powtarzalnej struktury danych. Na przykład zwykle daszek (^) jest używany jako separator powtórzeń. W przypadku schematów HIPAA można używać tylko karatów. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Ustawienia odbierania — numery sterujące

![Obsługa duplikatów numerów kontrolnych dla wiadomości przychodzących](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Właściwość | Opis |
|----------|-------------|
| **Nie Zezwalaj na duplikaty numerów kontrolnych wymiany** | Blokuj zduplikowane zmiany. Sprawdź numer kontroli wymiany (ISA13) dla odebranego numeru kontrolnego wymiany. W przypadku wykrycia dopasowania, potok odbierania EDI nie przetwarza wymiany. <p><p>Aby określić liczbę dni, przez jaką należy wykonać sprawdzanie, wprowadź wartość dla opcji **Sprawdź powieloną ISA13 co (dni)** . |
| **Nie Zezwalaj na duplikaty numerów kontrolnych grupy** | Blokuj zmiany, które mają zduplikowane numery kontrolne grup. |
| **Nie Zezwalaj na duplikaty numerów kontrolnych zestawu transakcji** | Blokuj zmiany, które mają duplikaty numerów kontrolnych zestawu transakcji. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Ustawienia odbierania — walidacje

![Walidacje komunikatów przychodzących](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

W wierszu **domyślnym** są wyświetlane reguły sprawdzania poprawności, które są używane dla typu wiadomości EDI. Jeśli chcesz zdefiniować różne reguły, zaznacz każde pole, w którym reguła ma mieć ustawioną **wartość true**. Po zakończeniu każdego wiersza zostanie wyświetlony nowy pusty wiersz.

| Właściwość | Opis |
|----------|-------------|
| **Typ komunikatu** | Typ wiadomości EDI |
| **Weryfikacja EDI** | Wykonaj walidację EDI dla typów danych, zgodnie z definicją w schemacie właściwości EDI, ograniczenia długości, puste elementy danych i końcowe separatory. |
| **Rozszerzona weryfikacja** | Jeśli typ danych nie jest EDI, walidacja jest wymagana dla elementu danych i dozwolone powtarzanie, wyliczenia i sprawdzanie długości elementu danych (min lub max). |
| **Zezwalaj na zera wiodące/końcowe** | Zachowaj wszystkie dodatkowe spacje wiodące lub końcowe zerowe i spacje. Nie usuwaj tych znaków. |
| **Przytnij zera wiodące/końcowe** | Usuń wszystkie spacje wiodące lub końcowe zerowe i spacje. |
| **Końcowe zasady separatora** | Generuj końcowe separatory. <p>- **Niedozwolone**: Zabroń końcowe ograniczniki i separatory w przychodzącej wymianie. Jeśli wymiana ma końcowe ograniczniki i separatory, wymiana jest zadeklarowana jako nieprawidłowa. <p>- **Opcjonalne**: akceptują zmiany z ogranicznikami i separatorami końcowymi lub bez. <p>- **Obowiązkowe**: w przypadku wymiany przychodzącej muszą być końcowe ograniczniki i separatory. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Ustawienia odbierania — ustawienia wewnętrzne

![Ustawienia wewnętrzne dla wiadomości przychodzących](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Właściwość | Opis |
|----------|-------------|
| **Konwertuj implikowany format dziesiętny NN na wartość numeryczną podstawową 10** | Przekonwertuj numer EDI, który jest określony w formacie "NN" na wartość numeryczną Base-10. |
| **Utwórz puste tagi XML, jeśli są dozwolone końcowe separatory** | Nadawca wymiany zawierają puste tagi XML dla końcowych separatorów. |
| **Rozdziel wymianę jako zestawy transakcji — Zawieś zestawy transakcji w przypadku błędu** | Przeanalizuj każdy zestaw transakcji, który jest w wymianie do oddzielnego dokumentu XML przez zastosowanie odpowiedniej koperty do zestawu transakcji. Wstrzymaj tylko transakcje, w których weryfikacja nie powiedzie się. |
| **Podział wymiany jako zestawy transakcji — Zawieś wymianę w przypadku błędu** | Przeanalizuj każdy zestaw transakcji, który jest w wymianie do oddzielnego dokumentu XML przez zastosowanie odpowiedniej formy. Zawieś całą wymianę, gdy co najmniej jeden zestaw transakcji w ramach walidacji nie powiodło się. |
| **Zachowaj zestawy transakcji zawieszania wymiany w przypadku błędu** | Pozostaw nienaruszoną wymianę i Utwórz dokument XML dla całej wymiany wsadowej. Zawieś tylko zestawy transakcji, których Walidacja nie powiodła się, ale nadal Przetwórz wszystkie inne zestawy transakcji. |
| **Zachowaj wymianę — Wstrzymaj transwymianę w przypadku błędu** |Pozostawia wymianę bez zmian, tworzy dokument XML dla całej wymiany wsadowej. Wstrzymuje całą wymianę, gdy co najmniej jeden zestaw transakcji w ramach walidacji nie powiodło się. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Ustawienia wysyłania

Po ustawieniu właściwości umowy można skonfigurować, w jaki sposób ta umowa identyfikuje i obsługuje komunikaty wychodzące wysyłane do partnera za pomocą tej umowy.

1. W obszarze **Dodaj** wybierz pozycję **Wyślij ustawienia**.

1. Skonfiguruj te właściwości na podstawie umowy z partnerem, który wymienia z nim wiadomości. Opisy właściwości znajdują się w tabelach w tej sekcji.

   **Ustawienia wysyłania** są zorganizowane w następujące sekcje:

   * [Identyfikatory](#outbound-identifiers)
   * [Potwierdzenia](#outbound-acknowledgement)
   * [Schematy](#outbound-schemas)
   * [Koperty](#outbound-envelopes)
   * [Numer wersji kontrolki](#outbound-control-version-number)
   * [Numery kontrolne](#outbound-control-numbers)
   * [Zestawy znaków i separatory](#outbound-character-sets-separators)
   * [Walidacja](#outbound-validation)

1. Gdy wszystko będzie gotowe, pamiętaj o zapisaniu ustawień, wybierając **przycisk OK**.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Ustawienia wysyłania — identyfikatory

![Właściwości identyfikatora dla komunikatów wychodzących](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Właściwość | Opis |
|----------|-------------|
| **ISA1 (kwalifikator autoryzacji)** | Wartość kwalifikatora autoryzacji, która ma zostać użyta. Wartość domyślna to **00 — brak informacji o autoryzacji**. <p>**Uwaga**: w przypadku wybrania innych wartości Określ wartość właściwości **ISA2** . |
| **ISA2** | Wartość informacji o autoryzacji, która ma być używana, gdy właściwość **ISA1** nie jest **00 — brak informacji o autoryzacji**. Ta wartość właściwości musi zawierać co najmniej jeden znak alfanumeryczny i maksymalnie 10. |
| **ISA3 (kwalifikator zabezpieczeń)** | Wartość kwalifikatora zabezpieczeń, która ma zostać użyta. Wartość domyślna to **00 — brak informacji o zabezpieczeniach**. <p>**Uwaga**: w przypadku wybrania innych wartości Określ wartość właściwości **ISA4** . |
| **ISA4** | Wartość informacji o zabezpieczeniach, która ma być używana, gdy właściwość **ISA3** nie jest **00 — brak informacji o zabezpieczeniach**. Ta wartość właściwości musi zawierać co najmniej jeden znak alfanumeryczny i maksymalnie 10. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Ustawienia wysyłania — potwierdzenie

![Właściwości potwierdzania dla komunikatów wychodzących](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Właściwość | Opis |
|----------|-------------|
| **Oczekiwano TA1** | Zwróć potwierdzenie techniczne (TA1) do nadawcy wymiany. <p>To ustawienie określa, że partner hosta, który wysyła wiadomość, żąda potwierdzenia od partnera gościa w umowie. Te potwierdzenia są oczekiwane przez partnera hosta na podstawie ustawień odbioru umowy. |
| **Przewidywany FA** | Zwróć potwierdzenie funkcjonalności (FA) do nadawcy wymiany. Dla właściwości **wersja ŚT** na podstawie wersji schematu wybierz potwierdzenia 997 lub 999. <p>To ustawienie określa, że partner hosta, który wysyła wiadomość, żąda potwierdzenia od partnera gościa w umowie. Te potwierdzenia są oczekiwane przez partnera hosta na podstawie ustawień odbioru umowy. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Ustawienia wysyłania — schematy

![Schematy dla komunikatów wychodzących](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

W tej sekcji Wybierz [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) z [konta integracji](./logic-apps-enterprise-integration-create-integration-account.md) dla każdego typu transakcji (ST01). Po zakończeniu każdego wiersza zostanie wyświetlony nowy pusty wiersz.

| Właściwość | Opis |
|----------|-------------|
| **Wersja** | Wersja X12 schematu |
| **Typ transakcji (ST01)** | Typ transakcji dla schematu |
| **Schemat** | Plik schematu, którego chcesz użyć. W przypadku wybrania najpierw schematu zostanie automatycznie ustawiona wersja i typ transakcji. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Ustawienia wysyłania — koperty

![Separatory w zestawie transakcji do użycia dla komunikatów wychodzących](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Właściwość | Opis |
|----------|-------------|
| **ISA11 użycie** | Separator, który ma być używany w zestawie transakcji: <p>- **Identyfikator standardowy**: Użyj kropki (.) dla notacji dziesiętnej, a nie notacji dziesiętnej dokumentu wychodzącego w potoku wysyłania EDI. <p>- **Separator powtórzeń**: Określ separator powtórzonych wystąpień prostego elementu danych lub powtarzalnej struktury danych. Na przykład zwykle daszek (^) jest używany jako separator powtórzeń. W przypadku schematów HIPAA można używać tylko karatów. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Ustawienia wysyłania — numer wersji kontroli

![Numer wersji kontrolki dla komunikatów wychodzących](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

W tej sekcji Wybierz [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) z [konta integracji](./logic-apps-enterprise-integration-create-integration-account.md) dla każdej wymiany. Po zakończeniu każdego wiersza zostanie wyświetlony nowy pusty wiersz.

| Właściwość | Opis |
|----------|-------------|
| **Numer wersji kontrolki (ISA12)** | Wersja standardu X12 |
| **Wskaźnik użycia (ISA15)** | Kontekst wymiany, który jest danymi **testowymi** , danymi **informacyjnymi** lub danymi **produkcyjnymi** |
| **Schemat** | Schemat służący do generowania segmentów GS i ST dla wymiany zakodowanej w X12, który jest wysyłany do potoku wysyłania EDI. |
| **GS1** | Opcjonalne, wybierz kod funkcjonalny. |
| **GS2** | Opcjonalnie określ nadawcę aplikacji. |
| **GS3** | Opcjonalnie określ odbiorcę aplikacji. |
| **GS4** | Opcjonalnie wybierz pozycję **CCYYMMDD** lub **YYMMDD**. |
| **GS5** | Opcjonalne, Select **hhmm**, **HHMMSS** lub **HHMMSSdd**. |
| **GS7** | Opcjonalne, wybierz wartość dla odpowiedzialnej agencji. |
| **GS8** | Opcjonalnie określ wersję dokumentu schematu. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Ustawienia wysyłania — numery sterujące

![Numery kontrolne dla komunikatów wychodzących](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Właściwość | Opis |
|----------|-------------|
| **Numer kontrolny wymiany (ISA13)** | Zakres wartości dla numeru kontrolnego wymiany, który może mieć wartość minimalną 1 i wartość maksymalną 999999999 |
| **Numer kontrolny grupy (GS06)** | Zakres wartości dla numeru kontrolnego grupy, który może mieć minimalną wartość 1 i wartość maksymalną 999999999 |
| **Numer kontrolny zestawu transakcji (ST02)** | Zakres wartości dla numeru kontrolnego zestawu transakcji, który może mieć minimalną wartość 1 i wartość maksymalną 999999999 <p>- **Prefix**: opcjonalne, wartość alfanumeryczna <br>- **Sufiks**: opcjonalny, wartość alfanumeryczna |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Ustawienia wysyłania — zestawy znaków i separatory

![Ograniczniki dla typów komunikatów w komunikatach wychodzących](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

Wiersz **domyślny** pokazuje zestaw znaków, który jest używany jako ograniczniki dla schematu wiadomości. Jeśli nie chcesz używać **domyślnego** zestawu znaków, możesz wprowadzić inny zestaw ograniczników dla każdego typu wiadomości. Po zakończeniu każdego wiersza zostanie wyświetlony nowy pusty wiersz.

> [!TIP]
> Aby podać wartości znaków specjalnych, Edytuj umowę jako kod JSON i podaj wartość ASCII dla znaku specjalnego.

| Właściwość | Opis |
|----------|-------------|
| **Zestaw znaków do użycia** | Zestaw znaków X12, który jest **podstawowy**, **rozszerzony** lub **UTF8**. |
| **Schemat** | Schemat, którego chcesz użyć. Po wybraniu schematu wybierz zestaw znaków, który ma być używany, na podstawie opisów separatora poniżej. |
| **Typ danych wejściowych** | Typ danych wejściowych dla zestawu znaków |
| **Separator składników** | Pojedynczy znak oddzielający złożone elementy danych |
| **Separator elementów danych** | Pojedynczy znak oddzielający proste elementy danych w ramach złożonych danych |
| **Separator znaków zastępczych** | Znak zastępczy, który zastępuje wszystkie znaki separatora w danych ładunku podczas generowania wychodzącego komunikatu X12 |
| **Terminator segmentu** | Pojedynczy znak wskazujący koniec segmentu EDI |
| **Przedrostk** | Znak, który ma być używany z identyfikatorem segmentu. W przypadku określenia sufiksu element danych terminatora segmentu może być pusty. Jeśli terminator segmentu pozostaje pusty, należy wyznaczyć sufiks. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Ustawienia wysyłania — Walidacja

![Właściwości walidacji komunikatów wychodzących](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

W wierszu **domyślnym** są wyświetlane reguły sprawdzania poprawności, które są używane dla typu wiadomości EDI. Jeśli chcesz zdefiniować różne reguły, zaznacz każde pole, w którym reguła ma mieć ustawioną **wartość true**. Po zakończeniu każdego wiersza zostanie wyświetlony nowy pusty wiersz.

| Właściwość | Opis |
|----------|-------------|
| **Typ komunikatu** | Typ wiadomości EDI |
| **Weryfikacja EDI** | Wykonaj walidację EDI dla typów danych, zgodnie z definicją w schemacie właściwości EDI, ograniczenia długości, puste elementy danych i końcowe separatory. |
| **Rozszerzona weryfikacja** | Jeśli typ danych nie jest EDI, walidacja jest wymagana dla elementu danych i dozwolone powtarzanie, wyliczenia i sprawdzanie długości elementu danych (min lub max). |
| **Zezwalaj na zera wiodące/końcowe** | Zachowaj wszystkie dodatkowe spacje wiodące lub końcowe zerowe i spacje. Nie usuwaj tych znaków. |
| **Przytnij zera wiodące/końcowe** | Usuń wszystkie spacje wiodące lub końcowe zerowe i spacje. |
| **Końcowe zasady separatora** | Generuj końcowe separatory. <p>- **Niedozwolone**: Zabroń końcowe ograniczniki i separatory w wychodzącej wymianie. Jeśli wymiana ma końcowe ograniczniki i separatory, wymiana jest zadeklarowana jako nieprawidłowa. <p>- **Opcjonalne**: Wysyłaj zmiany z ogranicznikami i separatorami końcowymi lub bez. <p>- **Obowiązkowe**: wymiana wychodząca musi mieć końcowe ograniczniki i separatory. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>Schematy HIPAA i typy komunikatów

Podczas pracy ze schematami HIPAA oraz typami komunikatów 277 lub 837 należy wykonać kilka dodatkowych kroków. [Numery wersji dokumentu (GS8)](#outbound-control-version-number) dla tych typów komunikatów mają więcej niż 9 znaków, na przykład "005010X222A1". Ponadto niektóre numery wersji dokumentu są mapowane na typy komunikatów typu variant. Jeśli nie odwołujesz się do poprawnego typu komunikatu w schemacie i w umowie, zostanie wyświetlony następujący komunikat o błędzie:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

W tej tabeli wymieniono typy komunikatów, których dotyczą zmiany, dowolne warianty i numery wersji dokumentów, które są mapowane na te typy komunikatów:

| Typ komunikatu lub wariant |  Opis | Numer wersji dokumentu (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Powiadomienie o stanie informacji opieki zdrowotnej | 005010X212 |
| 837_I | Instytucjonalne roszczeń w zakresie opieki zdrowotnej | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Dentystyczne opieki zdrowotnej | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Specjalista ds. opieki zdrowotnej | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

Należy również wyłączyć weryfikację EDI w przypadku używania tych numerów wersji dokumentu, ponieważ powodują one błąd, że długość znaku jest nieprawidłowa.

Aby określić numery wersji i typy komunikatów tych dokumentów, wykonaj następujące kroki:

1. W schemacie HIPAA zastąp bieżący typ komunikatu typem komunikatu Variant dla numeru wersji dokumentu, którego chcesz użyć.

   Załóżmy na przykład, że chcesz użyć numeru wersji dokumentu `005010X222A1` z `837` typem wiadomości. W schemacie zamiast każdej wartości Zastąp `"X12_00501_837"` `"X12_00501_837_P"` wartości wartością.

   Aby zaktualizować schemat, wykonaj następujące kroki:

   1. W Azure Portal przejdź do swojego konta integracji. Znajdź i Pobierz schemat. Zastąp typ komunikatu i Zmień nazwę pliku schematu i przekaż poprawiony schemat do konta integracji. Aby uzyskać więcej informacji, zobacz [Edytowanie schematów](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. W Twojej umowie możesz wybrać poprawiony schemat.

1. W obiekcie Twojej umowy `schemaReferences` Dodaj kolejną pozycję, która określa typ komunikatu Variant pasującego do numeru wersji dokumentu.

   Załóżmy na przykład, że chcesz użyć numeru wersji dokumentu `005010X222A1` dla `837` typu wiadomości. Twoja umowa zawiera `schemaReferences` sekcję z tymi właściwościami i wartościami:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   W tej `schemaReferences` sekcji Dodaj kolejny wpis zawierający te wartości:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   Gdy skończysz, Twoja sekcja będzie wyglądać następująco `schemaReferences` :

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. W ustawieniach wiadomości w Twojej umowie Wyłącz weryfikację EDI przez wyczyszczenie pola wyboru **walidacji EDI** dla każdego typu komunikatu lub dla wszystkich typów komunikatów, jeśli są używane wartości **domyślne** .

   ![Wyłącz weryfikację dla wszystkich typów komunikatów lub dla każdego typu komunikatu](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Dokumentacja łączników

Dodatkowe szczegóły techniczne dotyczące tego łącznika, takie jak akcje i limity, zgodnie z opisem w pliku struktury Swagger łącznika, można znaleźć na [stronie odniesienia łącznika](/connectors/x12/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja tego ŁĄCZNIKa ISE z oznaczeniem używa [limitów komunikatów B2B dla ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników dla Logic Apps](../connectors/apis-list.md)
