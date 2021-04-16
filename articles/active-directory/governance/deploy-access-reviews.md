---
title: Planowanie wdrożenia Azure Active Directory przeglądów dostępu
description: Przewodnik planowania pomyślnego wdrożenia przeglądów dostępu
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af783d7ff8be36c63af871ab4f2d214ca9f9405
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532593"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Planowanie Azure Active Directory przeglądów dostępu

[Azure Active Directory (Azure AD) Access Reviews (Przeglądy dostępu w](access-reviews-overview.md) usłudze Azure AD) pomagają organizacji zapewnić większe bezpieczeństwo sieci dzięki zarządzaniu [cyklem życia dostępu do zasobów.](identity-governance-overview.md) Przeglądy dostępu:

* Zaplanuj regularne przeglądy lub przeprowadzaj przeglądy ad hoc, aby zobaczyć, kto ma dostęp do określonych zasobów, takich jak aplikacje i grupy

* Śledzenie przeglądów w celu analizy, zgodności lub przyczyn dotyczących zasad

* Delegowanie przeglądów do określonych administratorów, właścicieli firm lub użytkowników końcowych, którzy mogą samodzielnie potwierdzać potrzebę dalszego dostępu

* Skorzystaj ze szczegółowych informacji, aby efektywnie określić, czy użytkownicy powinni nadal mieć dostęp

* Automatyzowanie wyników przeglądu, takich jak usuwanie dostępu użytkowników do zasobów

  ![Diagram przedstawiający przepływ przeglądów dostępu.](./media/deploy-access-review/1-planning-review.png)

Przeglądy dostępu to [Azure AD Identity Governance](identity-governance-overview.md) możliwości. Inne możliwości to [zarządzanie uprawnieniami,](entitlement-management-overview.md) [Privileged Identity Management](../privileged-identity-management/pim-configure.md) i [warunki użytkowania.](../conditional-access/terms-of-use.md) Razem pomagają organizacjom rozwiązać te cztery pytania:

* Którzy użytkownicy powinni mieć dostęp do jakich zasobów?

* Co ci użytkownicy robią z tym dostępem?

* Czy istnieje efektywna kontrola organizacyjna nad zarządzaniem dostępem?

* Czy audytorzy mogą sprawdzić, czy kontrolki działają?

Planowanie wdrożenia przeglądów dostępu jest niezbędne, aby upewnić się, że można osiągnąć żądaną strategię ładu dla użytkowników w organizacji.

### <a name="key-benefits"></a>Najważniejsze korzyści

Najważniejsze zalety włączania przeglądów dostępu to:

* **Kontroluj współpracę.** Przeglądy dostępu umożliwiają organizacjom zarządzanie dostępem do wszystkich zasobów potrzebnych użytkownikom. Gdy użytkownicy dzielą się informacjami i współpracują, organizacje mogą mieć pewność, że informacje są tylko wśród autoryzowanych użytkowników.

* **Zarządzanie ryzykiem.** Przeglądy dostępu zapewniają organizacjom sposób przeglądania dostępu do danych i aplikacji, co zmniejsza ryzyko wycieku danych i wycieku danych. Obejmuje to możliwości regularnego przeglądania dostępu partnerów zewnętrznych do zasobów firmy. 

* **Adresowanie zgodności i ładu.** Dzięki przeglądom dostępu można zarządzać cyklem życia dostępu do grup, aplikacji i witryn oraz ponownie je certyfikowania. Możesz kontrolować śledzenie przeglądów zgodności lub aplikacji wrażliwych na ryzyko specyficznych dla Twojej organizacji. 

* **Zmniejsz koszt.** Przeglądy dostępu są wbudowane w chmurze i natywnie współpracuje z zasobami w chmurze, takimi jak grupy, aplikacje i pakiety dostępu. Korzystanie z przeglądów dostępu jest mniej kosztowne niż tworzenie własnych narzędzi lub inne uaktualnianie lokalnego zestawu narzędzi.

### <a name="training-resources"></a>Zasoby szkoleniowe

Następujące filmy wideo mogą być przydatne podczas poznania przeglądów dostępu:

