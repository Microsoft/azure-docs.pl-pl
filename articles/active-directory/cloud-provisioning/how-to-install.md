---
title: Instalowanie agenta aprowizacji w chmurze programu Azure AD Connect
description: W tym artykule opisano sposób instalowania agenta aprowizacji Azure AD Connect Cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79263350"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalowanie agenta aprowizacji w chmurze programu Azure AD Connect
Ten dokument przeprowadzi Cię przez proces instalacji Azure Active Directory (Azure AD) Połącz agenta aprowizacji i sposób jego początkowego skonfigurowania w Azure Portal.

>[!IMPORTANT]
>W poniższych instrukcjach instalacji założono, że spełniono wszystkie [wymagania wstępne](how-to-prerequisites.md) .

Instalowanie i Konfigurowanie Azure AD Connect aprowizacji jest realizowane w następujących krokach:
    
- [Instalowanie agenta](#install-the-agent)
- [Weryfikuj instalację agenta](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalowanie agenta
Aby zainstalować agenta, wykonaj następujące kroki.

1. Zaloguj się na serwerze, który będzie używany z uprawnieniami administratora przedsiębiorstwa.
1. Przejdź do witryny Azure Portal. Po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)** > **Przejrzyj wszystkich agentów**.
1. Pobierz Azure AD Connect agenta aprowizacji z Azure Portal.

   ![Pobierz agenta lokalnego](media/how-to-install/install9.png)</br>
1. Uruchom Instalatora aprowizacji Azure AD Connect (AADConnectProvisioningAgent. Installer).
1. Na ekranie **Microsoft Azure AD Połącz pakiet agenta aprowizacji** zaakceptuj postanowienia licencyjne i wybierz pozycję **Zainstaluj**.

   ![Ekran Microsoft Azure AD łączenia się z pakietem agenta aprowizacji](media/how-to-install/install1.png)</br>

1. Po zakończeniu tej operacji zostanie uruchomiony Kreator konfiguracji. Zaloguj się przy użyciu konta administratora globalnego usługi Azure AD.
1. Na ekranie **połącz Active Directory** wybierz pozycję **Dodaj katalog**. Następnie zaloguj się przy użyciu konta administratora Active Directory. Ta operacja umożliwia dodanie katalogu lokalnego. Wybierz pozycję **Dalej**.

   ![Ekran Active Directory łączenia](media/how-to-install/install3.png)</br>

1. Na ekranie **Konfiguracja ukończona** wybierz pozycję **Potwierdź**. Ta operacja rejestruje i ponownie uruchamia agenta.

   ![Ekran ukończono konfigurację](media/how-to-install/install4.png)</br>

1. Po zakończeniu tej operacji powinna zostać wyświetlona informacja o tym, że **Konfiguracja agenta została pomyślnie zweryfikowana.** Wybierz pozycję **Zakończ**.

   ![Przycisk Zakończ](media/how-to-install/install5.png)</br>
1. Jeśli nadal widzisz ekran początkowy **Microsoft Azure AD łączenie się z pakietem agenta aprowizacji** , wybierz pozycję **Zamknij**.

## <a name="verify-agent-installation"></a>Weryfikuj instalację agenta
Weryfikacja agenta odbywa się w Azure Portal i na serwerze lokalnym, na którym jest uruchomiony Agent programu.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta Azure Portal
Aby sprawdzić, czy Agent jest widziany przez platformę Azure, wykonaj następujące kroki.

1. Zaloguj się do witryny Azure Portal.
1. Po lewej stronie wybierz pozycję **Azure Active Directory** > **Azure AD Connect**. W centrum wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)**.

   ![Azure Portal](media/how-to-install/install6.png)</br>

1.  Na ekranie **Azure AD Provisioning (wersja zapoznawcza)** wybierz pozycję **Przejrzyj wszystkich agentów**.

    ![Przejrzyj wszystkie opcje agentów](media/how-to-install/install7.png)</br>
 
1. Na ekranie **agenci aprowizacji lokalnego** zobaczysz zainstalowanych agentów. Sprawdź, czy dany Agent jest tam i jest oznaczony jako *aktywny*.

   ![Ekran lokalnych agentów aprowizacji](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Weryfikowanie portu
Aby sprawdzić, czy platforma Azure nasłuchuje na porcie 443 i czy Agent może się z nim komunikować, wykonaj następujące kroki.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ten test sprawdza, czy agenci mogą komunikować się z platformą Azure przez port 443. Otwórz przeglądarkę i przejdź do poprzedniego adresu URL z serwera, na którym zainstalowano agenta.

![Weryfikacja osiągalności portów](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Na serwerze lokalnym
Aby sprawdzić, czy agent działa, wykonaj następujące kroki.

1.  Zaloguj się na serwerze przy użyciu konta administratora.
1.  Otwórz **usługi** , przechodząc do niej lub**Run** > naciskając **Start** > pozycję Uruchom**Services. msc**.
1.  W obszarze **usługi**upewnij się, że **Microsoft Azure AD Connect agent Aktualizator** i **Microsoft Azure AD Connect Agent aprowizacji** jest tam i ich stan jest *uruchomiony*.

    ![Ekran usług](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Agent został zainstalowany, ale przed rozpoczęciem synchronizowania użytkowników musi zostać skonfigurowany i włączony. Aby skonfigurować nowego agenta, zobacz [Tworzenie nowej konfiguracji na potrzeby aprowizacji Azure AD Connect opartej na chmurze](how-to-configure.md).



## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
 
