---
title: Kontrolowana weryfikacja hybrydowego przyłączania do usługi Azure AD — Azure AD
description: Dowiedz się, jak przeprowadzić kontrolowane weryfikację hybrydowego sprzężenia usługi Azure AD przed włączeniem jej w całej organizacji.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66b216e5e511d2d80378ee7e2d124dccbc7abcb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85252716"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Kontrolowana walidacja przyłączenia do hybrydowej usługi Azure AD

Gdy wszystkie wymagania wstępne są stosowane, urządzenia z systemem Windows będą automatycznie rejestrowane jako urządzenia w dzierżawie usługi Azure AD. Stan tych tożsamości urządzeń w usłudze Azure AD jest określany jako sprzężenie hybrydowe usługi Azure AD. Więcej informacji na temat pojęć uwzględnionych w tym artykule można znaleźć w artykułach [wprowadzenie do zarządzania urządzeniami w Azure Active Directory](overview.md) i [planowanie implementacji dołączania hybrydowego Azure Active Directory](hybrid-azuread-join-plan.md).

Organizacje mogą chcieć przeprowadzić kontrolowane sprawdzenie poprawności hybrydowego sprzężenia usługi Azure AD przed włączeniem jej w całej organizacji. W tym artykule wyjaśniono, jak przeprowadzić kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Kontrolowana weryfikacja hybrydowego sprzężenia usługi Azure AD na bieżących urządzeniach z systemem Windows

W przypadku urządzeń z systemem operacyjnym Windows dla komputerów stacjonarnych obsługiwana wersja to Rocznicowa Aktualizacja systemu Windows 10 (wersja 1607) lub nowsza. Najlepszym rozwiązaniem jest uaktualnienie do najnowszej wersji systemu Windows 10.

Aby przeprowadzić kontrolowane sprawdzanie poprawności hybrydowego sprzężenia usługi Azure AD na bieżących urządzeniach z systemem Windows, należy wykonać następujące czynności:

1. Wyczyść wpis punktu połączenia usługi (SCP) z Active Directory (AD), jeśli istnieje
1. Konfigurowanie ustawienia rejestru po stronie klienta dla punktu połączenia usługi na komputerach przyłączonych do domeny przy użyciu obiektu zasady grupy (GPO)
1. W przypadku korzystania z AD FS należy również skonfigurować ustawienia rejestru po stronie klienta dla punktu połączenia usługi na serwerze AD FS przy użyciu obiektu zasad grupy  
1. Może być również konieczne [dostosowanie opcji synchronizacji](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) w Azure AD Connect, aby umożliwić synchronizację urządzeń. 


### <a name="clear-the-scp-from-ad"></a>Wyczyść punkt połączenia usługi w usłudze AD

Użyj edytora interfejsów Active Directory Services (ADSI Edit), aby zmodyfikować obiekty SCP w usłudze AD.

1. Uruchom **Edytor ADSI Edycja** aplikacji klasycznych z i administracyjnej stacji roboczej lub kontrolera domeny jako administrator przedsiębiorstwa.
1. Nawiąż połączenie z **kontekstem nazewnictwa konfiguracji** domeny.
1. Przejdź do **CN = Configuration, DC = contoso, DC = com**  >  **CN = Services**  >  **CN = Konfiguracja rejestracji urządzeń**
1. Kliknij prawym przyciskiem myszy obiekt liścia **CN = 62a0ff2e-97B9-4513-943f-0d221bd30080** i wybierz polecenie **Właściwości**
   1. Wybierz **słowa kluczowe** z okna **edytora atrybutów** , a następnie kliknij przycisk **Edytuj** .
   1. Wybierz wartości **azureADId** i **azureADName** (po jednym naraz), a następnie kliknij przycisk **Usuń** .
1. Zamknij **Edytowanie ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Konfigurowanie ustawienia rejestru po stronie klienta dla punktu połączenia usługi

Użyj poniższego przykładu, aby utworzyć obiekt zasady grupy (GPO) w celu wdrożenia ustawienia rejestru Konfigurowanie wpisu SCP w rejestrze urządzeń.

1. Otwórz konsolę zarządzania zasady grupy i Utwórz nowy obiekt zasady grupy w domenie.
   1. Podaj nazwę nowo utworzonego obiektu zasad grupy (na przykład ClientSideSCP).
