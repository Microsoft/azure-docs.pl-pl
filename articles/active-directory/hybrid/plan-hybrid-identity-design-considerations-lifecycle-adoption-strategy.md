---
title: Projektowanie tożsamości hybrydowej — strategia wdrażania cyklu życia platformy Azure | Microsoft Docs
description: Pomaga definiować hybrydowe zadania zarządzania tożsamościami zgodnie z opcjami dostępnymi dla każdej fazy cyklu życia.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68406d4b0806e1d533e0bb8669a01939387989
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410661"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Określ strategię wdrażania cyklu życia tożsamości hybrydowej
W tym zadaniu określisz strategię zarządzania tożsamościami dla swojego rozwiązania do obsługi tożsamości hybrydowej, aby spełniała wymagania biznesowe zdefiniowane w temacie [określanie zadań hybrydowego zarządzania tożsamościami](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Aby zdefiniować zadania hybrydowego zarządzania tożsamościami zgodnie z kompleksowym cyklem życia tożsamości przedstawionym wcześniej w tym kroku, należy wziąć pod uwagę opcje dostępne dla każdej fazy cyklu życia.

## <a name="access-management-and-provisioning"></a>Zarządzanie dostępem i Inicjowanie obsługi administracyjnej
Dzięki dobremu rozwiązaniu do zarządzania dostępem do konta organizacja może śledzić dokładne osoby, które mają dostęp do informacji w organizacji.

Kontrola dostępu jest krytyczną funkcją scentralizowanego, Jednopunktowego systemu aprowizacji. Oprócz ochrony poufnych informacji, kontrole dostępu uwidaczniają istniejące konta, które mają niezatwierdzone autoryzacji lub nie są już potrzebne. Aby kontrolować przestarzałe konta, system aprowizacji łączy informacje o koncie z autorytatywnymi informacjami o użytkownikach, którzy są właścicielami kont. Informacje o tożsamości użytkowników autorytatywnych są zwykle przechowywane w bazach danych i katalogach zasobów ludzkich.

Konta w zaawansowanych przedsiębiorstwach IT obejmują setki parametrów, które definiują urzędy, a te szczegóły mogą być kontrolowane przez system aprowizacji. Nowych użytkowników można zidentyfikować przy użyciu danych dostarczanych ze źródła autorytatywnego. Funkcja zatwierdzania żądań dostępu inicjuje procesy zatwierdzające (lub odrzucające) Inicjowanie obsługi zasobów.

| Faza zarządzania cyklem życia | Lokalnie | Chmura | Połączenie hybrydowe |
| --- | --- | --- | --- |
| Zarządzanie kontami i Inicjowanie obsługi administracyjnej |Korzystając z roli serwera usług domenowych Active Directory® (AD DS), można utworzyć skalowalną, bezpieczną i łatwą w obsłudze infrastrukturę do zarządzania użytkownikami i zasobami oraz zapewnić wsparcie aplikacji obsługujących katalogi, takich jak Microsoft® Exchange Server. <br><br> [Grupy można zainicjować przy użyciu programu Identity Manager, AD DS](/previous-versions/mim/ff686261(v=ws.10)) <br>[Użytkowników można udostępnić w AD DS](/previous-versions/mim/ff686263(v=ws.10)) <br><br> Administratorzy mogą używać kontroli dostępu do zarządzania dostępem użytkowników do udostępnionych zasobów ze względów bezpieczeństwa. W Active Directory kontrola dostępu jest administrowana na poziomie obiektu przez ustawienie różnych poziomów dostępu lub uprawnień do obiektów, takich jak Pełna kontrola, zapis, Odczyt lub brak dostępu. Kontrola dostępu w Active Directory definiuje, jak różni użytkownicy mogą używać obiektów Active Directory. Domyślnie uprawnienia do obiektów w Active Directory są ustawiane jako najbezpieczniejsze ustawienie. |Musisz utworzyć konto dla każdego użytkownika, który będzie miał dostęp do usługi w chmurze firmy Microsoft. Możesz również zmienić konta użytkowników lub usunąć je, gdy nie są już potrzebne. Domyślnie użytkownicy nie mają uprawnień administratora, ale można je opcjonalnie przypisać. <br><br> W ramach Azure Active Directory jedną z najważniejszych funkcji jest możliwość zarządzania dostępem do zasobów. Te zasoby mogą być częścią katalogu, jak w przypadku uprawnień do zarządzania obiektami za pośrednictwem ról w katalogu, lub znajdować się poza katalogiem, jak w przypadku aplikacji SaaS, usług platformy Azure, witryn programu SharePoint lub zasobów lokalnych. <br><br> W centrum rozwiązania do zarządzania dostępem w Azure Active Directory jest Grupa zabezpieczeń. Właściciel zasobu (lub administrator katalogu) może przypisać grupę, aby udzielić określonych uprawnień dostępu do jego zasobów. Członkowie grupy będą mieć dostęp, a właściciel zasobu może delegować prawo do zarządzania listą członków grupy dla kogoś innego — takiego jak Menedżer działu lub administrator pomocy technicznej<br> <br> Sekcja zarządzanie grupami w usłudze Azure AD zawiera więcej informacji na temat zarządzania dostępem za pomocą grup. |Poszerzanie tożsamości Active Directory w chmurze za pomocą synchronizacji i Federacji |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Kontrola dostępu oparta na rolach na platformie Azure (RBAC) korzysta z ról i zasad aprowizacji, aby oszacować, przetestować i wymusić procesy biznesowe oraz zasady udzielania dostępu użytkownikom. Administratorzy kluczy mogą tworzyć zasady aprowizacji i przypisywać użytkowników do ról oraz definiować zestawy uprawnień do zasobów dla tych ról. Usługa Azure RBAC rozszerza rozwiązanie do zarządzania tożsamościami w celu korzystania z procesów opartych na oprogramowaniu i zmniejsza ręczną interakcję użytkownika w procesie aprowizacji.
Usługa Azure RBAC pozwala firmie ograniczyć liczbę operacji, które może wykonać użytkownik, gdy mają dostęp do Azure Portal. Za pomocą funkcji RBAC platformy Azure do kontrolowania dostępu do portalu Administratorzy IT są delegowane dostępu za pomocą następujących metod zarządzania dostępem:

* **Przypisywanie ról oparte na grupach** : można przypisać dostęp do grup usługi Azure AD, które można synchronizować z lokalnego Active Directory. Dzięki temu można wykorzystać istniejące inwestycje, które organizacja wprowadziła w narzędzia i procesy do zarządzania grupami. Można również użyć funkcji delegowanej zarządzania grupami programu Azure AD — wersja Premium.
* Korzystanie z **wbudowanych ról na platformie Azure** : możesz użyć trzech ról — właściciela, współautora i czytnika, aby upewnić się, że użytkownicy i grupy mają uprawnienia do wykonywania tylko tych zadań, których potrzebują do wykonywania swoich zadań.
* **Szczegółowy dostęp do zasobów** : można przypisywać role do użytkowników i grup dla określonej subskrypcji, grupy zasobów lub pojedynczego zasobu platformy Azure, takiego jak witryna sieci Web lub baza danych. W ten sposób można zagwarantować, że użytkownicy będą mieli dostęp do wszystkich potrzebnych zasobów i nie mają dostępu do zasobów, które nie muszą być zarządzane.

## <a name="provisioning-and-other-customization-options"></a>Inicjowanie obsługi administracyjnej i inne opcje dostosowywania
Zespół może użyć planów i wymagań biznesowych, aby określić, jak dużo należy dostosować rozwiązanie do tworzenia tożsamości. Na przykład duże przedsiębiorstwo może wymagać planowanego planu zbiorczego dla przepływów pracy i adapterów niestandardowych opartych na wierszu czasu na potrzeby przyrostowych aplikacji, które są szeroko używane w ramach lokalizacje geograficzne. Po pomyślnym przetestowaniu w całej organizacji może zostać zainicjowana obsługa dwóch lub większej liczby aplikacji. Interakcja z aplikacją użytkownika może być dostosowana, a procedury aprowizacji zasobów mogą zostać zmienione w celu uwzględnienia automatycznej aprowizacji.

Można anulować Inicjowanie obsługi administracyjnej usługi lub składnika. Na przykład anulowanie aprowizacji konta oznacza, że konto zostanie usunięte z zasobu.

Model hybrydowy zasobów aprowizacji łączy żądania i podejścia oparte na rolach, które są obsługiwane przez usługę Azure AD. W przypadku podzbioru pracowników lub systemów zarządzanych firma może chcieć zautomatyzować dostęp z przypisaniem opartym na rolach. Firma może również obsługiwać wszystkie inne żądania dostępu lub wyjątki za pośrednictwem modelu opartego na żądaniach. Niektóre firmy mogą zacząć od ręcznego przypisywania i rozwijać modelem hybrydowym z zamiarem pełnego wdrożenia opartego na rolach w przyszłości.

Inne firmy mogą okazać się niepraktyczne dla powodów firmy, aby osiągnąć pełną obsługę administracyjną na podstawie ról, i kierować podejściem hybrydowym w miarę potrzeb. Nadal mogą być spełnione inne firmy z zainicjowaniem obsługi opartej na żądaniach, a nie chcemy zainwestować dodatkowego wysiłku w celu zdefiniowania i zarządzania zautomatyzowanymi zasadami aprowizacji opartymi na rolach.

## <a name="license-management"></a>Zarządzanie licencjami
Zarządzanie licencjami opartymi na grupach w usłudze Azure AD pozwala administratorom przypisywać użytkowników do grupy zabezpieczeń, a usługa Azure AD automatycznie przypisuje licencje wszystkim członkom tej grupy. Jeśli użytkownik zostanie następnie dodany do grupy lub usunięty z niej, licencja zostanie automatycznie przypisana lub usunięta zgodnie z potrzebami.

Można używać grup synchronizowanych z lokalnej usługi AD lub zarządzania nimi w usłudze Azure AD. Parowanie tego programu z usługą Azure AD w wersji Premium Self-Service zarządzanie grupami możesz łatwo delegować przypisanie licencji do odpowiednich decyzji. Możesz mieć pewność, że problemy, takie jak konflikty licencji i brakujące dane lokalizacji, są automatycznie sortowane.

## <a name="self-regulating-user-administration"></a>Samoobsługowe administrowanie użytkownikami
Gdy organizacja rozpocznie udostępnianie zasobów między wszystkimi organizacjami wewnętrznymi, wdrażana jest możliwość samodzielnego zarządzania użytkownikami. Zalety i korzyści płynące z aprowizacji użytkowników w granicach organizacyjnych. W tym środowisku zmiana stanu użytkownika jest automatycznie odzwierciedlana w prawach dostępu między granicami organizacji i lokalizacje geograficzne. Można zmniejszyć koszty aprowizacji i usprawnić procesy dostępu i zatwierdzania. Implementacja ta realizuje pełny potencjał implementacji kontroli dostępu opartej na rolach na potrzeby zarządzania dostępem kompleksowym w organizacji. Możesz zmniejszyć koszty administracyjne za poorednictwem zautomatyzowanych procedur dotyczących aprowizacji użytkowników. Aby zwiększyć bezpieczeństwo, można zautomatyzować wymuszanie zasad zabezpieczeń i usprawnić i scentralizować Zarządzanie cyklem życia użytkowników oraz inicjowanie obsługi zasobów dla dużych populacji użytkowników.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz Konfigurowanie usługi Azure AD do samoobsługowego zarządzania dostępem do aplikacji
> 
> 

Usługi Azure AD oparte na licencji (na podstawie uprawnień) działają przez aktywowanie subskrypcji w katalogu/dzierżawie usługi Azure AD. Gdy subskrypcja jest aktywna, możliwości usługi mogą być zarządzane przez administratorów katalogów/usług i używane przez licencjonowanych użytkowników. 

## <a name="integration-with-other-3rd-party-providers"></a>Integracja z innymi dostawcami innych firm

Azure Active Directory zapewnia dostęp do tysięcy aplikacji SaaS i lokalnych aplikacji sieci Web przy użyciu logowania jednokrotnego i rozszerzonego dostępu do aplikacji. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../develop/quickstart-register-app.md)