* [Co to są przeglądy dostępu w usłudze Azure AD?](https://youtu.be/kDRjQQ22Wkk)

* [Jak tworzyć przeglądy dostępu w usłudze Azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Jak włączyć przeglądy dostępu w usłudze Azure AD](https://youtu.be/X1SL2uubx9M)

* [Jak przejrzeć dostęp przy użyciu Mój dostęp](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licencje

Potrzebujesz ważnej licencji Azure AD — wersja Premium (P2) dla każdej osoby, innej niż administratorzy globalni lub administratorzy użytkowników, którzy będą tworzyć lub wykonywać przeglądy dostępu. Aby uzyskać więcej informacji, zobacz [Wymagania licencyjne przeglądów dostępu.](access-reviews-overview.md)

Mogą być również potrzebne inne funkcje zarządzania tożsamościami, takie jak [Zarządzanie cyklem życia uprawnień](entitlement-management-overview.md) lub Privileged Identity Management. W takim przypadku mogą być również potrzebne powiązane licencje. Aby uzyskać więcej informacji, zobacz [Azure Active Directory cennika](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="plan-the-access-reviews-deployment-project"></a>Planowanie projektu wdrażania przeglądów dostępu

Należy wziąć pod uwagę potrzeby organizacji, aby określić strategię wdrażania przeglądów dostępu w środowisku.

### <a name="engage-the-right-stakeholders"></a>Angażowanie odpowiednich uczestników projektu

Gdy projekty technologiczne nie powiodą się, zwykle robią to z powodu niedopasowanych oczekiwań w zakresie wpływu, wyników i obowiązków. Aby uniknąć tych pułapek, [upewnij się,](../fundamentals/active-directory-deployment-plans.md) że angażujesz odpowiednich uczestników projektu i że role projektu są jasne.

W przypadku przeglądów dostępu prawdopodobnie uwzględnisz przedstawicieli następujących zespołów w organizacji:

* **Administracja IT** zarządza infrastrukturą IT i administruje inwestycjami w chmurę oraz aplikacjami Typu oprogramowanie jako usługa (SaaS). Ten zespół:

   * Przejrzyj uprzywilejowany dostęp do infrastruktury i aplikacji, w tym do Microsoft 365 i usługi Azure AD.

   * Zaplanuj i uruchom przeglądy dostępu dla grup, które są używane do obsługi list wyjątków lub projektów pilotażowych IT, aby zachować aktualne listy dostępu.

   * Upewnij się, że dostęp programowy (skryptowy) do zasobów za pośrednictwem jednostki usługi podlega i podlega przeglądowi.

* **Zespoły programowe** kompilowały i konserwowały aplikacje dla twojej organizacji. Ten zespół:

   * Kontroluj, kto może uzyskać dostęp do składników SaaS, PaaS i IaaS, które składają się na opracowane rozwiązania, i zarządzaj nimi.

   * Zarządzanie grupami, które mogą uzyskać dostęp do aplikacji i narzędzi do tworzenia aplikacji wewnętrznych.

   * Wymagaj uprzywilejowanych tożsamości, które mają dostęp do produkcyjnego oprogramowania lub rozwiązań hostowanych dla klientów

* **Jednostki biznesowe zarządzają** projektami i własnymi aplikacjami. Ten zespół: 

   * Przejrzyj i zatwierdź lub odmów dostępu do grup i aplikacji dla użytkowników wewnętrznych i zewnętrznych.

   * Planowanie i przeprowadzanie przeglądów w celu poświadczania dalszego dostępu dla pracowników i tożsamości zewnętrznych, takich jak partnerzy biznesowi.

* **Ład korporacyjny** zapewnia, że organizacja przestrzega wewnętrznych zasad i jest zgodna z przepisami. Ten zespół:

   * Zażądaj lub zaplanuj nowe przeglądy dostępu.

   * Ocenianie procesów i procedur w celu przeglądania dostępu, w tym dokumentacji i przechowywania rekordów w celu zachowania zgodności.

   * Przejrzyj wyniki wcześniejszych przeglądów dla najbardziej krytycznych zasobów.

> [!NOTE]
> W przypadku przeglądów wymagających ręcznej oceny należy zaplanować odpowiednie recenzentów i przejrzeć cykle spełniające wymagania dotyczące zasad i zgodności. Jeśli cykle przeglądu są zbyt częste lub jest zbyt mało recenzentów, jakość może zostać utracona, a zbyt wiele lub zbyt mało osób może mieć dostęp. 

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja ma kluczowe znaczenie dla sukcesu każdego nowego procesu biznesowego. Proaktywne komunikowanie użytkownikom, jak i kiedy ich środowisko zmieni się, oraz jak uzyskać pomoc techniczną, jeśli wystąpią problemy. 

#### <a name="communicate-changes-in-accountability"></a>Przekaż zmiany w odpowiedzialności

Przeglądy dostępu obsługują zmianę odpowiedzialności za przeglądanie i działanie w zakresie dalszego dostępu do właścicieli firm. Oddzielenie decyzji dotyczących dostępu od it prowadzi do bardziej precyzyjnych decyzji dotyczących dostępu. Jest to zmiana kulturowa w zakresie odpowiedzialności i odpowiedzialności właściciela zasobu. Proaktywne komunikowanie tej zmiany i zapewnienie, że właściciele zasobów są przeszkoleni i mogą korzystać ze szczegółowych informacji w celu podejmowania dobrych decyzji.

Oczywiście it będzie chciał zachować kontrolę nad wszystkimi decyzjami dotyczącymi dostępu związanych z infrastrukturą i przypisaniami ról uprzywilejowanych. 

#### <a name="customize-email-communication"></a>Dostosowywanie komunikacji za pośrednictwem poczty e-mail

Podczas planowania przeglądu należy wyznaczyć użytkowników, którzy będą go wykonywać. Ci recenzentzy otrzymują następnie wiadomość e-mail z powiadomieniem o nowych przypisanych do nich recenzjach, a także przypomnienia przed wygaśnięciem przypisanej do nich recenzji.

Administratorzy mogą wysłać to powiadomienie w połowie okresu przed wygaśnięciem przeglądu lub dzień przed jego wygaśnięciem. 

Wiadomość e-mail wysłana do recenzentów może zostać dostosowana tak, aby zawierała niestandardową krótką wiadomość, która zachęca ich do działania w sprawie przeglądu. Zalecamy użycie dodatkowego tekstu w celu:

* Dołącz osobistą wiadomość do recenzentów, aby rozumieli, że jest ona wysyłana przez dział zgodności lub IT.

* Dołącz hiperlink lub odwołanie do wewnętrznych informacji na temat oczekiwań przeglądu oraz dodatkowe materiały referencyjne lub szkoleniowe.

* Dołącz link do instrukcji dotyczących [przeprowadzania samodzielnego przeglądu dostępu.](review-your-access.md) 

  ![Wiadomość e-mail recenzenta](./media/deploy-access-review/2-plan-reviewer-email.png)

Po wybraniu przycisku Rozpocznij przegląd recenzentzy zostaną kierowani do portalu [myAccess](https://myapplications.microsoft.com/) w celu uzyskania przeglądów dostępu do grup i aplikacji. Portal zawiera omówienie wszystkich użytkowników, którzy mają dostęp do przeglądanych zasobów, oraz rekomendacje systemowe na podstawie informacji dotyczących ostatniego logowania i dostępu.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Zachęcamy klientów do początkowego pilotażowego przeglądu dostępu z małą grupą i kierowania do nich zasobów niekrytycznie. Pilotaż może pomóc w dostosowaniu procesów i komunikacji zgodnie z potrzebami oraz zwiększeniu możliwości użytkowników i recenzentów do spełnienia wymagań dotyczących zabezpieczeń i zgodności.

W ramach pilotażu zalecamy:

* Zacznij od przeglądów, w których wyniki nie są automatycznie stosowane, i możesz kontrolować implikacje.

* Upewnij się, że wszyscy użytkownicy mają prawidłowe adresy e-mail wymienione w usłudze Azure AD i otrzymują wiadomość e-mail w celu podjęcia odpowiednich działań. 

* Udokumentuj dostęp usunięty w ramach pilotażu, jeśli trzeba go szybko przywrócić.

* Monitoruj dzienniki inspekcji, aby upewnić się, że wszystkie zdarzenia są prawidłowo monitorowane.

Aby uzyskać więcej informacji, [zobacz najlepsze rozwiązania dotyczące pilotażu.](../fundamentals/active-directory-deployment-plans.md)

## <a name="introduction-to-access-reviews"></a>Wprowadzenie do przeglądów dostępu

Ta sekcja zawiera wprowadzenie do pojęć związanych z przeglądem dostępu, które należy znać przed rozpoczęciem planowania przeglądów.

### <a name="what-resource-types-can-be-reviewed"></a>Jakie typy zasobów można przeglądać?

Po zintegrowaniu zasobów organizacji z usługą Azure AD (takich jak użytkownicy, aplikacje i grupy) można nimi zarządzać i przeglądać. 

Typowe cele przeglądu obejmują:

* [Aplikacje zintegrowane z usługą Azure AD na potrzeby logowania](../manage-apps/what-is-application-management.md) pojedynczego (na przykład SaaS, biznesowe).

* Członkostwo [w grupie](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) (zsynchronizowane z usługą Azure AD lub utworzone w usłudze Azure AD lub Microsoft 365, w tym Microsoft Teams).

* [Uzyskaj dostęp do](./entitlement-management-overview.md) pakietu, który grupuje zasoby (grupy, aplikacje i witryny) w jednym pakiecie w celu zarządzania dostępem.

* [Role usługi Azure AD i role zasobów platformy Azure zdefiniowane](../privileged-identity-management/pim-resource-roles-assign-roles.md) w Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Kto będzie tworzyć przeglądy dostępu i zarządzać nimi?

Rola administracyjna wymagana do tworzenia i odczytywania przeglądu dostępu oraz zarządzania nimi zależy od typu przeglądanych zasobów. W poniższej tabeli przedstawiono role wymagane dla każdego typu zasobu:

| Typ zasobu| Tworzenie przeglądów dostępu (twórców) i zarządzanie nimi| Wyniki przeglądu dostępu do odczytu |
| - | - | -|
| Grupa lub aplikacja| Administrator globalny <p>Administrator użytkowników| Twórcy i administrator zabezpieczeń |
| Role uprzywilejowane w usłudze Azure AD| Administrator globalny <p>Administrator ról uprzywilejowanych| twórcy <p>Czytelnik zabezpieczeń<p>Administrator zabezpieczeń |
| Role uprzywilejowane na platformie Azure (zasoby)| Administrator globalny<p>Administrator użytkowników<p>Właściciel zasobu| twórcy |
| Pakiet dostępu| Administrator globalny<p>Twórca pakietu dostępu| Tylko administrator globalny |


Aby uzyskać więcej informacji, zobacz [Uprawnienia roli administratora w Azure Active Directory](../roles/permissions-reference.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Kto będzie przeglądać dostęp do zasobu?

Twórca przeglądu dostępu decyduje o tym, kto w czasie tworzenia wykona przegląd. Tego ustawienia nie można zmienić po zakończeniu przeglądu. Recenzentów reprezentują trzy osoby:

* Właściciele zasobów, którzy są właścicielami biznesowymi zasobu.

* Zestaw indywidualnie wybranych delegatów wybranych przez administratora przeglądów dostępu.

* Użytkownicy końcowi, którzy będą samodzielnie potwierdzać potrzebę dalszego dostępu.

Podczas tworzenia przeglądu dostępu administratorzy mogą wybrać co najmniej jednego recenzenta. Wszyscy recenzentzy mogą rozpocząć i przeprowadzić przegląd, wybierając użytkowników w celu dalszego dostępu do zasobu lub usuwając ich. 

### <a name="components-of-an-access-review"></a>Składniki przeglądu dostępu

Przed zaimplementowaniem przeglądów dostępu należy zaplanować typy przeglądów odpowiednie dla twojej organizacji. W tym celu należy podjąć decyzje biznesowe dotyczące tego, co chcesz przejrzeć, oraz akcji do podjęcia na podstawie tych przeglądów.

Aby utworzyć zasady przeglądu dostępu, musisz mieć następujące informacje.

* Jakie zasoby należy przejrzeć?

* Czyj dostęp jest przeglądany.

* Jak często ma nastąpić przegląd?

* Kto wykona przegląd?

   * W jaki sposób będą oni powiadomieni o weryfikacji?

   * Jakie osie czasu mają być wymuszane do przeglądu?

* Jakie akcje automatyczne powinny być wymuszane na podstawie przeglądu?

   * Co się stanie, jeśli recenzent nie odpowie na czas?

* Jakie działania ręczne zostaną podjęte w wyniku przeglądu?

* Jaka komunikacja powinna być wysyłana na podstawie podjętych akcji?


**Przykładowy plan przeglądu dostępu**

| Składnik| Wartość |
| - | - |
| **Zasoby do przejrzenia**| Dostęp do usługi Microsoft Dynamics |
| **Częstotliwość przeglądu**| Co miesiąc |
| **Kto wykonuje przegląd**| Menedżerowie programu grupy biznesowej Dynamics |
| **Powiadomienie**| Wyślij wiadomość e-mail na 24 godziny przed sprawdzeniem aliasu Dynamics-Pms<p>Dołącz zachętę do niestandardowych wiadomości do recenzentów w celu zabezpieczenia swoich wpisów |
| **Oś czasu**| 48 godzin od powiadomienia |
|**Akcje automatyczne**| Usuń dostęp z dowolnego konta, które nie ma logowania interakcyjnego w ciągu 90 dni, usuwając użytkownika z grupy zabezpieczeń dynamics-access. <p>*Wykonaj akcje, jeśli nie są przeglądane na osi czasu.* |
| **Akcje ręczne**| W razie potrzeby recenzentzy mogą wykonać zatwierdzenie usunięcia przed wykonaniem akcji zautomatyzowanej. |
| **Wiadomości**| Wyślij do użytkowników wewnętrznych (członków), którzy zostali usunięci, wiadomość e-mail z wyjaśnieniem, że zostali usunięci i jak odzyskać dostęp. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Automatyzowanie akcji na podstawie przeglądów dostępu

Usuwanie dostępu można zautomatyzować, ustawiając opcję Automatycznie zastosuj wyniki do zasobu na pozycję Włącz.

  ![Planowanie przeglądów dostępu](./media/deploy-access-review/3-automate-actions-settings.png)

Po ukończeniu i zakończeniu przeglądu użytkownicy, którzy nie zostali zatwierdzeni przez recenzenta, zostaną automatycznie usunięci z zasobu lub będą nadal mieć dostęp. Może to oznaczać usunięcie członkostwa w grupach, przypisania aplikacji lub odwołanie prawa do podnieść uprawnienia do roli uprzywilejowanej.

Rekomendacje

Zalecenia są wyświetlane recenzentom w ramach obsługi recenzentów i wskazują ostatnie logowanie danej osoby do dzierżawy lub ostatni dostęp do aplikacji. Te informacje pomagają recenzentom w podejmowaniu właściwej decyzji dotyczącej dostępu. Wybranie opcji Take recommendations (Wykonaj rekomendacje) będzie zgodne z zaleceniami przeglądu dostępu. Po zakończeniu przeglądu dostępu system zastosuje te zalecenia automatycznie do użytkowników, na które recenzentzy nie odpowiedzieli.

Rekomendacje są oparte na kryteriach przeglądu dostępu. Jeśli na przykład skonfigurujesz przegląd w celu usunięcia dostępu bez logowania interakcyjnego przez 90 dni, zaleca się usunięcie wszystkich użytkowników, którzy spełniają te kryteria. Firma Microsoft nieustannie pracuje nad udoskonalaniem rekomendacji. 

### <a name="review-guest-user-access"></a>Przeglądanie dostępu użytkowników-gości

Użyj przeglądów dostępu, aby przeglądać i czyścić tożsamości partnerów współpracy z organizacji zewnętrznych. Konfiguracja przeglądu dla per-partner może spełniać wymagania dotyczące zgodności.

Tożsamościom zewnętrznym można przyznać dostęp do zasobów firmy za pomocą jednej z następujących akcji:

* Dodano do grupy 

* Zaproszenie do zespołu 

* Przypisane do aplikacji przedsiębiorstwa lub pakietu dostępu

* Przypisano rolę uprzywilejowana w usłudze Azure AD lub w subskrypcji platformy Azure

Zobacz [przykładowy skrypt](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Skrypt pokaże, gdzie są używane tożsamości zewnętrzne zaproszone do dzierżawy. W usłudze Azure AD można zobaczyć członkostwo w grupach użytkowników zewnętrznych, przypisania ról i przypisania aplikacji. Skrypt nie wyświetla żadnych przypisań poza usługą Azure AD, na przykład bezpośredniego przypisywania praw do zasobów programu Sharepoint bez użycia grup.

Podczas tworzenia przeglądu dostępu dla grup lub aplikacji można pozwolić recenzentowi skoncentrować się na wszystkich z dostępem lub tylko na użytkownikach-gościach. Wybranie tylko użytkowników-gości umożliwia recenzentom uzyskanie ukierunkowanej listy tożsamości zewnętrznych z usługi Azure AD B2B, które mają dostęp do zasobu.

 ![Przeglądanie użytkowników-gości](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> NIE będzie to dotyczyć zewnętrznych członków, którzy mają userType członka. Nie będzie to również obejmować użytkowników zapraszanych poza współpracę B2B w usłudze Azure AD, na przykład tych, którzy mają dostęp do zawartości udostępnionej bezpośrednio za pośrednictwem programu SharePoint.

## <a name="plan-access-reviews-for-access-packages"></a>Planowanie przeglądów dostępu dla pakietów dostępu

[Pakiety dostępu mogą](entitlement-management-overview.md) znacznie uprościć ład i strategię przeglądu dostępu. Pakiet dostępu to pakiet wszystkich zasobów z dostępem, który użytkownik musi mieć do pracy nad projektem lub wykonywania zadania. Na przykład można utworzyć pakiet dostępu, który zawiera wszystkie aplikacje potrzebne deweloperom w organizacji lub wszystkie aplikacje, do których użytkownicy zewnętrzni powinni mieć dostęp. Następnie administrator lub menedżer delegowanych pakietów dostępu grupuje zasoby (grupy lub aplikacje) i role, których użytkownicy potrzebują dla tych zasobów.

Podczas tworzenia pakietu [dostępu](entitlement-management-access-package-create.md)można utworzyć co najmniej jedną zasady dostępu, które ustawiają warunki, dla których użytkownicy mogą żądać pakietu dostępu, jak wygląda proces zatwierdzania i jak często dana osoba będzie musiała ponownie zażądać dostępu. Przeglądy dostępu są konfigurowane podczas tworzenia lub edytowania zasad pakietu dostępu.

Otwórz kartę Cykl życia, aby przewinąć w dół do ekranu Przeglądy dostępu.

 ![Zrzut ekranu przedstawiający pozycję "Edytuj zasady" na karcie "Cykl życia".](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Planowanie przeglądów dostępu dla grup

Oprócz pakietów dostępu przeglądanie członkostwa w grupie jest najskuteczniejszym sposobem regulowania dostępu. Zalecamy, aby dostęp do zasobów był przypisywany za pośrednictwem grup zabezpieczeń lub [grup Microsoft 365](../fundamentals/active-directory-manage-groups.md), a użytkownicy są dodawana do tych grup w celu uzyskania dostępu.

Jednej grupie można przyznać dostęp do wszystkich odpowiednich zasobów. Dostęp grupy można przypisać do poszczególnych zasobów lub do pakietu dostępu, który grupuje aplikacje i inne zasoby. Za pomocą tej metody można przeglądać dostęp do grupy, a nie dostęp osoby do poszczególnych aplikacji. 

Członkostwo w grupie można przeglądać przez: 

* Administratorzy

* Właściciele grup

* Wybrani użytkownicy, delegowana możliwość przeglądu po utworzeniu przeglądu

* Członkowie grupy, atestując się samodzielnie

### <a name="group-ownership"></a>Własność grupy

Zalecamy, aby właściciele grup przeglądali członkostwo, ponieważ najlepiej jest wiedzieć, kto potrzebuje dostępu. Własność grup różni się w zależności od typu grupy:

Grupy, które są tworzone w usługach Microsoft 365 i Azure AD, mają co najmniej jednego dobrze zdefiniowanego właściciela. W większości przypadków właściciele ci są doskonałymi recenzentami dla własnych grup, ponieważ wiedzą, kto powinien mieć dostęp. 

Na przykład usługa Microsoft Teams używa usługi Grupy usługi Microsoft 365 jako podstawowego modelu autoryzacji, aby udzielić użytkownikom dostępu do zasobów, które znajdują się w programie SharePoint, programie Exchange, programie OneNote lub innych Microsoft 365 usługach. Twórca zespołu automatycznie staje się właścicielem i powinien być odpowiedzialny za poświadczanie członkostwa w tej grupie. 

Grupy utworzone ręcznie w portalu usługi Azure AD lub za pomocą skryptów Microsoft Graph nie muszą mieć zdefiniowanych właścicieli. Zalecamy ich zdefiniowanie za pośrednictwem portalu usługi Azure AD w sekcji "Właściciele" grupy lub za pośrednictwem programu Graph.

Grupy synchronizowane z lokalna usługa Active Directory nie mogą mieć właściciela w usłudze Azure AD. Podczas tworzenia przeglądu dostępu należy wybrać osoby, które najlepiej nadają się do decydowania o członkostwie w nich.

> [!NOTE]
> Zalecamy zdefiniowanie zasad biznesowych, które definiują sposób tworzenia grup w celu zapewnienia jasnej własności grup i odpowiedzialności w przypadku regularnego przeglądu członkostwa. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Przeglądanie członkostwa w grupach wykluczeń w zasadach dostępu warunkowego 

Czasami zasady dostępu warunkowego zaprojektowane w celu zapewnienia bezpieczeństwa sieci nie powinny mieć zastosowania do wszystkich użytkowników. Na przykład zasady dostępu warunkowego, które zezwalają użytkownikom tylko na logowanie się w sieci firmowej, mogą nie mieć zastosowania do zespołu sprzedaży, który podróżuje w szerokim zakresie. W takim przypadku członkowie zespołu sprzedażowego zostaną uwzględnieni w grupie, a ta grupa zostanie wykluczona z zasad dostępu warunkowego. 

Regularnie sprawdzaj takie członkostwo w grupie, ponieważ wykluczenie reprezentuje potencjalne ryzyko, jeśli niewłaściwi członkowie zostaną wykluczeni z wymagania.

Przeglądy dostępu [usługi Azure AD mogą być służące do zarządzania użytkownikami wykluczonym z zasad dostępu warunkowego.](conditional-access-exclusion.md)

### <a name="review-external-users-group-memberships"></a>Przeglądanie członkosstwa w grupach użytkowników zewnętrznych

Aby zminimalizować pracę ręczną i związane z nią potencjalne błędy, rozważ użycie grup [dynamicznych](../enterprise-users/groups-create-rule.md) do przypisania członkostwa w grupie na podstawie atrybutów użytkownika. Możesz utworzyć jedną lub więcej grup dynamicznych dla użytkowników zewnętrznych. Sponsor wewnętrzny może działać jako recenzent członkostwa w grupie. 

Uwaga: Użytkownicy zewnętrzni usunięci z grupy w wyniku przeglądu dostępu nie są usuwani z dzierżawy. 

Można je usunąć z dzierżawy ręcznie lub za pomocą skryptu.

### <a name="review-access-to-on-premises-groups"></a>Przeglądanie dostępu do grup lokalnych

Przeglądy dostępu nie mogą zmieniać członkostwa w grupach grup, które są synchronizowane ze środowiskiem lokalnym z [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Jest to spowodowane tym, że źródło urzędu znajduje się w środowisku lokalnym.

Nadal można używać przeglądów dostępu do planowania i utrzymywania regularnych przeglądów grup lokalnych. Recenzentzy będą następnie podjąć działania w grupie lokalnej. Ta strategia zapewnia przeglądy dostępu jako narzędzie do wszystkich przeglądów.

Możesz użyć wyników przeglądu dostępu dla grup lokalnych i przetworzyć je dalej, za pomocą:

* Pobieranie raportu CSV z przeglądu dostępu i ręczne podejmowanie działań.

* Używanie Microsoft Graph programowego uzyskiwania dostępu do wyników i decyzji w ukończonych przeglądach dostępu.

Aby na przykład uzyskać dostęp do wyników dla grupy zarządzanej przez usługę Windows AD, użyj tego [przykładowego skryptu programu PowerShell.](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises) Skrypt zawiera opis wymaganych wywołań programu Graph i eksportuje polecenia AD-PowerShell Windows w celu wykonania zmian.

## <a name="plan-access-reviews-for-applications"></a>Planowanie przeglądów dostępu dla aplikacji 

Przeglądając dostęp do aplikacji, przeglądasz dostęp pracowników i tożsamości zewnętrznych do informacji i danych w aplikacji. Wybierz opcję przeglądania aplikacji, gdy musisz wiedzieć, kto ma dostęp do określonej aplikacji, zamiast pakietu dostępu lub grupy. 

Zalecamy zaplanowanie przeglądów aplikacji w następujących scenariuszach:

* Użytkownicy mają bezpośredni dostęp do aplikacji (spoza grupy lub pakietu dostępu).

* Aplikacja uwidacznia informacje krytyczne lub poufne.

* Aplikacja ma określone wymagania dotyczące zgodności, które należy atestować.

* Podejrzewasz niewłaściwy dostęp.

Aby utworzyć przeglądy dostępu dla aplikacji, ustaw wymagane przypisanie użytkownika? na wartość Tak. Jeśli ustawisz wartość Nie, wszyscy użytkownicy w katalogu, w tym tożsamości zewnętrzne, będą mieć dostęp do aplikacji i nie będzie można przejrzeć dostępu do aplikacji. 

 ![planowanie przypisań aplikacji](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Następnie należy [przypisać użytkowników i grupy,](../manage-apps/assign-user-or-group-access-portal.md) do których chcesz mieć dostęp. 

### <a name="reviewers-for-an-application"></a>Recenzentzy aplikacji

Przeglądy dostępu mogą być przeznaczone dla członków grupy lub użytkowników przypisanych do aplikacji. Aplikacje w usłudze Azure AD nie muszą mieć właściciela, dlatego opcja wyboru właściciela aplikacji jako recenzenta nie jest możliwa. Możesz dodatkowo określić zakres przeglądu, aby przeglądać tylko użytkowników-gości przypisanych do aplikacji, zamiast przeglądać cały dostęp.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Planowanie przeglądu ról zasobów usługi Azure AD i platformy Azure

[Privileged Identity Management (PIM) upraszcza](../privileged-identity-management/pim-configure.md) zarządzanie uprzywilejowanym dostępem do zasobów w usłudze Azure AD przez przedsiębiorstwa. Dzięki temu lista ról uprzywilejowanych, zarówno w usłudze [Azure AD,](../roles/permissions-reference.md) jak i zasobach platformy [Azure,](../../role-based-access-control/built-in-roles.md) jest znacznie mniejsza i zwiększa ogólny poziom zabezpieczeń katalogu.

Przeglądy dostępu umożliwiają recenzentom potwierdzanie, czy użytkownicy nadal muszą być w roli. Podobnie jak przeglądy dostępu dla pakietów dostępu, przeglądy ról usługi Azure AD i zasobów platformy Azure są zintegrowane ze środowiskom użytkownika administratora usługi PIM. Zalecamy regularne przeglądanie następujących przypisań ról:

* Administrator globalny

* Administrator użytkowników

* Administrator uwierzytelniania uprzywilejowanego

* Administrator dostępu warunkowego

* Administrator zabezpieczeń

* Wszystkie Microsoft 365 i administracja usługą Dynamics

Role wybrane w tym miejscu obejmują rolę trwałą i kwalifikującą się. 

W sekcji Recenzentzy wybierz co najmniej jedną osoba, aby przejrzeć wszystkich użytkowników. Możesz też wybrać, aby członkowie przejrzeli swój własny dostęp.

 ![Edycja zasad](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Wdrażanie przeglądów dostępu

Po przygotowaniu strategii i planu przeglądania dostępu do zasobów zintegrowanych z usługą Azure AD należy wdrożyć przeglądy i zarządzać nimi przy użyciu poniższych zasobów.

### <a name="review-access-packages"></a>Przeglądanie pakietów dostępu

Aby zmniejszyć ryzyko nieaktywnego dostępu, administratorzy mogą włączyć okresowe przeglądy użytkowników, którzy mają aktywne przypisania do pakietu dostępu. Postępuj zgodnie z instrukcjami podanymi pod poniższym linkiem:

| Instrukcje| Opis |
| - | - |
| [Tworzenie przeglądów dostępu](entitlement-management-access-reviews-create.md)| Włącz przeglądy pakietu dostępu. |
| [Przeprowadzanie przeglądów dostępu](entitlement-management-access-reviews-review-access.md)| Przeprowadzanie przeglądów dostępu dla innych użytkowników przypisanych do pakietu dostępu. |
| [Samodzielne przeglądanie przypisanych pakietów dostępu](entitlement-management-access-reviews-self-review.md)| Samodzielne przeglądanie przypisanych pakietów dostępu |


> [!NOTE]
> Użytkownicy końcowi, którzy samodzielnie przeglądają zawartość i mówią, że nie potrzebują już dostępu, nie są natychmiast usuwani z pakietu dostępu. Są one usuwane z pakietu dostępu po zakończeniu przeglądu lub po zatrzymaniu przeglądu przez administratora.

### <a name="review-groups-and-apps"></a>Przeglądanie grup i aplikacji

Dostęp do grup i aplikacji dla pracowników i gości prawdopodobnie zmienia się z czasem. Aby zmniejszyć ryzyko związane z nieodświeżonymi przypisaniami dostępu, administratorzy mogą tworzyć przeglądy dostępu dla członków grupy lub dostępu do aplikacji. Postępuj zgodnie z instrukcjami podanymi pod poniższym linkiem:

| Instrukcje| Opis |
| - | - |
| [Tworzenie przeglądów dostępu](create-access-review.md)| Utwórz co najmniej jeden przegląd dostępu dla członków grupy lub dostępu do aplikacji. |
| [Przeprowadzanie przeglądów dostępu](perform-access-review.md)| Przeprowadzanie przeglądu dostępu dla członków grupy lub użytkowników z dostępem do aplikacji. |
| [Samodzielne przeglądanie dostępu](review-your-access.md)| Członkowie przeglądają własny dostęp do grupy lub aplikacji |
| [Ukończenie przeglądu dostępu](complete-access-review.md)| Wyświetlanie przeglądu dostępu i stosowanie wyników |
| [Akcja dla grup lokalnych](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Przykładowy skrypt programu PowerShell do wykonywania przeglądów dostępu dla grup lokalnych. |


### <a name="review-azure-ad-roles"></a>Przeglądanie ról usługi Azure AD

Aby zmniejszyć ryzyko związane z nieodświeżonymi przypisaniami ról, należy regularnie przeglądać dostęp uprzywilejowanych ról usługi Azure AD.

![Zrzut ekranu przedstawiający listę "Przegląd członkostwa" ról usługi Azure A D.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Postępuj zgodnie z instrukcjami pod poniższymi linkami:

| Instrukcje | Opis |
| - | - |
 [Tworzenie przeglądów dostępu](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Tworzenie przeglądów dostępu dla uprzywilejowanych ról usługi Azure AD w usłudze PIM |
| [Samodzielne przeglądanie dostępu](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Jeśli masz przypisaną rolę administracyjną, zatwierdź lub odmów dostępu do swojej roli |
| [Ukończenie przeglądu dostępu](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Wyświetlanie przeglądu dostępu i stosowanie wyników |


### <a name="review-azure-resource-roles"></a>Przeglądanie ról zasobów platformy Azure

Aby zmniejszyć ryzyko związane z nieodświeżonymi przypisaniami ról, należy regularnie przeglądać dostęp do ról uprzywilejowanych zasobów platformy Azure. 

![przeglądanie ról usługi Azure AD](./media/deploy-access-review/9-review-azure-roles-picker.png)

Postępuj zgodnie z instrukcjami pod poniższymi linkami:

| Instrukcje| Opis |
| - | -|
| [Tworzenie przeglądów dostępu](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Tworzenie przeglądów dostępu dla ról uprzywilejowanych zasobów platformy Azure w usłudze PIM |
| [Samodzielne przeglądanie dostępu](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Jeśli masz przypisaną rolę administracyjną, zatwierdź lub odmów dostępu do swojej roli |
| [Ukończenie przeglądu dostępu](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Wyświetlanie przeglądu dostępu i stosowanie wyników |


## <a name="use-the-access-reviews-api"></a>Korzystanie z interfejsu API przeglądów dostępu

Zobacz graph API methods and [role and application permission authorization checks](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) to interact with and manage reviewable resources (Metody [interfejsu API](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) programu Graph oraz sprawdzanie autoryzacji uprawnień ról i aplikacji), aby wchodzić w interakcje z przeglądalnymi zasobami i zarządzać nimi. Metody przeglądów dostępu w interfejsie API Microsoft Graph są dostępne dla kontekstów aplikacji i użytkownika. Podczas uruchamiania skryptów w kontekście aplikacji konto używane do uruchamiania interfejsu API (zasada usługi) musi mieć przyznane uprawnienie "AccessReview.Read.All", aby wykonać zapytanie o informacje dotyczące przeglądów dostępu.

Popularne zadania przeglądów dostępu do automatyzacji przy użyciu interfejs Graph API przeglądów dostępu to:

* Tworzenie i uruchamianie przeglądu dostępu

* Ręczne zakończenie przeglądu dostępu przed zaplanowanym zakończeniem

* Lista wszystkich uruchomionych przeglądów dostępu i ich stanu

* Zapoznaj się z historią serii przeglądów oraz decyzjami i akcjami podejmowanymi w poszczególnych przeglądach.

* Zbieranie decyzji z przeglądu dostępu

* Zbieranie decyzji z ukończonych przeglądów, w przypadku których recenzent podjął inną decyzję niż zalecana przez system.

Podczas tworzenia nowych zapytań interfejs Graph API automatyzacji zalecamy użycie [Eksploratora programu Graph.](https://developer.microsoft.com/en-us/graph/graph-explorer) Zapytania programu Graph można tworzyć i eksplorować przed umieszczeniem ich w skryptach i kodzie. Może to pomóc w szybkiej iterowania zapytania w taki sposób, aby uzyskać dokładnie te wyniki, których szukasz, bez konieczności zmieniania kodu skryptu.

## <a name="monitor-access-reviews"></a>Monitorowanie przeglądów dostępu

Działania przeglądów dostępu są rejestrowane i dostępne w dziennikach inspekcji [usługi Azure AD.](../reports-monitoring/concept-audit-logs.md) Dane inspekcji można filtrować według kategorii, typu działania i zakresu dat. Oto przykładowe zapytanie:

| Kategoria| Zasady |
| - | - |
| Typ działania| Tworzenie przeglądu dostępu |
| | Aktualizowanie przeglądu dostępu |
| | Przegląd dostępu zakończył się |
| | Usuwanie przeglądu dostępu |
| | Zatwierdzanie decyzji |
| | Odmów decyzji |
| | Decyzja o zresetowaniu |
| | Stosowanie decyzji |
| Zakres dat| siedem dni |


Aby uzyskać bardziej zaawansowane zapytania i analizę przeglądów dostępu oraz śledzić zmiany i uzupełnianie przeglądów, zalecamy wyeksportowanie dzienników inspekcji usługi Azure AD do usługi [Azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) lub Azure Event Hubs. W przypadku przechowywania w usłudze Azure Log Analytics można używać zaawansowanego języka analizy [i](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) tworzyć własne pulpity nawigacyjne.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o poniższych powiązanych technologiach.

* [Co to jest zarządzanie uprawnieniami usługi Azure AD?](entitlement-management-overview.md)

* [Co to jest usługa Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)