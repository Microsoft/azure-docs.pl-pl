---
title: Konfigurowanie planu szablonu rozwiązania
description: Informacje na temat konfigurowania planu szablonu rozwiązania dla oferty aplikacji platformy Azure w centrum partnerskim.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8469cad02009d054bd8ba97fb4aabfdae84ef842
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744622"
---
# <a name="configure-a-solution-template-plan"></a>Konfigurowanie planu szablonu rozwiązania

Ten artykuł dotyczy tylko planów szablonów rozwiązań dla oferty aplikacji platformy Azure. W przypadku konfigurowania planu aplikacji zarządzanej przejdź do pozycji [Konfigurowanie zarządzanego planu aplikacji](create-new-azure-apps-offer-managed.md).

## <a name="choose-who-can-see-your-plan"></a>Wybierz, kto może zobaczyć Twój plan

Każdy plan można skonfigurować tak, aby był widoczny dla wszystkich użytkowników lub tylko do określonych odbiorców. Możesz udzielić dostępu do prywatnych odbiorców przy użyciu identyfikatorów subskrypcji platformy Azure z opcją dołączenia opisu każdego przypisanego identyfikatora subskrypcji. Możesz dodać maksymalnie 10 identyfikatorów subskrypcji ręcznie lub maksymalnie 10 000 identyfikatorów subskrypcji przy użyciu. Plik CSV. Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID i litery muszą być małymi literami.

> [!NOTE]
> W przypadku opublikowania planu prywatnego można później zmienić jego widoczność na publiczną. Jednak po opublikowaniu planu publicznego nie można zmienić jego widoczności na prywatną.

Na karcie **dostępność** w obszarze **widoczność planu** wykonaj jedną z następujących czynności:

- Aby określić plan jako publiczny, wybierz przycisk opcji **Public** (znany również jako _przycisk radiowy_).
- Aby plan był prywatny, wybierz przycisk opcji **prywatny** , a następnie ręcznie Dodaj identyfikatory subskrypcji platformy Azure lub w pliku CSV.

    > [!NOTE]
    > Prywatna lub ograniczona Grupa odbiorców różni się od odbiorców w wersji zapoznawczej zdefiniowanej na karcie **Podgląd** . Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty przed opublikowaniem jej na żywo w portalu Marketplace. Gdy wybór odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany (prywatne lub nie) do celów weryfikacji.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Ręczne dodawanie identyfikatorów subskrypcji platformy Azure dla planu prywatnego

1. W obszarze **widoczność planu** wybierz przycisk opcji **prywatny** .
1. W wyświetlonym polu **Identyfikator subskrypcji platformy Azure** wprowadź identyfikator subskrypcji platformy Azure dla odbiorców, którym chcesz udzielić dostępu do tego planu prywatnego. Wymagany jest co najmniej jeden identyfikator subskrypcji.
1. Obowiązkowe Wprowadź opis tych odbiorców w polu **Opis** .
1. Aby dodać inny identyfikator subskrypcji, wybierz łącze **Dodaj identyfikator (maks. 10)** i powtórz kroki 2 i 3.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Użyj. Plik CSV służący do dodawania identyfikatorów subskrypcji platformy Azure dla planu prywatnego

1. W obszarze **widoczność planu** wybierz przycisk opcji **prywatny** .
1. Wybierz łącze **Eksportuj odbiorców (CSV)** .
1. Otwórz okno. Plik CSV i Dodaj identyfikatory subskrypcji platformy Azure, dla których chcesz udzielić dostępu do prywatnej oferty do kolumny **ID** .
1. Opcjonalnie wprowadź opis dla każdej grupy odbiorców w kolumnie **Opis** .
1. Dodaj wartość "Identyfikator subskrypcji" w kolumnie **Typ** dla każdego wiersza z identyfikatorem subskrypcji.
1. Zapisz. Plik CSV.
1. Na karcie **dostępność** w obszarze **widoczność planu** wybierz łącze **Importuj odbiorców (CSV)** .
1. W wyświetlonym oknie dialogowym wybierz pozycję **tak**.
1. Wybierz pozycję. Plik CSV, a następnie wybierz pozycję **Otwórz**. Zostanie wyświetlony komunikat z informacją, że. Plik CSV został pomyślnie zaimportowany.

