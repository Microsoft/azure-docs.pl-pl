---
title: Instalowanie agenta aprowizacji w chmurze programu Azure AD Connect
description: Dowiedz się, jak zainstalować agenta aprowizacji Azure AD Connect chmury i jak skonfigurować go w Azure Portal.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9553608fdd72d4dec4c1572e0d38cd460018a469
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584344"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalowanie agenta aprowizacji w chmurze programu Azure AD Connect
Ten dokument przeprowadzi Cię przez proces instalacji Azure Active Directory (Azure AD) Połącz agenta aprowizacji i sposób jego początkowego skonfigurowania w Azure Portal.

>[!IMPORTANT]
>W poniższych instrukcjach instalacji założono, że spełniono wszystkie [wymagania wstępne](how-to-prerequisites.md) .

Instalowanie i Konfigurowanie Azure AD Connect aprowizacji jest realizowane w następujących krokach:

- [Konta usług zarządzane przez grupę](#group-managed-service-accounts) 
- [Instalowanie agenta](#install-the-agent)
- [Weryfikuj instalację agenta](#verify-agent-installation)


## <a name="group-managed-service-accounts"></a>Konta usług zarządzane przez grupę
Konto usługi zarządzane przez grupę to zarządzane konto domeny zapewniające automatyczne zarządzanie hasłami, uproszczone zarządzanie nazwami główna usługi (SPN), możliwość delegowania zarządzania do innych administratorów, a także rozszerza te funkcje na wiele serwerów.  Azure AD Connect Cloud Sync obsługuje i zaleca użycie konta usługi zarządzanego przez grupę do uruchamiania agenta.  Aby uzyskać więcej informacji na temat gMSA, zobacz [konta usług zarządzane przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>Uaktualnianie istniejącego agenta w celu korzystania z konta gMSA
Aby uaktualnić istniejącego agenta do korzystania z konta gMSA utworzonego podczas instalacji, wystarczy zaktualizować usługę agenta do najnowszej wersji, uruchamiając AADConnectProvisioningAgent.msi.  Spowoduje to uaktualnienie usługi do najnowszej wersji.  Teraz ponownie uruchom Kreatora instalacji i podaj poświadczenia, aby utworzyć konto po wyświetleniu monitu.



## <a name="install-the-agent"></a>Instalowanie agenta
Aby zainstalować agenta, wykonaj następujące kroki.

 1. Zaloguj się na serwerze, który będzie używany z uprawnieniami administratora przedsiębiorstwa.
 2. Zaloguj się do Azure Portal, a następnie przejdź do **Azure Active Directory**.
 3. W menu po lewej stronie wybierz pozycję **Azure AD Connect**.
 4. Wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)**  >  **Przejrzyj wszystkich agentów**.
 5. Pobierz Azure AD Connect agenta aprowizacji z Azure Portal.
   ![Pobierz agenta lokalnego](media/how-to-install/install-9.png)</br>
 6. Uruchom AADConnectProvisioningAgent.msi Instalatora aprowizacji Azure AD Connect.
 7. Na ekranie **Microsoft Azure AD Połącz pakiet agenta aprowizacji** zaakceptuj postanowienia licencyjne i wybierz pozycję **Zainstaluj**.
   ![Ekran Microsoft Azure AD łączenia się z pakietem agenta aprowizacji](media/how-to-install/install-1.png)</br>
 8. Po zakończeniu tej operacji zostanie uruchomiony Kreator konfiguracji. Zaloguj się przy użyciu konta administratora globalnego usługi Azure AD.
 9. Na stronie **Konfiguruj konto usługi** wybierz opcję **Utwórz gMSA** lub **Użyj niestandardowego gMSA**.  Jeśli zezwolisz agentowi na utworzenie konta, będzie on miał nazwę provAgentgMSA $. Jeśli określisz **użycie niestandardowej gMSA** , zostanie wyświetlony monit o podanie tego konta.
 10. Wprowadź poświadczenia administratora domeny, aby utworzyć konto usługi zarządzane przez grupę, które będzie używane do uruchamiania usługi agenta. Kliknij przycisk **Dalej**.  
   ![Utwórz gMSA](media/how-to-install/install-12.png)</br>
 11. Na ekranie **połącz Active Directory** wybierz pozycję **Dodaj katalog**. Następnie zaloguj się przy użyciu konta administratora Active Directory. Ta operacja umożliwia dodanie katalogu lokalnego. 
 12. Opcjonalnie można zarządzać preferencjami kontrolerów domeny, które będą używane przez agenta, wybierając **pozycję Wybierz priorytet kontrolera domeny** i porządkując listę kontrolerów domeny.   Kliknij przycisk **OK**.
  ![Zamawianie kontrolerów domeny](media/how-to-install/install-2a.png)</br>
 13. Wybierz pozycję **Dalej**.
  ![Ekran Active Directory łączenia](media/how-to-install/install-3a.png)</br>
 14.  Na ekranie **Instalacja agenta** Potwierdź ustawienia i konto, które zostanie utworzone, a następnie kliknij przycisk **Potwierdź**.
  ![Potwierdź ustawienia](media/how-to-install/install-11.png)</br>
 15. Po zakończeniu tej operacji powinna zostać wyświetlona **Instalacja agenta.** Wybierz pozycję **Zakończ**.
  ![Ekran ukończono konfigurację](media/how-to-install/install-4a.png)</br>
1. Jeśli nadal widzisz ekran początkowy **Microsoft Azure AD łączenie się z pakietem agenta aprowizacji** , wybierz pozycję **Zamknij**.

## <a name="verify-agent-installation"></a>Weryfikuj instalację agenta
Weryfikacja agenta odbywa się w Azure Portal i na serwerze lokalnym, na którym jest uruchomiony Agent programu.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta Azure Portal
Aby sprawdzić, czy Agent jest widziany przez platformę Azure, wykonaj następujące kroki.

1. Zaloguj się do witryny Azure Portal.
1. Po lewej stronie wybierz pozycję **Azure Active Directory**  >  **Azure AD Connect**. W centrum wybierz pozycję **Zarządzaj Provisioning (wersja zapoznawcza)**.

   ![Witryna Azure Portal](media/how-to-install/install-6.png)</br>

1.  Na ekranie **Azure AD Provisioning (wersja zapoznawcza)** wybierz pozycję **Przejrzyj wszystkich agentów**.

    ![Przejrzyj wszystkie opcje agentów](media/how-to-install/install-7.png)</br>
 
1. Na ekranie **agenci aprowizacji lokalnego** zobaczysz zainstalowanych agentów. Sprawdź, czy dany Agent jest tam i jest oznaczony jako *aktywny*.

   ![Ekran lokalnych agentów aprowizacji](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>Na serwerze lokalnym
Aby sprawdzić, czy agent działa, wykonaj następujące kroki.

1.  Zaloguj się na serwerze przy użyciu konta administratora.
1.  Otwórz **usługi** , przechodząc do niej lub naciskając **pozycję Uruchom**  >    >  **Services. msc**.
1.  W obszarze **usługi** upewnij się, że **Microsoft Azure AD Connect agent Aktualizator** i **Microsoft Azure AD Connect Agent aprowizacji** jest tam i ich stan jest *uruchomiony*.

    ![Ekran usług](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>Agent został zainstalowany, ale przed rozpoczęciem synchronizowania użytkowników musi zostać skonfigurowany i włączony. Aby skonfigurować nowego agenta, zobacz [Tworzenie nowej konfiguracji na potrzeby aprowizacji Azure AD Connect opartej na chmurze](how-to-configure.md).




## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
