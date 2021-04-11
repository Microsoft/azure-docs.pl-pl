---
title: Korzystanie z profili Wi-Fi przy użyciu urządzeń Azure Stack Edge mini R
description: W tym artykule opisano sposób tworzenia Wi-Fi profilów dla urządzeń z systemem Azure Stack Edge mini R w sieciach przedsiębiorstwa o wysokim poziomie zabezpieczeń i sieciach prywatnych.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050547"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Korzystanie z profili Wi-Fi przy użyciu urządzeń Azure Stack Edge mini R

W tym artykule opisano, jak używać profilów sieci bezprzewodowej (Wi-Fi) z urządzeniami Azure Stack Edge mini R.

Sposób przygotowywania profilu Wi-Fi zależy od typu sieci bezprzewodowej:

- W przypadku Wi-Fi dostępu chronionego 2 (WPA2) — sieci osobistej, takiej jak sieć domowa lub Wi-Fi otwarty hotspot, może być możliwe pobranie i użycie istniejącego profilu bezprzewodowego przy użyciu tego samego hasła, które jest używane z innymi urządzeniami.

- W środowisku przedsiębiorstwa o wysokim poziomie zabezpieczeń masz dostęp do urządzenia za pośrednictwem sieci WPA2-Enterprise. W przypadku tego typu sieci każdy komputer kliencki będzie miał unikatowy profil Wi-Fi i zostanie uwierzytelniony za pośrednictwem certyfikatów. Aby określić wymaganą konfigurację, należy skontaktować się z administratorem sieci.

W dalszej części omówiono wymagania dotyczące profilu dla obu typów sieci.

W obu przypadkach bardzo ważne jest, aby upewnić się, że profil spełnia wymagania dotyczące zabezpieczeń Twojej organizacji przed przetestowaniem lub użyciem profilu z urządzeniem.

## <a name="about-wi-fi-profiles"></a>Informacje o profilach Wi-Fi

Profil Wi-Fi zawiera identyfikator SSID (identyfikatora zestawu usług lub **nazwę sieci**), klucz hasła i informacje o zabezpieczeniach, które są konieczne, aby połączyć urządzenie z siecią bezprzewodową Azure Stack Edge mini R.

Poniższy przykład kodu przedstawia podstawowe ustawienia profilu do użycia w typowej sieci bezprzewodowej:

* `SSID` jest nazwą sieciową.

* `name` jest przyjazną dla użytkownika nazwą połączenia Wi-Fi. Jest to nazwa, którą użytkownicy zobaczą podczas przeglądania dostępnych połączeń na ich urządzeniach.

