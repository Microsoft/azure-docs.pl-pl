---
title: Harmonogram instalacji usługi Azure AD Connect i Azure AD Connect Health. | Microsoft Docs
description: Ten dokument zawiera omówienie opcji instalacji i dostępnych ścieżek instalacji usług Azure AD Connect i Connect Health.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 244456298e9a85f7cf7a5bdc175f1df5397ca207
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89662141"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Harmonogram instalacji usług Azure AD Connect i Azure AD Connect Health.

## <a name="install-azure-ad-connect"></a>Instalowanie programu Azure AD Connect

> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania ani działania synchronizacji programu Azure AD Connect poza akcjami, które zostały formalnie udokumentowane. Każda z tych akcji może skutkować niespójnością lub nieobsługiwanym stanem synchronizacji Azure AD Connect. W związku z tym firma Microsoft nie może zapewnić pomocy technicznej dotyczącej takich wdrożeń.

Program Azure AD Connect można pobrać z [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).

| Rozwiązanie | Scenariusz |
| --- | --- |
| Przed rozpoczęciem — [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) |<li>Czynności, które należy wykonać przed rozpoczęciem instalacji programu Azure AD Connect.</li> |
| [Ustawienia ekspresowe](how-to-connect-install-express.md) |<li>Jeśli masz usługę AD z jednym lasem, jest to zalecana opcja.</li> <li>Użytkownicy logują się przy użyciu tego samego hasła dzięki synchronizacji haseł.</li> |
| [Ustawienia dostosowane](how-to-connect-install-custom.md) |<li>Opcja używana, gdy masz większą liczbę lasów. Obsługuje wiele [topologii](plan-connect-topologies.md) lokalnych.</li> <li>Możesz dostosować opcje logowania, takie jak uwierzytelnianie przekazywane, usługi federacyjne AD FS lub inny dostawca tożsamości.</li> <li>Możesz dostosować funkcje synchronizacji, na przykład filtrowanie i zapisywanie zwrotne.</li> |
| [Uaktualnianie z narzędzia DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Używane, jeśli masz już działający serwer DirSync.</li> |
| [Uaktualnienie z narzędzia Azure AD Sync lub Azure AD Connect](how-to-upgrade-previous-version.md) |<li>Istnieje kilka różnych metod do wyboru w zależności od preferencji.</li> |

[Po instalacji](how-to-connect-post-installation.md) należy sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, i przydzielić licencje do użytkowników.

### <a name="next-steps-to-install-azure-ad-connect"></a>Następne kroki instalowania programu Azure AD Connect
|Temat |Łącze|  
| --- | --- |
|Pobieranie programu Azure AD Connect | [Pobieranie programu Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalowanie przy użyciu ustawień ekspresowych | [Ekspresowa instalacja programu Azure AD Connect](./how-to-connect-install-express.md)|
|Instalowanie przy użyciu ustawień dostosowanych | [Niestandardowa instalacja programu Azure AD Connect](./how-to-connect-install-custom.md)|
|Uaktualnianie przy użyciu narzędzia DirSync | [Uaktualnienie przy użyciu narzędzia Azure AD Sync (DirSync)](./how-to-dirsync-upgrade-get-started.md)|
|Po instalacji | [Weryfikowanie instalacji i przypisywanie licencji](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Więcej informacji o instalowaniu programu Azure AD Connect
Warto również przygotować się na kwestie związane z [działaniem](./how-to-connect-sync-staging-server.md). Może być wskazane zastosowanie serwera zapasowego umożliwiającego łatwe przełączenie w przypadku [awarii](how-to-connect-sync-staging-server.md#disaster-recovery). Jeśli planujesz częste wprowadzanie zmian konfiguracji, należy zaplanować serwer [trybu przejściowego](how-to-connect-sync-staging-server.md).

|Temat |Łącze|  
| --- | --- |
|Obsługiwane topologie | [Topologie obsługiwane w programie Azure AD Connect](plan-connect-topologies.md)|
|Zagadnienia dotyczące projektowania | [Zagadnienia dotyczące projektowania przy korzystaniu z programu Azure AD Connect](plan-connect-design-concepts.md)|
|Konta używane do instalacji | [Więcej informacji na temat poświadczeń i uprawnień dla programu Azure AD Connect](reference-connect-accounts-permissions.md)|
|Planowanie operacyjne | [Synchronizacja programu Azure AD Connect: zagadnienia i zadania operacyjne](./how-to-connect-sync-staging-server.md)|
|Opcje logowania użytkowników | [Opcje logowania użytkowników w programie Azure AD Connect](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>Konfigurowanie funkcji synchronizacji
Program Azure AD Connect zawiera szereg funkcji, które są domyślnie włączone lub które można włączyć opcjonalnie. Niektóre funkcje mogą wymagać wykonania dodatkowych czynności konfiguracyjnych w określonych scenariuszach i topologiach.

[Filtrowanie](how-to-connect-sync-configure-filtering.md) umożliwia ograniczenie zakresu obiektów synchronizowanych z usługą Azure AD. Domyślnie synchronizacja obejmuje wszystkich użytkowników, kontakty, grupy i komputery z systemem Windows 10. Możesz zmienić ustawienia filtrowania na podstawie domen, jednostek organizacyjnych lub atrybutów.

[Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md) umożliwia synchronizowanie skrótów haseł w usłudze Active Directory z usługą Azure AD. Użytkownik końcowy może korzystać z tego samego hasła lokalnie i w chmurze, zarządzając nim w jednej lokalizacji. Ponieważ źródłem jest lokalna usługa Active Directory, można używać również własnych zasad haseł.

[Zapisywanie zwrotne haseł](../authentication/tutorial-enable-sspr.md) umożliwia użytkownikom zmienianie i resetowanie haseł w chmurze i stosowanie lokalnych zasad haseł.

[Zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md) umożliwi zapisanie urządzenia w usłudze Azure AD z powrotem do Active Directory lokalnego, dzięki czemu będzie można go używać na potrzeby dostępu warunkowego.

Funkcja [zapobiegania przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) jest domyślnie włączona i zabezpiecza katalog w chmurze przed usunięciem dużej liczby elementów jednocześnie. Domyślnie dozwolone jest usunięcie 500 elementów w jednym przebiegu. Możesz zmienić to ustawienie w zależności od wielkości organizacji.

[Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md) jest domyślnie włączone w przypadku instalacji z ustawieniami ekspresowymi i zapewnia korzystanie z najnowszej wersji programu Azure AD Connect.

### <a name="next-steps-to-configure-sync-features"></a>Następne kroki konfigurowania funkcji synchronizacji
|Temat |Łącze|  
| --- | --- |
|Konfigurowanie filtrowania | [Synchronizacja programu Azure AD Connect: konfigurowanie filtrowania](how-to-connect-sync-configure-filtering.md)|
|Synchronizacja skrótów haseł | [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md)|
|Uwierzytelnianie przekazywane | [Uwierzytelnianie przekazywane](how-to-connect-pta.md)
|Zapisywanie zwrotne haseł | [Wprowadzenie do zarządzania hasłami](../authentication/tutorial-enable-sspr.md)|
|Zapisywanie zwrotne urządzeń | [Włączanie zapisywania zwrotnego urządzeń w programie Azure AD Connect](how-to-connect-device-writeback.md)|
|Zapobieganie przypadkowemu usuwaniu | [Synchronizacja programu Azure AD Connect: zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|Automatycznie uaktualnianie | [Azure AD Connect: automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Dostosowywanie synchronizacji w programie Azure AD Connect
Składnik synchronizacji programu Azure AD Connect ma konfigurację domyślną, która powinna działać w przypadku większości klientów i topologii. Zawsze jednak mogą występować sytuacje, w których konfiguracja domyślna nie sprawdza się i wymaga dostosowania. Obsługiwane jest wprowadzanie zmian zgodnie z opisem w tej sekcji i w połączonych tematach.

Jeśli nie masz doświadczenia z topologią synchronizacji, warto zapoznać się z podstawowymi informacjami i używanymi pojęciami przedstawionymi w temacie [zagadnienia techniczne](how-to-connect-sync-technical-concepts.md). Program Azure AD Connect powstał na podstawie narzędzi MIIS2003, ILM2007 i FIM2010. Mimo że niektóre elementy są identyczne, wprowadzono także wiele zmian.

[Konfiguracja domyślna](concept-azure-ad-connect-sync-default-configuration.md) dopuszcza istnienie więcej niż jednego lasu w konfiguracji. W takich topologiach obiekt użytkownika może być reprezentowany jako kontakt w innym lesie. Użytkownik może także mieć połączoną skrzynkę pocztową w innym lesie zasobów. Działanie konfiguracji domyślnej przedstawiono w temacie [użytkownicy i kontakty](concept-azure-ad-connect-sync-user-and-contacts.md).

Model konfiguracji synchronizacji jest nazywany [aprowizacją deklaratywną](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md). Zaawansowane przepływy atrybutów używają [funkcji](reference-connect-sync-functions-reference.md) do wyrażania przekształceń atrybutów. Całą konfigurację można wyświetlić i sprawdzić za pomocą narzędzi dostarczanych wraz z programem Azure AD Connect. Jeśli konieczne jest wprowadzenie zmian w konfiguracji, pamiętaj o postępowaniu zgodnie z [najlepszymi rozwiązaniami](how-to-connect-sync-best-practices-changing-default-configuration.md), co ułatwi przyjmowanie nowych wersji.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Następne kroki dostosowywania synchronizacji w programie Azure AD Connect
|Temat |Łącze|  
| --- | --- |
|Wszystkie artykuły dotyczące synchronizacji programu Azure AD Connect | [Synchronizacja Azure AD Connect](how-to-connect-sync-whatis.md)|
|Zagadnienia techniczne | [Azure AD Connect Sync: koncepcje techniczne](how-to-connect-sync-technical-concepts.md)|
|Opis konfiguracji domyślnej | [Synchronizacja programu Azure AD Connect: opis konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md)|
|Opis użytkowników i kontaktów | [Synchronizacja programu Azure AD Connect: opis użytkowników i kontaktów](concept-azure-ad-connect-sync-user-and-contacts.md)|
|Aprowizacja deklaratywna | [Synchronizacja programu Azure AD Connect: wyjaśnienie wyrażeń związanych z aprowizacją deklaratywną](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|Zmienianie konfiguracji domyślnej | [Najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Konfigurowanie funkcji federacyjnych

Program Azure AD Connect udostępnia pewne funkcje, które upraszczają sfederowanie z usługą Azure AD za pomocą usług AD FS i zarządzanie relacjami zaufania federacji. Program Azure AD Connect obsługuje usługi AD FS w systemie Windows Server 2012 R2 lub nowszym.

[Zaktualizuj certyfikat TLS/SSL farmy AD FS](how-to-connect-fed-ssl-update.md) , nawet jeśli nie używasz Azure AD Connect do zarządzania relacjami zaufania federacji.

[Dodaj serwer usług AD FS](how-to-connect-fed-management.md#addadfsserver) do farmy, aby rozwinąć farmę zgodnie z potrzebami.

[Napraw zaufanie](how-to-connect-fed-management.md#repairthetrust) przy użyciu usługi Azure AD za pomocą kilku prostych kliknięć.

Usługi AD FS można skonfigurować pod kątem obsługi [wielu domen](how-to-connect-install-multiple-domains.md). Możesz na przykład mieć wiele domen najwyższego poziomu, których musisz użyć na potrzeby federacji.

Jeśli serwer usług AD FS nie został skonfigurowany tak, aby automatycznie aktualizował certyfikaty z usługi Azure AD, lub jeśli korzystasz z rozwiązania innego niż AD FS, otrzymasz powiadomienie o konieczności [zaktualizowania certyfikatów](how-to-connect-fed-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Następne kroki konfigurowania funkcji federacyjnych
|Temat |Łącze|  
| --- | --- |
|Wszystkie artykuły dotyczące usług AD FS | [Azure AD Connect i Federacja](how-to-connect-fed-whatis.md)|
|Konfigurowanie usług AD FS z poddomenami | [Obsługa wielu domen do federowania w usłudze Azure AD](how-to-connect-install-multiple-domains.md)|
|Zarządzanie farmą usług AD FS | [Dostosowywanie usług AD FS i zarządzanie nimi za pomocą programu Azure AD Connect](how-to-connect-fed-management.md)|
|Ręczne aktualizowanie certyfikatów federacji | [Odnawianie certyfikatów federacyjnych dla Microsoft 365 i usługi Azure AD](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Wprowadzenie do programu Azure AD Connect Health
Aby rozpocząć pracę z programem Azure AD Connect Health, należy wykonać następujące czynności:

1. [Pobierz Azure AD — wersja Premium](../fundamentals/active-directory-get-started-premium.md) lub [Rozpocznij okres próbny](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Pobierz i zainstaluj agentów Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) na serwerach tożsamości.
3. Wyświetl pulpit nawigacyjny Azure AD Connect Health na stronie [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) .

> [!NOTE]
> Pamiętaj, że przed wyświetleniem danych na pulpicie nawigacyjnym programu Azure AD Connect Health musisz zainstalować agentów programu Azure AD Connect Health na serwerach docelowych.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Pobieranie i instalowanie agenta programu Azure AD Connect Health
* Sprawdź, czy [wymagania programu Azure AD Connect Health zostały spełnione](how-to-connect-health-agent-install.md#requirements).
* Wprowadzenie do korzystania z programu Azure AD Connect Health dla usług AD FS
    * [Pobierz agenta programu Azure AD Connect Health dla usług AD FS.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Zobacz instrukcje instalacji](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Wprowadzenie do korzystania z programu Azure AD Connect Health do celów synchronizacji
    * [Pobierz i zainstaluj najnowszą wersję programu Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). Agent kondycji do celów synchronizacji zostanie zainstalowany w ramach instalacji programu Azure AD Connect (w wersji 1.0.9125.0 lub nowszej).
* Wprowadzenie do korzystania z programu Azure AD Connect Health dla usług AD DS
    * [Pobierz Azure AD Connect Health agenta dla AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Zobacz instrukcje instalacji](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).


## <a name="azure-ad-connect-health-portal"></a>Portal programu Azure AD Connect Health
Portal programu Azure AD Connect Health umożliwia wyświetlanie alertów, wyników monitorowania wydajności i analiz użycia. Adres URL https://aka.ms/aadconnecthealth pozwala na wyświetlenie głównego bloku programu Azure AD Connect Health. Blok możesz traktować jak okno. W głównym bloku znajduje się przycisk **Szybki start**, usługi dostępne w programie Azure AD Connect Health i dodatkowe opcje konfiguracji. Zobacz poniższy zrzut ekranu i zamieszczone pod nim krótkie objaśnienia. Po wdrożeniu agentów usługa kondycji automatycznie identyfikuje usługi monitorowane przez program Azure AD Connect Health.

> [!NOTE]
> Aby uzyskać informacje o licencjonowaniu, zobacz temat [Azure AD Connect Health FAQ (Program Azure AD Connect Health — często zadawane pytania)](reference-connect-health-faq.md) lub [stronę z cennikiem usługi Azure AD](https://aka.ms/aadpricing).
    
![Portal programu Azure AD Connect Health](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **Szybki Start**: wybranie tej opcji powoduje otwarcie bloku **Szybki start**. Agenta programu Azure AD Connect Health można pobrać, wybierając pozycję **Pobierz narzędzia**. Można również uzyskać dostęp do dokumentacji i przekazać swoją opinię.
* **Azure Active Directory Connect (synchronizacja)**: ta opcja umożliwia pokazanie serwerów programu Azure AD Connect, które są aktualnie monitorowane przez program Azure AD Connect Health. Pozycja **Błędy synchronizacji** umożliwia wyświetlenie podstawowych błędów synchronizacji Twojej pierwszej dołączonej usługi synchronizacji według kategorii. Wybranie pozycji **Usługi synchronizacji** powoduje otwarcie bloku z informacjami na temat serwerów programu Azure AD Connect. Aby dowiedzieć się więcej o dostępnych możliwościach, zobacz [Używanie programu Azure AD Connect Health w celu synchronizacji](how-to-connect-health-sync.md).
* **Active Directory Federation Services**: ta opcja umożliwia pokazanie wszystkich usług AD FS, które są aktualnie monitorowane przez program Azure AD Connect Health. Po wybraniu wystąpienia zostaje otwarty blok z informacjami dotyczącymi tego wystąpienia usługi. Informacje te obejmują przegląd, właściwości, alerty, wyniki monitorowania i analizy użycia. Aby dowiedzieć się więcej o dostępnych możliwościach, zobacz [Używanie programu Azure AD Connect Health z usługami AD FS](how-to-connect-health-adfs.md).
* **Active Directory Domain Services**: ta opcja umożliwia pokazanie wszystkich lasów usług AD DS, które są aktualnie monitorowane przez program Azure AD Connect Health. Po wybraniu lasu zostaje otwarty blok z informacjami dotyczącymi tego lasu. Te informacje obejmują przegląd najważniejszych danych, pulpit nawigacyjny Kontrolery domeny, pulpit nawigacyjny Stan replikacji, alerty i monitorowanie. Aby dowiedzieć się więcej o dostępnych możliwościach, zobacz [Używanie programu Azure AD Connect Health z usługami AD DS](how-to-connect-health-adds.md).
* **Konfiguracja**: ta sekcja obejmuje opcje umożliwiające włączanie i wyłączanie ustawień:

   - **Automatyczna aktualizacja** agenta Azure AD Connect Health do najnowszej wersji: Agent Azure AD Connect Health jest automatycznie aktualizowany, gdy są dostępne nowe wersje. Ta opcja jest domyślnie włączona.
   - **Dostęp do danych** z poziomu integralności katalogu usługi Azure AD przez firmę Microsoft tylko do celów związanych z rozwiązywaniem problemów: Jeśli ta opcja jest włączona, firma Microsoft może uzyskiwać dostęp do tych samych danych, które są wyświetlane przez użytkownika. Te informacje mogą być przydatne do rozwiązywania problemów i zapewnienia niezbędnej pomocy. Ta opcja jest domyślnie wyłączona
* W sekcji **Kontrola dostępu oparta na rolach (IAM)** można zarządzać dostępem do danych programu Connect Health na podstawie ról. 

## <a name="next-steps"></a>Następne kroki

- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Ustawienia dostosowane](how-to-connect-install-custom.md)
- [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md)|
- [Uwierzytelnianie przekazywane](how-to-connect-pta.md)
- [Azure AD Connect i Federacja](how-to-connect-fed-whatis.md)
- [Instalowanie agentów programu Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Synchronizacja Azure AD Connect](how-to-connect-sync-whatis.md)