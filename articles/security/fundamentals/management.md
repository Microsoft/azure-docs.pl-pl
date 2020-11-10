---
title: Poprawianie bezpieczeństwa zdalnego zarządzania na platformie Azure | Microsoft Docs
description: W tym artykule omówiono procedurę rozszerzania zabezpieczeń zdalnego zarządzania podczas administrowania środowiskami Microsoft Azure, w tym usługami w chmurze, maszynami wirtualnymi i aplikacjami niestandardowymi.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 2431feba-3364-4a63-8e66-858926061dd3
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2020
ms.author: terrylan
ms.openlocfilehash: 779330d7881040026f45a031f95f44d770f39a56
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412769"
---
# <a name="security-management-in-azure"></a>Zarządzanie zabezpieczeniami na platformie Azure
Subskrybenci platformy Azure mogą zarządzać środowiskami chmury przy użyciu wielu urządzeń, łącznie ze stacjami roboczymi do zarządzania, komputerami deweloperów, a nawet urządzeniami uprzywilejowanych użytkowników końcowych, którzy mają uprawnienia specyficzne dla zadania. W niektórych przypadkach funkcje administracyjne są wykonywane za pośrednictwem konsol sieci Web, takich jak [Azure Portal](https://azure.microsoft.com/features/azure-portal/). W innych przypadkach mogą istnieć bezpośrednie połączenia z platformą Azure z systemów lokalnych za pośrednictwem wirtualnych sieci prywatnych (VPN), usług terminalowych, protokołów aplikacji klienckich lub (programowo) interfejsu API zarządzania usługami Azure (SMAPI, Service Management API). Ponadto punkty końcowe klienta mogą być przyłączone do domeny lub odizolowane i niezarządzane (np. tablety lub smartfony).

Liczne możliwości uzyskania dostępu i zarządzania zapewniają bogaty zestaw opcji, jednak mogą spowodować podwyższenie ryzyka związanego z wdrożeniem w chmurze. Może to utrudniać śledzenie i inspekcję czynności administracyjnych oraz zarządzanie nimi. To zróżnicowanie może również wprowadzać zagrożenia bezpieczeństwa związane z nieuregulowanym dostępem do punktów końcowych klienta używanych do zarządzania usługami w chmurze. Użycie ogólnych lub osobistych stacji roboczych do opracowywania infrastruktury i zarządzania nią powoduje, że zagrożenia mogą nadchodzić z nieprzewidywalnych kierunków, na przykład podczas przeglądania sieci Web (na przykład ataki za pośrednictwem używanych witryn) lub korzystania z poczty e-mail (na przykład techniki socjotechniczne i wyłudzanie informacji).

![Diagram przedstawiający różne sposoby instalowania ataków przez zagrożenie.](./media/management/typical-management-network-topology.png)

Zagrożenie atakami jest większe w środowisku tego typu, ponieważ utrudnione jest skonstruowanie zasad i mechanizmów zabezpieczeń umożliwiających odpowiednie zarządzanie dostępem do interfejsów Azure (na przykład SMAPI) z bardzo różnorodnych punktów końcowych.

### <a name="remote-management-threats"></a>Zagrożenia związane ze zdalnym zarządzaniem
Osoby atakujące często usiłują uzyskać uprzywilejowany dostęp przez uzyskanie poświadczeń konta (na przykład w wyniku ataków siłowych w celu odkrycia hasła, wyłudzenia informacji lub przechwycenia poświadczeń) albo podstępne spowodowanie uruchomienia szkodliwego kodu przez użytkownika (na przykład pobieranego automatycznie ze szkodliwych witryn sieci Web lub pochodzącego ze szkodliwych załączników wiadomości e-mail). W zdalnie zarządzanym środowisku chmury naruszenia zabezpieczeń kont mogą powodować podwyższenie ryzyka za względu na możliwość uzyskania dostępu z dowolnego miejsca i w dowolnej chwili.

Nawet w przypadku ścisłej kontroli kont głównych administratorów luki w strategii zabezpieczeń mogą być wykorzystywane przy użyciu kont użytkowników niższego poziomu. Brak odpowiedniego szkolenia w zakresie zabezpieczeń również może prowadzić do naruszenia zabezpieczeń na skutek przypadkowego ujawnienia lub eksponowania informacji o koncie.

Jeśli stacja robocza użytkownika jest używana również do wykonywania zadań administracyjnych, zabezpieczenia mogą zostać naruszone w wielu różnych punktach. Przykładem może być przeglądanie sieci Web przez użytkownika, używanie narzędzi innych firm lub narzędzi typu open-source albo otwarcie szkodliwego pliku dokumentu zawierającego konia trojańskiego.

Zazwyczaj większość ataków ukierunkowanych, których wynikiem jest naruszenie zabezpieczeń danych, polega na wykorzystaniu luk w zabezpieczeniach przeglądarek i wtyczek (takich jak Flash, PDF i Java) lub ukierunkowanego wyłudzania informacji (poczta e-mail) na komputerach stacjonarnych. Te komputery mogą mieć uprawnienia na poziomie administracyjnym lub serwisowym, umożliwiające dostęp do działających serwerów lub urządzeń sieciowych i wykonanie operacji, gdy są one używane do opracowywania innych elementów zawartości lub zarządzania nimi.

### <a name="operational-security-fundamentals"></a>Podstawy bezpieczeństwa operacyjnego
Aby lepiej zabezpieczyć zarządzanie i operacje, można zminimalizować możliwości ataku na klienta, zmniejszając liczbę możliwych punktów wejścia. Można to zrobić za pomocą zasad zabezpieczeń „podział obowiązków” i „podział środowisk”.

Izolowanie ważnych funkcji umożliwia zmniejszenie prawdopodobieństwa, że błąd na jednym z poziomów spowoduje naruszenie zabezpieczeń na innym poziomie. Przykłady:

* Zadania administracyjne nie powinny być łączone z działaniami, które mogą prowadzić do naruszenia zabezpieczeń (na przykład zainfekowanie serwera infrastruktury przez złośliwe oprogramowanie w wiadomości e-mail administratora).
* Stacja robocza używana do wykonywania ważnych operacji nie powinna być używana do wykonywania czynności związanych z wysokim ryzykiem, takich jak przeglądanie Internetu.

Ogranicz obszar systemu narażony na ataki, usuwając zbędne oprogramowanie. Przykład:

* Nie instaluj klienta poczty e-mail ani innych aplikacji biurowych na standardowych stacjach roboczych, używanych do wykonywania zadań administracyjnych, świadczenia pomocy technicznej lub programowania, jeśli dany komputer jest przeznaczony przede wszystkim do zarządzania usługami w chmurze.

W przypadku systemów klienckich, które mają dostęp z uprawnieniami administratora do składników infrastruktury, należy stosować możliwie najbardziej rygorystyczne zasady, aby ograniczyć zagrożenie bezpieczeństwa. Przykłady:

* Zasady zabezpieczeń mogą zawierać ustawienia zasad grupy, które odmawiają otwartego dostępu do Internetu przy użyciu danego urządzenia i wymuszają stosowanie restrykcyjnej konfiguracji zapory.
* Korzystanie z sieci VPN z zabezpieczeniami protokołu internetowego (IPSec), jeśli wymagany jest bezpośredni dostęp.
* Skonfigurowanie oddzielnych domen usługi Active Directory na potrzeby zarządzania i programowania.
* Izolowanie i filtrowanie ruchu sieciowego stacji roboczej używanej do zarządzania.
* Korzystanie z oprogramowania chroniącego przed złośliwym kodem.
* Zaimplementowanie uwierzytelniania wieloskładnikowego, aby ograniczyć ryzyko związane z kradzieżą poświadczeń.

Konsolidacja zasobów umożliwiających dostęp i eliminacja niezarządzanych punktów końcowych dodatkowo upraszcza zadania związane z zarządzaniem.

### <a name="providing-security-for-azure-remote-management"></a>Zapewnianie bezpieczeństwa zdalnego zarządzania na platformie Azure
Platforma Azure zapewnia mechanizmy zabezpieczeń ułatwiające administratorom zarządzanie usługami w chmurze i maszynami wirtualnymi Azure. Te mechanizmy to m.in.:

* Uwierzytelnianie i [Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Monitorowanie, rejestrowanie i inspekcje.
* Certyfikaty i szyfrowanie komunikacji.
* Portal zarządzania w sieci Web.
* Filtrowanie pakietów sieciowych.

Dzięki konfiguracji zabezpieczeń po stronie klienta i wdrożeniu centrum danych bramy zarządzania można ograniczyć i monitorować dostęp administratorów do aplikacji i danych w chmurze.

> [!NOTE]
> Zastosowanie niektórych zaleceń zamieszczonych w tym artykule może spowodować zwiększenie użycia danych, sieci lub zasobów obliczeniowych i podwyższenie kosztów licencji lub subskrypcji.
>
>

## <a name="hardened-workstation-for-management"></a>Stacja robocza do zarządzania ze wzmocnionymi zabezpieczeniami
Celem wzmocnienia zabezpieczeń stacji roboczej jest wyeliminowanie wszystkich funkcji poza niezbędnymi do działania w celu minimalizacji możliwości ataku. Wzmocnienie zabezpieczeń systemu obejmuje minimalizację liczby zainstalowanych usług i aplikacji, ograniczenie uruchamiania aplikacji i dostępu do sieci do niezbędnego poziomu oraz nieustanne aktualizowanie systemu. Ponadto wzmocnienie zabezpieczeń stacji roboczej do zarządzania powoduje oddzielenie działań i narzędzi administracyjnych od innych zadań użytkowników końcowych.

W lokalnym środowisku przedsiębiorstwa można ograniczyć obszar infrastruktury fizycznej narażony na ataki, korzystając z dedykowanych sieci do zarządzania, kart dostępu do serwerowni i stacji roboczych działających w chronionych obszarach sieci. W chmurowym lub hybrydowym modelu IT zapewnienie większego bezpieczeństwa usług zarządzania może być bardziej złożonym zadaniem ze względu na brak fizycznego dostępu do zasobów informatycznych. W przypadku implementowania rozwiązań w zakresie ochrony wymagane jest rozważne konfigurowanie oprogramowania oraz stosowanie procesów zorientowanych na bezpieczeństwo i kompleksowych zasad.

Minimalizując użycie oprogramowania i poziom jego uprawnień oraz blokując dostęp do stacji roboczej używanej do zarządzania chmurą (i opracowywania aplikacji), można ograniczyć ryzyko naruszenia zabezpieczeń dzięki standaryzacji zdalnego zarządzania i środowisk deweloperskich. Konfiguracja zapewniająca większe bezpieczeństwo stacji roboczej ułatwia zapobieganie naruszeniu zabezpieczeń kont używanych do zarządzania najważniejszymi zasobami chmury przez eliminację wielu typowych możliwości ataku z wykorzystaniem złośliwego oprogramowania i luk w zabezpieczeniach. W szczególności można użyć funkcji [Windows AppLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759117(v=ws.11)) i technologii Hyper-V w celu kontrolowania i izolowania zachowania systemu klienta oraz ograniczenia zagrożeń związanych między innymi z pocztą e-mail lub przeglądaniem Internetu.

Wzmocnienie zabezpieczeń stacji roboczej gwarantuje, że administrator korzysta z konta użytkownika standardowego (które blokuje wykonywanie na poziomie administratora), a skojarzone aplikacje są kontrolowane przez listę dozwolonych aplikacji. Podstawowe zasady wzmacniania zabezpieczeń stacji roboczej są następujące:

* Aktywne skanowanie i stosowanie poprawek. Wdrożenie oprogramowania chroniącego przez złośliwym kodem, regularne skanowanie w celu wykrycia luk w zabezpieczeniach i szybkie aktualizowanie wszystkich stacji roboczych przy użyciu najnowszych aktualizacji zabezpieczeń.
* Ograniczenie funkcjonalności. Odinstalowanie zbędnych aplikacji i wyłączenie zbędnych usług (startowych).
* Wzmocnienie zabezpieczeń sieci. Użycie reguł zapory systemu Windows do akceptowania tylko prawidłowych adresów IP, portów i adresów URL związanych z zarządzaniem platformą Azure. Upewnienie się, że zdalne połączenia przychodzące do stacji roboczej są również blokowane.
* Ograniczenie wykonywania. Zezwalanie na uruchamianie tylko wstępnie zdefiniowanych plików wykonywalnych wymaganych do zarządzania (zwane „odmową domyślną”). Należy domyślnie odmawiać użytkownikom uprawnienia do uruchamiania programów, które nie zostały uwzględnione na liście dozwolonych programów.
* Stosowanie najniższych uprawnień. Użytkownicy stacji roboczej używanej do zarządzania nie powinni mieć żadnych uprawnień administracyjnych na tym komputerze. Dzięki temu nie mogą oni zmieniać konfiguracji systemu ani plików systemowych, celowo lub przypadkowo.

Wszystkie te zasady można wymusić przy użyciu [obiektów zasad grupy](../../active-directory-domain-services/manage-group-policy.md) w usłudze Active Directory Domain Services (AD DS), stosując je za pośrednictwem (lokalnej) domeny zarządzania do wszystkich kont związanych z zarządzaniem.

### <a name="managing-services-applications-and-data"></a>Zarządzanie usługami, aplikacjami i danymi
Usługi w chmurze Azure są konfigurowane przy użyciu witryny Azure Portal, interfejsu SMAPI, interfejsu wiersza polecenia programu Windows PowerShell lub aplikacji niestandardowej, która korzysta z tych interfejsów RESTful. Z tych mechanizmów korzystają usługi takie jak Azure Active Directory (Azure AD), Azure Storage, Azure Websites, Azure Virtual Network i inne.

Aplikacje wdrożone na maszynie wirtualnej zapewniają własne narzędzia i interfejsy klienckie zgodnie z potrzebami, takie jak program Microsoft Management Console (MMC), konsolę zarządzania przedsiębiorstwem (na przykład Microsoft System Center lub Windows Intune) albo inne aplikacje do zarządzania (na przykład Microsoft SQL Server Management Studio). Te narzędzia zazwyczaj znajdują się w środowisku przedsiębiorstwa lub sieci klienta. Mogą być one zależne od określonych protokołów sieciowych, takich jak protokół Remote Desktop Protocol (RDP), wymagających stanowych połączeń bezpośrednich. Niektóre mogą mieć interfejsy sieci Web, które nie powinny być jawnie publikowane ani dostępne za pośrednictwem Internetu.

Dostęp do infrastruktury i zarządzania usługami platformy Azure można ograniczyć, korzystając z [uwierzytelniania wieloskładnikowego](../../active-directory/authentication/concept-mfa-howitworks.md), [certyfikatów zarządzania X.509](/archive/blogs/azuresecurity/certificate-management-in-azure-dos-and-donts) i reguł zapory. Witryna Azure Portal i interfejs SMAPI wymagają protokołu Transport Layer Security (TLS). Usługi i aplikacje wdrażane na platformie Azure wymagają jednak stosowania zabezpieczeń odpowiednich dla aplikacji. Te mechanizmy często można zapewnić łatwiej przy użyciu standardowej konfiguracji stacji roboczej ze wzmocnionymi zabezpieczeniami.

### <a name="management-gateway"></a>Brama zarządzania
Aby scentralizować cały dostęp administracyjny oraz uprościć monitorowanie i rejestrowanie, w sieci lokalnej można wdrożyć dedykowany serwer [bramy usług pulpitu zdalnego](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560672(v=ws.10)) połączony ze środowiskiem platformy Azure.

Brama usług pulpitu zdalnego jest opartą na zasadach usługą serwera proxy RDP wymuszającą wymagania dotyczące zabezpieczeń. Zaimplementowanie bramy usług pulpitu zdalnego wraz z ochroną dostępu do sieci (NAP, Network Access Protection) systemu Windows Server gwarantuje, że połączenia mogą ustanawiać tylko klienci spełniający określone kryteria kondycji zabezpieczeń określone przez obiekty zasad grupy w usłudze Active Directory Domain Services. Dodatkowo:

* Dla bramy usług pulpitu zdalnego aprowizuj [certyfikat zarządzania Azure](/previous-versions/azure/gg551722(v=azure.100)), aby był to jedyny host uprawniony do dostępu do witryny Azure Portal.
* Bramę usług pulpitu zdalnego przyłącz do tej samej [domeny zarządzania](/previous-versions/windows/it-pro/windows-2000-server/bb727085(v=technet.10)), do której są przyłączone stacje robocze administratorów. Jest to konieczne, gdy jest używana sieć VPN między lokacjami z zabezpieczeniami IPsec lub usługa ExpressRoute w domenie z jednokierunkową relacją zaufania do usługi Azure AD, albo w przypadku federacji poświadczeń między lokalnym wystąpieniem usług AD DS a usługą Azure AD.
* Skonfiguruj [zasady autoryzacji połączeń klientów](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753324(v=ws.11)), aby umożliwić bramie usług pulpitu zdalnego sprawdzanie, czy maszyna kliencka ma prawidłową nazwę (jest przyłączona do domeny) i jest uprawniona do dostępu do witryny Azure Portal.
* Aby zapewnić lepszą ochronę ruchu związanego z zarządzaniem przed podsłuchem i kradzieżą tokenów, używaj zabezpieczeń IPsec dla [Sieci VPN Azure](https://azure.microsoft.com/documentation/services/vpn-gateway/) albo rozważ możliwość utworzenia izolowanego łącza internetowego za pośrednictwem usługi [Azure ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).
* Zapewnij uwierzytelnianie wieloskładnikowe (za pomocą usługi [Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)) lub uwierzytelnianie przy użyciu kart inteligentnych dla administratorów logujących się przy użyciu bramy usług pulpitu zdalnego.
* Skonfiguruj [ograniczenia źródłowych adresów IP](https://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/) lub [sieciowe grupy zabezpieczeń](../../virtual-network/network-security-groups-overview.md) na platformie Azure, aby zminimalizować liczbę punktów końcowych uprawnionych do zarządzania.

## <a name="security-guidelines"></a>Zalecenia dotyczące zabezpieczeń
Ogólnie rzecz biorąc, ulepszanie zabezpieczeń stacji roboczych administratorów używanych do pracy z chmurą jest podobne do rozwiązań stosowanych w przypadku dowolnych lokalnych stacji roboczych (na przykład minimalizacja funkcjonalności i ograniczanie uprawnień). Niektóre unikatowe aspekty zarządzania chmurą są bardziej zbliżone do zdalnego zarządzania lub zarządzania poza pasmem w przedsiębiorstwach. Przykładem może być użycie i inspekcja poświadczeń, rozszerzone zabezpieczenia dostępu zdalnego oraz wykrywanie zagrożeń i podejmowanie działań zaradczych.

### <a name="authentication"></a>Authentication
Korzystając z ograniczeń logowania platformy Azure, można zmniejszyć liczbę źródłowych adresów IP używanych do uzyskiwania dostępu do narzędzi administracyjnych i prowadzić inspekcję żądań dostępu. Aby pomóc platformie Azure identyfikować klientów zarządzania (stacje robocze i/lub aplikacje), można skonfigurować oba typy SMAPI (przy użyciu narzędzi opracowanych przez klienta, takich jak polecenia cmdlet programu Windows PowerShell) i Azure Portal, aby wymagały zainstalowania certyfikatów zarządzania po stronie klienta, oprócz certyfikatów TLS/SSL. Zalecamy również stosowanie uwierzytelniania wieloskładnikowego w przypadku dostępu administratorów.

Niektóre aplikacje lub usługi wdrażane na platformie Azure mogą mieć własne mechanizmy uwierzytelniania zarówno użytkowników końcowych, jak i administratorów, podczas gdy inne wykorzystują w pełnym zakresie usługę Azure AD. W zależności od tego, czy jest dokonywana federacja poświadczeń za pośrednictwem usługi Active Directory Federation Services (AD FS), używana synchronizacja katalogów, czy obsługa kont użytkowników jedynie w chmurze, użycie usługi [Microsoft Identity Manager](/microsoft-identity-manager/) (część usługi Azure AD Premium) ułatwia zarządzanie cyklami życia tożsamości między zasobami.

### <a name="connectivity"></a>Łączność
Dostępnych jest kilka mechanizmów ułatwiających zabezpieczanie połączeń klientów z sieciami wirtualnymi platformy Azure. Dwa z tych mechanizmów — [sieć VPN między lokacjami](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (S2S, site-to-site) i [sieć VPN między punktem a lokacją](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) (P2S, point-to-site) — umożliwiają użycie zabezpieczeń IPsec (S2S) będących standardem branżowym lub protokołu [Secure Socket Tunneling Protocol](/previous-versions/technet-magazine/cc162322(v=msdn.10)) (SSTP) (P2S) do szyfrowania i tunelowania. Gdy ustanawiane jest połączenie platformy Azure z publicznymi zasobami do zarządzania usługami Azure, takimi jak witryna Azure Portal, platforma Azure wymaga protokołu Hypertext Transfer Protocol Secure (HTTPS).

Autonomiczna stacja robocza ze wzmocnionymi zabezpieczeniami, która nie łączy się z platformą Azure za pośrednictwem bramy usług pulpitu zdalnego, powinna używać sieci VPN między punktem a lokacją korzystającej z protokołu SSTP do utworzenia początkowego połączenia z usługą Azure Virtual Network, a następnie ustanawiać połączenie RDP z poszczególnymi maszynami wirtualnymi z poziomu tunelu VPN.

### <a name="management-auditing-vs-policy-enforcement"></a>Inspekcja zarządzania a wymuszanie zasad
Zazwyczaj istnieją dwa podejścia do zabezpieczania procesów związanych z zarządzaniem: inspekcja i wymuszanie zasad. Zastosowanie obu podejść zapewnia kompleksową kontrolę, ale nie zawsze jest możliwe. Ponadto każde podejście jest związane z różnym poziomem ryzyka, kosztem i zasobami wymaganymi do zarządzania zabezpieczeniami, zwłaszcza w kontekście poziomu zaufania do osób i architektur systemów.

Monitorowanie, rejestrowanie i inspekcja stanowią podstawę dla śledzenia i zrozumienia czynności administracyjnych, ale nie zawsze można przeprowadzić szczegółową inspekcję wszystkich akcji ze względu na ilość generowanych danych. Inspekcja skuteczności zasad zarządzania jest jednak najlepszym rozwiązaniem.

Wymuszanie zasad, które obejmuje ścisłą kontrolę dostępu, polega na stosowaniu programowych mechanizmów zarządzających akcjami wykonywanymi przez administratorów i ułatwia zapewnienie stosowania wszystkich możliwych środków ochrony. Rejestrowanie umożliwia potwierdzenie wymuszenia zasad i dokumentowanie tożsamości osób, wykonywanych przez nich czynności oraz lokalizacji i czasu wykonania. Rejestrowanie umożliwia również inspekcję i porównanie informacji dotyczących przestrzegania zasad przez administratorów oraz potwierdzenie wykonania czynności.

## <a name="client-configuration"></a>Konfiguracja klientów
Zalecamy trzy podstawowe konfiguracje stacji roboczych ze wzmocnionymi zabezpieczeniami. Najważniejsze różnice są związane z kosztem, użytecznością i dostępnością przy zachowaniu podobnego profilu zabezpieczeń dla wszystkich opcji. Poniższa tabela zawiera zwięzłe informacje umożliwiające analizę korzyści i zagrożeń związanych z poszczególnymi konfiguracjami. Określenie „komputer firmowy” oznacza standardową konfigurację komputera stacjonarnego wdrażaną dla wszystkich użytkowników w domenie niezależnie od roli.

| Konfiguracja | Korzyści | Wady |
| --- | --- | --- |
| Autonomiczna stacja robocza ze wzmocnionymi zabezpieczeniami |Ściśle kontrolowana stacja robocza |Wyższy koszt dla dedykowanych komputerów stacjonarnych |
| - | Mniejsze ryzyko wykorzystania luk w zabezpieczeniach aplikacji |Większa ilość zasobów wymaganych do zarządzania |
| - | Przejrzysty podział obowiązków | - |
| Komputer firmowy jako maszyna wirtualna |Niższe koszty sprzętu | - |
| - | Podział ról i aplikacji | - |

Stacja robocza ze wzmocnionymi zabezpieczeniami musi być hostem, a nie gościem, bez żadnych składników między systemem operacyjnym hosta a sprzętem. Zgodnie z „zasadą czystego źródła” (zwaną również „bezpiecznym pochodzeniem”) host musi mieć najlepsze zabezpieczenia. W przeciwnym razie stacja robocza ze wzmocnionymi zabezpieczeniami (gość) jest podatna na ataki na system, w którym jest hostowana.

Dodatkowo można oddzielić funkcje administracyjne za pomocą dedykowanych obrazów systemu dla każdej stacji roboczej ze wzmocnionymi zabezpieczeniami, obejmujących tylko narzędzia i uprawnienia wymagane do zarządzania wybranymi aplikacjami na platformie Azure i w chmurze oraz specyficzne lokalne obiekty zasad grupy usług AD DS dla niezbędnych zadań.

W przypadku środowisk IT bez infrastruktury lokalnej (na przykład brak dostępu do lokalnego wystąpienia usług AD DS dla obiektów zasad grupy, ponieważ wszystkie serwery są w chmurze) usługa taka jak [Microsoft Intune](/mem/intune/) może uprościć wdrażanie i konserwację konfiguracji stacji roboczych.

### <a name="stand-alone-hardened-workstation-for-management"></a>Autonomiczna stacja robocza do zarządzania ze wzmocnionymi zabezpieczeniami
W przypadku autonomicznej stacji roboczej ze wzmocnionymi zabezpieczeniami administratorzy mają komputer lub laptop, używany do wykonywania zadań administracyjnych, i oddzielny komputer lub laptop do wykonywania innych zadań. Na stacji roboczej dedykowanej do zarządzania usługami Azure nie trzeba instalować innych aplikacji. Ponadto korzystanie ze stacji roboczych obsługujących moduł [Trusted Platform Module](/previous-versions/windows/it-pro/windows-vista/cc766159(v=ws.10)) (TPM) lub podobną sprzętową technologię kryptograficzną ułatwia uwierzytelnianie urządzeń i zapobieganie niektórym atakom. Moduł TPM może również obsługiwać pełną ochronę woluminów dysku systemowego przy użyciu [szyfrowania dysków funkcją BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)).

W scenariuszu autonomicznej stacji roboczej ze wzmocnionymi zabezpieczeniami (przedstawionym poniżej) lokalne wystąpienie zapory systemu Windows (lub zapory klienta firmy innej niż Microsoft) jest skonfigurowane do blokowania połączeń przychodzących, takich jak połączenia RDP. Administrator może zalogować się do stacji roboczej ze wzmocnionymi zabezpieczeniami i uruchomić sesję RDP, która nawiązuje połączenie z platformą Azure po ustanowieniu połączenia sieci VPN z usługą Azure Virtual Network, ale nie może zalogować się na komputerze firmowym i użyć protokołu RDP do połączenia się z tą stacją roboczą.

![Diagram przedstawiający autonomiczny scenariusz stacji roboczej ze wzmocnionymi zabezpieczeniami.](./media/management/stand-alone-hardened-workstation-topology.png)

### <a name="corporate-pc-as-virtual-machine"></a>Komputer firmowy jako maszyna wirtualna
Jeśli nie można użyć oddzielnej autonomicznej stacji roboczej ze wzmocnionymi zabezpieczeniami z powodu zbyt wysokiego kosztu lub innych problemów, stacja robocza może hostować maszynę wirtualną używaną do wykonywania zadań innych niż administracyjne.

![Diagram przedstawiający zaostrzoną stację roboczą obsługującą maszynę wirtualną w celu wykonywania zadań nieadministracyjnych.](./media/management/hardened-workstation-enabled-with-hyper-v.png)

Aby uniknąć kilku zagrożeń wynikających z użycia pojedynczej stacji roboczej do zarządzania systemami i wykonywania innych codziennych zadań, na stacji roboczej ze wzmocnionymi zabezpieczeniami można wdrożyć maszynę wirtualną z funkcją Hyper-V systemu Windows. Ta maszyna wirtualna może być używana jako komputer firmowy. Środowisko komputera firmowego może pozostać odizolowane od hosta. Umożliwia to ograniczenie obszaru narażonego na ataki i oddzielenie czynności wykonywanych codziennie przez użytkowników (takich jak korzystanie z poczty e-mail) od ważnych zadań administracyjnych.

Maszyna wirtualna komputera firmowego jest uruchomiona w obszarze chronionym i udostępnia aplikacje użytkownika. Host pozostaje „czystym źródłem” i wymusza ścisłe zasady sieci w głównym systemie operacyjnym (na przykład blokowanie dostępu RDP z maszyny wirtualnej).

## <a name="best-practices"></a>Najlepsze rozwiązania
Podczas zarządzania aplikacjami i danymi na platformie Azure należy uwzględnić poniższe dodatkowe zalecenia.

### <a name="dos-and-donts"></a>Zalecenia i zakazy
Nie wolno zakładać, że zabezpieczenie stacji roboczej zwalnia z obowiązku spełnienia innych wymagań dotyczących bezpieczeństwa. Potencjalne ryzyko jest wyższe ze względu na podwyższony poziom uprawnień kont administratorów. W poniższej tabeli przedstawiono przykładowe zagrożenia i alternatywne, bezpieczne praktyki.

| Zakazy | Zalecenia |
| --- | --- |
| Nie wysyłaj wiadomości e-mail z poświadczeniami dotyczącymi dostępu administratora ani innych wpisów tajnych (na przykład protokołu TLS/SSL lub certyfikatów zarządzania) |Zachowaj poufność, przekazując nazwy kont i hasła werbalnie (ale nie nagrywaj ich na poczcie głosowej), wykonując zdalną instalację certyfikatów klienta/serwera (za pośrednictwem sesji zaszyfrowanej), pobierając je z chronionego udziału sieciowego lub przekazując osobiście na nośniku wymiennym. |
| - | Aktywnie zarządzaj cyklami życia certyfikatów zarządzania. |
| Nie przechowuj haseł nieszyfrowanych lub nieprzetworzonych za pomocą funkcji skrótu w magazynie aplikacji (takim jak arkusze kalkulacyjne, witryny programu SharePoint lub udziały plików). |Ustanów zasady zarządzania zabezpieczeniami i zwiększania bezpieczeństwa systemu oraz stosuj je w środowisku deweloperskim. |
| - | Używaj reguł przypinania certyfikatów z zestawu narzędzi [Enhanced Mitigation Experience Toolkit 5.5](https://technet.microsoft.com/security/jj653751), aby zapewnić prawidłowy dostęp do witryn Azure z obsługą protokołów SSL/TLS. |
| Nie udostępniaj kont i haseł innym administratorom ani nie używaj ponownie tych samych haseł dla wielu kont użytkowników lub usług, zwłaszcza związanych z mediami społecznościowymi lub innymi działaniami niezwiązanymi z administrowaniem. |Utwórz dedykowane konto Microsoft do zarządzania subskrypcją Azure — konto, które nie jest używane na potrzeby osobistej poczty e-mail. |
| Nie przesyłaj plików konfiguracji pocztą e-mail. |Profile i pliki konfiguracji należy instalować z zaufanego źródła (na przykład zaszyfrowanego dysku flash USB), a nie przy użyciu mechanizmu, którego zabezpieczenia można łatwo złamać, takiego jak poczta e-mail. |
| Nie używaj słabych lub prostych haseł do logowania. |Wymuszaj zasady silnych haseł, cykle wygaśnięcia (zmiana po pierwszym użyciu), limity czasu konsoli i automatyczne blokowanie kont. Używaj systemu zarządzania hasłami klientów z uwierzytelnianiem wieloskładnikowym do uzyskiwania dostępu do magazynu haseł. |
| Nie zezwalaj na połączenia z Internetu na portach używanych do zarządzania. |Zablokuj porty i adresy IP platformy Azure, aby ograniczyć dostęp do zarządzania. Aby uzyskać więcej informacji, zobacz oficjalny dokument [Azure Network Security](https://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) (Zabezpieczenia sieci platformy Azure). |
| - | Używaj zapór, sieci VPN i ochrony dostępu do sieci dla wszystkich połączeń związanych z zarządzaniem. |

## <a name="azure-operations"></a>Operacje platformy Azure
W związku z obsługą platformy Azure przez firmę Microsoft inżynierowie operacyjni i personel pomocy technicznej, uzyskujący dostęp do systemów produkcyjnych Azure, używają [stacji roboczych ze wzmocnionymi zabezpieczeniami z maszynami wirtualnymi](#stand-alone-hardened-workstation-for-management) aprowizowanymi na nich na potrzeby dostępu do wewnętrznej sieci firmy i aplikacji (takich jak poczta e-mail, intranet itp.). Wszystkie stacje robocze używane do zarządzania są wyposażone w moduł TPM, mają dysk rozruchowy hosta zaszyfrowany za pomocą funkcji BitLocker i są przyłączone do specjalnej jednostki organizacyjnej w podstawowej domenie firmy Microsoft.

Wzmacnianie zabezpieczeń systemu jest wymuszane za pośrednictwem zasad grupy ze scentralizowanymi aktualizacjami oprogramowania. Dla celów związanych z inspekcją i analizą dzienniki zdarzeń (np. zabezpieczeń i funkcji AppLocker) są zbierane ze stacji roboczych używanych do zarządzania i zapisywane w centralnej lokalizacji.

Ponadto do ustanawiania połączeń z siecią produkcyjną Azure są używane dedykowane serwery przeskoku w sieci firmy Microsoft wymagające uwierzytelniania dwuskładnikowego.

## <a name="azure-security-checklist"></a>Lista kontrolna zabezpieczeń platformy Azure
Minimalizacja liczby zadań, które administratorzy mogą wykonywać na stacji roboczej ze wzmocnionymi zabezpieczeniami, ułatwia minimalizację obszaru narażonego na ataki w środowisku projektowania i zarządzania. Skorzystaj z następujących technologii ułatwiających ochronę stacji roboczej ze wzmocnionymi zabezpieczeniami:

* Wzmacnianie zabezpieczeń programu Internet Explorer. Przeglądarka Internet Explorer (lub dowolna inna przeglądarka sieci Web) jest głównym punktem wejścia dla szkodliwego kodu z powodu rozległych interakcji z serwerami zewnętrznymi. Przejrzyj zasady klienta i wymuś uruchamianie w trybie chronionym, co powoduje wyłączenie dodatków i pobierania plików oraz korzystanie z filtrowania [Microsoft SmartScreen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj618329(v=ws.11)). Upewnij się, że ostrzeżenia dotyczące zabezpieczeń są wyświetlane. Korzystaj ze stref internetowych i utwórz listę zaufanych witryn, dla których skonfigurowano odpowiednie zabezpieczenia. Zablokuj wszystkie pozostałe witryny i kod w przeglądarce, np. ActiveX i Java.
* Użytkownik standardowy. Uruchamianie jako użytkownik standardowy jest związane z kilkoma korzyściami, z których największą jest utrudnienie kradzieży poświadczeń administratora przy użyciu złośliwego oprogramowania. Ponadto konto użytkownika standardowego nie ma podwyższonych uprawnień w głównym systemie operacyjnym, a wiele opcji konfiguracji i interfejsów API jest domyślnie zablokowanych.
* Funkcja AppLocker. Korzystając z funkcji [AppLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619725(v=ws.10)), można ograniczyć programy i skrypty, które użytkownicy mogą uruchamiać. Funkcję AppLocker można uruchomić w trybie inspekcji lub wymuszania. Domyślnie funkcja AppLocker ma regułę zezwalającą użytkownikom, którzy otrzymali token administratora, na uruchamianie całego kodu na kliencie. Tę regułę wprowadzono, aby zapobiec blokowaniu własnych kont przez administratorów. Dotyczy ona tylko tokenów z podwyższonym poziomem uprawnień. Zobacz też integralność kodu w ramach [podstawowych zabezpieczeń](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd348705(v=ws.10)) systemu Windows Server.
* Podpisywanie kodu. Podpisywanie kodu wszystkich narzędzi i skryptów używanych przez administratorów zapewnia mechanizm wdrażania zasad blokowania aplikacji, którym można zarządzać. Skróty nie skalują się przy szybkich zmianach kodu, a ścieżki plików nie zapewniają wysokiego poziomu bezpieczeństwa. Połącz reguły funkcji AppLocker z [zasadami wykonywania](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)) programu PowerShell, które zezwalają na [wykonywanie](/powershell/module/microsoft.powershell.security/set-executionpolicy) tylko określonego podpisanego kodu i określonych podpisanych skryptów.
* Zasady grupy. Utwórz globalne zasady administracyjne stosowane do każdej stacji roboczej w domenie używanej do zarządzania (i zablokuj dostęp ze wszystkich pozostałych) oraz do kont użytkowników uwierzytelnionych na tych stacjach roboczych.
* Bezpieczna aprowizacja. Chroń podstawowy obraz stacji roboczej ze wzmocnionymi zabezpieczeniami przed nieautoryzowaną modyfikacją. Do przechowywania obrazów, maszyn wirtualnych i skryptów stosuj środki ochrony, takie jak szyfrowanie i izolacja, oraz ogranicz dostęp (można użyć procesu zaewidencjonowania/wyewidencjonowania podlegającego inspekcji).
* Stosowanie poprawek. Zachowaj spójną konfigurację (lub utwórz oddzielne obrazy dla projektowania, działań operacyjnych i innych zadań administracyjnych), regularnie skanuj w celu wykrycia zmian i złośliwego oprogramowania, aktualizuj konfigurację i uaktywniaj komputery tylko wtedy, gdy są potrzebne.
* Szyfrowanie. Upewnij się, że stacje robocze używane do zarządzania są wyposażone w moduł TPM umożliwiający bardziej bezpieczne korzystanie z [systemu szyfrowania plików](/previous-versions/tn-archive/cc700811(v=technet.10)) (EFS, Encrypting File System) i funkcji BitLocker.
* Zarządzanie. Używaj obiektów zasad grupy usług AD DS do kontrolowania wszystkich interfejsów systemu Windows administratorów, takich jak udostępnianie plików. Uwzględnij stacje robocze używane do zarządzania w procesach inspekcji, monitorowania i rejestrowania. Śledź dostęp i używanie przez wszystkich administratorów i deweloperów.

## <a name="summary"></a>Podsumowanie
Użycie konfiguracji stacji roboczej ze wzmocnionymi zabezpieczeniami do administrowania usługami w chmurze, usługą Virtual Machines i aplikacjami platformy Azure może pomóc w uniknięciu licznych zagrożeń związanych ze zdalnym zarządzaniem newralgiczną infrastrukturą informatyczną. Zarówno platforma Azure, jak i system Windows zapewniają mechanizmy, które można wdrożyć w celu ochrony i kontrolowania komunikacji, uwierzytelniania i zachowania klientów.

## <a name="next-steps"></a>Następne kroki
Dostępne są następujące zasoby zawierające bardziej ogólne informacje dotyczące platformy Azure i powiązanych usług firmy Microsoft, oprócz szczegółowych zagadnień omówionych w tym dokumencie:

* [Securing Privileged Access (Zabezpieczanie uprzywilejowanego dostępu)](/windows-server/identity/securing-privileged-access/securing-privileged-access) — szczegółowe informacje techniczne dotyczące projektowania i konfigurowania bezpiecznej administracyjnej stacji roboczej do zarządzania platformą Azure
* [Microsoft Trust Center (Centrum zaufania Microsoft)](https://microsoft.com/en-us/trustcenter/cloudservices/azure) — dowiedz się więcej na temat funkcji platformy Azure umożliwiających ochronę sieci szkieletowej Azure i obciążeń uruchamianych na platformie Azure
* [Microsoft Security Response Center (Centrum zabezpieczeń firmy Microsoft)](https://www.microsoft.com/msrc) — służące do zgłaszania luk w zabezpieczeniach oprogramowania firmy Microsoft, w tym problemów z platformą Azure — w tym celu można również korzystać z adresu e-mail [secure@microsoft.com](mailto:secure@microsoft.com)