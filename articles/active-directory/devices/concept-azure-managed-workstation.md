---
title: Informacje na temat bezpiecznych stacji roboczych zarządzanych przez platformę Azure — Azure Active Directory
description: Poznaj bezpieczne, zarządzane przez platformę Azure stacje robocze i zrozumieć, dlaczego są ważne.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84d0731a67ac47b8b0fc73cb485857458b3febbb
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093314"
---
# <a name="understand-secure-azure-managed-workstations"></a>Informacje na temat bezpiecznych stacji roboczych zarządzanych przez platformę Azure

Zabezpieczone, izolowane stacje robocze mają kluczowe znaczenie dla bezpieczeństwa poufnych ról, takich jak Administratorzy, deweloperzy i krytyczne operatory usług. Jeśli zabezpieczenia stacji roboczej klienta zostały naruszone, wiele kontroli zabezpieczeń i gwarancji może zakończyć się niepowodzeniem lub być nieskuteczne.

W tym dokumencie wyjaśniono, co jest potrzebne do tworzenia bezpiecznej stacji roboczej, często znanej jako stacja robocza dostępu uprzywilejowanego (dostępem UPRZYWILEJOWANYM). Artykuł zawiera również szczegółowe instrukcje dotyczące konfigurowania początkowych kontroli zabezpieczeń. W tym przewodniku opisano sposób, w jaki technologia oparta na chmurze może zarządzać usługą. Opierają się one na funkcjach zabezpieczeń, które zostały wprowadzone w systemie Windows 10RS5, w ramach zaawansowanej ochrony przed zagrożeniami (ATP) w usłudze Microsoft Defender, Azure Active Directory i Microsoft Intune.