## <a name="define-synchronization-management"></a>Zdefiniuj Zarządzanie synchronizacją
Zintegrowanie katalogów lokalnych z usługą Azure AD zwiększa produktywność użytkowników, zapewniając wspólną tożsamość na potrzeby dostępu do zasobów, zarówno lokalnych, jak i w chmurze. Dzięki tej integracji Użytkownicy i organizacje mogą skorzystać z następujących czynności:

* Organizacje mogą zapewnić użytkownikom wspólną tożsamość hybrydową w ramach usług lokalnych lub opartych na chmurze wykorzystujących system Windows Server Active Directory a następnie łącząc się z Azure Active Directory.
* Administratorzy mogą zapewnić dostęp warunkowy na podstawie zasobów aplikacji, tożsamości urządzenia i użytkownika, lokalizacji sieciowej i uwierzytelniania wieloskładnikowego.
* Użytkownicy mogą wykorzystać swoją wspólną tożsamość za pomocą kont w usłudze Azure AD do Microsoft 365, usługi Intune, aplikacji SaaS i aplikacji innych firm.
* Deweloperzy mogą tworzyć aplikacje korzystające ze wspólnego modelu tożsamości, integrując aplikacje do Active Directory lokalnie lub na platformie Azure dla aplikacji opartych na chmurze

