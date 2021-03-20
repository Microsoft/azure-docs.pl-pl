---
title: 'Azure AD Connect: Włączanie zapisywania zwrotnego urządzeń | Microsoft Docs'
description: Ten dokument zawiera szczegółowe informacje dotyczące włączania zapisywania zwrotnego urządzeń przy użyciu Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d526394ac89e2d29b2002004736e8480bb15b954
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95973426"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Włączanie zapisywania zwrotnego urządzeń
> [!NOTE]
> Do zapisywania zwrotnego urządzeń jest wymagana subskrypcja Azure AD — wersja Premium.
> 
> 

Poniższa dokumentacja zawiera informacje dotyczące włączania funkcji zapisywania zwrotnego urządzeń w programie Azure AD Connect. Zapisywanie zwrotne urządzeń jest używane w następujących scenariuszach:

* Włącz funkcję [Windows Hello dla firm przy użyciu hybrydowego wdrożenia zaufania certyfikatu](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Włącz dostęp warunkowy oparty na urządzeniach do usług AD FS (2012 R2 lub nowszych) chronionych aplikacji (jednostki uzależnione zaufania).

Zapewnia to dodatkowe zabezpieczenia i gwarancję, że dostęp do aplikacji jest udzielany tylko zaufanym urządzeniom. Aby uzyskać więcej informacji na temat dostępu warunkowego, zobacz [Zarządzanie ryzykiem przy użyciu dostępu warunkowego](../conditional-access/overview.md) i [Konfigurowanie lokalnego dostępu warunkowego przy użyciu Azure Active Directory Device Registration](../devices/overview.md).

> [!IMPORTANT]
> <li>Urządzenia muszą znajdować się w tym samym lesie co użytkownicy. Ponieważ urządzenia muszą być zapisywane z powrotem do jednego lasu, ta funkcja nie obsługuje obecnie wdrożenia z wieloma lasami użytkownika.</li>
> <li>Tylko jeden obiekt konfiguracji rejestracji urządzenia można dodać do lokalnego lasu Active Directory. Ta funkcja nie jest zgodna z topologią, w której Active Directory lokalna jest synchronizowana z wieloma katalogami usługi Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Część 1. Instalowanie Azure AD Connect
Zainstaluj Azure AD Connect przy użyciu ustawień niestandardowych lub ekspresowych. Firma Microsoft zaleca rozpoczęcie od wszystkich użytkowników i grup, które zostały pomyślnie zsynchronizowane przed włączeniem funkcji zapisywania zwrotnego urządzeń.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Część 2. Włączanie zapisywania zwrotnego urządzeń w Azure AD Connect
1. Uruchom ponownie Kreatora instalacji. Wybierz pozycję **Konfiguruj opcje urządzenia** na stronie dodatkowe zadania, a następnie kliknij przycisk **dalej**. 

    ![Konfiguruj opcje urządzenia](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Nowe opcje konfigurowania urządzeń są dostępne tylko w wersji 1.1.819.0 i nowszych.

2. Na stronie Opcje urządzenia wybierz pozycję **Konfiguruj zapisywanie zwrotne urządzeń**. Opcja **wyłączenia zapisywania zwrotnego urządzeń** nie będzie dostępna, dopóki nie zostanie włączone zapisywanie zwrotne urządzeń. Kliknij przycisk **dalej** , aby przejść do następnej strony kreatora.
    ![Wybierz operację urządzenia](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Na stronie zapisywanie zwrotne zostanie wyświetlona podaną domenę jako domyślny Las zapisywania zwrotnego urządzeń.
   ![Niestandardowa wersja lasu docelowego zapisywania zwrotnego urządzeń](./media/how-to-connect-device-writeback/writebackforest.png)

4. Strona **kontenera urządzeń** zawiera opcję przygotowania usługi Active Directory przy użyciu jednej z dwóch dostępnych opcji:

    a. **Podaj poświadczenia administratora przedsiębiorstwa**: Jeśli w lesie, w którym mają być zapisywane urządzenia, są podane poświadczenia administratora przedsiębiorstwa, program Azure AD Connect przygotuje Las automatycznie podczas konfigurowania funkcji zapisywania zwrotnego urządzeń.

    b. **Pobierz skrypt programu PowerShell**: Azure AD Connect automatycznie generuje skrypt programu PowerShell umożliwiający przygotowanie usługi Active Directory do zapisywania zwrotnego urządzeń. W przypadku braku poświadczeń administratora przedsiębiorstwa w Azure AD Connect zalecane jest pobranie skryptu programu PowerShell. Podaj pobrany skrypt programu PowerShell **CreateDeviceContainer.ps1** do administratora przedsiębiorstwa w lesie, w którym będą zapisywane urządzenia z powrotem.
    ![Przygotuj Las usługi Active Directory](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    W przypadku przygotowywania lasu usługi Active Directory wykonywane są następujące operacje:
    * Jeśli jeszcze nie istnieje, program tworzy i konfiguruje nowe kontenery i obiekty w obszarze CN = Konfiguracja rejestracji urządzeń, CN = Services, CN = Configuration, [Forest-DN].
    * Jeśli jeszcze nie istnieje, program tworzy i konfiguruje nowe kontenery i obiekty w obszarze CN = RegisteredDevices, [domena-DN]. Obiekty urządzeń zostaną utworzone w tym kontenerze.
    * Ustawia odpowiednie uprawnienia na koncie łącznika usługi Azure AD, aby zarządzać urządzeniami w Active Directory.
    * Należy uruchomić tylko w jednym lesie, nawet jeśli Azure AD Connect jest instalowany w wielu lasach.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Sprawdź, czy urządzenia są zsynchronizowane z usługą Active Directory
Zapisywanie zwrotne urządzeń powinno teraz działać prawidłowo. Należy pamiętać, że może upłynąć do 3 godzin, aby obiekty urządzeń mogły być zapisywane z powrotem do usługi AD.  Aby sprawdzić, czy urządzenia są synchronizowane prawidłowo, wykonaj następujące czynności po zakończeniu reguł synchronizacji:

1. Uruchom Centrum administracyjne usługi Active Directory.
2. Rozwiń węzeł RegisteredDevices w domenie, która jest federacyjny.

   ![Active Directory zarejestrowanych urządzeń centrum administracyjnego](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Aktualnie zarejestrowane urządzenia zostaną wyświetlone na liście.

   ![Lista zarejestrowanych urządzeń Active Directory centrum administracyjnego](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Włącz dostęp warunkowy
Szczegółowe instrukcje dotyczące włączania tego scenariusza są dostępne w ramach [konfigurowania lokalnego dostępu warunkowego przy użyciu Azure Active Directory Device Registration](../devices/overview.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="the-writeback-checkbox-is-still-disabled"></a>Pole wyboru zapisywania zwrotnego jest nadal wyłączone
Jeśli pole wyboru dla zapisywania zwrotnego urządzeń nie jest włączone, mimo że wykonano powyższe kroki, następujące kroki przeprowadzą Cię przez Kreatora instalacji, zanim pole zostanie włączone.

Pierwsze rzeczy:

* Las, w którym znajdują się urządzenia, musi mieć uaktualniony schemat lasu do poziomu systemu Windows 2012 R2, dzięki czemu obiekt urządzenia i skojarzone atrybuty są obecne.
* Jeśli Kreator instalacji jest już uruchomiony, nie zostaną wykryte żadne zmiany. W takim przypadku Ukończ pracę Kreatora instalacji i uruchom go ponownie.
* Upewnij się, że konto, które podano w skrypcie inicjalizacji, jest w rzeczywistości prawidłowym użytkownikiem używanym przez łącznik Active Directory. Aby to sprawdzić, wykonaj następujące kroki:
  * Z menu Start Otwórz pozycję **usługa synchronizacji**.
  * Otwórz kartę **Łączniki** .
  * Znajdź łącznik z typem Active Directory Domain Services i wybierz go.
  * W obszarze **Akcje** wybierz pozycję **Właściwości**.
  * Przejdź do obszaru **łączenie z Active Directory lasu**. Sprawdź, czy nazwa domeny i użytkownika określona na tym ekranie jest zgodna z kontem udostępnionym dla skryptu.
    ![Konto łącznika w Service Manager synchronizacji](./media/how-to-connect-device-writeback/connectoraccount.png)

Sprawdź konfigurację w Active Directory:

* Upewnij się, że usługa rejestracji urządzeń znajduje się w następującej lokalizacji (CN = DeviceRegistrationService, CN = Device Registration Services, CN = Configuration rejestracji urządzenia, CN = Services, CN = Configuration) w kontekście nazewnictwa konfiguracji.

![Rozwiązywanie problemów, DeviceRegistrationService w przestrzeni nazw konfiguracji](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Sprawdź, czy istnieje tylko jeden obiekt konfiguracji, przeszukując przestrzeń nazw konfiguracji. Jeśli istnieje więcej niż jeden, Usuń duplikat.

![Rozwiązywanie problemów, wyszukiwanie duplikatów obiektów](./media/how-to-connect-device-writeback/troubleshoot2.png)

* W obiekcie usługa rejestracji urządzeń upewnij się, że atrybut msDS-DeviceLocation jest obecny i ma wartość. Wyszukaj tę lokalizację i upewnij się, że jest ona obecna przy użyciu obiektu objectType msDS-DeviceContainer.

![Rozwiązywanie problemów, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Rozwiązywanie problemów, Klasa obiektów RegisteredDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Sprawdź, czy konto używane przez łącznik Active Directory ma wymagane uprawnienia do kontenera zarejestrowanych urządzeń znalezionego w poprzednim kroku. Jest to oczekiwane uprawnienia w tym kontenerze:

![Rozwiązywanie problemów, weryfikowanie uprawnień do kontenera](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Sprawdź, czy konto Active Directory ma uprawnienia do obiektu konfiguracji CN = Device Registration Configuration, CN = Services, CN = Configuration.

![Rozwiązywanie problemów, weryfikowanie uprawnień do konfiguracji rejestracji urządzeń](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Dodatkowe informacje
* [Zarządzanie ryzykiem przy użyciu dostępu warunkowego](../conditional-access/overview.md)
* [Konfigurowanie lokalnego dostępu warunkowego przy użyciu Azure Active Directory Device Registration](../devices/overview.md)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).