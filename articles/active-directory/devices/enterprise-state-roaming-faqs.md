---
title: Często zadawane pytania Enterprise State Roaming — Azure Active Directory
description: Często zadawane pytania dotyczące ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb889298a09c30a629c69442ebf31bc735af31d1
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173128"
---
# <a name="settings-and-data-roaming-faq"></a>Roaming ustawień i danych — często zadawane pytania

W tym artykule znajdują się odpowiedzi na pytania, które mogą być dostępne dla administratorów IT dotyczących ustawień i synchronizacji danych aplikacji.

## <a name="what-data-roams"></a>Jakie dane są przekazywane?

**Ustawienia systemu Windows**: ustawienia komputera wbudowane w system operacyjny Windows. Zazwyczaj są to ustawienia, które spersonalizują Twój komputer i obejmują następujące szerokie Kategorie:

* *Motyw*, który obejmuje funkcje, takie jak kompozycja pulpitu i ustawienia paska zadań.
* *Ustawienia programu Internet Explorer*, w tym ostatnio otwierane karty i elementy ulubione.
* *Ustawienia przeglądarki Microsoft Edge*, takie jak Ulubione i listy do czytania.
* *Hasła*, w tym hasła internetowe, profile Wi-Fi i inne.
* *Preferencje językowe*, które obejmują ustawienia układów klawiatury, języka systemu, daty i godziny itd.
* *Funkcje ułatwień dostępu*, takie jak motyw o wysokim kontraście, Narrator i Lupa.
* *Inne ustawienia systemu Windows*, takie jak ustawienia myszy.

