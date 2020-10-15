---
title: Planowanie sprzężenia Azure Active Directory hybrydowego — Azure Active Directory
description: Dowiedz się, jak skonfigurować urządzenia dołączone hybrydowo do usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 955e77bc947baed889de24ce34e7acec737164f6
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097307"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Instrukcje: planowanie implementacji dołączania hybrydowego Azure Active Directory

Podobnie jak w przypadku użytkownika, urządzenie jest kolejną tożsamością podstawową, która ma być chroniona, i używanie jej do ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji. Aby osiągnąć ten cel, można przełączać tożsamości urządzeń i zarządzać nimi w usłudze Azure AD przy użyciu jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przenosząc urządzenia do usługi Azure AD, można zmaksymalizować wydajność użytkowników dzięki zastosowaniu logowania jednokrotnego (SSO) w zasobach chmury i zasobach lokalnych. W tym samym czasie można zabezpieczyć dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../conditional-access/overview.md).

Jeśli masz lokalne środowisko Active Directory (AD) i chcesz dołączyć komputery przyłączone do domeny usługi AD do usługi Azure AD, możesz to zrobić, wykonując sprzężenie hybrydowe usługi Azure AD. Ten artykuł zawiera powiązane kroki umożliwiające zaimplementowanie hybrydowego sprzężenia usługi Azure AD w środowisku. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz już [wprowadzenie do zarządzania tożsamościami urządzeń w Azure Active Directory](./overview.md).

> [!NOTE]
> Minimalna wymagana wersja kontrolera domeny dla hybrydowego sprzężenia usługi Azure AD systemu Windows 10 to Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planowanie implementacji

Aby zaplanować implementację hybrydowej usługi Azure AD, zapoznaj się z:

> [!div class="checklist"]
> - Przejrzyj obsługiwane urządzenia
> - Przejrzyj interesujące Cię rzeczy
> - Przeglądanie kontrolowanej weryfikacji hybrydowego sprzężenia usługi Azure AD
> - Wybierz swój scenariusz w oparciu o infrastrukturę tożsamości
> - Zapoznaj się z lokalną obsługą UPN usługi AD na potrzeby hybrydowego dołączania do usługi Azure AD

## <a name="review-supported-devices"></a>Przejrzyj obsługiwane urządzenia

Sprzężenie hybrydowe usługi Azure AD obsługuje szeroką gamę urządzeń z systemem Windows. Ponieważ konfiguracja urządzeń ze starszymi wersjami systemu Windows wymaga dodatkowych lub różnych kroków, obsługiwane urządzenia są pogrupowane w dwie kategorie:

### <a name="windows-current-devices"></a>Bieżące urządzenia z systemem Windows

- Windows 10
- Windows Server 2016
  - **Uwaga**: klienci chmury krajowej platformy Azure wymagają wersji 1803
- Windows Server 2019

W przypadku urządzeń z systemem operacyjnym Windows dla komputerów stacjonarnych obsługiwana wersja jest wymieniona w tym artykule [Informacje o wersji systemu Windows 10](/windows/release-information/). Najlepszym rozwiązaniem, firma Microsoft zaleca uaktualnienie do najnowszej wersji systemu Windows 10.

### <a name="windows-down-level-devices"></a>Urządzenia niskiego poziomu systemu Windows