> [!NOTE]
> W tym artykule wyjaśniono koncepcję bezpiecznej stacji roboczej i jej znaczenia. Jeśli znasz już koncepcję i chcesz przejść do wdrożenia, odwiedź stronę [wdrażanie zabezpieczonej stacji roboczej](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Dlaczego bezpieczny dostęp do stacji roboczej jest ważny

Szybkie wdrażanie usług w chmurze i możliwość pracy z dowolnego miejsca spowodowała utworzenie nowej metody wykorzystywania. Dzięki wykorzystaniu słabych kontroli zabezpieczeń na urządzeniach, na których administratorzy pracują, osoby atakujące mogą uzyskać dostęp do zasobów uprzywilejowanych.

Ataki uprzywilejowane i w łańcuchu dostaw są wśród najlepszych pięciu metod używanych przez osoby atakujące w celu naruszenia organizacji. Są one również drugimi najczęściej wykrytymi taktykę w zdarzeniach raportowanych w 2018 zgodnie z [raportem o zagrożeniach Verizon](https://enterprise.verizon.com/resources/reports/dbir/)i [raportach analizy zabezpieczeń](https://aka.ms/sir).

Większość osób atakujących postępuje zgodnie z następującymi krokami:

1. Rekonesans, aby znaleźć sposób w programie, często specyficzny dla branży.
1. Analiza umożliwiająca zbieranie informacji i identyfikowanie najlepszego sposobu Infiltrate stacji roboczej, która jest postrzegana jako niska wartość.
1. Trwałość, aby wyszukać środek do [późniejszego](https://en.wikipedia.org/wiki/Network_Lateral_Movement)przeniesienia.
1. Eksfiltracji poufnych i poufnych danych.

Podczas Rekonesans osoby atakujące często infiltrateą urządzenia, na których występuje niskie ryzyko lub podnoszenia wartości. Wykorzystują one te podatne urządzenia do lokalizowania możliwości ruchu bocznego i znajdowania użytkowników administracyjnych i urządzeń. Po uzyskaniu dostępu do ról uprzywilejowanych użytkowników atakujący identyfikują dane o wysokiej wartości i pomyślnie wyprowadzać te dane.

![Typowy wzorzec naruszenia](./media/concept-azure-managed-workstation/typical-timeline.png)

W tym dokumencie opisano rozwiązanie, które może pomóc w zabezpieczeniu urządzeń komputerowych przed takimi atakami poprzecznymi. Rozwiązanie izoluje zarządzanie i usługi z mniej cennych urządzeń produkcyjnych, przerywając łańcuch, zanim urządzenie, które ma dostęp do poufnych zasobów w chmurze, może być infiltrated. Rozwiązanie korzysta z natywnych usług platformy Azure, które są częścią stosu Microsoft 365 Enterprise:

* Usługa Intune dla zarządzania urządzeniami oraz bezpieczna lista aplikacji i adresów URL
* Autopilotaż dla konfiguracji, wdrożenia i odświeżania urządzenia
* Usługa Azure AD do zarządzania użytkownikami, dostępu warunkowego i uwierzytelniania wieloskładnikowego
* Windows 10 (bieżąca wersja) dla zaświadczania o kondycji urządzenia i środowiska użytkownika
* Usługa Defender ATP dla zarządzanego w chmurze ochrony punktu końcowego, wykrywania i odpowiedzi
* Usługa Azure AD PIM do zarządzania autoryzacją i dostępem uprzywilejowanym just-in-Time (JIT) do zasobów
* Log Analytics i wskaźnik kontroli i zgłaszanie alertów

## <a name="who-benefits-from-a-secure-workstation"></a>Kto ma korzyść z bezpiecznej stacji roboczej?

Wszyscy użytkownicy i operatorzy korzystają z bezpiecznej stacji roboczej. Osoba atakująca, która narusza komputer lub urządzenie, może personifikować wszystkie konta w pamięci podręcznej. Po zalogowaniu się do urządzenia mogą również używać poświadczeń i tokenów. To zagrożenie sprawia, że ważne jest zabezpieczenie urządzeń, które są używane dla ról uprzywilejowanych, w tym uprawnień administracyjnych. Urządzenia z kontami uprzywilejowanymi są obiektami docelowymi ataków na ruch poprzeczny i podwyższenie poziomu uprawnień. Te konta mogą być używane dla różnych zasobów, takich jak:

* Administrator systemów lokalnych lub opartych na chmurze
* Stacja robocza dla deweloperów w systemach krytycznych
* Administrator konta mediów społecznościowych o wysokim zagrożeniu
* Wysoce wrażliwa stacja robocza, na przykład w przypadku terminalu płatności SWIFT
* Obsługa tajemnic handlowych dla stacji roboczej

Aby zmniejszyć ryzyko, należy zaimplementować podwyższone poziomu zabezpieczeń dla uprzywilejowanych stacji roboczych, które korzystają z tych kont. Aby uzyskać więcej informacji, zobacz [Przewodnik wdrażania funkcji Azure Active Directory](../fundamentals/active-directory-deployment-checklist-p2.md), [Microsoft 365 plan](/microsoft-365/security/office-365-security/security-roadmap)i [Zabezpieczanie planu dostępu uprzywilejowanego](/windows-server/identity/securing-privileged-access/securing-privileged-access)).

## <a name="why-use-dedicated-workstations"></a>Dlaczego warto korzystać z dedykowanych stacji roboczych?

Chociaż istnieje możliwość dodania zabezpieczeń do istniejącego urządzenia, lepiej zacząć od bezpiecznej podstawy. Aby umieścić swoją organizację w najlepszej pozycji, aby zachować wysoki poziom zabezpieczeń, Zacznij od znanego urządzenia i zaimplementuj zestaw znanych kontroli zabezpieczeń.

Rosnąca liczba wektorów ataków za pośrednictwem poczty e-mail i przeglądania sieci Web sprawia, że urządzenie może być zaufane. W tym przewodniku przyjęto założenie, że dedykowana stacja robocza jest izolowana od standardowej wydajności, przeglądania i poczty e-mail. Usunięcie wydajności, przeglądania sieci Web i poczty e-mail z urządzenia może mieć negatywny wpływ na wydajność. Jednak takie zabezpieczenia są zwykle akceptowalne w scenariuszach, w których zadania nie wymagają ich jawnie, a ryzyko związane z bezpieczeństwem jest wysokie.

> [!NOTE]
> Przeglądanie sieci Web w tym miejscu odnosi się do ogólnego dostępu do dowolnych witryn sieci Web, które mogą być działaniem wysokiego ryzyka. Takie przeglądanie różni się od używania przeglądarki sieci Web w celu uzyskania dostępu do niewielkiej liczby dobrze znanych administracyjnych witryn sieci Web dla usług takich jak platforma Azure, Microsoft 365, inni dostawcy chmury i aplikacje SaaS.

Strategie zawiera zwiększają bezpieczeństwo przez zwiększenie liczby i typu kontrolek, które pomogą intruzom w uzyskaniu dostępu do poufnych zasobów. Model opisany w tym artykule używa projektowania uprawnień warstwowych i ogranicza uprawnienia administracyjne do określonych urządzeń.

## <a name="supply-chain-management"></a>Zarządzanie łańcuchem dostaw

Kluczową podstawą dla zabezpieczonej stacji roboczej jest rozwiązanie łańcucha dostaw, w którym można używać zaufanej stacji roboczej o nazwie "root of Trust". Technologia, którą należy wziąć pod uwagę w wyborze głównego sprzętu z zaufaniem, powinna obejmować następujące technologie zawarte w nowoczesnych laptopach: 

* [Moduł TPM (TPM) 2,0](/windows-hardware/design/device-experiences/oem-tpm)
* [Szyfrowanie dysków funkcją BitLocker](/windows-hardware/design/device-experiences/oem-bitlocker)
* [Bezpieczny rozruch z interfejsem UEFI](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Sterowniki i oprogramowanie układowe rozpowszechniane za Windows Update](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Wirtualizacja i zasady wymagające WYMUSZONEJ włączone](/windows-hardware/design/device-experiences/oem-vbs)
* [Sterowniki i aplikacje zasady wymagające WYMUSZONEJ — gotowe](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [Ochrona we/wy DMA](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Ochrona systemu](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Nowoczesne wstrzymanie](/windows-hardware/design/device-experiences/modern-standby)

W przypadku tego rozwiązania certyfikat główny zaufania zostanie wdrożony przy użyciu technologii [Microsoft autopilotaż](/windows/deployment/windows-autopilot/windows-autopilot) ze sprzętem spełniającym nowoczesne wymagania techniczne. Aby zabezpieczyć stację roboczą, program autopilotaż umożliwia korzystanie z urządzeń z systemem Windows 10 zoptymalizowanych od producenta OEM. Te urządzenia są w znanym dobrym stanie od producenta. Zamiast odtwarzania obrazu potencjalnie niezabezpieczonego urządzenia, autopilotaż może przekształcić urządzenie z systemem Windows w stan "gotowe do pracy". Stosuje on ustawienia i zasady, instaluje aplikacje, a nawet zmienia wersję systemu Windows 10. Na przykład funkcja autopilotażu może zmienić instalację systemu Windows w systemie Windows 10 Pro do systemu Windows 10 Enterprise, aby mogła korzystać z zaawansowanych funkcji.

![Bezpieczeństwo poziomów stacji roboczej](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Role i profile urządzeń

Te wskazówki odnoszą się do kilku profilów zabezpieczeń i ról, które mogą pomóc w tworzeniu bardziej bezpiecznych rozwiązań dla użytkowników, deweloperów i pracowników działu IT. Te profile równoważą użyteczność i ryzyko dla typowych użytkowników, którzy mogą korzystać z zwiększonej lub zabezpieczonej stacji roboczej. Konfiguracje ustawień podane w tym miejscu są oparte na przyjętych standardach branżowych. Wskazówki te pokazują, jak zabezpieczyć system Windows 10 i zmniejszyć ryzyko związane z naruszeniem bezpieczeństwa urządzeń lub użytkowników. Aby skorzystać z nowoczesnej technologii sprzętowej i głównego urządzenia z zaufaniem, będziemy używać [zaświadczanie o kondycji urządzenia](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), które jest włączone od profilu **wysokiego poziomu zabezpieczeń** . Ta funkcja jest dostępna w celu zapewnienia, że osoby atakujące nie mogą być trwałe podczas wczesnego rozruchu urządzenia. Robi to za pomocą zasad i technologii, które ułatwiają zarządzanie funkcjami zabezpieczeń i zagrożeniami.
![Bezpieczeństwo poziomów stacji roboczej](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Zabezpieczenia podstawowe** — zarządzana, standardowa stacja robocza zapewnia dobry punkt wyjścia dla większości i małych firm. Te urządzenia są zarejestrowane w usłudze Azure AD i zarządzane za pomocą usługi Intune. Ten profil pozwala użytkownikom na uruchamianie dowolnych aplikacji i przeglądanie dowolnej witryny sieci Web. Rozwiązanie chroniące przed złośliwym oprogramowaniem, takie jak [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) , powinno być włączone.

* **Ulepszone zabezpieczenia** — to na poziomie tego, chronione rozwiązanie jest dobre dla użytkowników domowych, małych użytkowników i deweloperów.

   Ulepszona stacja robocza to oparta na zasadach Metoda zwiększania bezpieczeństwa profilu niskiego poziomu zabezpieczeń. Zapewnia ona bezpieczny sposób pracy z danymi klientów, a także korzysta z narzędzi do produktywności, takich jak poczta e-mail i przeglądanie w sieci Web. Za pomocą zasad inspekcji i usługi Intune można monitorować rozszerzoną stację roboczą w celu zachowania użytkowników i użycia profilów. Ulepszony profil stacji roboczej można wdrożyć za pomocą skryptu Windows10 (1809) i korzystać z zaawansowanej ochrony przed złośliwym oprogramowaniem przy użyciu funkcji [zaawansowanej ochrony przed zagrożeniami (ATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection).

* **Wysoki poziom zabezpieczeń** — najbardziej skutecznym sposobem na zmniejszenie podatności na ataki stacji roboczej jest usunięcie możliwości samodzielnego administrowania stacją roboczą. Usuwanie lokalnych praw administracyjnych to krok, który zwiększa bezpieczeństwo, ale może mieć wpływ na wydajność, jeśli został zaimplementowany nieprawidłowo. Profil wysokiego poziomu zabezpieczeń jest oparty na ulepszonym profilu zabezpieczeń i ma jedną znaczną zmianę: usunięcie lokalnego administratora. Ten profil jest przeznaczony dla użytkowników z wysokim profilem: dyrektorzy, płace i użytkownicy danych poufnych, osoby zatwierdzające usługi i procesy.

   Użytkownik o wysokim poziomie zabezpieczeń wymaga bardziej kontrolowanego środowiska, chociaż nadal mogą wykonywać działania, takie jak poczta e-mail i przeglądanie w Internecie, przy użyciu prostego środowiska do użycia. Użytkownicy oczekują funkcji, takich jak pliki cookie, Ulubione i inne skróty do pracy. Jednak Ci użytkownicy mogą nie wymagać możliwości modyfikacji ani debugowania urządzenia. Nie muszą również instalować sterowników. Profil wysokiego poziomu zabezpieczeń jest wdrażany przy użyciu skryptu High Security-Windows10 (1809).

* **Wyspecjalizowane** — osoby atakujące są deweloperami i administratorami IT, ponieważ mogą zmieniać systemy interesujące osoby atakujące. Wyspecjalizowana stacja robocza rozszerza zasady dotyczące wysokiej zabezpieczeń stacji roboczej przez Zarządzanie aplikacjami lokalnymi i ograniczanie witryn sieci Web. Ogranicza to również funkcje produktywności wysokiego ryzyka, takie jak ActiveX, Java, wtyczki przeglądarki i inne formanty systemu Windows. Ten profil jest wdrażany za pomocą skryptu SecurityBaseline DeviceConfiguration_NCSC-Windows10 (1803).

* **Zabezpieczony** — osoba atakująca, która narusza konto administracyjne, może spowodować znaczącą szkodę biznesową przez kradzież danych, zmianę danych lub przerwanie działania usługi. W tym stanie zaostrzonym stacja robocza włącza wszystkie mechanizmy kontroli zabezpieczeń i zasady ograniczające bezpośrednią kontrolę nad zarządzaniem aplikacjami lokalnymi. Zabezpieczona stacja robocza nie ma narzędzi do zwiększania produktywności, dzięki czemu urządzenie jest trudniejsze do złamania. Blokuje najbardziej typowy wektor dla ataków wyłudzania informacji: wiadomości e-mail i mediów społecznościowych. Bezpieczną stację roboczą można wdrożyć za pomocą skryptu SecurityBaseline Secure Workstation-Windows10 (1809).

   ![Zabezpieczona stacja robocza](./media/concept-azure-managed-workstation/secure-workstation.png)

   Bezpieczna stacja robocza zapewnia administratorowi z zaostrzoną stacją roboczą, która ma jasno kontrolę aplikacji i ochronę aplikacji. Stacja robocza wykorzystuje funkcję Credential Guard, funkcję Device Guard i funkcję Exploit Guard do ochrony hosta przed złośliwym zachowaniem. Wszystkie dyski lokalne również są szyfrowane za pomocą funkcji BitLocker.

* **Izolowany** — ten niestandardowy scenariusz w trybie offline reprezentuje skrajne zakończenie spektrum. W tym przypadku nie ma żadnych skryptów instalacyjnych. Może być konieczne zarządzanie funkcją o krytycznym znaczeniu dla firmy, która wymaga nieobsługiwanego lub nieaktualnego starszego systemu operacyjnego. Na przykład linia produkcyjna o wysokiej wartości lub system pomocy technicznej. Ze względu na to, że zabezpieczenia są krytyczne i usługi w chmurze są niedostępne, można je zarządzać i aktualizować ręcznie lub za pomocą izolowanej architektury Active Directory lasu, takiej jak środowisko administracyjne zwiększonych zabezpieczeń (ESAE). W takich sytuacjach należy rozważyć usunięcie wszystkich dostępu z wyjątkiem podstawowych testów kondycji usługi Intune i usługi ATP.

   * [Wymagania dotyczące komunikacji sieciowej usługi Intune](/intune/network-bandwidth-use)
   * [Wymagania dotyczące komunikacji sieciowej ATP](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Następne kroki

[Wdrożenie bezpiecznej stacji roboczej zarządzanej przez platformę Azure](howto-azure-managed-workstation.md).