1. Edytuj obiekt zasad grupy i znajdź następującą ścieżkę: **Preferencje konfiguracji komputera**  >  **Preferences**  >  **rejestr ustawień systemu Windows**  >  **Registry**
1. Kliknij prawym przyciskiem myszy rejestr i wybierz pozycję **Nowy**  >  **element rejestru**
   1. Na karcie **Ogólne** skonfiguruj następujące ustawienia:
      1. Akcja: **Aktualizacja**
      1. Gałąź: **HKEY_LOCAL_MACHINE**
      1. Ścieżka klucza: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nazwa wartości: **TenantId**
      1. Typ wartości: **REG_SZ**
      1. Dane wartości: identyfikator GUID lub **katalog** Twojego wystąpienia usługi Azure AD (tę wartość można znaleźć w **Azure Portal**  >  **Azure Active Directory**  >  **Właściwości**  >  **katalogu**
   1. Kliknij przycisk **OK** .
1. Kliknij prawym przyciskiem myszy rejestr i wybierz pozycję **Nowy**  >  **element rejestru**
   1. Na karcie **Ogólne** skonfiguruj następujące ustawienia:
      1. Akcja: **Aktualizacja**
      1. Gałąź: **HKEY_LOCAL_MACHINE**
      1. Ścieżka klucza: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nazwa wartości: **dzierżawca**
      1. Typ wartości: **REG_SZ**
      1. Dane wartości: Nazwa zweryfikowanej **domeny** , jeśli używasz środowiska federacyjnego, takiego jak AD FS. Zweryfikowana **nazwa domeny** lub nazwa domeny onmicrosoft.com na przykład w `contoso.onmicrosoft.com` przypadku korzystania ze środowiska zarządzanego
   1. Kliknij przycisk **OK** .
1. Zamknij Edytor dla nowo utworzonego obiektu zasad grupy
1. Połącz nowo utworzony obiekt zasad grupy z odpowiednią jednostką organizacyjną zawierającą komputery przyłączone do domeny, które należą do kontrolowanej populacji wdrożenia

### <a name="configure-ad-fs-settings"></a>Skonfiguruj ustawienia AD FS

Jeśli używasz AD FS, najpierw musisz skonfigurować punkt połączenia usługi po stronie klienta, korzystając z instrukcji wymienionych powyżej przez połączenie obiektu zasad grupy z serwerami AD FS. Obiekt SCP definiuje Źródło uprawnień dla obiektów urządzeń. Może być lokalna lub Azure AD. Gdy punkt połączenia usługi klienta jest skonfigurowany dla AD FS, Źródło dla obiektów urządzeń jest ustanawiane jako usługa Azure AD.

> [!NOTE]
> Jeśli nie powiodło się skonfigurowanie punktu połączenia usługi po stronie klienta na serwerach AD FS, Źródło tożsamości urządzeń będzie traktowane jako lokalne. Następnie Usługa AD FS rozpocznie usuwanie obiektów urządzeń z katalogu lokalnego po określonym okresie zdefiniowanym w atrybucie rejestracji urządzenia ADFS "MaximumInactiveDays". Obiekty rejestracji urządzeń ADFS można znaleźć za pomocą [polecenia cmdlet Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Kontrolowana weryfikacja hybrydowego sprzężenia usługi Azure AD na urządzeniach niższego poziomu systemu Windows

Aby zarejestrować urządzenia niskiego poziomu systemu Windows, organizacje muszą zainstalować [Workplace Join firmy Microsoft dla komputerów z systemem innym niż Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) , które są dostępne w centrum pobierania Microsoft.

Pakiet można wdrożyć przy użyciu systemu dystrybucji oprogramowania, takiego jak [Microsoft Endpoint Configuration Manager](/configmgr/). Pakiet obsługuje standardowe opcje instalacji dyskretnej z cichym parametrem. Bieżąca gałąź Configuration Manager oferuje korzyści w porównaniu z wcześniejszymi wersjami, takie jak możliwość śledzenia zakończonych rejestracji.

Instalator tworzy zaplanowane zadanie w systemie, który działa w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do systemu Windows. Zadanie dyskretnie sprzęga urządzenie z usługą Azure AD przy użyciu poświadczeń użytkownika po uwierzytelnieniu w usłudze Azure AD.

Aby kontrolować rejestrację urządzeń, należy wdrożyć pakiet Instalator Windows w wybranej grupie urządzeń niższego poziomu systemu Windows.

> [!NOTE]
> Jeśli punkt połączenia usługi nie jest skonfigurowany w usłudze AD, należy wykonać takie samo podejście jak opisane w temacie [Konfigurowanie ustawień rejestru po stronie klienta dla punktu połączenia](#configure-client-side-registry-setting-for-scp)usługi na komputerach przyłączonych do domeny przy użyciu obiektu zasady grupy (GPO).


Po zweryfikowaniu, że wszystko działa zgodnie z oczekiwaniami, można automatycznie zarejestrować pozostałe urządzenia bieżące i niskiego poziomu systemu Windows w usłudze Azure AD, [konfigurując punkt SCP przy użyciu Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Następne kroki

[Planowanie implementacji z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)
