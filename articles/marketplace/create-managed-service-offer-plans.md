---
title: Jak utworzyć plany dla oferty usługi zarządzanej w witrynie Azure Marketplace
description: Dowiedz się, jak utworzyć plany dla oferty usługi zarządzanej w witrynie Azure Marketplace przy użyciu Centrum partnerskiego firmy Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 624a3a194b451dc1f498a0ded9d68f641b304eab
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918429"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Jak utworzyć plany dla oferty usługi zarządzanej

Oferty usługi zarządzanej sprzedawane za pomocą komercyjnej witryny Marketplace firmy Microsoft muszą mieć co najmniej jeden plan. Możesz tworzyć różne plany z różnymi opcjami w ramach tej samej oferty. Plany te (czasami określane jako jednostki SKU) mogą różnić się w odniesieniu do wersji, zysków lub warstw usługi. Aby uzyskać szczegółowe wskazówki dotyczące planów, zobacz [plany i cenniki dla ofert komercyjnych w portalu Marketplace](./plans-pricing.md).

## <a name="create-a-plan"></a>Tworzenie planu

1. Na karcie **Przegląd planu** swojej oferty w centrum partnerskim wybierz pozycję **+ Utwórz nowy plan**.
2. W wyświetlonym oknie dialogowym w obszarze **identyfikator planu** wprowadź unikatowy identyfikator planu. Użyj maksymalnie 50 małych znaków alfanumerycznych, łączników lub podkreśleń. Nie można zmodyfikować identyfikatora planu po wybraniu opcji **Utwórz**. Ten identyfikator będzie widoczny dla klientów.
3. W polu **Nazwa planu** wprowadź unikatową nazwę dla tego planu. Użyj maksymalnie 50 znaków. Ta nazwa będzie widoczna dla klientów.
4. Wybierz przycisk **Utwórz**.

## <a name="define-the-plan-listing"></a>Zdefiniuj listę planów

Na karcie **aukcja planu** Zdefiniuj nazwę i opis planu, które mają być wyświetlane na komercyjnym rynku.

1. Pole **Nazwa planu** zawiera nazwę podaną wcześniej dla tego planu. W każdej chwili można ją zmienić. Ta nazwa będzie wyświetlana na komercyjnej witrynie Marketplace jako tytuł planu oferty.
2. W polu **Podsumowanie planu** Podaj krótki opis planu, który może być używany w wynikach wyszukiwania w portalu Marketplace.
3. W polu **Opis planu** Wyjaśnij, co sprawia, że ten plan jest unikatowy i różny od innych planów w ramach oferty.
4. Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty.

## <a name="define-pricing-and-availability"></a>Definiowanie cen i dostępności

Jedynym modelem cen dostępnym dla ofert usług zarządzanych jest **Bring Your Own License (BYOL)**. Oznacza to, że opłaty są naliczane bezpośrednio za koszty związane z tą ofertą, a firma Microsoft nie obciąża opłat.

Każdy plan można skonfigurować tak, aby był widoczny dla wszystkich (publicznych) lub tylko do określonych odbiorców (prywatnych).

> [!NOTE]
> Plany prywatne nie są obsługiwane w przypadku subskrypcji ustanowionych przez odsprzedawcę programu w programie Cloud Solution Provider (CSP).

> [!IMPORTANT]
> Po opublikowaniu planu jako publicznego nie można go zmienić na prywatny. Aby kontrolować, którzy klienci mogą akceptować ofertę i delegować zasoby, należy użyć planu prywatnego. W przypadku planu publicznego nie można ograniczyć dostępności do określonych klientów, a nawet do określonej liczby klientów (mimo że można już zrezygnować z sprzedaży planu w przypadku wybrania tej opcji). Dostęp do delegowania można usunąć, gdy klient zaakceptuje ofertę tylko wtedy, gdy została uwzględniona autoryzacja z definicją roli ustawioną na przypisanie rejestracji usług zarządzanych, po opublikowaniu oferty. Możesz również skontaktować się z klientem i poproś o usunięcie dostępu.

