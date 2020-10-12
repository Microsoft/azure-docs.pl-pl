---
title: Skonfiguruj serwer konfiguracji na potrzeby odzyskiwania po awarii serwerów fizycznych na platformie Azure przy użyciu Azure Site Recovery | Microsoft Docs "
description: W tym artykule opisano sposób konfigurowania lokalnego serwera konfiguracji na potrzeby odzyskiwania po awarii lokalnych serwerów fizycznych na platformie Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: d2cc3627222bcd8de5a5cb1b5c83b04c5d4ef880
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86130197"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Konfigurowanie serwera konfiguracji na potrzeby odzyskiwania po awarii serwerów fizycznych na platformie Azure

W tym artykule opisano sposób konfigurowania środowiska lokalnego do uruchamiania replikacji serwerów fizycznych z systemem Windows lub Linux do platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz już:
- Magazyn Recovery Services w [Azure Portal](https://portal.azure.com "Azure Portal").
- Komputer fizyczny, na którym ma zostać zainstalowany serwer konfiguracji.
- W przypadku wyłączenia protokołu TLS 1,0 na komputerze, na którym jest instalowany serwer konfiguracji, upewnij się, że włączono protokół TLs 1,2 oraz że na maszynie jest zainstalowana .NET Framework wersja 4,6 lub nowsza (z włączonym silną kryptografią). [Dowiedz się więcej](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Minimalne wymagania dotyczące serwera konfiguracji
W poniższej tabeli przedstawiono minimalne wymagania dotyczące sprzętu, oprogramowania i sieci dla serwera konfiguracji.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Serwery proxy oparte na protokole HTTPS nie są obsługiwane przez serwer konfiguracji.

## <a name="choose-your-protection-goals"></a>Wybierz cele ochrony

1. W Azure Portal przejdź do bloku magazyny **Recovery Services** i wybierz swój magazyn.
2. W menu **zasób** magazynu kliknij **wprowadzenie**  >  **Site Recovery**  >  **krok 1: przygotowanie**  >  **celu ochrony**infrastruktury.

    ![Wybieranie celów](./media/physical-azure-set-up-source/choose-goals.png)
3. W obszarze **cel ochrony**wybierz pozycję **na platformie Azure** , a **nie zwirtualizowany/inny**, a następnie kliknij przycisk **OK**.

    ![Wybieranie celów](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Konfigurowanie środowiska źródłowego

1. Jeśli nie masz serwera konfiguracji, w obszarze **Przygotowywanie źródła**kliknij pozycję **+ serwer konfiguracji** , aby dodać jeden.

   ![Konfiguracja źródła](./media/physical-azure-set-up-source/plus-config-srv.png)
2. W bloku **Dodawanie serwera** Sprawdź, czy **serwer konfiguracji** jest wyświetlany w polu **Typ serwera**.
4. Pobierz plik instalacyjny programu Site Recovery Unified Setup.
5. Pobierz klucz rejestracji magazynu. Po uruchomieniu ujednoliconej konfiguracji wymagany jest klucz rejestracji. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

    ![Konfiguracja źródła](./media/physical-azure-set-up-source/set-source2.png)
6. Na komputerze, którego używasz jako serwera konfiguracji, uruchom **Azure Site Recovery ujednoliconą konfigurację** , aby zainstalować serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy.

#### <a name="run-azure-site-recovery-unified-setup"></a>Uruchom Azure Site Recovery ujednoliconą konfigurację

> [!TIP]
> Rejestracja serwera konfiguracji kończy się niepowodzeniem, jeśli czas zegara systemowego komputera jest większy niż pięć minut od czasu lokalnego. Przed rozpoczęciem instalacji zsynchronizuj zegar systemowy z [serwerem czasu](/windows-server/networking/windows-time-service/windows-time-service-top) .

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Serwer konfiguracji można zainstalować za pomocą wiersza polecenia. [Dowiedz się więcej](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Typowe problemy

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Następne kroki

Następny krok obejmuje [skonfigurowanie środowiska docelowego](physical-azure-set-up-target.md) na platformie Azure.