* Profil jest skonfigurowany do automatycznego łączenia komputera z siecią bezprzewodową, gdy znajduje się on w zasięgu sieci ( `connectionMode`  =  `auto` ).

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Aby uzyskać więcej informacji na temat ustawień profilu Wi-Fi, zobacz **profil przedsiębiorstwa** w obszarze [Dodaj Wi-Fi ustawienia dla urządzeń z systemem Windows 10 lub nowszym](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile), a następnie zobacz [Konfigurowanie profilu Cisco Wi-Fi](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Aby włączyć połączenia bezprzewodowe na urządzeniu z systemem Azure Stack Edge mini R, skonfiguruj port Wi-Fi na urządzeniu, a następnie Dodaj do niego profile Wi-Fi. W sieci przedsiębiorstwa przekażesz również certyfikaty do urządzenia. Następnie można nawiązać połączenie z siecią bezprzewodową z poziomu lokalnego interfejsu użytkownika sieci Web urządzenia. Aby uzyskać więcej informacji, zobacz [Zarządzanie łącznością bezprzewodową na urządzeniu Azure Stack Edge mini R](./azure-stack-edge-mini-r-manage-wifi.md).

## <a name="profile-for-wpa2---personal-network"></a>Profil dla sieci WPA2-Personal

W przypadku Wi-Fi dostępu chronionego 2 (WPA2) — sieci osobistej, takiej jak sieć domowa lub Wi-Fi otwarty hotspot, wiele urządzeń może używać tego samego profilu i tego samego hasła. W sieci domowej Twój telefon komórkowy i laptop używają tego samego profilu bezprzewodowego i hasła do łączenia się z siecią.

Na przykład klient systemu Windows 10 może wygenerować profil środowiska uruchomieniowego. Po zalogowaniu się do sieci bezprzewodowej zostanie wyświetlony monit o podanie hasła Wi-Fi i, po podaniu tego hasła, nastąpi połączenie. W tym środowisku nie jest wymagany żaden certyfikat.

W przypadku tego typu sieci można wyeksportować profil Wi-Fi z komputera przenośnego, a następnie dodać go do urządzenia z systemem Azure Stack Edge mini R. Aby uzyskać instrukcje, zobacz sekcję [Eksportowanie profilu Wi-Fi](#export-a-wi-fi-profile)poniżej.

> [!IMPORTANT]
> Przed utworzeniem profilu Wi-Fi dla urządzenia z systemem Azure Stack Edge mini R skontaktuj się z administratorem sieci, aby dowiedzieć się, jakie są wymagania dotyczące zabezpieczeń sieci bezprzewodowej. Nie należy testować ani używać żadnego profilu Wi-Fi na urządzeniu, dopóki nie będzie wiadomo, że sieć bezprzewodowa spełnia wymagania.

## <a name="profiles-for-wpa2---enterprise-network"></a>Profile dla sieci WPA2-Enterprise

W przypadku sieci o ochronie dostępu bezprzewodowego 2 (WPA2) — Enterprise należy skontaktować się z administratorem sieci w celu uzyskania niezbędnego Wi-Fi profilu i certyfikatu w celu połączenia urządzenia z urządzeniem Azure Stack Edge mini R do sieci.

W przypadku wysoce zabezpieczonych sieci urządzenie platformy Azure może korzystać z protokołu uwierzytelniania rozszerzonego (PEAP) z rozszerzalnym uwierzytelnianiem Protocol-Transport warstwy zabezpieczeń (EAP-TLS). Protokół PEAP z protokołem EAP-TLS używa uwierzytelniania maszyny: klient i serwer używają certyfikatów do weryfikowania tożsamości do siebie nawzajem.

> [!NOTE]
> * Uwierzytelnianie użytkowników przy użyciu protokołu PEAP Microsoft Challenge Handshake Authentication Protocol w wersji 2 (PEAP MSCHAPv2) nie jest obsługiwane w przypadku urządzeń z systemem Azure Stack Edge mini R.
> * Uwierzytelnianie za pomocą protokołu EAP-TLS jest wymagane w celu uzyskania dostępu do funkcji mini R Azure Stack Edge. Połączenie bezprzewodowe skonfigurowane przy użyciu Active Directory nie będzie działało.

Administrator sieci wygeneruje unikatowy profil Wi-Fi i certyfikat klienta dla każdego komputera. Administrator sieci decyduje, czy dla każdego urządzenia lub udostępnionego certyfikatu ma być używany osobny certyfikat.

Jeśli pracujesz w więcej niż jednej lokalizacji fizycznej w miejscu pracy, administrator sieci może potrzebować więcej niż jednego Wi-Fi profilu i certyfikatu dla połączeń bezprzewodowych.

W przypadku sieci przedsiębiorstwa zaleca się, aby nie zmieniać ustawień w profilach Wi-Fi udostępnianych przez administratora sieci. Jedyną korektą, którą można wprowadzić, jest ustawienie automatyczne połączenie. Aby uzyskać więcej informacji, zobacz [profil podstawowy](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) w Wi-Fi ustawienia dla urządzeń z systemem Windows 10 lub nowszym.

W środowisku przedsiębiorstwa o wysokim poziomie zabezpieczeń może być możliwe użycie istniejącego profilu sieci bezprzewodowej jako szablonu:

* Możesz pobrać firmowy profil sieci bezprzewodowej z komputera służbowego. Aby uzyskać instrukcje, zobacz sekcję [Eksportowanie profilu Wi-Fi](#export-a-wi-fi-profile)poniżej.

* Jeśli inne osoby w Twojej organizacji nawiązują połączenie ze swoimi urządzeniami Azure Stack Edge mini R za pośrednictwem sieci bezprzewodowej, mogą pobrać profil Wi-Fi z urządzenia. Aby uzyskać instrukcje, zobacz [Pobierz profil Wi-Fi](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## <a name="export-a-wi-fi-profile"></a>Eksportowanie profilu sieci Wi-Fi

Aby wyeksportować profil dla interfejsu Wi-Fi na komputerze, wykonaj następujące czynności:

1. Aby wyświetlić profile sieci bezprzewodowej na komputerze, w menu **Start** Otwórz **wiersz polecenia** (cmd.exe) i wprowadź następujące polecenie:

   `netsh wlan show profiles`

   Dane wyjściowe będą przypominać następujące:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. Aby wyeksportować profil, wprowadź następujące polecenie:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   Na przykład następujące polecenie zapisuje profil ContosoFTINET w formacie XML do folderu downloads dla użytkownika o nazwie `gusp` .

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Dodawanie certyfikatu, Wi-Fi profilu do urządzenia

Jeśli masz profile Wi-Fi i certyfikaty, które są potrzebne, wykonaj następujące kroki, aby skonfigurować urządzenie usługi Azure Stack Edge mini R dla połączeń bezprzewodowych:

1. W przypadku sieci WPA2-Enterprise należy przekazać do urządzenia odpowiednie certyfikaty, postępując zgodnie ze wskazówkami zawartymi w temacie [przekazywanie certyfikatów](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates).

1. Przekaż profile Wi-Fi do urządzenia Mini R, a następnie połącz się z nim, postępując zgodnie ze wskazówkami w temacie [Dodawanie, nawiązywanie połączenia z profilem Wi-Fi](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować sieć na potrzeby Azure Stack Edge w trybie mini R](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).
- Dowiedz się, jak [zarządzać Wi-Fi na Azure Stack Edge mini R](azure-stack-edge-mini-r-manage-wifi.md).