## <a name="make-your-plan-public"></a>Ustaw swój plan jako publiczny

1. W obszarze **widoczność planu** wybierz pozycję **publiczny**.
2. Wybierz pozycję **Zapisz wersję roboczą**. Aby powrócić do karty Przegląd planu, wybierz pozycję **Przegląd planu** w lewym górnym rogu.
3. Aby utworzyć kolejny plan dla tej oferty, wybierz pozycję **+ Utwórz nowy plan** na karcie **Przegląd planu** .

## <a name="make-your-plan-private"></a>Ustaw swój plan jako prywatny

Możesz udzielić dostępu do planu prywatnego przy użyciu identyfikatorów subskrypcji platformy Azure. Możesz dodać maksymalnie 10 identyfikatorów subskrypcji ręcznie lub maksymalnie 10 000 identyfikatorów subskrypcji przy użyciu. Plik CSV.

Aby ręcznie dodać do 10 identyfikatorów subskrypcji:

1. W obszarze **widoczność planu** wybierz pozycję **prywatny**.
2. Wprowadź identyfikator subskrypcji platformy Azure dla odbiorców, do których chcesz udzielić dostępu.
3. Opcjonalnie wprowadź opis tych odbiorców w polu **Opis** .
4. Aby dodać kolejny identyfikator, wybierz pozycję **Dodaj identyfikator (maks. 10)**.
5. Po zakończeniu dodawania identyfikatorów wybierz pozycję **Zapisz wersję roboczą**.

Aby dodać do 10 000 identyfikatorów subskrypcji przy użyciu. Plik CSV:

1. W obszarze **widoczność planu** wybierz pozycję **prywatny**.
2. Wybierz łącze **Eksportuj odbiorców (CSV)** . Spowoduje to pobranie. Plik CSV.
3. Otwórz okno. Plik CSV. W kolumnie **Identyfikator** wprowadź identyfikatory subskrypcji platformy Azure, do których chcesz udzielić dostępu.
4. W kolumnie **Opis**   można dodać opis dla każdego wpisu.
5. W kolumnie **Typ**   Dodaj identyfikator **subskrypcji**   do każdego wiersza, który ma ID.
6. Zapisz plik jako. Plik CSV.
7. W centrum partnerskim wybierz łącze **Importuj odbiorców (CSV)** .
8. W ****   oknie dialogowym potwierdzenia wybierz pozycję **tak**, a następnie Przekaż. Plik CSV.
9. Wybierz pozycję **Zapisz wersję roboczą**.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Ta sekcja tworzy manifest z informacjami o autoryzacji do zarządzania zasobami klienta. Te informacje są wymagane w celu włączenia [zarządzania zasobami delegowanymi przez platformę Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

Zapoznaj się z [dzierżawcami, rolami i użytkownikami w scenariuszach usługi Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) , aby zrozumieć, które role są obsługiwane, oraz najlepsze rozwiązania dotyczące definiowania autoryzacji.

> [!NOTE]
> Użytkownicy i role w wpisach autoryzacji będą stosowane do każdego klienta, który aktywuje plan. Jeśli chcesz ograniczyć dostęp do określonego klienta, musisz opublikować plan prywatny do ich wyłącznego użytku.

### <a name="manifest"></a>Manifest

1. W obszarze **manifest** Podaj **wersję** manifestu. Użyj formatu n. n. n (na przykład 1.2.5).
2. Wprowadź swój **Identyfikator dzierżawy**. Jest to identyfikator GUID skojarzony z IDENTYFIKATORem dzierżawy usługi Azure Active Directory (Azure AD) organizacji; oznacza to, że dzierżawy zarządzającej, z której będziesz uzyskiwać dostęp do zasobów klientów. Jeśli go nie masz, możesz go znaleźć, umieszczając kursor nad nazwą swojego konta w prawym górnym rogu Azure Portal lub wybierając pozycję **Przełącz katalog**.

Jeśli opublikujesz nową wersję oferty i chcesz utworzyć zaktualizowany manifest, wybierz pozycję **+ nowy manifest**. Pamiętaj, aby zwiększyć numer wersji z poprzedniej wersji manifestu.

### <a name="authorizations"></a>Autoryzacje

Autoryzacje definiują jednostki w dzierżawie zarządzającej, które mają dostęp do zasobów i subskrypcji dla klientów, którzy kupują plan. Każda z tych jednostek ma przypisaną wbudowaną rolę, która przyznaje określony poziom dostępu.

Dla każdego planu można utworzyć maksymalnie 20 autoryzacji.

> [!TIP]
> W większości przypadków chcesz przypisać role do grupy użytkowników usługi Azure AD lub nazwy głównej usług, a nie do serii poszczególnych kont użytkowników. Pozwala to na dodawanie lub usuwanie dostępu dla poszczególnych użytkowników bez konieczności aktualizacji i ponownego publikowania planu, gdy zmienią się wymagania dotyczące dostępu. W przypadku przypisywania ról do grup usługi Azure AD [typem grupy powinien być zabezpieczenia, a nie Office 365](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Aby uzyskać dodatkowe zalecenia, zobacz [dzierżawy, role i użytkowników w scenariuszach usługi Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md).

Dla każdej autoryzacji należy podać poniższe informacje. Następnie można wybrać opcję **+ Dodaj autoryzację** dowolną liczbę razy, aby dodać więcej użytkowników i definicje ról.

* **Identyfikator obiektu AAD**: identyfikator usługi Azure AD użytkownika, grupy użytkowników lub aplikacji, które będą mieć określone uprawnienia (zgodnie z definicją roli) do zasobów klientów.
* **Nazwa wyświetlana obiektu usługi AAD**: przyjazna nazwa, która pomaga klientowi zrozumieć cel tej autoryzacji. Klient zobaczy tę nazwę podczas delegowania zasobów.
* **Definicja roli**: Wybierz jedną z dostępnych ról wbudowanych usługi Azure AD z listy. Ta rola określi uprawnienia, które użytkownik w polu **identyfikatora podmiotu zabezpieczeń** będzie miał w zasobach klientów. Opisy tych ról można znaleźć w temacie [role wbudowane](../role-based-access-control/built-in-roles.md) i [Obsługa ról w usłudze Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).

> [!NOTE]
> Nowe wbudowane role są dodawane do platformy Azure, a będą dostępne w tym miejscu. Może wystąpić pewne opóźnienie przed ich wyświetleniem.

* **Role** możliwe do przypisania: Ta opcja będzie wyświetlana tylko wtedy, gdy w **definicji roli** dla tej autoryzacji wybrano administratora dostępu użytkownika. Jeśli tak, należy dodać co najmniej jedną przypisaną rolę w tym miejscu. Użytkownik w polu **Identyfikator obiektu usługi Azure AD** będzie mógł przypisywać te role do tożsamości zarządzanych, które są wymagane w celu [wdrożenia zasad, które można skorygować](../lighthouse/how-to/deploy-policy-remediation.md). Dla tego użytkownika nie są stosowane żadne inne uprawnienia zwykle skojarzone z rolą administratora dostępu użytkownika.

> [!TIP]
> Aby mieć pewność, że w razie potrzeby będzie można [usunąć dostęp do delegowania](../lighthouse/how-to/remove-delegation.md) , Uwzględnij **autoryzację** z **definicją roli** ustawioną na [przydział rejestracji usług zarządzanych](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.

Po zakończeniu wszystkich sekcji planu możesz wybrać pozycję **+ Utwórz nowy plan** , aby utworzyć dodatkowe plany. Gdy skończysz, wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="next-steps"></a>Następne kroki

* [Przeglądanie i publikowanie](review-publish-offer.md)
