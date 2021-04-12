---
title: Projekt tożsamości hybrydowej — strategia ochrony danych Azure | Microsoft Docs
description: Należy zdefiniować strategię ochrony danych dla rozwiązania do obsługi tożsamości hybrydowej w celu spełnienia określonych wymagań firmy.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bac3f53def6db1038a6dd7e45d7933daa22df9f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98703856"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definiowanie strategii ochrony danych dla rozwiązania do tworzenia tożsamości hybrydowej
W tym zadaniu zdefiniujemy strategię ochrony danych dla rozwiązania do obsługi tożsamości hybrydowej w celu spełnienia wymagań firmy zdefiniowanych w programie:

* [Określanie wymagań dotyczących ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Określanie wymagań dotyczących zarządzania zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Określanie wymagań dotyczących kontroli dostępu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Określanie wymagań dotyczących odpowiadania na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definiowanie opcji ochrony danych
Zgodnie z opisem w temacie [Określanie wymagań dotyczących synchronizacji katalogów](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)Microsoft Azure AD można synchronizować z lokalnym Active Directory Domain Services (AD DS). Ta Integracja umożliwia organizacjom używanie usługi Azure AD do weryfikowania poświadczeń użytkowników próbujących uzyskać dostęp do zasobów firmy. Można to zrobić w obu scenariuszach: dane przechowywane lokalnie i w chmurze. Dostęp do danych w usłudze Azure AD wymaga uwierzytelnienia użytkownika za pośrednictwem usługi tokenu zabezpieczającego (STS).

Po uwierzytelnieniu, główna nazwa użytkownika (UPN) jest odczytywana z tokenu uwierzytelniania. Następnie system autoryzacji określa zreplikowaną partycję i kontener odpowiadający domenie użytkownika. Informacje dotyczące istnienia, włączonego stanu i roli użytkownika pomagają systemowi autoryzacji ustalić, czy dostęp do dzierżawy docelowej jest autoryzowany dla użytkownika w tej sesji. Niektóre autoryzowane akcje (w tym w celu utworzenia użytkownika i resetowania hasła) tworzą dziennik inspekcji, który następnie używa Administrator dzierżawy do zarządzania wysiłkami lub badaniami zgodności.

Przeniesienie danych z lokalnego centrum dane do usługi Azure Storage za pośrednictwem połączenia internetowego może nie być możliwe z powodu ilości danych, dostępności przepustowości lub innych zagadnień. [Usługa Azure Storage Import/Export](../../import-export/storage-import-export-service.md) oferuje sprzętową opcję umieszczania/pobierania dużych ilości danych w usłudze BLOB Storage. Umożliwia wysyłanie dysków twardych [szyfrowanych funkcją BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn306081(v=ws.11)#BKMK_BL2012R2) bezpośrednio do centrum danych platformy Azure, w których operatory chmury przekazują zawartość do konta magazynu, lub mogą pobrać dane platformy Azure na dyski, aby powrócić do Ciebie. Tylko zaszyfrowane dyski są akceptowane dla tego procesu (przy użyciu klucza funkcji BitLocker wygenerowanego przez usługę podczas konfigurowania zadania). Klucz funkcji BitLocker jest dostarczany osobno na platformie Azure, co zapewnia Udostępnianie kluczy poza pasmem.

Ponieważ dane w tranzycie mogą odbywać się w różnych scenariuszach, należy również wiedzieć, że Microsoft Azure używa [wirtualnych sieci](https://azure.microsoft.com/documentation/services/virtual-network/) do izolowania ruchu dzierżawców od siebie, wykorzystując miary takie jak zapory na poziomie hosta i gościa, filtrowanie pakietów IP, blokowanie portów i punkty końcowe HTTPS. Jednak większość komunikacji wewnętrznej platformy Azure, w tym infrastruktura do infrastruktury i infrastruktura na klienta (lokalnie), jest również szyfrowana. Innym ważnym scenariuszem jest komunikacja w centrach danych platformy Azure; Firma Microsoft zarządza sieciami, aby upewnić się, że żadna maszyna wirtualna nie może personifikować lub eavesdrop na adres IP innego typu. Protokół TLS/SSL jest używany podczas uzyskiwania dostępu do usługi Azure Storage lub baz danych SQL lub podczas nawiązywania połączenia z usługą Cloud Services. W takim przypadku administrator klienta jest odpowiedzialny za uzyskanie certyfikatu TLS/SSL i wdrożenie go w infrastrukturze dzierżawców. Ruch danych przenoszony między Virtual Machines w tym samym wdrożeniu lub między dzierżawcami w jednym wdrożeniu za pośrednictwem Microsoft Azure Virtual Network może być chroniony za pomocą szyfrowanych protokołów komunikacyjnych, takich jak HTTPS, SSL/TLS lub inne.

W zależności od odpowiedzi na pytania w temacie [Określanie wymagań dotyczących ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)należy mieć możliwość określenia sposobu ochrony danych oraz sposobu, w jaki rozwiązanie do obsługi tożsamości hybrydowej może pomóc w tym procesie. W poniższej tabeli przedstawiono opcje obsługiwane przez platformę Azure, które są dostępne dla każdego scenariusza ochrony danych.

| Opcje ochrony danych | W chmurze | W środowisku lokalnym | Przesyłanie |
| --- | --- | --- | --- |
| Szyfrowanie dysków funkcją BitLocker |X |X | |
| SQL Server szyfrować baz danych |X |X | |
| Szyfrowanie między MASZYNami wirtualnymi | | |X |
| Protokół SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Przeczytaj temat [zgodność przez funkcję](https://azure.microsoft.com/support/trust-center/services/) w [Centrum zaufania Microsoft Azure](https://azure.microsoft.com/support/trust-center/) , aby dowiedzieć się więcej o certyfikatach, z którymi jest zgodna każda usługa systemu Azure.
> Ponieważ opcje ochrony danych wykorzystują podejście Multilayer, porównanie między tymi opcjami nie ma zastosowania do tego zadania. Upewnij się, że korzystasz ze wszystkich opcji dostępnych dla każdego stanu danych.
>
>

## <a name="define-content-management-options"></a>Definiowanie opcji zarządzania zawartością

Jedną z zalet korzystania z usługi Azure AD do zarządzania hybrydową infrastrukturą tożsamości jest to, że proces jest w pełni przejrzysty od perspektywy użytkownika końcowego. Użytkownik próbuje uzyskać dostęp do zasobu udostępnionego, a zasób wymaga uwierzytelnienia, a użytkownik musi wysłać żądanie uwierzytelnienia do usługi Azure AD w celu uzyskania tokenu i uzyskania dostępu do zasobu. Ten cały proces odbywa się w tle bez interakcji z użytkownikiem. 

Organizacje, które mają problemy z ochroną danych, zwykle wymagają klasyfikacji danych dla swojego rozwiązania. Jeśli bieżąca infrastruktura lokalna już korzysta z klasyfikacji danych, można użyć usługi Azure AD jako głównego repozytorium dla tożsamości użytkownika. Typowym narzędziem, które jest używane lokalnie do klasyfikacji danych, jest nazywany [zestaw narzędzi do klasyfikacji danych](/previous-versions/tn-archive/hh204743(v=technet.10)) dla systemu Windows Server 2012 R2. To narzędzie pomaga identyfikować, klasyfikować i chronić dane na serwerach plików w chmurze prywatnej. W celu wykonania tego zadania można również użyć [automatycznej klasyfikacji plików](/windows-server/identity/solution-guides/deploy-automatic-file-classification--demonstration-steps-) w systemie Windows Server 2012.

Jeśli organizacja nie ma klasyfikacji danych, ale wymaga ochrony poufnych plików bez konieczności dodawania nowych serwerów lokalnie, może korzystać z [usługi Microsoft Azure Rights Management](/azure/information-protection/what-is-azure-rms).  Azure RMS korzysta z zasad szyfrowania, tożsamości i autoryzacji, aby ułatwić Zabezpieczanie plików i wiadomości e-mail oraz działa na wielu urządzeniach — telefonach, tabletach i komputerach. Ponieważ Azure RMS jest usługą w chmurze, nie ma potrzeby jawnego konfigurowania relacji zaufania z innymi organizacjami przed udostępnieniem im chronionej zawartości. Jeśli masz już Microsoft 365 lub katalog usługi Azure AD, współpraca między organizacjami jest obsługiwana automatycznie. Można również synchronizować tylko atrybuty katalogu, które Azure RMS muszą obsługiwać wspólną tożsamość lokalnych kont Active Directory, za pomocą usług Azure Active Directory Synchronization Services (Azure AD Sync) lub Azure AD Connect.

Istotną częścią zarządzania zawartością jest zrozumienie, kto ma dostęp do tego zasobu, dlatego zaawansowana funkcja rejestrowania jest ważna dla rozwiązania do zarządzania tożsamościami. Usługa Azure AD umożliwia logowanie przez 30 dni, w tym:

* Zmiany członkostwa w rolach (np. użytkownik został dodany do roli administratora globalnego)
* Aktualizacje poświadczeń (np. zmiany hasła)
* Zarządzanie domeną (np. Weryfikowanie domeny niestandardowej, Usuwanie domeny)
* Dodawanie lub usuwanie aplikacji
* Zarządzanie użytkownikami (np.: Dodawanie, usuwanie, aktualizowanie użytkownika)
* Dodawanie lub usuwanie licencji

> [!NOTE]
> Przeczytaj [Microsoft Azure Security and Audit zarządzanie dziennikiem](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) , aby dowiedzieć się więcej na temat możliwości rejestrowania na platformie Azure.
> W zależności od odpowiedzi na pytania w temacie [Określanie wymagań związanych z zarządzaniem zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)należy być w stanie określić, w jaki sposób zawartość ma być zarządzana w rozwiązaniu tożsamości hybrydowej. Wszystkie opcje uwidocznione w tabeli 6 umożliwiają integrację z usługą Azure AD, dlatego ważne jest, aby zdefiniować, co jest bardziej odpowiednie dla potrzeb Twojej firmy.
>
>

| Opcje zarządzania zawartością | Zalety | Wady |
| --- | --- | --- |
| Scentralizowany lokalny (Active Directory Rights Management Server) |Pełna kontrola nad infrastrukturą serwera odpowiedzialną za klasyfikowanie danych <br> Wbudowana możliwość w systemie Windows Server bez konieczności stosowania dodatkowej licencji lub subskrypcji <br> Można zintegrować z usługą Azure AD w scenariuszu hybrydowym <br> Obsługuje funkcje zarządzania prawami do informacji (IRM) w usługach online firmy Microsoft, takich jak Exchange Online i SharePoint Online, a także Microsoft 365 <br> Obsługuje lokalne produkty serwerowe firmy Microsoft, takie jak Exchange Server, SharePoint Server i serwery plików z systemem Windows Server i infrastrukturą klasyfikacji plików (FCI). |Wyższa konserwacja (Zadbaj o aktualizacje, konfigurację i potencjalne uaktualnienia), ponieważ jest ona właścicielem serwera <br> Wymagaj lokalnej infrastruktury serwerowej<br> Nie wykorzystuje natywnych możliwości platformy Azure |
| Scentralizowane w chmurze (Azure RMS) |Łatwiejsze zarządzanie w porównaniu z rozwiązaniem lokalnym <br> Można zintegrować z AD DS w scenariuszu hybrydowym <br>  W pełni zintegrowana z usługą Azure AD <br> Nie wymaga lokalnego serwera do wdrożenia usługi <br> Obsługuje lokalne produkty serwerowe firmy Microsoft, takie jak Exchange Server, SharePoint, Server i serwery plików z systemem Windows Server i klasyfikacją plików, infrastruktura (FCI) <br> Może mieć pełną kontrolę nad kluczem dzierżawy z możliwością BYOK. |Twoja organizacja musi mieć subskrypcję w chmurze, która obsługuje usługę RMS <br> Organizacja musi mieć katalog usługi Azure AD, aby obsługiwać uwierzytelnianie użytkowników na potrzeby usługi RMS |
| Hybrydowe (Azure RMS zintegrowane z lokalnym serwerem Active Directory Rights Management) |W tym scenariuszu są gromadzone zalety zarówno scentralizowanej, jak i w chmurze. |Twoja organizacja musi mieć subskrypcję w chmurze, która obsługuje usługę RMS <br> Organizacja musi mieć katalog usługi Azure AD, aby obsługiwać uwierzytelnianie użytkowników na potrzeby usługi RMS. <br> Wymaga połączenia między usługą w chmurze platformy Azure i infrastrukturą lokalną |

## <a name="define-access-control-options"></a>Definiowanie opcji kontroli dostępu
Korzystając z funkcji uwierzytelniania, autoryzacji i kontroli dostępu dostępnych w usłudze Azure AD, można umożliwić firmie korzystanie z centralnego repozytorium tożsamości, jednocześnie umożliwiając użytkownikom i partnerom korzystanie z funkcji logowania jednokrotnego (SSO), jak pokazano na poniższym rysunku:

![Scentralizowane zarządzanie](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Scentralizowane zarządzanie i pełna integracja z innymi katalogami

Azure Active Directory zapewnia Logowanie jednokrotne do tysięcy aplikacji SaaS i lokalnych aplikacji sieci Web. Zobacz [listę zgodności federacji Azure Active Directory: dostawcy tożsamości innych firm, których można używać do implementowania logowania](how-to-connect-fed-compatibility.md) jednokrotnego, aby uzyskać więcej szczegółowych informacji na temat logowania jednokrotnego, który został przetestowany przez firmę Microsoft. Dzięki tej funkcji organizacja może zaimplementować różne scenariusze B2B, jednocześnie kontrolując Zarządzanie tożsamościami i dostępem. Jednak w trakcie procesu projektowania B2B ważne jest zrozumienie metody uwierzytelniania używanej przez partnera i sprawdzenie, czy ta metoda jest obsługiwana przez platformę Azure. Obecnie następujące metody są obsługiwane przez usługę Azure AD:

* SAML (SAML)
* OAuth
* Kerberos
* Tokeny
* Certyfikaty

> [!NOTE]
> Przeczytaj [Azure Active Directory protokoły uwierzytelniania](/previous-versions/azure/dn151124(v=azure.100)) , aby dowiedzieć się więcej na temat każdego protokołu i jego możliwości na platformie Azure.
>
>

Korzystając z pomocy technicznej usługi Azure AD, aplikacje mobilne firmy mogą korzystać z tego samego prostego środowiska uwierzytelniania Mobile Services, aby umożliwić pracownikom zalogowanie się do swoich aplikacji mobilnych przy użyciu poświadczeń Active Directory firmowych. W przypadku tej funkcji usługa Azure AD jest obsługiwana jako dostawca tożsamości w Mobile Services wraz z innymi obsługiwanymi dostawcami tożsamości (w tym kontami Microsoft, IDENTYFIKATORem Facebook, identyfikatorem Google ID i IDENTYFIKATORem usługi Twitter). Jeśli lokalne aplikacje używają poświadczeń użytkownika znajdujących się w AD DS firmy, dostęp od partnerów i użytkowników pochodzących z chmury powinien być przezroczysty. Można zarządzać kontrolą dostępu warunkowego użytkownika do aplikacji sieci Web (opartych na chmurze), internetowego interfejsu API, usług w chmurze firmy Microsoft, aplikacji SaaS innych firm oraz natywnych aplikacji klienckich (mobilnych), a także skorzystać z zalet zabezpieczeń, inspekcji i raportowania wszystkich w jednym miejscu. Zaleca się jednak Weryfikowanie implementacji w środowisku nieprodukcyjnym lub ograniczonej liczbie użytkowników.

> [!TIP]
> należy pamiętać, że usługa Azure AD nie ma zasady grupy, jak AD DS ma. Aby wymusić zasady dla urządzeń, potrzebne jest rozwiązanie do zarządzania urządzeniami przenośnymi, takie jak [Microsoft Intune](/mem/intune/).
>
>

Po uwierzytelnieniu użytkownika przy użyciu usługi Azure AD ważne jest, aby oszacować poziom dostępu, który użytkownik ma. Poziom dostępu, który użytkownik ma za pośrednictwem zasobu, może się różnić. Mimo że usługa Azure AD może dodać dodatkową warstwę zabezpieczeń, kontrolując dostęp do niektórych zasobów, należy pamiętać, że sam zasób może również mieć osobną listę kontroli dostępu, na przykład kontrolę dostępu dla plików znajdujących się na serwerze plików. Na poniższej ilustracji przedstawiono podsumowanie poziomów kontroli dostępu, które można wykonać w scenariuszu hybrydowym:

![Kontrola dostępu](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Każda interakcja na diagramie pokazuje jeden scenariusz kontroli dostępu, który może być objęty przez usługę Azure AD. Poniżej znajdziesz opis każdego scenariusza:

1. Dostęp warunkowy do aplikacji hostowanych lokalnie: można używać zarejestrowanych urządzeń z zasadami dostępu dla aplikacji skonfigurowanych do korzystania z AD FS z systemem Windows Server 2012 R2.

2. Access Control Azure Portal: platforma Azure umożliwia również kontrolowanie dostępu do portalu przy użyciu kontroli dostępu opartej na rolach (Azure RBAC)). Ta metoda pozwala firmie ograniczyć liczbę operacji, które może wykonać osoba w Azure Portal. Korzystając z funkcji RBAC platformy Azure, aby kontrolować dostęp do portalu, Administratorzy IT mogą delegować dostęp przy użyciu następujących metod zarządzania dostępem:

   - Przypisywanie ról oparte na grupach: można przypisać dostęp do grup usługi Azure AD, które można synchronizować z lokalnego Active Directory. Dzięki temu można wykorzystać istniejące inwestycje, które organizacja wprowadziła w narzędzia i procesy do zarządzania grupami. Można również użyć funkcji delegowanej zarządzania grupami programu Azure AD — wersja Premium.
   - Używanie wbudowanych ról na platformie Azure: możesz użyć trzech ról — właściciela, współautora i czytnika, aby upewnić się, że użytkownicy i grupy mają uprawnienia do wykonywania tylko tych zadań, których potrzebują do wykonywania swoich zadań.
   -  Szczegółowy dostęp do zasobów: można przypisywać role do użytkowników i grup dla określonej subskrypcji, grupy zasobów lub pojedynczego zasobu platformy Azure, takiego jak witryna sieci Web lub baza danych. W ten sposób można zagwarantować, że użytkownicy będą mieli dostęp do wszystkich potrzebnych zasobów i nie mają dostępu do zasobów, które nie muszą być zarządzane.

   > [!NOTE]
   > Jeśli tworzysz aplikacje i chcesz dostosować kontrolę dostępu do nich, można również użyć ról aplikacji usługi Azure AD do autoryzacji. Zapoznaj się z tym [przykładem webapp-RoleClaims-dotnet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) , jak skompilować aplikację w celu korzystania z tej funkcji.


3. Dostęp warunkowy dla aplikacji Microsoft 365 z Microsoft Intune: Administratorzy IT mogą udostępniać zasady urządzeń dostępu warunkowego, aby zabezpieczyć zasoby firmowe, jednocześnie umożliwiając pracownikom przetwarzającym informacje na zgodnych urządzeniach dostęp do usług. 
  
4. Dostęp warunkowy dla aplikacji SaaS: [Ta funkcja](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) pozwala konfigurować reguły dostępu do usługi uwierzytelniania wieloskładnikowego dla poszczególnych aplikacji oraz możliwość blokowania dostępu dla użytkowników spoza zaufanej sieci. Można zastosować reguły uwierzytelniania wieloskładnikowego dla wszystkich użytkowników przypisanych do aplikacji lub tylko dla użytkowników w określonych grupach zabezpieczeń. Użytkownicy mogą być wykluczeni z wymaganego uwierzytelniania wieloskładnikowego, jeśli uzyskują dostęp do aplikacji z adresu IP, który znajduje się w sieci organizacji.

Ponieważ opcje kontroli dostępu wykorzystują podejście Multilayer, porównanie między tymi opcjami nie ma zastosowania do tego zadania. Upewnij się, że korzystasz ze wszystkich opcji dostępnych dla każdego scenariusza, który wymaga kontroli dostępu do zasobów.

## <a name="define-incident-response-options"></a>Zdefiniuj opcje odpowiedzi na zdarzenia
Usługa Azure AD może pomóc IT w identyfikacji potencjalnych zagrożeń bezpieczeństwa w środowisku przez monitorowanie aktywności użytkownika. Umożliwia ona uzyskanie wglądu w integralność i bezpieczeństwo katalogu organizacji przy użyciu raportów dotyczących dostępu i użycia usługi Azure AD. Dzięki tym informacjom administrator IT może lepiej określić, gdzie mogą być dostępne zagrożenia bezpieczeństwa, aby mogły one być odpowiednio zaplanowane w celu ograniczenia ryzyka.  [Subskrypcja Azure AD — wersja Premium](../fundamentals/active-directory-get-started-premium.md) zawiera zestaw raportów zabezpieczeń, które umożliwiają uzyskanie tych informacji. [Raporty usługi Azure AD](../reports-monitoring/overview-reports.md) są podzielone w następujący sposób:

* **Raporty anomalii**: zawierają zdarzenia logowania, które okazały się nietypowe. Celem jest świadome tego działania i umożliwienie ustalenia, czy zdarzenie jest podejrzane.
* **Raport zintegrowanych aplikacji**: zawiera szczegółowe informacje dotyczące sposobu używania aplikacji w chmurze w organizacji. Azure Active Directory oferuje integrację z tysiącami aplikacji w chmurze.
* **Raporty o błędach**: wskazuje błędy, które mogą wystąpić podczas aprowizacji kont do aplikacji zewnętrznych.
* **Raporty specyficzne dla użytkownika**: wyświetla dane dotyczące urządzenia/logowania dla określonego użytkownika.
* **Dzienniki aktywności**: zawierają rekord wszystkich zdarzeń poddawanych inspekcji w ciągu ostatnich 24 godzin, ostatnich 7 dni lub ostatnich 30 dni, a także zmian aktywności grup oraz działania resetowania i rejestracji hasła.

> [!TIP]
> Innym raportem, który może ułatwić zespołowi reagowanie na incydenty pracującym w przypadku, jest raport [z nieujawnionymi poświadczeniami](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) . Ten raport przedstawia wszystkie dopasowania między listą przecieków poświadczeń a dzierżawcą.
>


Inne ważne raporty wbudowane w usłudze Azure AD, które mogą być używane podczas badania odpowiedzi na zdarzenia i są następujące:

* **Działanie resetowania hasła**: umożliwia administratorowi wgląd w informacje na temat sposobu korzystania z funkcji aktywnej resetowania haseł w organizacji.
* **Działanie rejestracji resetowania hasła**: zawiera szczegółowe informacje o tym, którzy użytkownicy zarejestrowali swoje metody resetowania haseł oraz które metody zostały wybrane.
* **Działanie grupy**: zawiera historię zmian w grupie (np. dodano lub usunięto użytkowników), które zostały zainicjowane w panelu dostępu.

Oprócz podstawowych możliwości raportowania Azure AD — wersja Premium, których można użyć podczas procesu badania odpowiedzi na zdarzenia, można także skorzystać z raportu inspekcji, aby uzyskać informacje takie jak:

* Zmiany członkostwa w rolach (na przykład użytkownik został dodany do roli administratora globalnego)
* Aktualizacje poświadczeń (na przykład zmiany hasła)
* Zarządzanie domeną (na przykład Weryfikowanie domeny niestandardowej, Usuwanie domeny)
* Dodawanie lub usuwanie aplikacji
* Zarządzanie użytkownikami (na przykład dodawanie, usuwanie, aktualizowanie użytkownika)
* Dodawanie lub usuwanie licencji

Ponieważ opcje odpowiedzi na zdarzenia używają podejścia Multilayer, porównanie między tymi opcjami nie ma zastosowania do tego zadania. Upewnij się, że korzystasz ze wszystkich opcji dostępnych dla każdego scenariusza, który wymaga korzystania z funkcji raportowania usługi Azure AD w ramach procesu reagowania na zdarzenia firmy.

## <a name="next-steps"></a>Następne kroki
[Określanie zadań hybrydowego zarządzania tożsamościami](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)