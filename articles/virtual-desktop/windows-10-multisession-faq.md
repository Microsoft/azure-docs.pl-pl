---
title: Często zadawane pytania dotyczące systemu Windows 10 Enterprise — Azure
description: Często zadawane pytania i najlepsze rozwiązania dotyczące korzystania z wielu sesji systemu Windows 10 Enterprise dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aafac1133b2bee54624d5f0b00061c5332fcd90b
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102631884"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Wiele sesji systemu Windows 10 Enterprise — często zadawane pytania

W tym artykule przedstawiono odpowiedzi na często zadawane pytania i opisano najlepsze rozwiązania dotyczące wielu sesji systemu Windows 10 Enterprise.

## <a name="what-is-windows-10-enterprise-multi-session"></a>Co to jest wiele sesji systemu Windows 10 Enterprise?

Wiele sesji systemu Windows 10 Enterprise, wcześniej znanych jako Windows 10 Enterprise dla pulpitów wirtualnych (EVD), jest nowym hostem sesji Pulpit zdalny, który umożliwia wielu współbieżnych sesji interakcyjnych. Wcześniej tylko system Windows Server może to zrobić. Ta funkcja zapewnia użytkownikom znane środowisko systemu Windows 10, a jednocześnie może korzystać z zalet kosztów wielu sesji i korzystać z istniejącej licencjonowania systemu Windows w przypadku użytkowników zamiast licencji dostępu klienta usług pulpitu zdalnego. Aby uzyskać więcej informacji o licencjach i cenach, zobacz [Cennik pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Ilu użytkowników może jednocześnie korzystać z interaktywnej sesji w wielu sesjach systemu Windows 10 Enterprise?

Liczba interaktywnych sesji, które mogą być aktywne w tym samym czasie, zależy od zasobów sprzętowych systemu (vCPU, pamięci, dysku i vGPU), sposobu korzystania przez użytkowników z aplikacji podczas logowania się do sesji oraz intensywnego obciążenia systemu. Sugerujemy sprawdzenie wydajności systemu, aby poznać liczbę użytkowników, którzy mogą korzystać z wielu sesji systemu Windows 10 Enterprise. Aby dowiedzieć się więcej, zobacz [Cennik pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Dlaczego moja aplikacja raportuje wiele sesji systemu Windows 10 Enterprise jako system operacyjny serwera?

Wiele sesji systemu Windows 10 Enterprise to wersja wirtualna systemu Windows 10 Enterprise. Jedna z różnic polega na tym, że ten system operacyjny (OS) raportuje element [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) jako wartość 3 o takiej samej wartości jak system Windows Server. Ta właściwość utrzymuje system operacyjny zgodny z istniejącymi narzędziami do zarządzania hostami usług pulpitu zdalnego, aplikacjami obsługującymi wiele sesji hosta usług pulpitu zdalnego i większością optymalizacji wydajności systemu niskiego poziomu dla środowisk hosta usług pulpitu zdalnego. Niektóre Instalatory aplikacji mogą blokować instalację w wielu sesjach systemu Windows 10, w zależności od tego, czy wykrywane są ustawienia ProductType dla klienta. Jeśli Twoja aplikacja nie zostanie zainstalowana, skontaktuj się z dostawcą aplikacji w celu uzyskania zaktualizowanej wersji.

## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Czy można uruchomić wiele sesji systemu Windows 10 Enterprise w środowisku lokalnym?

Nie można uruchomić wielu sesji systemu Windows 10 Enterprise w lokalnych środowiskach produkcyjnych, ponieważ są one zoptymalizowane pod kątem usługi pulpitu wirtualnego systemu Windows dla platformy Azure. Jest to umowa licencyjna na uruchamianie wielu sesji systemu Windows 10 Enterprise poza platformą Azure w celach produkcyjnych. Nie można aktywować wielu sesji systemu Windows 10 Enterprise w przypadku lokalnych usług zarządzania kluczami (KMS).

## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Jak mogę dostosować obraz wielosesyjny systemu Windows 10 Enterprise dla mojej organizacji?

Maszynę wirtualną (VM) można uruchomić na platformie Azure z obsługą wielu sesji dla systemu Windows 10 Enterprise i dostosować ją przez zainstalowanie aplikacji LOB, narzędzia sysprep/generalize, a następnie utworzenie obrazu przy użyciu Azure Portal.

Aby rozpocząć, Utwórz maszynę wirtualną na platformie Azure przy użyciu wielu sesji systemu Windows 10 Enterprise. Zamiast uruchamiać maszynę wirtualną na platformie Azure, możesz bezpośrednio pobrać dysk VHD. Następnie będzie można użyć pobranego dysku VHD, aby utworzyć nową maszynę wirtualną generacji 1 na komputerze z systemem Windows 10 z włączoną funkcją Hyper-V.

Dostosuj obraz do swoich potrzeb przez zainstalowanie aplikacji biznesowych i obrazu programu Sysprep. Po zakończeniu dostosowywania Przekaż obraz do platformy Azure przy użyciu wirtualnego dysku twardego. Następnie należy pobrać pulpit wirtualny systemu Windows z witryny Azure Marketplace i użyć go do wdrożenia nowej puli hostów z dostosowanym obrazem.

## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Jak mogę zarządzać wielosesyjną obsługą systemu Windows 10 Enterprise po wdrożeniu?

Możesz użyć dowolnego obsługiwanego narzędzia konfiguracji, ale zalecamy Configuration Manager wersji 1906, ponieważ obsługuje wielosesyjny system Windows 10 Enterprise. Obecnie pracujemy nad Microsoft Intune pomocą techniczną.

## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Czy dla wielu sesji systemu Windows 10 Enterprise można przyłączyć Azure Active Directory (AD)?

Wielosesyjna obsługa systemu Windows 10 Enterprise jest obecnie obsługiwana w przypadku hybrydowej usługi Azure AD. Po przyłączeniu do domeny wielu sesji systemu Windows 10 Enterprise Użyj istniejącego obiektu zasady grupy, aby włączyć rejestrację usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Planowanie wdrożenia hybrydowego dołączania Azure Active Directory](../active-directory/devices/hybrid-azuread-join-plan.md).

## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Gdzie mogę znaleźć obraz wielosesyjny dla systemu Windows 10 Enterprise?

Wiele sesji systemu Windows 10 Enterprise znajduje się w galerii platformy Azure. Aby je znaleźć, przejdź do Azure Portal i Wyszukaj pozycję Windows 10 Enterprise for Virtual Desktops Release. Aby zapoznać się z obrazem zintegrowanym z Microsoft 365 aplikacjami dla przedsiębiorstw, przejdź do Azure Portal i Wyszukaj **aplikacje Microsoft Windows 10 i Microsoft 365 dla przedsiębiorstw**.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Którego obrazu z obsługą wielu sesji systemu Windows 10 Enterprise należy używać?

Galeria systemu Azure zawiera kilka wersji, w tym wiele sesji systemu Windows 10 Enterprise, w wersji 1809 i Windows 10 Enterprise z wieloma sesjami, wersja 1903. Zalecamy użycie najnowszej wersji, aby zwiększyć wydajność i niezawodność.

## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Które wersje wielosesyjne systemu Windows 10 są obsługiwane?

Wiele sesji systemu Windows 10 Enterprise, wersje 1809 i nowsze są obsługiwane i są dostępne w galerii platformy Azure. Te wersje są zgodne z tymi samymi zasadami cyklu pomocy technicznej co system Windows 10 Enterprise, co oznacza, że wydanie z marca jest obsługiwane przez 18 miesięcy i wydanie z września przez 30 miesięcy.

## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Którego rozwiązania do zarządzania profilami należy używać w przypadku wielu sesji dla systemu Windows 10 Enterprise?

Zalecamy używanie kontenerów profilów FSLogix podczas konfigurowania systemu Windows 10 Enterprise w środowiskach nietrwałych lub w innych scenariuszach, które wymagają profilu przechowywanego centralnie. FSLogix gwarantuje, że profil użytkownika jest dostępny i aktualny dla każdej sesji użytkownika. Zalecamy również używanie kontenera profilu FSLogix do przechowywania profilu użytkownika w dowolnym udziale SMB z odpowiednimi uprawnieniami, ale w razie potrzeby można przechowywać profile użytkowników w usłudze Azure Page BLOB Storage. Użytkownicy pulpitu wirtualnego systemu Windows mogą korzystać z FSLogix bez dodatkowych kosztów.  FSLogix jest wstępnie zainstalowany we wszystkich obrazach wielosesyjnych systemu Windows 10 Enterprise, ale administrator IT nadal jest odpowiedzialny za Konfigurowanie kontenera profilu FSLogix.

Aby uzyskać więcej informacji o konfigurowaniu kontenera profilu FSLogix, zobacz [Konfigurowanie kontenera profilu FSLogix](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Jakiej licencji muszę uzyskać dostęp do wielu sesji systemu Windows 10 Enterprise?

Aby uzyskać pełną listę odpowiednich licencji, zobacz [Cennik pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Dlaczego moje aplikacje znikają po wylogowaniu?

Dzieje się tak, ponieważ korzystasz z wielu sesji systemu Windows 10 Enterprise z rozwiązaniem do zarządzania profilami, takim jak FSLogix. Rozwiązanie administratora lub profilu skonfigurowała system do usuwania profilów użytkowników podczas wylogowywania użytkowników. Ta konfiguracja oznacza, że gdy system usunie Twój profil użytkownika po wylogowaniu, spowoduje również usunięcie wszystkich aplikacji zainstalowanych podczas sesji. Jeśli chcesz zachować zainstalowane aplikacje, musisz polecić administratorowi obsługę tych aplikacji dla wszystkich użytkowników w środowisku pulpitu wirtualnego systemu Windows.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Jak mogę upewnij się, że aplikacje nie znikną po wylogowaniu się użytkowników?

Większość zwirtualizowanych środowisk jest domyślnie konfigurowana, aby uniemożliwić użytkownikom instalowanie dodatkowych aplikacji w swoich profilach. Jeśli chcesz mieć pewność, że aplikacja nie znika, gdy użytkownik wyloguje się z pulpitu wirtualnego systemu Windows, musisz zainicjować obsługę administracyjną tej aplikacji dla wszystkich profilów użytkowników w danym środowisku. Aby uzyskać więcej informacji na temat aprowizacji aplikacji, zapoznaj się z następującymi zasobami:

- [Publikowanie wbudowanych aplikacji w systemie Windows Virtual Desktop](publish-apps.md)
- [Opcje wiersza polecenia obsługi pakietu aplikacji DISM](/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](/powershell/module/dism/add-appxprovisionedpackage)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Jak mogę upewnić się, że użytkownicy nie będą pobierać i instalować aplikacji z Microsoft Store?

Możesz wyłączyć aplikację Microsoft Store, aby upewnić się, że użytkownicy nie pobierają dodatkowych aplikacji poza aplikacje, dla których już Zainicjowano obsługę administracyjną.

Aby wyłączyć aplikację ze sklepu:

1. Utwórz nowy zasady grupy.
2. Wybierz pozycję **Konfiguracja komputera**  >  **Szablony administracyjne**  >  **składniki systemu Windows**.
3. Wybierz pozycję **sklep**.
4. Wybierz pozycję **aplikacja ze sklepu**.
5. Wybierz pozycję **wyłączone**, a następnie wybierz przycisk **OK**.
6. Wybierz przycisk **Zastosuj**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wielu sesjach systemu Windows Virtual Desktop i Windows 10 Enterprise:

- Przeczytaj [dokumentację pulpitu wirtualnego systemu Windows](overview.md)
- Odwiedź naszą [TechCommunity pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Konfigurowanie wdrożenia pulpitu wirtualnego systemu Windows przy użyciu [samouczków pulpitu wirtualnego systemu Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)
