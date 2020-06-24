---
title: Odzyskiwanie po awarii całego regionu
description: Dowiedz się, w jaki sposób Azure App Service pomaga zachować funkcje ciągłości działania i odzyskiwania po awarii (BCDR). Odzyskaj aplikację z awarii całego regionu na platformie Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 8c57cf5054bea898370cdccc7bea4243877d27b5
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84947076"
---
# <a name="move-an-app-service-app-to-another-region"></a>Przenoszenie aplikacji App Service do innego regionu

W tym artykule opisano sposób przenoszenia zasobów usługi App Service z powrotem do trybu online w innym regionie platformy Azure podczas awarii, która ma wpływ na cały region świadczenia usługi Azure. Gdy awaria powoduje przeniesienie całego regionu platformy Azure do trybu offline, wszystkie App Service aplikacje hostowane w tym regionie są umieszczane w trybie odzyskiwania po awarii. Dostępne są funkcje ułatwiające przywrócenie aplikacji do innego regionu lub odzyskiwanie plików z aplikacji, której to dotyczy.

Zasoby App Service są specyficzne dla regionu i nie można ich przenosić między regionami. Należy przywrócić aplikację do nowej aplikacji w innym regionie, a następnie utworzyć konfiguracje dublowania lub zasoby dla nowej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Brak. [Przywracanie z migawki](app-service-web-restore-snapshots.md) zwykle wymaga warstwy **Premium** , ale w trybie odzyskiwania po awarii jest automatycznie włączana dla aplikacji, której dotyczy problem, niezależnie od tego, która warstwa ma wpływ na aplikację.

## <a name="prepare"></a>Przygotowanie

Zidentyfikuj wszystkie zasoby App Service aktualnie używane przez aplikację, której dotyczy problem. Przykład:

- Aplikacje usługi App Service
- [Plany usługi App Service](overview-hosting-plans.md)
- [Miejsca wdrożenia](deploy-staging-slots.md)
- [Domeny niestandardowe zakupione na platformie Azure](manage-custom-dns-buy-domain.md)
- [Certyfikaty SSL](configure-ssl-certificate.md)
- [Integracja z usługą Azure Virtual Network](web-sites-integrate-with-vnet.md)
- [Połączenia hybrydowe](app-service-hybrid-connections.md).
- [Zarządzane tożsamości](overview-managed-identity.md)
- [Ustawienia kopii zapasowej](manage-backup.md)

Niektóre zasoby, takie jak zaimportowane certyfikaty lub połączenia hybrydowe, zawierają integrację z innymi usługami platformy Azure. Aby uzyskać informacje na temat przenoszenia tych zasobów między regionami, zapoznaj się z dokumentacją odpowiednich usług.

## <a name="restore-app-to-a-different-region"></a>Przywróć aplikację do innego regionu

1. Utwórz nową aplikację App Service w *innym* regionie świadczenia usługi Azure niż aplikacja, której dotyczy problem. Jest to aplikacja docelowa w scenariuszu odzyskiwania po awarii.

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania mającej wpływ na aplikację. W nieprawidłowym regionie platformy Azure aplikacja, której dotyczy problem, wyświetla tekst ostrzegawczy. Kliknij tekst ostrzegawczy.

    ![](media/manage-disaster-recovery/restore-start.png)

1. Na stronie **przywracanie kopii zapasowej** Skonfiguruj operację przywracania zgodnie z poniższą tabelą. Po zakończeniu kliknij przycisk **OK**.

   | Ustawienie | Wartość | Opis |
   |-|-|-|
   | **Migawka (wersja zapoznawcza)** | Wybierz migawkę. | Dostępne są dwie ostatnie migawki. |
   | **Miejsce docelowe przywracania** | **Istniejąca aplikacja** | Kliknij poniżej uwagę, **Aby zmienić aplikację docelową przywracania** i wybrać aplikację docelową. W scenariuszu awarii można przywrócić migawkę tylko do aplikacji w innym regionie świadczenia usługi Azure. |
   | **Przywróć konfigurację lokacji** | **Tak** | |

    ![](media/manage-disaster-recovery/restore-configure.png)

3. Skonfiguruj [wszystkie inne elementy](#prepare) w aplikacji docelowej, aby zdublować aplikację, której dotyczy problem, i sprawdź konfigurację.

4. Gdy domena niestandardowa ma wskazywać aplikację docelową, należy ponownie [zamapować nazwę domeny](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

## <a name="recover-app-content-only"></a>Odzyskaj tylko zawartość aplikacji

Jeśli chcesz tylko odzyskać pliki z aplikacji, której dotyczy problem, bez przywracania, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania mającej wpływ na aplikację, a następnie kliknij pozycję **Pobierz profil publikowania**.

    ![](media/manage-disaster-recovery/get-publish-profile.png)

1. Otwórz pobrany plik i znajdź profil publikowania, który zawiera `ReadOnly - FTP` nazwę. Jest to profil odzyskiwania po awarii. Przykład:

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    Skopiuj trzy wartości atrybutów: 
        
    - `publishUrl`: Nazwa hosta FTP
    - `userName`i `userPWD` : poświadczenia FTP

1. Użyj wybranego klienta FTP, aby nawiązać połączenie z hostem FTP aplikacji, których dotyczy problem, przy użyciu nazwy hosta i poświadczeń.

1. Po nawiązaniu połączenia Pobierz cały folder */site/wwwroot* . Poniższy zrzut ekranu przedstawia sposób pobierania w [FileZilla](https://filezilla-project.org/).

    ![](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>Następne kroki
[Przywracanie aplikacji na platformie Azure z migawki](app-service-web-restore-snapshots.md)
