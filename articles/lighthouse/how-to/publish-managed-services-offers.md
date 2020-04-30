---
title: Publikowanie oferty usługi zarządzanej w witrynie Azure Marketplace
description: Dowiedz się, jak opublikować ofertę usługi zarządzanej, która dołączy klientów do zarządzania zasobami delegowanymi przez platformę Azure.
ms.date: 04/24/2020
ms.topic: conceptual
ms.openlocfilehash: 19c4d1a4bd0ffd7c0162cbf7f20c49a5b219b9bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146738"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publikowanie oferty usługi zarządzanej w witrynie Azure Marketplace

> [!IMPORTANT]
> Od 13 kwietnia 2020 zaczniemy przenieść oferty usług zarządzanych do Centrum partnerskiego. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie nowej oferty usługi zarządzanej](../../marketplace/partner-center-portal/create-new-managed-service-offer.md) w celu zarządzania zmigrowanymi ofertami.

W tym artykule dowiesz się, jak opublikować publiczną lub prywatną ofertę usługi zarządzanej w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com) przy użyciu [Portal Cloud partner](https://cloudpartner.azure.com/). Klienci, którzy kupili ofertę, będą mogli dodać subskrypcje i grupy zasobów na potrzeby [zarządzania zasobami delegowanymi przez platformę Azure](../concepts/azure-delegated-resource-management.md).

## <a name="publishing-requirements"></a>Wymagania dotyczące publikowania

Musisz mieć prawidłowe [konto w centrum partnerskim](../../marketplace/partner-center-portal/create-account.md) , aby tworzyć i publikować oferty. Jeśli nie masz jeszcze konta, [proces rejestracji](https://aka.ms/joinmarketplace) przeprowadzi Cię przez kroki tworzenia konta w centrum partnerskim i rejestrowania się w komercyjnym programie Marketplace.

Zgodnie z [wymaganiami dotyczącymi certyfikacji oferty usługi zarządzanej](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)musisz mieć [poziom kompetencji w chmurze Silver lub Gold](https://docs.microsoft.com/partner-center/learn-about-competencies) , aby opublikować ofertę [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) usługi zarządzanej.

Identyfikator Microsoft Partner Network (MPN) zostanie [automatycznie skojarzony](../../billing/billing-partner-admin-link-started.md) z ofertami, które publikujesz, aby śledzić swój wpływ na zaangażowanie klientów.

> [!NOTE]
> Jeśli nie chcesz publikować oferty w witrynie Azure Marketplace, możesz dołączyć klientów ręcznie przy użyciu szablonów Azure Resource Manager. Aby uzyskać więcej informacji, zobacz Dołączanie [klienta do zarządzania zasobami delegowanymi przez platformę Azure](onboard-customer.md).

Publikowanie oferty usługi zarządzanej jest podobne do publikowania dowolnego innego typu oferty w witrynie Azure Marketplace. Aby dowiedzieć się więcej o ogólnym procesie publikowania, zobacz artykuł [Azure Marketplace i Podręcznik publikowania AppSource](../../marketplace/marketplace-publishers-guide.md). Należy również zapoznać się z [komercyjnymi zasadami certyfikacji portalu Marketplace](https://docs.microsoft.com/legal/marketplace/certification-policies), szczególnie w sekcji [usługi zarządzane](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services) .

Po dodaniu oferty przez klienta będzie można delegować jedną lub więcej subskrypcji lub grup zasobów, które zostaną następnie dołączone [do zarządzania zasobami delegowanymi przez platformę Azure](#the-customer-onboarding-process).

> [!IMPORTANT]
> Każdy plan w ofercie usługi zarządzanej zawiera sekcję **szczegóły manifestu** , w której definiujesz jednostki Azure Active Directory (Azure AD) w dzierżawie, które będą miały dostęp do delegowanych grup zasobów i/lub subskrypcji dla klientów, którzy kupili ten plan. Należy pamiętać, że każda dołączona grupa (lub użytkownik lub usługa) będzie mieć takie same uprawnienia dla każdego klienta, który kupuje plan. Aby przypisać różne grupy do pracy z każdym klientem, należy opublikować oddzielny [Plan prywatny](../../marketplace/private-offers.md) , który jest wyłączny dla każdego klienta.

## <a name="create-your-offer"></a>Tworzenie oferty

1. Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).
2. W menu nawigacji po lewej stronie wybierz pozycję **Nowa oferta**, a następnie wybierz pozycję **zarządzane usługi**.
3. Zostanie wyświetlona sekcja **edytora** oferty z czterema częściami do wypełnienia: **Ustawienia oferty**, **plany**, **Marketplace**i **Pomoc techniczna**. Zapoznaj się ze wskazówkami dotyczącymi sposobu wykonywania tych sekcji.

### <a name="enter-offer-settings"></a>Wprowadź ustawienia oferty

W sekcji **Ustawienia oferty** podaj następujące elementy:

|Pole  |Opis  |
|---------|---------|
|**Identyfikator oferty**     | Unikatowy identyfikator oferty (w ramach profilu wydawcy). Ten identyfikator może zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia, maksymalnie 50 znaków. Należy pamiętać, że identyfikator oferty może być widoczny dla klientów w miejscach takich jak w przypadku adresów URL produktów i raportów dotyczących rozliczeń. Po opublikowaniu oferty nie można zmienić tej wartości.        |
|**IDENTYFIKATOR wydawcy**     | IDENTYFIKATOR wydawcy, który zostanie skojarzony z ofertą. Jeśli masz więcej niż jeden identyfikator wydawcy, możesz wybrać ten, którego chcesz użyć dla tej oferty.       |
|**Nazwa**     | Nazwa (do 50 znaków), którą klienci będą widzieć dla oferty w portalu Azure Marketplace i w Azure Portal. Użyj rozpoznawalnej nazwy marki, która będzie rozpoznawana przez klientów — w przypadku promowania tej oferty za pomocą własnej witryny sieci Web upewnij się, że w tym miejscu użyto dokładnie tej samej nazwy.        |

Po zakończeniu wybierz pozycję **Zapisz**. Teraz możesz przejść do sekcji **plany** .

### <a name="create-plans"></a>Utwórz plany

Każda oferta musi mieć co najmniej jeden plan (czasami określany jako jednostki SKU). Możesz dodać wiele planów, aby obsługiwać różne zestawy funkcji w różnych cenach lub dostosować konkretny plan dla ograniczonej liczby odbiorców określonych klientów. Klienci mogą przeglądać plany, które są dostępne dla nich w ramach oferty nadrzędnej.

W sekcji plany wybierz pozycję **nowy plan**. Następnie wprowadź **identyfikator planu**. Ten identyfikator może zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia, maksymalnie 50 znaków. Identyfikator planu może być widoczny dla klientów w miejscach takich jak w przypadku adresów URL produktów i raportów dotyczących rozliczeń. Po opublikowaniu oferty nie można zmienić tej wartości.

#### <a name="plan-details"></a>Szczegóły planu

Wykonaj następujące sekcje w sekcji **szczegóły planu** :

|Pole  |Opis  |
|---------|---------|
|**Tytuły**     | Przyjazna nazwa planu do wyświetlenia. Maksymalna długość 50 znaków.        |
|**Podsumowanie**     | Zwięzły opis planu, który ma być wyświetlany pod tytułem. Maksymalna długość 100 znaków.        |
|**Opis**     | Tekst opisu, który zawiera bardziej szczegółowy opis planu.         |
|**Model rozliczania**     | W tym miejscu są wyświetlane 2 modele rozliczeń, ale należy wybrać **Bring Your Own License** dla ofert usług zarządzanych. Oznacza to, że klienci będą rozliczani bezpośrednio za koszty związane z tą ofertą, a firma Microsoft nie nalicza opłat za Ciebie.   |
|**Czy jest to plan prywatny?**     | Wskazuje, czy jednostka SKU jest prywatna, czy publiczna. Wartość domyślna to **no** (Public). Jeśli opuścisz tę opcję, Twój plan nie zostanie ograniczony do określonych klientów (lub do określonej liczby klientów); Po opublikowaniu planu publicznego nie można go później zmienić do prywatnego. Aby ten plan był dostępny tylko dla określonych klientów, wybierz opcję **tak**. W takim przypadku należy zidentyfikować klientów, podając ich identyfikatory subskrypcji. Można je wprowadzić jeden po jednym (dla maksymalnie 10 subskrypcji) lub przez przekazanie pliku CSV (dla maksymalnie 20 000 subskrypcji). Pamiętaj o dołączeniu własnych subskrypcji tutaj, aby móc testować i sprawdzać poprawność oferty. Aby uzyskać więcej informacji, zobacz [prywatne jednostki SKU i plany](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  |

> [!IMPORTANT]
> Po opublikowaniu planu jako publicznego nie można go zmienić na prywatny. Aby kontrolować, którzy klienci mogą akceptować ofertę i delegować zasoby, należy użyć planu prywatnego. W przypadku planu publicznego nie można ograniczyć dostępności do określonych klientów, a nawet do określonej liczby klientów (mimo że można już zrezygnować z sprzedaży planu w przypadku wybrania tej opcji). [Dostęp do delegowania można usunąć](remove-delegation.md) , gdy klient zaakceptuje ofertę tylko wtedy, gdy została uwzględniona **autoryzacja** z **definicją roli** ustawioną na [przypisanie rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , po opublikowaniu oferty. Możesz również skontaktować się z klientem i poproś o [usunięcie dostępu](view-manage-service-providers.md#add-or-remove-service-provider-offers).

#### <a name="manifest-details"></a>Szczegóły manifestu

Wypełnij sekcję **szczegóły manifestu** dla Twojego planu. Spowoduje to utworzenie manifestu z informacjami o autoryzacji do zarządzania zasobami klienta. Te informacje są wymagane w celu włączenia zarządzania zasobami delegowanymi przez platformę Azure.

> [!NOTE]
> Jak wspomniano powyżej, użytkownicy i role w wpisach **autoryzacji** będą stosowane do każdego klienta, który kupuje plan. Jeśli chcesz ograniczyć dostęp do określonego klienta, musisz opublikować plan prywatny do ich wyłącznego użytku.

Najpierw Podaj **wersję** manifestu. Użyj formatu *n. n. n* (na przykład 1.2.5).

Następnie wprowadź swój **Identyfikator dzierżawy**. Jest to identyfikator GUID skojarzony z IDENTYFIKATORem dzierżawy Azure Active Directory organizacji (tj. dzierżawcą, w którym będziesz pracować w celu zarządzania zasobami klientów). Jeśli go nie masz, możesz go znaleźć, umieszczając kursor nad nazwą swojego konta w prawym górnym rogu Azure Portal lub wybierając pozycję **Przełącz katalog**.

Na koniec Dodaj jeden lub więcej wpisów **autoryzacji** do planu. Autoryzacje definiują jednostki, które mogą uzyskiwać dostęp do zasobów i subskrypcji dla klientów, którzy kupują plan, i przypisują role, które udzielają określonych poziomów dostępu.

> [!TIP]
> W większości przypadków użytkownik chce przypisać uprawnienia do grupy użytkowników usługi Azure AD lub nazwy głównej usług, a nie do serii poszczególnych kont użytkowników. Pozwala to na dodawanie lub usuwanie dostępu dla poszczególnych użytkowników bez konieczności aktualizacji i ponownego publikowania planu, gdy zmienią się wymagania dotyczące dostępu. Aby uzyskać dodatkowe zalecenia, zobacz [dzierżawy, role i użytkowników w scenariuszach usługi Azure Lighthouse](../concepts/tenants-users-roles.md).

Dla każdej **autoryzacji**należy podać następujące dane. Następnie można wybrać **nową autoryzację** dowolną liczbę razy, aby dodać więcej użytkowników i definicje ról.

- **Identyfikator obiektu usługi Azure AD**: identyfikator usługi Azure AD użytkownika, grupy użytkowników lub aplikacji, do której zostaną przyznane określone uprawnienia (zgodnie z opisem w definicji roli) do zasobów klientów.
- **Nazwa wyświetlana obiektu usługi Azure AD**: przyjazna nazwa, która pomaga klientowi zrozumieć cel tej autoryzacji. Klient zobaczy tę nazwę podczas delegowania zasobów.
- **Definicja roli**: Wybierz jedną z dostępnych ról wbudowanych usługi Azure AD z listy. Ta rola określi uprawnienia, które użytkownik w polu **Identyfikator obiektu usługi Azure AD** będzie mieć w zasobach klientów. Opisy tych ról można znaleźć w temacie [wbudowane role](../../role-based-access-control/built-in-roles.md) i [Obsługa ról dla delegowanego zarządzania zasobami platformy Azure](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management).
  > [!NOTE]
  > Nowe wbudowane role są dodawane do platformy Azure, ale staną się dostępne w tym miejscu, chociaż może wystąpić pewne opóźnienie przed ich wyświetleniem.
- **Role**możliwe do przypisania: jest to wymagane tylko wtedy, gdy w **definicji roli** dla tej autoryzacji została wybrana wartość administrator dostępu użytkowników. Jeśli tak, należy dodać co najmniej jedną przypisaną rolę w tym miejscu. Użytkownik w polu **Identyfikator obiektu usługi Azure AD** będzie mógł przypisać te role możliwe do **przypisania** do [tożsamości zarządzanych](../../active-directory/managed-identities-azure-resources/overview.md), które są wymagane w celu [wdrożenia zasad, które można skorygować](deploy-policy-remediation.md). Należy pamiętać, że żadne inne uprawnienia zwykle skojarzone z rolą administratora dostępu użytkowników będą stosowane dla tego użytkownika. Jeśli nie wybierzesz w tym miejscu co najmniej jednej roli, przesyłanie nie zostanie przekazane do certyfikacji. (Jeśli nie wybrano administratora dostępu użytkowników dla definicji roli tego użytkownika, to pole nie ma żadnego efektu).

> [!TIP]
> Aby mieć pewność, że w razie potrzeby będzie można [usunąć dostęp do delegowania](remove-delegation.md) , Uwzględnij **autoryzację** z **definicją roli** ustawioną na [przydział rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.

Po zakończeniu tych informacji możesz wybrać pozycję **nowy plan** dowolną liczbę razy, aby utworzyć dodatkowe plany. Gdy skończysz, wybierz pozycję **Zapisz**, a następnie przejdź do sekcji **Marketplace** .

### <a name="provide-marketplace-text-and-images"></a>Podaj tekst i obrazy z witryny Marketplace

W sekcji **Marketplace** znajduje się tekst i obrazy, które klienci zobaczą w witrynie Azure Marketplace i Azure Portal.

Wykonaj następujące pola w sekcji **Przegląd** :

|Pole  |Opis  |
|---------|---------|
|**Tytuły**     |  Tytuł oferty, często długi, formalna nazwa. Ten tytuł zostanie wyświetlony w widocznym miejscu w portalu Marketplace. Maksymalna długość 50 znaków. W większości przypadków powinna być taka sama jak **Nazwa** wprowadzona w sekcji **Ustawienia oferty** .       |
|**Podsumowanie**     | Krótki cel lub funkcja oferty. Jest to zwykle wyświetlane pod tytułem. Maksymalna długość 100 znaków.        |
|**Długie podsumowanie**     | Dłuższe podsumowanie przeznaczenia lub funkcji oferty. Maksymalna długość 256 znaków.        |
|**Opis**     | Więcej informacji o ofercie. To pole ma maksymalną długość 3000 znaków i obsługuje proste formatowanie HTML. W opisie należy uwzględnić słowa "usługa zarządzana" lub "zarządzane usługi".       |
|**Identyfikator marketingu**     | Unikatowy identyfikator przyjazny dla adresu URL. Ten identyfikator może zawierać tylko małe znaki alfanumeryczne i łączniki. Zostanie ona użyta w adresach URL witryny Marketplace dla tej oferty. Na przykład jeśli identyfikator wydawcy to *contoso* , a Twój identyfikator marketingu to *sampleApp*, adres URL oferty w witrynie Azure Marketplace zostanie wyszukany `https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp`.       |
|**Podgląd identyfikatorów subskrypcji**     | Dodaj jeden do 100 identyfikatorów subskrypcji. Klienci powiązani z tymi subskrypcjami będą mogli wyświetlać ofertę w portalu Azure Marketplace przed rozpoczęciem jej używania. Zalecamy dołączenie własnych subskrypcji tutaj, aby można było wyświetlić podgląd oferty w portalu Azure Marketplace przed udostępnieniem jej klientom.  (Zespoły pomocy technicznej i inżynierów firmy Microsoft będą mogli wyświetlać swoją ofertę w trakcie okresu zapoznawczego).   |
|**Przydatne linki**     | Adresy URL powiązane z ofertą, takie jak dokumentacja, informacje o wersji, często zadawane pytania itp.        |
|**Sugerowane kategorie (maks. 5)**     | Co najmniej jedna kategoria (do pięciu), która ma zastosowanie do oferty. Te kategorie pomagają klientom w znalezieniu oferty w witrynie Azure Marketplace i Azure Portal.        |

W sekcji **artefakty marketingowe** można przekazać logo i inne zasoby, które mają być wyświetlane wraz z ofertą. Możesz opcjonalnie przekazać zrzuty ekranu lub linki do filmów wideo, które mogą pomóc klientom w zrozumieniu oferty.

Wymagane są cztery rozmiary logo: **małe (40x40)**, **średnie (90x90)**, **duże (115x115)** i **szerokie (255x115)**. Postępuj zgodnie z poniższymi wskazówkami dotyczącymi logo:

- Projekt platformy Azure ma prostą paletę kolorów. Ogranicz liczbę podstawowych i pomocniczych kolorów w logo.
- Kolory motywu portalu to biały i czarny. Nie używaj tych kolorów jako kolorów tła swojego logo. Użyj koloru, który sprawi, że logo będzie się wyróżniać w portalu. Zalecamy proste kolory podstawowe.
- Jeśli używasz przezroczystego tła, upewnij się, że logo i tekst nie są białe, czarne ani niebieskie.
- Logo powinno być proste i bez gradientów. Nie używaj w logo tła gradientowego.
- Nie umieszczaj tekstu w logo, nawet nazwy firmy ani marki.
- Upewnij się, że logo nie jest rozciągnięte.

Logo **Hero (815x290)** jest opcjonalne, ale zalecane. Jeśli dołączysz logo Hero, postępuj zgodnie z następującymi wskazówkami:

- Nie dołączaj żadnego tekstu do logo Hero i pamiętaj o pozostawieniu 415 pikseli pustego miejsca po prawej stronie logo. Jest to wymagane, aby pozostawiać miejsce na elementy tekstowe, które będą osadzone programowo: Nazwa wyświetlana wydawcy, tytuł planu, długi podsumowanie oferty.
- Tło logo Hero może nie być czarne, białe ani przezroczyste. Upewnij się, że kolor tła nie jest zbyt jasny, ponieważ tekst osadzony będzie wyświetlany jako biały.
- Po opublikowaniu oferty przy użyciu ikony Hero nie można jej usunąć (mimo że można ją zaktualizować przy użyciu innej wersji, jeśli jest to konieczne).

W sekcji **Zarządzanie potencjalnymi klientami** można wybrać system CRM, w którym będą przechowywane potencjalni klienci. Należy pamiętać, że zgodnie z [zasadami certyfikacji usług zarządzanych](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)wymagany jest **obiekt docelowy potencjalnego klienta** .

Na koniec Podaj **adres URL zasad ochrony prywatności** i **warunki użytkowania** w sekcji informacje **prawne** . Możesz również określić, czy ma być używany [Standardowy kontrakt](../../marketplace/standard-contract.md) dla tej oferty.

Pamiętaj, aby zapisać zmiany przed przejściem do sekcji **Pomoc techniczna** .

### <a name="add-support-info"></a>Dodaj informacje o pomocy technicznej

W sekcji **Pomoc techniczna** Podaj nazwę, adres e-mail i numer telefonu dla kontaktu inżynierowego i skontaktuj się z działem pomocy technicznej klienta. Należy również podać adresy URL pomocy technicznej. Firma Microsoft może używać tych informacji w razie konieczności skontaktowania się z Tobą w sprawie problemów z firmą i pomocą techniczną.

Po dodaniu tych informacji wybierz pozycję **Zapisz.**

## <a name="publish-your-offer"></a>Publikowanie oferty

Po zakończeniu wszystkich sekcji następnym krokiem jest opublikowanie oferty w portalu Azure Marketplace. Wybierz przycisk **Publikuj** , aby zainicjować proces tworzenia oferty na żywo. Aby uzyskać więcej informacji o tym procesie, zobacz temat [Publikowanie ofert Azure Marketplace i AppSource](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md).

[Zaktualizowaną wersję oferty można opublikować](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md) w dowolnym momencie. Na przykład możesz chcieć dodać nową definicję roli do wcześniej opublikowanej oferty. Po wykonaniu tej czynności klienci, którzy już dodaliśmy do oferty, będą widzieć ikonę na stronie [**dostawcy usług**](view-manage-service-providers.md) w Azure Portal, która pozwala im znać aktualizację. Każdy klient będzie mógł [przejrzeć zmiany](view-manage-service-providers.md#update-service-provider-offers) i zdecydować, czy chcą one zaktualizować do nowej wersji. 

## <a name="the-customer-onboarding-process"></a>Proces dołączania klienta

Po dodaniu oferty przez klienta będzie można [delegować co najmniej jedną określoną subskrypcję lub grupę zasobów](view-manage-service-providers.md#delegate-resources), która następnie zostanie dołączona do zarządzania zasobami delegowanymi przez platformę Azure. Jeśli klient zaakceptuje ofertę, ale nie oddelegowano jeszcze żadnych zasobów, w górnej części **strony dostawcy** [**usług**](view-manage-service-providers.md) w Azure Portal zostanie wyświetlona Uwaga.

> [!IMPORTANT]
> Delegowanie musi odbywać się przez konto niebędące Gośćmi w dzierżawie klienta, które ma [wbudowaną rolę właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) dla dołączanej subskrypcji (lub która zawiera grupy zasobów, które są dołączane). Aby wyświetlić wszystkich użytkowników, którzy mogą delegować subskrypcję, użytkownik w dzierżawie może wybrać subskrypcję w Azure Portal, otworzyć funkcję **Kontrola dostępu (IAM)** i [wyświetlić wszystkich użytkowników z rolą właściciela](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Gdy klient deleguje subskrypcję (lub co najmniej jedną grupę zasobów w ramach subskrypcji), dostawca zasobów **Microsoft. ManagedServices** zostanie zarejestrowany dla tej subskrypcji, a użytkownicy w Twojej dzierżawie będą mogli uzyskiwać dostęp do delegowanych zasobów zgodnie z autoryzacjami w ofercie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [komercyjnym rynku](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- [Wyświetlaj klientów i zarządzaj nimi](view-manage-customers.md) , przechodząc do **moich klientów** w Azure Portal.
