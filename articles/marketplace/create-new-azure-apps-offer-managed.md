---
title: Konfigurowanie zarządzanego planu aplikacji
description: Dowiedz się, jak skonfigurować zarządzany plan aplikacji dla oferty aplikacji platformy Azure w centrum partnerskim.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 66da9124a6b46fa34d0a13c8992cd3141b095422
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370320"
---
# <a name="configure-a-managed-application-plan"></a>Konfigurowanie zarządzanego planu aplikacji

Ten artykuł dotyczy tylko planów aplikacji zarządzanych dla oferty aplikacji platformy Azure. W przypadku konfigurowania planu szablonu rozwiązania przejdź do pozycji [Konfigurowanie planu szablonu rozwiązania](create-new-azure-apps-offer-solution.md).

## <a name="define-markets-pricing-and-availability"></a>Definiowanie rynków, cen i dostępności

Każdy plan musi być dostępny na co najmniej jednym rynku. Na karcie **Cennik i dostępność** można skonfigurować rynki, w których ten plan będzie dostępny, Cena i zdecydować, czy plan ma być widoczny dla wszystkich, czy tylko dla określonych klientów (nazywanych również planem prywatnym).

1. Na stronie **rynki** wybierz łącze **Edytuj rynki** .
1. W wyświetlonym oknie dialogowym Wybierz lokalizacje rynkowe, w których chcesz udostępnić plan. Należy wybrać co najmniej jeden i maksymalnie 141 rynków.

    > [!NOTE]
    > To okno dialogowe zawiera pole wyszukiwania i opcję filtrowania tylko w krajach, w których firma Microsoft przekazuje sprzedaż i korzysta z opodatkowania w Twoim imieniu.

1. Wybierz pozycję **Zapisz** , aby zamknąć okno dialogowe.

## <a name="define-pricing"></a>Definiowanie cen

W polu **Cena** Podaj cenę za miesiąc dla tego planu. Ta cena jest uzupełnieniem wszelkich kosztów związanych z infrastrukturą platformy Azure lub użyciem ponoszonych przez zasoby wdrożone w tym rozwiązaniu.

Oprócz ceny za miesiąc można także ustawić ceny za użycie jednostek niestandardowych przy użyciu [rozliczeń naliczanych](partner-center-portal/azure-app-metered-billing.md). Cena za miesiąc można ustawić na zero, a opłaty za korzystanie wyłącznie z naliczanych opłat.

Ceny są ustawiane w USD (USD = Stany Zjednoczone dolara) są konwertowane na walutę lokalną wszystkich wybranych rynków przy użyciu bieżących kursów wymiany przy zapisywaniu. Sprawdź te ceny przed opublikowaniem, eksportując arkusz kalkulacyjny z cennikiem i sprawdzając cenę na każdym rynku. Jeśli chcesz ustawić ceny niestandardowe na indywidualnym rynku, zmodyfikuj i zaimportuj arkusz cen.

### <a name="add-a-custom-meter-dimension-optional"></a>Dodaj niestandardowy wymiar miernika (opcjonalnie)

1. W obszarze **Wymiary usługi pomiaru w portalu Marketplace** wybierz link **Dodaj niestandardowy wymiar miernika (Max 18)** .
1. W polu **Identyfikator** wprowadź niezmienne odwołanie do identyfikatora podczas emitowania zdarzeń użycia.
1. W polu **Nazwa wyświetlana** wprowadź nazwę wyświetlaną skojarzoną z wymiarem. Na przykład "wysłane wiadomości SMS".
1. W polu **Jednostka miary** wprowadź opis jednostki rozliczeniowej. Na przykład "za wiadomość SMS" lub "na 100 wiadomości e-mail".
1. W polu **Cena jednostkowa w USD** wprowadź cenę jednej jednostki wymiaru.
1. W polu **Liczba miesięczna uwzględnionych w programie** wprowadź ilość (jako liczbę całkowitą) wymiaru, który jest uwzględniany w każdym miesiącu dla klientów, którzy zwracają cykliczną opłatą miesięczną. Aby ustawić nieograniczoną liczbę, zaznacz to pole wyboru.
1. Aby dodać kolejny niestandardowy wymiar miernika, powtórz kroki od 1 do 7.

### <a name="set-custom-prices-optional"></a>Ustawianie cen niestandardowych (opcjonalnie)

Ceny ustawione w USD (USD = Stany Zjednoczone dolara) są konwertowane na walutę lokalną wszystkich wybranych rynków przy użyciu bieżących kursów wymiany przy zapisywaniu. Sprawdź te ceny przed opublikowaniem, eksportując arkusz kalkulacyjny z cennikiem i sprawdzając cenę na każdym rynku. Jeśli chcesz ustawić ceny niestandardowe na indywidualnym rynku, zmodyfikuj i zaimportuj arkusz cen.