Poniższy rysunek zawiera przykład widoku wysokiego poziomu procesu synchronizacji tożsamości.

![Synchronizuj](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Proces Synchronizacja tożsamości

Zapoznaj się z poniższą tabelą, aby porównać opcje synchronizacji:

| Opcja zarządzania synchronizacją | Zalety | Wady |
| --- | --- | --- |
| Oparta na synchronizacji (za pośrednictwem narzędzia DirSync lub AADConnect) |Użytkownicy i grupy synchronizowane z poziomu lokalnego i w chmurze <br>  **Kontrola zasad** : Zasady konta można skonfigurować za pomocą Active Directory, co umożliwia administratorom zarządzanie zasadami haseł, stacjami roboczymi, ograniczeniami, formantami blokady i innymi, bez konieczności wykonywania dodatkowych zadań w chmurze.  <br>  **Kontrola dostępu** : można ograniczyć dostęp do usługi w chmurze, dzięki czemu usługi są dostępne w środowisku firmowym, za pomocą serwerów online lub obu. <br>  Zredukowane wywołania obsługi: Jeśli użytkownicy mają mniej haseł do zapamiętania, mogą one nie być zapomniane. <br>  Zabezpieczenia: tożsamości i informacje o użytkowniku są chronione, ponieważ wszystkie serwery i usługi używane w ramach logowania jednokrotnego są nadzorowane i kontrolowane lokalnie. <br>  Obsługa silnego uwierzytelniania: można użyć silnego uwierzytelniania (nazywanego również uwierzytelnianiem dwuskładnikowym) za pomocą usługi w chmurze. Jeśli jednak używasz silnego uwierzytelniania, musisz użyć rejestracji jednokrotnej. | |
| Oparte na Federacji (za pośrednictwem AD FS) |Włączone przez usługę tokenu zabezpieczającego (STS). W przypadku skonfigurowania usługi STS w celu zapewnienia dostępu przy użyciu logowania jednokrotnego w usłudze firmy Microsoft w chmurze zostanie utworzone zaufanie federacyjne między lokalną usługą STS i domeną federacyjną określoną w dzierżawie usługi Azure AD. <br> Umożliwia użytkownikom końcowym korzystanie z tego samego zestawu poświadczeń w celu uzyskania dostępu do wielu zasobów <br>Użytkownicy końcowi nie muszą obsługiwać wielu zestawów poświadczeń. Użytkownicy muszą jeszcze podać swoje poświadczenia dla każdego z zasobów uczestniczących. obsługiwane są scenariusze B2B i B2C. |Wymaga wyspecjalizowanego personelu do wdrożenia i konserwacji dedykowanych serwerów AD FS lokalnych. Istnieją ograniczenia dotyczące korzystania z silnego uwierzytelniania, jeśli planujesz używać AD FS dla usługi STS. Aby uzyskać więcej informacji, zobacz [Konfigurowanie opcji zaawansowanych dla AD FS 2,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh237448(v=ws.10)). |

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [integrowanie tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)