- Windows 8.1
- Obsługa systemu Windows 7 zakończyła się 14 stycznia 2020. Aby uzyskać więcej informacji, zobacz [Pomoc techniczna dla systemu Windows 7 została zakończona](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 z dodatkiem R2
- Windows Server 2012
- Windows Server 2008 R2. Informacje o pomocy technicznej dotyczące systemów Windows Server 2008 i 2008 R2 znajdują się w temacie [przygotowanie do systemu Windows server 2008 End of support](https://www.microsoft.com/cloud-platform/windows-server-2008).

Jako pierwszy krok planowania należy przejrzeć środowisko i określić, czy należy obsługiwać urządzenia niskiego poziomu systemu Windows.

## <a name="review-things-you-should-know"></a>Przejrzyj interesujące Cię rzeczy

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze
- Sprzężenie hybrydowe usługi Azure AD nie jest obecnie obsługiwane, jeśli środowisko składa się z jednego lasu usługi Active Directory, który synchronizuje dane tożsamości z więcej niż jedną dzierżawą usługi Azure AD.

- Hybrydowe dołączanie usługi Azure AD nie jest obsługiwane w przypadku systemu Windows Server z uruchomioną rolą kontrolera domeny (DC).

- Funkcja hybrydowego przyłączania do usługi Azure AD nie jest obsługiwana na urządzeniach niskiego poziomu systemu Windows podczas korzystania z roamingu poświadczeń lub roamingu profilu użytkownika lub profilu obowiązkowego.

- Podstawowe systemy operacyjne serwera nie obsługują żadnego typu rejestracji urządzeń.

### <a name="os-imaging-considerations"></a>Zagadnienia dotyczące tworzenia obrazu systemu operacyjnego
- Jeśli korzystasz z narzędzia przygotowywania systemu (Sysprep) i używasz obrazu **sprzed systemu Windows 10 1809** do instalacji, upewnij się, że obraz nie pochodzi z urządzenia, które jest już zarejestrowane w usłudze Azure AD jako sprzężenie hybrydowe usługi Azure AD.

- Jeśli korzystasz z migawki maszyny wirtualnej w celu utworzenia dodatkowych maszyn wirtualnych, upewnij się, że migawka nie pochodzi z maszyny wirtualnej, która jest już zarejestrowana w usłudze Azure AD jako sprzężenie hybrydowe usługi Azure AD.

- Jeśli używasz [ujednoliconego filtru zapisu](/windows-hardware/customize/enterprise/unified-write-filter) i podobnych technologii, które usuwają zmiany na dysku po ponownym uruchomieniu, muszą one być stosowane po przyłączeniu urządzenia do hybrydowej usługi Azure AD. Włączenie takich technologii przed ukończeniem hybrydowego sprzężenia usługi Azure AD spowoduje odłączenie urządzenia do każdego ponownego uruchomienia

### <a name="handling-devices-with-azure-ad-registered-state"></a>Obsługa urządzeń z zarejestrowanym stanem usługi Azure AD
Jeśli urządzenia przyłączone do domeny systemu Windows 10 są zarejestrowane w dzierżawie [usługi Azure AD](overview.md#getting-devices-in-azure-ad) , może to doprowadzić do podwójnego stanu hybrydowego przyłączonego do usługi Azure AD i zarejestrowanego urządzenia usługi Azure AD. Zalecamy uaktualnienie do systemu Windows 10 1803 (z zastosowaniem programu KB4489894) lub nowszego, aby automatycznie rozwiązać ten scenariusz. W wersjach wcześniejszych niż 1803 należy ręcznie usunąć zarejestrowany stan usługi Azure AD przed włączeniem hybrydowego sprzężenia usługi Azure AD. W 1803 i nowszych wersjach wprowadzono następujące zmiany, aby uniknąć tego podwójnego stanu:

- Wszystkie istniejące zarejestrowane w usłudze Azure AD stan użytkownika zostaną automatycznie usunięte <i>po przyłączeniu urządzenia do hybrydowej usługi Azure AD i tego samego użytkownika</i>. Na przykład jeśli użytkownik ma zarejestrowany stan usługi Azure AD na urządzeniu, podwójny stan dla użytkownika A zostanie oczyszczony tylko wtedy, gdy użytkownik loguje się do urządzenia. Jeśli na tym samym urządzeniu istnieje wielu użytkowników, podwójny stan jest czyszczony indywidualnie, gdy użytkownicy zalogują się. Oprócz usuwania zarejestrowanego stanu usługi Azure AD system Windows 10 również wyrejestruje urządzenie z usługi Intune lub innego rozwiązania MDM, jeśli rejestracja nastąpiła w ramach rejestracji w usłudze Azure AD za pośrednictwem automatycznej rejestracji.
- Aby uniemożliwić urządzeniu przyłączonym do domeny usługę Azure AD, Dodaj następującą wartość rejestru do HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin" = DWORD: 00000001.
- W systemie Windows 10 1803, jeśli masz skonfigurowaną usługi Windows Hello dla firm, użytkownik musi zmienić konfigurację usługi Windows Hello dla firm po oczyszczeniu podwójnego stanu. Ten problem został rozwiązany z KB4512509

> [!NOTE]
> Mimo że system Windows 10 automatycznie usunie zarejestrowany stan usługi Azure AD lokalnie, obiekt urządzenia w usłudze Azure AD nie zostanie natychmiast usunięty, jeśli jest zarządzany przez usługę Intune. Możesz sprawdzić poprawność usuwania zarejestrowanego stanu usługi Azure AD, uruchamiając dsregcmd/status i Rozważmy, aby urządzenie nie było zarejestrowane w usłudze Azure AD na podstawie tego programu.

### <a name="additional-considerations"></a>Dodatkowe zagadnienia
- Jeśli środowisko używa infrastruktury pulpitów wirtualnych (VDI), zobacz [tożsamość urządzenia i Wirtualizacja pulpitu](./howto-device-identity-virtual-desktop-infrastructure.md).

- Hybrydowe dołączanie usługi Azure AD jest obsługiwane w przypadku modułu TPM zgodnego ze standardem FIPS 2,0 i nie jest obsługiwane dla modułu TPM 1,2 Jeśli urządzenia są zgodne ze standardem FIPS 1,2, należy je wyłączyć przed przejściem do hybrydowego sprzężenia usługi Azure AD. Firma Microsoft nie udostępnia żadnych narzędzi do wyłączania trybu FIPS dla moduły TPM, ponieważ jest on zależny od producenta modułu TPM. Aby uzyskać pomoc techniczną, skontaktuj się z producentem OEM. 

- Począwszy od wersji Windows 10 1903, moduły TPM 1,2 nie są używane z hybrydowym sprzężeniem usługi Azure AD, a urządzenia z tymi moduły TPM będą uważane za niemające modułu TPM.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Przeglądanie kontrolowanej weryfikacji hybrydowego sprzężenia usługi Azure AD

Gdy wszystkie wymagania wstępne są stosowane, urządzenia z systemem Windows będą automatycznie rejestrowane jako urządzenia w dzierżawie usługi Azure AD. Stan tych tożsamości urządzeń w usłudze Azure AD jest określany jako sprzężenie hybrydowe usługi Azure AD. Więcej informacji na temat pojęć uwzględnionych w tym artykule można znaleźć w artykule [wprowadzenie do zarządzania tożsamościami urządzeń w Azure Active Directory](overview.md).

Organizacje mogą chcieć przeprowadzić kontrolowane sprawdzenie poprawności hybrydowego sprzężenia usługi Azure AD przed włączeniem jej w całej organizacji. Zapoznaj się z artykułem [Sprawdzanie poprawności funkcji dołączania hybrydowego usługi Azure AD](hybrid-azuread-join-control.md) , aby zrozumieć, jak to zrobić.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Wybierz swój scenariusz w oparciu o infrastrukturę tożsamości

Sprzężenie hybrydowe usługi Azure AD współdziała z systemami, zarządzanymi i federacyjnymi, w zależności od tego, czy nazwa UPN jest w trakcie routingu czy bez obsługi routingu. W dolnej części strony tabeli zapoznaj się z obsługiwanymi scenariuszami.  

### <a name="managed-environment"></a>Środowisko zarządzane

Środowisko zarządzane można wdrożyć przy użyciu opcji [Synchronizacja skrótów haseł (PHS)](../hybrid/whatis-phs.md) lub [uwierzytelnianie przekazywane (PTA)](../hybrid/how-to-connect-pta.md) z [bezproblemowym logowaniem jednokrotnym](../hybrid/how-to-connect-sso.md).

Te scenariusze nie wymagają konfigurowania serwera federacyjnego na potrzeby uwierzytelniania.

> [!NOTE]
> [Uwierzytelnianie w chmurze przy użyciu wdrożenia etapowego](../hybrid/how-to-connect-staged-rollout.md) jest obsługiwane tylko po uruchomieniu aktualizacji systemu Windows 10 1903

### <a name="federated-environment"></a>Środowisko federacyjne

Środowisko federacyjne powinno mieć dostawcę tożsamości, który obsługuje poniższe wymagania. Jeśli masz środowisko federacyjne wykorzystujące Active Directory Federation Services (AD FS), poniższe wymagania są już obsługiwane.

- **WIAORMULTIAUTHN:** To żądanie jest wymagane do hybrydowego przyłączenia do usługi Azure AD dla urządzeń niższego poziomu systemu Windows.
- **Protokół WS-Trust:** Ten protokół jest wymagany do uwierzytelniania bieżących urządzeń przyłączonych hybrydowo usługi Azure AD za pomocą usługi Azure AD. W przypadku korzystania z AD FS należy włączyć następujące punkty końcowe WS-Trust: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **Usługi ADFS/Services/Trust/2005/windowstransport** lub **ADFS/Services/Trust/13/windowstransport** powinny być włączone tylko jako punkty końcowe dostępne dla intranetu i nie mogą być udostępniane jako punkty końcowe dla ekstranetu za pośrednictwem serwera proxy aplikacji sieci Web. Aby dowiedzieć się więcej o tym, jak wyłączyć WS-Trust punkty końcowe systemu Windows, zobacz temat [wyłączanie WS-Trust punktów końcowych systemu Windows na serwerze proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Punkty końcowe można sprawdzić za pomocą konsoli zarządzania AD FS w obszarze **Service**  >  **punkty końcowe**usługi.

> [!NOTE]
> Usługa Azure AD nie obsługuje kart inteligentnych ani certyfikatów w domenach zarządzanych.

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia kreator umożliwiający konfigurowanie dołączania hybrydowego do usługi Azure AD. Kreator pozwala znacznie uprościć proces konfiguracji. Jeśli instalacja wymaganej wersji Azure AD Connect nie jest opcją dla Ciebie, zobacz [Ręczne konfigurowanie rejestracji urządzeń](hybrid-azuread-join-manual.md). 

W zależności od scenariusza, który jest zgodny z infrastrukturą tożsamości, zobacz:

- [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md)
- [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska zarządzanego](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Zapoznaj się z lokalną obsługą UPN lokalnych użytkowników usługi AD w celu dołączania hybrydowej usługi Azure AD

Czasami nazwy UPN lokalnych użytkowników usługi AD mogą różnić się od nazw UPN usługi Azure AD. W takich przypadkach hybrydowe dołączenie usługi Azure AD systemu Windows 10 zapewnia ograniczoną obsługę lokalnych nazw UPN usługi AD na podstawie [metody uwierzytelniania](../hybrid/choose-ad-authn.md), typu domeny i wersji systemu Windows 10. Istnieją dwa typy lokalnych nazw UPN usługi AD, które mogą istnieć w Twoim środowisku:

- Nazwa UPN użytkowników z obsługą routingu: nazwa UPN z routingiem ma poprawną zweryfikowaną domenę, która jest zarejestrowana w rejestratorze domeny. Na przykład jeśli contoso.com jest domeną podstawową w usłudze Azure AD, contoso.org jest domeną podstawową w lokalnej usłudze AD należącej do firmy Contoso i [zweryfikowaną w usłudze Azure AD](../fundamentals/add-custom-domain.md)
- Nazwa UPN użytkowników bez obsługi routingu: nazwa UPN bez routingu nie ma zweryfikowanej domeny. Ma to zastosowanie tylko w sieci prywatnej organizacji. Na przykład jeśli contoso.com jest domeną podstawową w usłudze Azure AD, contoso. local jest domeną podstawową w lokalnej usłudze AD, ale nie jest domeną zweryfikowaną w Internecie i używaną tylko w sieci firmy Contoso.

> [!NOTE]
> Informacje przedstawione w tej sekcji dotyczą tylko lokalnej nazwy UPN użytkownika. Nie ma zastosowania do sufiksu domeny komputera lokalnego (przykład: Komputer1. contoso. local).

Poniższa tabela zawiera szczegółowe informacje na temat pomocy technicznej dotyczącej lokalnych nazw UPN w systemie Windows 10 hybrydowego sprzężenia usługi Azure AD

| Typ lokalnej nazwy UPN usługi AD | Typ domeny | Wersja systemu Windows 10 | Opis |
| ----- | ----- | ----- | ----- |
| Wzajemn | Federacyjni | Od wersji 1703 | Ogólnie dostępne |
| Bez obsługi routingu | Federacyjni | Od wersji 1803 | Ogólnie dostępne |
| Wzajemn | Zarządzani | Od wersji 1803 | Ogólnie dostępna usługa Azure AD SSPR w systemie Windows ekranu blokady nie jest obsługiwana |
| Bez obsługi routingu | Zarządzani | Nieobsługiwane | |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md) 
>  [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska zarządzanego](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