### <a name="hide-your-plan"></a>Ukryj plan

Jeśli szablon rozwiązania jest przeznaczony do wdrożenia tylko pośrednio, gdy istnieje odwołanie do innego szablonu rozwiązania lub aplikacji zarządzanej, zaznacz pole wyboru w obszarze **Ukryj plan** , aby opublikować szablon rozwiązania, ale Ukryj go od klientów, którzy przeszukują i przeglądają je bezpośrednio.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji: Zdefiniuj konfigurację techniczną.

## <a name="define-the-technical-configuration"></a>Zdefiniuj konfigurację techniczną

Na karcie **konfiguracja techniczna** przekaż pakiet wdrożeniowy, który umożliwi klientom wdrożenie planu i podanie numeru wersji pakietu.

> [!NOTE]
> Ta karta nie będzie widoczna, jeśli wybrano ponowne użycie pakietów z innego planu na karcie **Konfiguracja planu** . Jeśli tak jest, przejdź do pozycji [Wyświetl plany](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Przypisywanie numeru wersji pakietu

W polu **wersja** Podaj bieżącą wersję konfiguracji technicznej. Zwiększ tę wersję przy każdej publikacji zmiany na tej stronie. Numer wersji musi mieć format: Integer. Integer. Integer. Na przykład `1.0.2`.

### <a name="upload-a-package-file"></a>Przekaż plik pakietu

W obszarze **plik pakietu (zip)** przeciągnij plik pakietu do szarego pola lub wybierz łącze **Przeglądaj w poszukiwaniu plików** .

> [!NOTE]
> Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje `https://upload.xboxlive.com` usługi używanej przez centrum partnerskie.

### <a name="previously-published-packages"></a>Poprzednio opublikowane pakiety

Po opublikowaniu oferty na żywo na stronie **konfiguracji technicznej** zostanie wyświetlona Podkarta **poprzednio opublikowanych pakietów** . Na tej karcie znajduje się lista wszystkich poprzednio opublikowanych wersji konfiguracji technicznej.

## <a name="view-your-plans"></a>Wyświetlanie planów

- Wybierz pozycję **Zapisz wersję roboczą**, a następnie w lewym górnym rogu strony wybierz pozycję **planowanie przegląd** , aby powrócić do strony **Przegląd planu** .

Po utworzeniu jednego lub kilku planów zobaczysz nazwę planu, identyfikator planu, typ planu, dostępność (publiczna lub prywatna), bieżący stan publikowania oraz wszystkie dostępne akcje na karcie **Przegląd planu** .

Akcje, które są dostępne w kolumnie **Akcja** karty **Przegląd planu** , różnią się w zależności od stanu planu i mogą obejmować następujące elementy:

- Jeśli plan ma stan **wersja robocza**, w kolumnie **Akcja** zostanie wyświetlona wartość **Usuń wersję roboczą**.
- Jeśli plan ma stan **Live**, link w kolumnie **akcji** będzie **zatrzymywać plan sprzedaży** lub **zsynchronizować odbiorców prywatnych**. Link **Synchronizuj odbiorców prywatnych** będzie publikować tylko zmiany w prywatnych odbiorcach, bez publikowania innych aktualizacji, które mogły zostać wprowadzone do oferty.
- Aby utworzyć kolejny plan dla tej oferty, w górnej części karty **Przegląd planu** wybierz pozycję **+ Utwórz nowy plan**. Następnie powtórz kroki opisane w temacie [jak utworzyć plany dla oferty aplikacji platformy Azure](create-new-azure-apps-offer-plans.md). W przeciwnym razie, jeśli skończysz tworzyć plany, przejdź do następnej sekcji: Następne kroki.

## <a name="next-steps"></a>Następne kroki

- [Testowanie i publikowanie oferty aplikacji platformy Azure](create-new-azure-apps-offer-test-publish.md).
- Dowiedz się, [jak sprzedawać oferty aplikacji platformy Azure](create-new-azure-apps-offer-marketing.md) w ramach współsprzedaży z firmą Microsoft i odsprzedawać je za pomocą programów CSP.