> [!NOTE]
> Ten artykuł ma zastosowanie do starszej przeglądarki opartej na języku HTML Microsoft Edge, która została uruchomiona z systemem Windows 10 w lipcu 2015. Artykuł nie ma zastosowania do nowej przeglądarki opartej na formacie chromu Microsoft Edge wydanej 15 stycznia 2020. Aby uzyskać więcej informacji na temat zachowania synchronizacji dla nowej przeglądarki Microsoft Edge, zobacz artykuł [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

**Dane aplikacji**: Aplikacje uniwersalne systemu Windows mogą zapisywać dane ustawień w folderze mobilnym, a wszystkie dane zapisane w tym folderze zostaną automatycznie zsynchronizowane. Aby skorzystać z tej możliwości, można zaprojektować aplikację przy użyciu poszczególnych deweloperów aplikacji. Aby uzyskać więcej informacji na temat sposobu tworzenia uniwersalnej aplikacji systemu Windows, która korzysta z roamingu, zobacz blog [interfejsu API magazynu AppData](/windows/uwp/design/app-settings/store-and-retrieve-app-data) i [Windows 8 AppData mobilnego dewelopera](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/).

## <a name="what-account-is-used-for-settings-sync"></a>Jakie konto jest używane na potrzeby synchronizacji ustawień?

W Windows 8.1 ustawienia synchronizują zawsze używane odbiorcy konta Microsoft. Użytkownicy korporacyjni mieli możliwość łączenia konto Microsoft z Active Directory konta domeny w celu uzyskania dostępu do synchronizacji ustawień. W systemie Windows 10 ta podłączona funkcja konto Microsoft jest zastępowana przez podstawową/pomocniczą strukturę konta.

Konto podstawowe jest zdefiniowane jako konto używane do logowania się do systemu Windows. Może to być konto Microsoft, konto Azure Active Directory (Azure AD), konto Active Directory lokalnego lub konto lokalne. Oprócz konta podstawowego użytkownicy systemu Windows 10 mogą dodać do urządzenia co najmniej jedno konto chmury dodatkowej. Konto pomocnicze to zwykle konto Microsoft, konto usługi Azure AD lub inne konto, takie jak Gmail lub Facebook. Te konta pomocnicze zapewniają dostęp do dodatkowych usług, takich jak logowanie jednokrotne i Sklep Windows, ale nie mają możliwości synchronizacji ustawień.

W systemie Windows 10 do synchronizacji ustawień można używać tylko konta podstawowego dla tego urządzenia (zobacz [Jak mogę upgrade from konto Microsoft Settings Sync w systemie Windows 8 do synchronizacji ustawień usługi Azure AD w systemie Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Dane nigdy nie są mieszane między różnymi kontami użytkowników na urządzeniu. Istnieją dwie reguły synchronizacji ustawień:

* Ustawienia systemu Windows będą zawsze przenoszone do konta głównego.
* Dane aplikacji będą znakowane przy użyciu konta używanego do uzyskiwania aplikacji. Synchronizowane będą tylko aplikacje otagowane przy użyciu konta podstawowego. Znakowanie własności aplikacji jest określane, gdy aplikacja jest ładowana bezpośrednio przez Sklep Windows lub zarządzanie urządzeniami przenośnymi (MDM).

Jeśli nie można zidentyfikować właściciela aplikacji, będzie ona przenoszona z kontem podstawowym. Jeśli urządzenie zostanie uaktualnione z systemu Windows 8 lub Windows 8.1 do systemu Windows 10, wszystkie aplikacje zostaną oznaczone jako pobrane przez konto Microsoft. Wynika to z faktu, że większość użytkowników uzyskuje aplikacje za pomocą Sklepu Windows i nie ma pomocy technicznej sklepu Windows dla kont usługi Azure AD przed systemem Windows 10. Jeśli aplikacja zostanie zainstalowana za pośrednictwem licencji w trybie offline, aplikacja zostanie otagowana przy użyciu konta podstawowego na urządzeniu.

> [!NOTE]
> Urządzenia z systemem Windows 10 należące do firmy i połączone z usługą Azure AD nie mogą już łączyć kont Microsoft z kontem domeny. Możliwość łączenia konto Microsoft z kontem domeny i synchronizowania danych użytkownika z konto Microsoft (to oznacza, że konto Microsoft roaming za pośrednictwem funkcji połączonej konto Microsoft i Active Directory) zostanie usunięty z urządzeń z systemem Windows 10, które są przyłączone do podłączonego Active Directory lub środowiska usługi Azure AD.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Jak mogę uaktualnianie konto Microsoft synchronizacji ustawień w systemie Windows 8 do usługi Azure AD ustawienia synchronizacji w systemie Windows 10?

W przypadku przyłączenia do domeny Active Directory z Windows 8.1 z podłączonym konto Microsoft, ustawienia zostaną zsynchronizowane za pomocą konto Microsoft. Po uaktualnieniu do systemu Windows 10 będziesz w dalszym ciągu synchronizować ustawienia użytkownika za pośrednictwem konto Microsoft, o ile jesteś przyłączony do domeny, a domena Active Directory nie łączy się z usługą Azure AD.

Jeśli domena lokalna Active Directory nawiązuje połączenie z usługą Azure AD, urządzenie podejmie próbę synchronizacji ustawień przy użyciu połączonego konta usługi Azure AD. Jeśli administrator usługi Azure AD nie włączy Enterprise State Roaming, połączone konto usługi Azure AD przestanie synchronizować ustawienia. Jeśli jesteś użytkownikiem systemu Windows 10 i zalogujesz się przy użyciu tożsamości usługi Azure AD, będziesz rozpoczynać synchronizację ustawień systemu Windows, gdy tylko administrator włączy synchronizację ustawień za pośrednictwem usługi Azure AD.

Jeśli dane osobowe są przechowywane na urządzeniu firmowym, należy pamiętać, że system operacyjny Windows i dane aplikacji rozpoczną synchronizację z usługą Azure AD. Ma to następujące konsekwencje:

* Twoje ustawienia konto Microsoft osobistych zostaną oddzielone od ustawień na kontach usługi Azure AD w firmie lub szkole. Wynika to z faktu, że usługa konto Microsoft i Synchronizacja ustawień usługi Azure AD używają teraz oddzielnych kont.
* Dane osobowe, takie jak hasła Wi-Fi, poświadczenia sieci Web i Ulubione programu Internet Explorer, które zostały wcześniej zsynchronizowane za pośrednictwem połączonego konto Microsoft, zostaną zsynchronizowane za pośrednictwem usługi Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Jak działają konto Microsoft i współdziałanie usługi Azure AD Enterprise State Roaming?

W listopadzie 2015 lub nowszych wersjach systemu Windows 10 Enterprise State Roaming jest obsługiwane tylko dla jednego konta naraz. Jeśli zalogujesz się do systemu Windows przy użyciu służbowego konta usługi Azure AD, wszystkie dane zostaną zsynchronizowane za pośrednictwem usługi Azure AD. Jeśli zalogujesz się do systemu Windows za pomocą konto Microsoft osobistych, wszystkie dane zostaną zsynchronizowane za pośrednictwem konto Microsoft. Uniwersalne AppData będzie przenoszone tylko przy użyciu konta podstawowego logowania na urządzeniu i będzie przenoszona tylko wtedy, gdy licencja aplikacji jest własnością konta podstawowego. Uniwersalnego elementu AppData dla aplikacji należących do wszystkich kont dodatkowych nie będzie synchronizowany.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Czy synchronizacja ustawień dla kont usługi Azure AD jest przeprowadzana z wielu dzierżawców?

Gdy wiele kont usługi Azure AD z różnych dzierżaw usługi Azure AD znajduje się na tym samym urządzeniu, należy zaktualizować rejestr urządzenia, aby komunikować się z usługą Azure Rights Management dla każdej dzierżawy usługi Azure AD.  

1. Znajdź identyfikator GUID dla każdej dzierżawy usługi Azure AD. Otwórz Azure Portal i wybierz dzierżawę usługi Azure AD. Identyfikator GUID dla dzierżawy znajduje się na stronie właściwości wybranej dzierżawy ( https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) , **Identyfikator katalogu** oznaczonego nazwą. 
2. Po skonfigurowaniu identyfikatora GUID należy dodać klucz rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>**.
   W kluczu **GUID identyfikatora dzierżawy** Utwórz nową wartość ciągu wielociągowego (reg-wiele-SZ) o nazwie **AllowedRMSServerUrls**. W przypadku danych określ adresy URL punktów dystrybucji licencjonowania innych dzierżawców platformy Azure, do których uzyskuje dostęp urządzenie.
3. Adresy URL punktów dystrybucji licencjonowania można znaleźć, uruchamiając polecenie cmdlet **Get-AadrmConfiguration** w module aadrm. Jeśli wartości dla **LicensingIntranetDistributionPointUrl** i **LicensingExtranetDistributionPointUrl** są różne, określ obie wartości. Jeśli wartości są takie same, określ wartość tylko raz.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Jakie są opcje ustawień roamingu dla istniejących aplikacji klasycznych systemu Windows?

Roaming działa tylko w przypadku aplikacji uniwersalnych systemu Windows. Dostępne są dwie opcje włączania roamingu w istniejącej aplikacji klasycznej systemu Windows:

* [Mostek Desktop](/windows/msix/desktop/source-code-overview) ułatwia przenoszenie istniejących aplikacji klasycznych systemu Windows do platforma uniwersalna systemu Windows. W tym miejscu wymagane są minimalne zmiany w kodzie, aby móc korzystać z roamingu danych aplikacji usługi Azure AD. Mostek Desktop oferuje aplikacje z tożsamością aplikacji, która jest wymagana do włączenia roamingu danych aplikacji dla istniejących aplikacji klasycznych.
* [Wirtualizacja środowiska użytkownika (UE-V)](/previous-versions//dn458947(v=vs.85)) pomaga utworzyć niestandardowy szablon ustawień dla istniejących aplikacji klasycznych systemu Windows i włączyć roaming dla aplikacji Win32. Ta opcja nie wymaga, aby Deweloper aplikacji zmienił kod aplikacji. W przypadku klientów, którzy kupili pakiet Microsoft Desktop Optimization Pack, są ograniczone do lokalnego Active Directory roamingu.

Administratorzy mogą skonfigurować program UE-V do roamingu danych aplikacji klasycznych systemu Windows, zmieniając roaming ustawień systemu operacyjnego Windows i danych aplikacji uniwersalnej za pomocą [zasad grupy UE-V](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), w tym:

* Roaming zasad grupy ustawień systemu Windows
* Nie Synchronizuj zasad grupy aplikacji systemu Windows
* Roaming programu Internet Explorer w sekcji aplikacje

W przyszłości firma Microsoft może zbadać metody, które pozwalają na przechodzenie do systemu Windows w wersji UE-V, i rozbudowanie w celu przechodzenia do ustawień w chmurze usługi Azure AD.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Czy można przechowywać zsynchronizowane ustawienia i dane lokalnie?

Enterprise State Roaming przechowuje wszystkie zsynchronizowane dane w chmurze firmy Microsoft. UE-V oferuje lokalne rozwiązanie do roamingu.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kto jest właścicielem danych, które są przekazywane?

Przedsiębiorstwa są własnością danych przenoszona przez Enterprise State Roaming. Dane są przechowywane w centrum danych platformy Azure. Wszystkie dane użytkownika są szyfrowane zarówno podczas przesyłania, jak i w chmurze za pomocą usługi Rights Management platformy Azure z Azure Information Protection. Jest to udoskonalenie w porównaniu do synchronizacji ustawień opartych na konto Microsoft, która szyfruje tylko pewne dane poufne, takie jak poświadczenia użytkownika, zanim opuściją urządzenie.

Firma Microsoft dokłada starań, aby chronić dane klientów. Dane ustawień użytkownika przedsiębiorstwa są automatycznie szyfrowane przez usługę Rights Management platformy Azure, zanim opuściją urządzenie z systemem Windows 10, więc żaden inny użytkownik nie będzie mógł odczytać tych danych. Jeśli Twoja organizacja ma płatną subskrypcję usługi Azure Rights Management, możesz użyć innych funkcji ochrony, takich jak śledzenie i odwoływanie dokumentów, automatyczne włączanie ochrony wiadomości e-mail zawierających informacje poufne i zarządzanie własnymi kluczami (rozwiązanie "Przenieś własny klucz", znane także jako BYOK). Aby uzyskać więcej informacji o tych funkcjach i sposobie działania tej usługi ochrony, zobacz artykuł [co to jest usługa Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Czy mogę zarządzać synchronizacją dla określonej aplikacji lub ustawienia?

W systemie Windows 10 nie ma ustawienia MDM ani zasady grupy, aby wyłączyć roaming dla pojedynczej aplikacji. Administratorzy dzierżawy mogą wyłączyć funkcję synchronizacji programu AppData dla wszystkich aplikacji na zarządzanym urządzeniu, ale nie ma bardziej precyzyjnej kontroli na poziomie aplikacji lub w aplikacji.

## <a name="how-can-i-enable-or-disable-roaming"></a>Jak włączyć lub wyłączyć roaming?

W aplikacji **Ustawienia** przejdź do pozycji **konta**  >  **Synchronizowanie ustawień**. Na tej stronie można sprawdzić, które konto jest używane do roamingu ustawień, a także włączyć lub wyłączyć poszczególne grupy ustawień do przeroamingu.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Jakie są zalecenia firmy Microsoft dotyczące włączania roamingu w systemie Windows 10?

Firma Microsoft oferuje kilka różnych ustawień dostępnych dla rozwiązań mobilnych, w tym profile użytkowników mobilnych, UE-V i Enterprise State Roaming.  Firma Microsoft dokłada starań, aby inwestycje w Enterprise State Roaming w przyszłych wersjach systemu Windows. Jeśli Twoja organizacja nie jest gotowa lub nie ma doświadczenia z przeniesieniem danych do chmury, zalecamy użycie wersji UE-V jako podstawowej technologii mobilnej. Jeśli Twoja organizacja wymaga obsługi roamingu dla istniejących aplikacji klasycznych systemu Windows, ale eager przenieść się do chmury, zalecamy użycie obu Enterprise State Roaming i UE-V. Chociaż Stany IT i Enterprise State Roaming są bardzo podobne, nie wykluczają się wzajemnie. Są one uzupełniane, aby pomóc w zapewnieniu, że organizacja udostępnia Usługi mobilne, których potrzebują użytkownicy.  

W przypadku używania zarówno Enterprise State Roaming, jak i UE-V są stosowane następujące reguły:

* Enterprise State Roaming jest głównym agentem roamingu na urządzeniu. UE-V jest używany do uzupełnienia "przerwy Win32".
* W przypadku korzystania z zasad grupy programu UE-v mobilne dane dla ustawień systemu Windows i nowoczesnych aplikacji platformy UWP powinny być wyłączone. Są one już objęte Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Jak Enterprise State Roaming obsługiwać infrastrukturę pulpitu wirtualnego (VDI)?

Enterprise State Roaming jest obsługiwana w jednostkach SKU klienta systemu Windows 10, ale nie w jednostkach SKU serwera. Jeśli maszyna wirtualna klienta jest hostowana na komputerze funkcji hypervisor i użytkownik jest zdalnie zalogowany do maszyny wirtualnej, Twoje dane będą przenoszone. Jeśli wielu użytkowników współużytkują ten sam system operacyjny i Użytkownicy logują się zdalnie na serwerze w celu pełnego środowiska pulpitu, roaming może nie zadziałał. Ostatni Scenariusz oparty na sesji nie jest oficjalnie obsługiwany.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Co się stanie, gdy moja Organizacja kupuje subskrypcję obejmującą usługę Azure Rights Management po użyciu roamingu?

Jeśli Twoja organizacja korzysta już z roamingu w systemie Windows 10 i korzysta z bezpłatnej subskrypcji usługi Azure Rights Management Limited, kupowanie [płatnej subskrypcji](https://azure.microsoft.com/pricing/details/information-protection/) obejmującej usługę Azure Rights Management Protection nie będzie miało żadnego wpływu na funkcjonalność funkcji roamingu, a administrator IT nie wymaga żadnych zmian konfiguracji.

## <a name="known-issues"></a>Znane problemy

Listę znanych problemów można znaleźć w dokumentacji w sekcji [Rozwiązywanie problemów](enterprise-state-roaming-troubleshooting.md) . 

## <a name="next-steps"></a>Następne kroki 

Aby zapoznać się z omówieniem, zobacz [Omówienie usługi Enterprise State roaming](enterprise-state-roaming-overview.md)