Uważnie Przejrzyj ceny przed opublikowaniem, ponieważ istnieją pewne ograniczenia dotyczące tego, co można zmienić po opublikowaniu planu.

> [!NOTE]
> Po opublikowaniu ceny na rynku planu nie można jej później zmienić.

Aby ustawić ceny niestandardowe na indywidualnym rynku, należy wyeksportować, zmodyfikować, a następnie zaimportować arkusz cen. Użytkownik jest odpowiedzialny za sprawdzanie poprawności cen i posiadanie tych ustawień. Aby uzyskać szczegółowe informacje, zobacz [ceny niestandardowe](plans-pricing.md#custom-prices).

1. Najpierw musisz zapisać zmiany cen, aby włączyć eksportowanie danych cen. W dolnej części karty **Cennik i dostępność** wybierz pozycję **Zapisz wersję roboczą**.
1. W obszarze **Cennik** wybierz łącze **Eksportuj dane cennika** .
1. Otwórz plik exportedPrice.xlsx w programie Microsoft Excel.
1. W arkuszu kalkulacyjnym wprowadź aktualizacje, aby uzyskać informacje o cenach, a następnie Zapisz plik.

   Aby można było zaktualizować plik, może być konieczne włączenie edycji w programie Excel.

1. Na karcie **Cennik i dostępność** w obszarze **Cennik** wybierz łącze **Importuj dane cennika** .
1. W wyświetlonym oknie dialogowym kliknij przycisk **tak**.
1. Wybierz zaktualizowany plik exportedPrice.xlsx a następnie kliknij przycisk **Otwórz**.

## <a name="choose-who-can-see-your-plan"></a>Wybierz, kto może zobaczyć Twój plan

Każdy plan można skonfigurować tak, aby był widoczny dla wszystkich użytkowników lub tylko do określonych odbiorców. Możesz udzielić dostępu do prywatnych odbiorców przy użyciu identyfikatorów subskrypcji platformy Azure z opcją dołączenia opisu każdego przypisanego identyfikatora subskrypcji. Możesz dodać maksymalnie 10 identyfikatorów subskrypcji ręcznie lub maksymalnie 10 000 identyfikatorów subskrypcji przy użyciu. Plik CSV. Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID i litery muszą być małymi literami.

> [!NOTE]
> W przypadku opublikowania planu prywatnego można później zmienić jego widoczność na publiczną. Jednak po opublikowaniu planu publicznego nie można zmienić jego widoczności na prywatną.

W obszarze **widoczność planu** wykonaj jedną z następujących czynności:

- Aby określić plan jako publiczny, wybierz przycisk opcji **Public** (znany również jako _przycisk radiowy_ ).
- Aby plan był prywatny, wybierz przycisk opcji **prywatny** , a następnie ręcznie Dodaj identyfikatory subskrypcji platformy Azure lub w pliku CSV.

> [!NOTE]
> Prywatna lub ograniczona Grupa odbiorców różni się od odbiorców w wersji zapoznawczej zdefiniowanej na karcie **Podgląd** . Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty przed opublikowaniem jej na żywo w portalu Marketplace. Gdy wybór odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany (prywatne lub nie) do celów weryfikacji.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Ręczne dodawanie identyfikatorów subskrypcji platformy Azure dla planu prywatnego

1. W obszarze **widoczność planu** wybierz przycisk opcji **prywatny** .
1. W wyświetlonym polu **Identyfikator subskrypcji platformy Azure** wprowadź identyfikator subskrypcji platformy Azure dla odbiorców, którym chcesz udzielić dostępu do tego planu prywatnego. Wymagany jest co najmniej jeden identyfikator subskrypcji.
1. Obowiązkowe Wprowadź opis tych odbiorców w polu **Opis** .
1. Aby dodać inny identyfikator subskrypcji, wybierz łącze **Dodaj identyfikator (maks. 10)** i powtórz kroki 2 i 3.

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Użyj. Plik CSV służący do dodawania identyfikatorów subskrypcji platformy Azure dla planu prywatnego

1. W obszarze **widoczność planu** wybierz przycisk opcji **prywatny** .
1. Wybierz łącze **Eksportuj odbiorców (CSV)** .
1. Otwórz okno. Plik CSV i Dodaj identyfikatory subskrypcji platformy Azure, dla których chcesz udzielić dostępu do prywatnej oferty do kolumny **ID** .
1. Opcjonalnie wprowadź opis dla każdej grupy odbiorców w kolumnie **Opis** .
1. Dodaj wartość "Identyfikator subskrypcji" w kolumnie **Typ** dla każdego wiersza z identyfikatorem subskrypcji.
1. Zapisz. Plik CSV.
1. Na karcie **dostępność** w obszarze **widoczność planu** wybierz łącze **Importuj odbiorców (CSV)** .
1. W wyświetlonym oknie dialogowym wybierz pozycję **tak**.
1. Wybierz pozycję. Plik CSV, a następnie wybierz pozycję **Otwórz**. Zostanie wyświetlony komunikat z informacją, że. Plik CSV został pomyślnie zaimportowany.

## <a name="define-the-technical-configuration"></a>Zdefiniuj konfigurację techniczną

Na karcie **konfiguracja techniczna** przekaż pakiet wdrożeniowy, który umożliwi klientom wdrożenie planu i podanie numeru wersji pakietu. Podajesz również inne informacje techniczne.

> [!NOTE]
> Ta karta nie będzie widoczna, jeśli wybrano ponowne użycie pakietów z innego planu na karcie **Konfiguracja planu** . Jeśli tak jest, przejdź do pozycji [Wyświetl plany](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Przypisywanie numeru wersji pakietu

W polu **wersja** Podaj bieżącą wersję konfiguracji technicznej. Zwiększ tę wersję przy każdej publikacji zmiany na tej stronie. Numer wersji musi mieć format: Integer. Integer. Integer. Na przykład `1.0.2`.

### <a name="upload-a-package-file"></a>Przekaż plik pakietu

W obszarze **plik pakietu (zip)** przeciągnij plik pakietu do szarego pola lub wybierz łącze **Przeglądaj w poszukiwaniu plików** .

> [!NOTE]
> Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje `https://upload.xboxlive.com` usługi używanej przez centrum partnerskie.

#### <a name="previously-published-packages"></a>Poprzednio opublikowane pakiety

Podkarta **poprzednio opublikowanych pakietów** umożliwia wyświetlenie wszystkich opublikowanych wersji konfiguracji technicznej.

### <a name="enable-just-in-time-jit-access-optional"></a>Włącz dostęp just-in-Time (JIT) (opcjonalnie)

Aby włączyć dostęp JIT dla tego planu, zaznacz pole wyboru **Włącz dostęp just-in-Time (JIT)** . Aby wymagać, aby klienci aplikacji zarządzanej mogli udzielić stałego dostępu do konta, pozostaw tę opcję niezaznaczone. Aby dowiedzieć się więcej na temat tej opcji, zobacz [dostęp just in Time (JIT)](plan-azure-app-managed-app.md#just-in-time-jit-access).

### <a name="select-a-deployment-mode"></a>Wybierz tryb wdrażania

Wybierz tryb wdrażania **kompletnego** lub **przyrostowego** .

- W trybie **kompletnym** ponowne wdrożenie aplikacji przez klienta spowoduje usunięcie zasobów w zarządzanej grupie zasobów, jeśli zasoby nie są zdefiniowane w [mainTemplate.jsna](/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#create-the-arm-template.md).
- W trybie **przyrostowym** ponowne wdrożenie aplikacji pozostawia istniejące zasoby bez zmian.

Aby dowiedzieć się więcej na temat trybów wdrażania, zobacz [Azure Resource Manager trybami wdrożenia](/azure/azure-resource-manager/deployment-modes.md).

### <a name="provide-a-notification-endpoint-url"></a>Podaj adres URL punktu końcowego powiadomienia

W polu **adres URL punktu końcowego powiadomienia** Podaj punkt końcowy elementu WEBHOOK protokołu HTTPS, aby otrzymywać powiadomienia o wszystkich operacjach CRUD na wystąpieniach aplikacji zarządzanej tej wersji planu.

### <a name="customize-allowed-customer-actions-optional"></a>Dostosuj dozwolone akcje klienta (opcjonalnie)

1. Aby określić akcje, które klienci mogą wykonywać na zarządzanych zasobach oprócz " `*/read` " akcje, które są dostępne domyślnie, wybierz pole **Dostosuj dozwolone akcje klienta** .
1. W wyświetlonych polach podaj dodatkowe akcje kontroli i dozwolone akcje dotyczące danych, które mają być możliwe do wykonania przez klienta, oddzielone średnikami. Na przykład, aby zezwolić użytkownikom na ponowne uruchomienie maszyn wirtualnych, Dodaj `Microsoft.Compute/virtualMachines/restart/action` do pola **dozwolone akcje kontroli** .

### <a name="choose-who-can-manage-the-application"></a>Wybierz, kto może zarządzać aplikacją

Wskaż, kto powinien mieć dostęp do tej aplikacji zarządzanej w każdym wybranym regionie świadczenia usługi Azure: _globalna platforma Azure i usługa_ _Azure Government Cloud_. Tożsamości usługi Azure AD będą używane do identyfikowania użytkowników, grup i aplikacji, którym chcesz udzielić uprawnień do zarządzanej grupy zasobów. Aby uzyskać więcej informacji, zobacz [planowanie aplikacji zarządzanej przez platformę Azure dla oferty aplikacji platformy Azure](plan-azure-application-offer.md).

W razie potrzeby wykonaj następujące kroki dla globalnych usług Azure i Azure Government Cloud.

1. W polu **Identyfikator dzierżawy Azure Active Directory** wprowadź identyfikator dzierżawy usługi Azure AD (znany również jako identyfikator katalogu) zawierający tożsamości użytkowników, grup lub aplikacji, do których chcesz udzielić uprawnień.
1. W polu **Identyfikator podmiotu zabezpieczeń** podaj identyfikator obiektu usługi Azure AD użytkownika, grupy lub aplikacji, którym chcesz nadać uprawnienia do zarządzanej grupy zasobów. Zidentyfikuj użytkownika według identyfikatora podmiotu zabezpieczeń, który można znaleźć w [bloku Azure Active Directory użytkownicy](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) na Azure Portal.
1. Z listy **definicja roli** wybierz wbudowaną rolę usługi Azure AD. Wybrana rola opisuje uprawnienia podmiotu zabezpieczeń w ramach zasobów w ramach subskrypcji klienta.
1. Aby dodać kolejną autoryzację, wybierz łącze **Dodaj autoryzację (maks 100)** i powtórz kroki od 1 do 3.

### <a name="policy-settings-optional"></a>Ustawienia zasad (opcjonalnie)

Można skonfigurować maksymalnie pięć zasad i tylko jedno wystąpienie poszczególnych zasad. Niektóre zasady wymagają dodatkowych parametrów.

1. W obszarze **Ustawienia zasad** wybierz łącze **+ Dodaj zasady (maks. 5)** .
1. W polu **Nazwa** wprowadź nazwę przydziału zasad (ograniczona do 50 znaków).
1. W polu Lista **zasad** wybierz zasady platformy Azure, które będą stosowane do zasobów utworzonych przez aplikację zarządzaną w ramach subskrypcji klienta.
1. W polu **parametry zasad** Podaj parametr, dla którego mają być stosowane zasady inspekcji i ustawień diagnostycznych.
1. W polu listy **jednostka SKU zasad** wybierz typ jednostki SKU zasad.

    > [!NOTE]
    > Dla zasad inspekcji wymagana jest jednostka SKU _zasad standardowych_ .

## <a name="view-your-plans"></a>Wyświetlanie planów

- Wybierz pozycję **Zapisz wersję roboczą** , a następnie w lewym górnym rogu strony wybierz pozycję **planowanie przegląd** , aby powrócić do strony **Przegląd planu** .

Po utworzeniu jednego lub kilku planów zobaczysz nazwę planu, identyfikator planu, typ planu, dostępność (publiczna lub prywatna), bieżący stan publikowania oraz wszystkie dostępne akcje na karcie **Przegląd planu** .

Akcje, które są dostępne w kolumnie **Akcja** karty **Przegląd planu** , różnią się w zależności od stanu planu i mogą obejmować następujące elementy:

- Jeśli plan ma stan **wersja robocza** , w kolumnie **Akcja** zostanie wyświetlona wartość **Usuń wersję roboczą**.
- Jeśli plan ma stan **Live** , link w kolumnie **akcji** będzie **zatrzymywać plan sprzedaży** lub **zsynchronizować odbiorców prywatnych**. Link **Synchronizuj odbiorców prywatnych** będzie publikować tylko zmiany w prywatnych odbiorcach, bez publikowania innych aktualizacji, które mogły zostać wprowadzone do oferty.
- Aby utworzyć kolejny plan dla tej oferty, w górnej części karty **Przegląd planu** wybierz pozycję **+ Utwórz nowy plan**. Następnie powtórz kroki opisane w temacie [jak utworzyć plany dla oferty aplikacji platformy Azure](create-new-azure-apps-offer-plans.md). W przeciwnym razie, jeśli skończysz tworzyć plany, przejdź do następnej sekcji: Następne kroki.

## <a name="next-steps"></a>Następne kroki

- [Testowanie i publikowanie oferty aplikacji platformy Azure](create-new-azure-apps-offer-test-publish.md).
- Dowiedz się, [jak sprzedawać oferty aplikacji platformy Azure](create-new-azure-apps-offer-marketing.md) , korzystając ze współsprzedaży z firmą Microsoft i odsprzedaży za pomocą programów CSP.
