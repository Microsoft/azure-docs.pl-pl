---
title: Samouczek — integruje istniejący Las i nowy las z jedną dzierżawą usługi Azure AD przy użyciu Azure AD Connect synchronizacji w chmurze.
description: Dowiedz się, jak dodać synchronizację w chmurze do istniejącego środowiska tożsamości hybrydowej.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64536024af7e939de2bd8f98a9ce14bb4df3303f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614608"
---
# <a name="integrate-an-existing-forest-and-a-new-forest-with-a-single-azure-ad-tenant"></a>Integrowanie istniejącego i nowego lasu z jedną dzierżawą usługi Azure AD

Ten samouczek przeprowadzi Cię przez proces dodawania synchronizacji w chmurze do istniejącego hybrydowego środowiska tożsamości. 

![Utwórz](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Możesz użyć środowiska utworzonego w tym samouczku do testowania lub w celu uzyskania bardziej znajomości sposobu działania tożsamości hybrydowej. 

W tym scenariuszu istnieje synchronizowanie istniejącego lasu przy użyciu Azure AD Connect synchronizacji z dzierżawą usługi Azure AD. I masz nowy las, który chcesz zsynchronizować z tą samą dzierżawą usługi Azure AD. Zostanie skonfigurowana synchronizacja z chmurą dla nowego lasu. 

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="in-the-azure-active-directory-admin-center"></a>W centrum administracyjnym Azure Active Directory

1. Utwórz konto administratora globalnego tylko w chmurze w dzierżawie usługi Azure AD. W ten sposób możesz zarządzać konfiguracją dzierżawy, jeśli usługi lokalne zakończą się niepowodzeniem lub staną się niedostępne. Dowiedz się więcej [na temat dodawania konta administratora globalnego tylko w chmurze](../fundamentals/add-users-azure-active-directory.md). Wykonanie tego kroku jest niezwykle ważne, aby upewnić się, że dzierżawa nie została zablokowana.
2. Dodaj co najmniej jedną [niestandardową nazwę domeny](../fundamentals/add-custom-domain.md) do dzierżawy usługi Azure AD. Użytkownicy mogą logować się przy użyciu jednej z tych nazw domen.

### <a name="in-your-on-premises-environment"></a>W środowisku lokalnym

1. Identyfikowanie serwera hosta przyłączonego do domeny z systemem Windows Server 2012 R2 lub nowszym z co najmniej 4 GB pamięci RAM i .NET 4.7.1 + środowisko uruchomieniowe 

2. Jeśli między serwerami i usługą Azure AD istnieje Zapora, skonfiguruj następujące elementy:
   - Upewnij się, że agenci mogą wykonywać żądania *wychodzące* do usługi Azure AD za pośrednictwem następujących portów:

     | Numer portu | Zastosowanie |
     | --- | --- |
     | **80** | Pobiera listy odwołania certyfikatów (CRL) podczas weryfikacji certyfikatu TLS/SSL |
     | **443** | Obsługa komunikacji wychodzącej do usługi |
     | **8080** (opcjonalnie) | Agenci raportują swój stan co 10 minut przez port 8080, jeśli port 443 jest niedostępny. Informacje o stanie agentów są wyświetlane w portalu usługi Azure AD. |
     
     Jeśli reguły zapory są stosowane w zależności od użytkowników generujących ruch, otwórz te porty dla ruchu przychodzącego z usług systemu Windows działających jako usługi sieciowe.
   - Jeśli zapora lub serwer proxy umożliwia określenie bezpiecznych sufiksów, Dodaj połączenia do **\* . msappproxy.NET** i **\* . ServiceBus.Windows.NET**. W przeciwnym razie Zezwól na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są aktualizowane co tydzień.
   - Twoje Agenci muszą mieć dostęp do **login.Windows.NET** i **login.microsoftonline.com** na potrzeby rejestracji wstępnej. Należy również otworzyć Zaporę dla tych adresów URL.
   - Aby sprawdzić poprawność certyfikatu, Odblokuj następujące adresy URL: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80** i **www \. Microsoft.com:80**. Ponieważ te adresy URL są używane do sprawdzania poprawności certyfikatu z innymi produktami firmy Microsoft, te adresy URL mogą już być odblokowane.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Zainstaluj agenta aprowizacji Azure AD Connect
1. Zaloguj się na serwerze przyłączonym do domeny.  Jeśli używasz podstawowego samouczka  [usługi AD i środowiska Azure](tutorial-basic-ad-azure.md) , będzie to DC1.
2. Zaloguj się do Azure Portal przy użyciu poświadczeń administratora globalnego tylko w chmurze.
3. Po lewej stronie wybierz pozycję **Azure Active Directory**, kliknij pozycję **Azure AD Connect** i w centrum wybierz pozycję **Zarządzaj synchronizacją chmury**.</br>
![Azure Portal](media/how-to-install/install-6.png)</br>
4. Kliknij pozycję "Pobierz agenta"
5. Uruchom agenta aprowizacji Azure AD Connect
6. Na ekranie powitalnym **Zaakceptuj** postanowienia licencyjne, a następnie kliknij przycisk **Instaluj**.</br>
![Zrzut ekranu przedstawiający ekran powitalny "Microsoft Azure AD łączenie pakietu agenta aprowizacji".](media/how-to-install/install-1.png)</br>

7. Po zakończeniu tej operacji zostanie uruchomiony Kreator konfiguracji.  Zaloguj się przy użyciu konta administratora globalnego usługi Azure AD.  Należy pamiętać, że jeśli masz włączone rozszerzone zabezpieczenia programu IE, spowoduje to zablokowanie logowania.  W takim przypadku Zamknij instalację, wyłącz zaawansowane zabezpieczenia programu IE w Menedżer serwera, a następnie kliknij **Kreatora agenta aprowizacji usługi AAD Connect** , aby ponownie uruchomić instalację.
8. Na ekranie **połącz Active Directory** kliknij pozycję **Dodaj katalog** , a następnie zaloguj się przy użyciu konta administratora domeny Active Directory. Konto administratora domeny nie powinno mieć wymagań dotyczących zmiany hasła. W przypadku wygaśnięcia lub zmiany hasła należy ponownie skonfigurować agenta przy użyciu nowych poświadczeń. Ta operacja spowoduje dodanie katalogu lokalnego.  Kliknij przycisk **Dalej**.</br>
![Zrzut ekranu przedstawiający ekran "Connect Active Directory".](media/how-to-install/install-3a.png)</br>

9. Na ekranie **Konfiguracja ukończona** kliknij przycisk **Potwierdź**.  Ta operacja spowoduje zarejestrowanie i ponowne uruchomienie agenta.</br>
![Zrzut ekranu przedstawiający ekran "Konfiguracja ukończona".](media/how-to-install/install-4a.png)</br>

10. Po zakończeniu tej operacji powinna zostać wyświetlona informacja: **Konfiguracja agenta została pomyślnie zweryfikowana.**  Możesz kliknąć przycisk **Zakończ**.</br>
![Ekran powitalny](media/how-to-install/install-5.png)</br>
11. Jeśli nadal widzisz początkowy ekran powitalny, kliknij przycisk **Zamknij**.


## <a name="verify-agent-installation"></a>Weryfikuj instalację agenta
Weryfikacja agenta odbywa się w Azure Portal i na serwerze lokalnym, na którym jest uruchomiony Agent programu.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta Azure Portal
Aby sprawdzić, czy Agent jest widziany przez platformę Azure, wykonaj następujące kroki:

1. Zaloguj się w witrynie Azure Portal.
2. Po lewej stronie wybierz pozycję **Azure Active Directory**, kliknij pozycję **Azure AD Connect** i w centrum wybierz pozycję **Zarządzaj synchronizacją chmury**.</br>
![Azure Portal](media/how-to-install/install-6.png)</br>

3.  Na ekranie **Azure AD Connect Cloud Sync** kliknij kolejno pozycje **Przejrzyj Wszyscy agenci**.
![Inicjowanie obsługi usługi Azure AD](media/how-to-install/install-7.png)</br>
 
4. Na **ekranie agenci aprowizacji lokalnego** zostaną zainstalowani agenci.  Sprawdź, czy dany Agent jest tam i jest oznaczony jako **aktywny**.
![Agenci aprowizacji](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Na serwerze lokalnym
Aby sprawdzić, czy agent działa, wykonaj następujące kroki:

1.  Zaloguj się na serwerze przy użyciu konta administratora
2.  Otwórz **usługi** , przechodząc do niej lub uruchamiając/Uruchom/Services. msc.
3.  W obszarze **usługi** upewnij się, że są obecne **Microsoft Azure AD Connect agent Aktualizator** i **Microsoft Azure AD Connecting Agent** , a stan jest **uruchomiony**.
![Usługi](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Konfigurowanie Azure AD Connect synchronizacji w chmurze
 Wykonaj następujące kroki, aby skonfigurować aprowizacji

1.  Zaloguj się do portalu usługi Azure AD.
2.  Kliknij **Azure Active Directory**
3.  Kliknij **Azure AD Connect**
4.  Wybierz pozycję **Zarządzaj** 
 ![ zrzutem ekranu synchronizacji chmury, pokazując link "Zarządzaj synchronizacją w chmurze".](media/how-to-configure/manage-1.png)
5.  Kliknij pozycję **Nowy** 
 ![ zrzut ekranu konfiguracji ekranu Azure AD Connect synchronizacji w chmurze z wyróżnionym linkiem "Nowa konfiguracja".](media/tutorial-single-forest/configure-1.png)
7.  Na ekranie Konfiguracja wprowadź **wiadomość e-mail z powiadomieniem**, Przenieś selektor, aby go **włączyć** , a następnie kliknij przycisk **Zapisz**.
![Zrzut ekranu przedstawiający ekran Konfigurowanie i wypełnianie wiadomości e-mail z powiadomieniem.](media/how-to-configure/configure-2.png)
1.  Stan konfiguracji powinien być teraz w **dobrej kondycji**.
![Zrzut ekranu przedstawiający ekran Azure AD Connect synchronizacji w chmurze, w którym jest wyświetlany stan kondycji.](media/how-to-configure/manage-4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Weryfikowanie utworzenia użytkowników i przeprowadzania synchronizacji
Teraz sprawdź, czy użytkownicy, którzy mieli dostęp do naszego katalogu lokalnego, zostali zsynchronizowani i teraz istnieją w naszej dzierżawie usługi Azure AD.  Ukończenie tego procesu może potrwać kilka godzin.  Aby potwierdzić, że użytkownicy zostali zsynchronizowani, wykonaj następujące czynności.


1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta, które ma subskrypcję platformy Azure.
2. W obszarze po lewej stronie wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
4. Zweryfikuj, czy nowi użytkownicy są widoczni w dzierżawie</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testowanie logowania się przy użyciu jednego z kont użytkowników

1. Przejdź do [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Zaloguj się przy użyciu konta użytkownika utworzonego w nowej dzierżawie.  Należy zalogować się przy użyciu następującego formatu: (user@domain.onmicrosoft.com). Użyj tego samego hasła, za pomocą którego użytkownik loguje się lokalnie.</br>
   ![Weryfikacja](media/tutorial-single-forest/verify-1.png)</br>

Pomyślnie skonfigurowano hybrydowe środowisko tożsamości, za pomocą którego można testować i zapoznać się z możliwościami oferowanymi przez platformę Azure.

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)