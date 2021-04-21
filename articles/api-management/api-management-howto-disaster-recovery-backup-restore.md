---
title: Implementowanie odzyskiwania po awarii przy użyciu kopii zapasowej i przywracania w API Management
titleSuffix: Azure API Management
description: Dowiedz się, jak używać kopii zapasowych i przywracania do odzyskiwania po awarii w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/05/2020
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c7901dd4a238b27a31f95f1e22ddf9dc1ae5327a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813071"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Jak zaimplementować odzyskiwanie po awarii przy użyciu funkcji tworzenia i przywracania kopii zapasowych w usłudze Azure API Management

Publikowanie interfejsów API i zarządzanie nimi za pośrednictwem usługi Azure API Management pozwala korzystać z odporności na uszkodzenia i możliwości infrastruktury, które w przeciwnym razie należy ręcznie zaprojektować, zaimplementować i zarządzać nimi. Platforma Azure ogranicza znaczną część potencjalnych awarii przy ułamku kosztów.

Aby odzyskać dane po problemach z dostępnością, które mają wpływ na region hostowania usługi API Management, należy przygotować usługę w innym regionie w dowolnym momencie. W zależności od celu czasu odzyskiwania warto zachować usługę rezerwową w co najmniej jednym regionie. Możesz również spróbować zachować synchronizację ich konfiguracji i zawartości z aktywną usługą zgodnie z celem punktu odzyskiwania. Funkcje tworzenia kopii zapasowej i przywracania usługi stanowią niezbędne bloki konstrukcyjne do zaimplementowania strategii odzyskiwania po awarii.

Operacje tworzenia kopii zapasowej i przywracania mogą być również używane do replikowania API Management konfiguracji usługi między środowiskami operacyjnymi, na przykład w środowiskach deweloperskich i przejściowych. Należy również pamiętać, że dane środowiska uruchomieniowego, takie jak użytkownicy i subskrypcje, również zostaną skopiowane, co może nie zawsze być pożądane.

W tym przewodniku pokazano, jak zautomatyzować operacje tworzenia kopii zapasowych i przywracania oraz jak zapewnić pomyślne uwierzytelnianie żądań kopii zapasowej i przywracania przez Azure Resource Manager.

> [!IMPORTANT]
> Operacja przywracania nie zmienia niestandardowej konfiguracji nazwy hosta usługi docelowej. Zalecamy używanie tej samej niestandardowej nazwy hosta i certyfikatu TLS dla usług aktywnych i rezerwowych, tak aby po zakończeniu operacji przywracania ruch można było ponownie skierować do wystąpienia rezerwowego przez prostą zmianę rekordu CNAME systemu DNS.
>
> Operacja tworzenia kopii zapasowej nie przechwytuje wstępnie zagregowanych danych dziennika używanych w raportach wyświetlanych w bloku Analiza w Azure Portal.

> [!WARNING]
> Każda kopia zapasowa wygasa po 30 dniach. Jeśli spróbujemy przywrócić kopię zapasową po upływie 30-dniowego okresu ważności, przywracanie nie powiedzie się z `Cannot restore: backup expired` komunikatem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Uwierzytelnianie Azure Resource Manager żądaniami

> [!IMPORTANT]
> Interfejs API REST do tworzenia kopii zapasowych i przywracania używa Azure Resource Manager i ma inny mechanizm uwierzytelniania niż interfejsy API REST do zarządzania jednostkami API Management użytkownika. Kroki opisane w tej sekcji opisują sposób uwierzytelniania Azure Resource Manager żądań. Aby uzyskać więcej informacji, zobacz [Authenticating Azure Resource Manager requests (Uwierzytelnianie Azure Resource Manager żądań).](/rest/api/index)

Wszystkie zadania wykonywane na zasobach przy użyciu serwera Azure Resource Manager muszą zostać uwierzytelnione za pomocą Azure Active Directory następujące czynności:

-   Dodaj aplikację do dzierżawy Azure Active Directory dzierżawy.
-   Ustaw uprawnienia dla dodanej aplikacji.
-   Pobierz token do uwierzytelniania żądań do Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji Azure Active Directory aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Korzystając z subskrypcji zawierającej twoje API Management usługi, przejdź do **karty** Rejestracje aplikacji w skrypcie **Azure Active Directory** (Azure Active Directory > Zarządzanie/Rejestracje aplikacji).

    > [!NOTE]
    > Jeśli Azure Active Directory domyślny katalog nie jest widoczny dla Twojego konta, skontaktuj się z administratorem subskrypcji platformy Azure, aby udzielić wymaganych uprawnień do Twojego konta.

3. Kliknij pozycję **Rejestrowanie nowej aplikacji**.

    Po **prawej** stronie zostanie wyświetlone okno Tworzenie. W tym miejscu należy wprowadzić odpowiednie informacje dotyczące aplikacji usługi AAD.

4. Wprowadź nazwę aplikacji.
5. Jako typ aplikacji wybierz pozycję **Natywna**.
6. Wprowadź adres URL symbolu zastępczego, taki jak w polu `http://resources` **Redirect URI**(Adres URI przekierowania), ponieważ jest to wymagane pole, ale wartość nie będzie używana później. Kliknij pole wyboru, aby zapisać aplikację.
7. Kliknij pozycję **Utwórz**.

### <a name="add-an-application"></a>Dodawanie aplikacji

1. Po utworzeniu aplikacji kliknij pozycję **Uprawnienia interfejsu API.**
2. Kliknij pozycję **+ Dodaj uprawnienie**.
4. Naciśnij **pozycję Wybierz interfejsy API firmy Microsoft.**
5. Wybierz **pozycję Azure Service Management.**
6. Naciśnij **pozycję Wybierz**.

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/add-app-permission.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania uprawnień aplikacji."::: 

7. Kliknij **pozycję Uprawnienia delegowane** obok nowo dodanej aplikacji i zaznacz pole wyboru **Dostęp do usługi Azure Service Management (wersja zapoznawcza).**

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/delegated-app-permission.png" alt-text="Zrzut ekranu przedstawiający dodawanie delegowanych uprawnień aplikacji.":::

8. Naciśnij **pozycję Wybierz**.
9. Kliknij **pozycję Dodaj uprawnienia.**

### <a name="configuring-your-app"></a>Konfigurowanie aplikacji

Przed wywołaniem interfejsów API, które generują kopię zapasową i przywracają ją, należy uzyskać token. W poniższym przykładzie token jest pobierany za pomocą pakietu NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Zastąp `{tenant id}` elementy , i , korzystając z następujących `{application id}` `{redirect uri}` instrukcji:

1. Zastąp `{tenant id}` identyfikatorem dzierżawy Azure Active Directory utworzonej aplikacji. Aby uzyskać dostęp do identyfikatora, kliknij **pozycję Rejestracje aplikacji**  ->  **końcowe**.

    ![Punkty końcowe][api-management-endpoint]

2. Zastąp `{application id}` wartość wartością, przechodząc do **strony** Ustawienia.
3. Zastąp `{redirect uri}` wartość wartością z karty **Redirect URIs** (Interfejsy UR) Azure Active Directory aplikacji.

    Po podaniem wartości w przykładzie kodu powinien zostać zwrócony token podobny do poniższego przykładu:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Token może wygasnąć po upływie określonego czasu. Wykonaj ponownie przykładowy kod, aby wygenerować nowy token.

## <a name="calling-the-backup-and-restore-operations"></a>Wywoływanie operacji tworzenia kopii zapasowej i przywracania

Interfejsy API REST to [Api Management Service — Backup](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) i Api Management Service — [Przywracanie.](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

Przed wywołaniem operacji tworzenia kopii zapasowej i przywracania opisanych w poniższych sekcjach ustaw nagłówek żądania autoryzacji dla wywołania REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Back up an API Management service (API Management kopii zapasowej usługi)

Aby uzyskać kopię zapasową API Management usługi, należy zażądać następującego żądania HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

gdzie:

-   `subscriptionId` — identyfikator subskrypcji, która zawiera API Management usługi, dla których chcesz wrócić do kopii zapasowej
-   `resourceGroupName` — nazwa grupy zasobów usługi Azure API Management Service
-   `serviceName` — nazwa usługi API Management, dla których tworzyć kopię zapasową określona w czasie jej tworzenia
-   `api-version` — zastąp z `2019-12-01`

W treści żądania określ nazwę docelowego konta usługi Azure Storage, klucz dostępu, nazwę kontenera obiektów blob i nazwę kopii zapasowej:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ustaw wartość nagłówka `Content-Type` żądania na `application/json` .

Tworzenie kopii zapasowej to długotrwała operacja, która może potrwać ponad minutę. Jeśli żądanie zakończyło się pomyślnie i rozpoczęto proces tworzenia kopii zapasowej, otrzymasz `202 Accepted` kod stanu odpowiedzi z `Location` nagłówkiem. Aby sprawdzić stan operacji, należy wykonać żądania "GET" do adresu URL `Location` w nagłówku. Gdy kopia zapasowa jest w toku, nadal otrzymujesz kod stanu "202 Zaakceptowane". Kod odpowiedzi wskazuje `200 OK` pomyślne ukończenie operacji tworzenia kopii zapasowej.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Przywracanie usługi API Management danych

Aby przywrócić API Management z wcześniej utworzonej kopii zapasowej, należy wykonać następujące żądanie HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

gdzie:

-   `subscriptionId` — identyfikator subskrypcji, która zawiera API Management usługi, do których przywracasz kopię zapasową
-   `resourceGroupName` — nazwa grupy zasobów, która zawiera usługę Azure API Management, do których przywracasz kopię zapasową
-   `serviceName` — nazwa usługi API Management przywracana do określonej w czasie jej tworzenia
-   `api-version` — zastąp z `api-version=2019-12-01`

W treści żądania określ lokalizację pliku kopii zapasowej. Oznacza to, że dodaj nazwę konta usługi Azure Storage, klucz dostępu, nazwę kontenera obiektów blob i nazwę kopii zapasowej:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Ustaw wartość nagłówka `Content-Type` żądania na `application/json` .

Przywracanie to długotrwała operacja, która może potrwać do 30 minut lub dłużej. Jeśli żądanie zakończyło się pomyślnie i rozpoczęto proces przywracania, otrzymasz kod `202 Accepted` stanu odpowiedzi z `Location` nagłówkiem. Aby sprawdzić stan operacji, należy wykonać żądania "GET" do adresu URL `Location` w nagłówku. Gdy przywracanie jest w toku, nadal jest odbierany kod stanu "202 Zaakceptowane". Kod odpowiedzi wskazuje `200 OK` pomyślne ukończenie operacji przywracania.

> [!IMPORTANT]
> **SKU usługi** przywracanej do  musi odpowiadać sku przywracanej kopii zapasowej usługi.
>
> **Zmiany** wprowadzone w konfiguracji usługi (na przykład interfejsy API, zasady, wygląd portalu dla deweloperów) podczas operacji przywracania w toku mogą **zostać zastąpione**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Operacje tworzenia kopii zapasowej i przywracania można również wykonywać za pomocą poleceń [_PowerShell Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) i [_Restore-AzApiManagement._](/powershell/module/az.apimanagement/restore-azapimanagement)

## <a name="constraints-when-making-backup-or-restore-request"></a>Ograniczenia podczas tworzenia żądania tworzenia kopii zapasowej lub przywracania

-   **Kontener** określony w treści żądania **musi istnieć**.
-   Podczas tworzenia kopii  zapasowej należy unikać zmian zarządzania w usłudze, takich jak uaktualnienie lub obniżenie poziomu sku, zmiana nazwy domeny i inne.
-   Przywracanie kopii **zapasowej jest gwarantowane tylko przez 30 dni** od momentu jej utworzenia.
-   **Zmiany** wprowadzone w konfiguracji usługi (na przykład interfejsy API, zasady i wygląd portalu dla deweloperów) podczas wykonywania operacji tworzenia kopii zapasowej mogą zostać wykluczone z kopii zapasowej i zostaną **utracone.**
-   Jeśli konto usługi [][azure-storage-ip-firewall] Azure Storage ma włączoną zaporę, klient musi zezwolić na pracę zestawu adresów IP płaszczyzny kontroli usługi [Azure API Management][control-plane-ip-address] na koncie magazynu na potrzeby tworzenia kopii zapasowej lub przywracania z tego konta.  Konto usługi Azure Storage może być w dowolnym regionie świadczenia usługi Azure poza regionem, w API Management znajduje się usługa magazynu. Jeśli na przykład usługa API Management znajduje się w zachodnich usa, konto usługi Azure Storage może być w zachodnich stanyach USA 2, a klient musi otworzyć w zaporze adres IP płaszczyzny sterowania 13.64.39.16 (adres IP płaszczyzny sterowania platformy API Management zachodnie stany USA). Jest to spowodowane tym, że żądania do usługi Azure Storage nie są przysłane do publicznego adresu IP z zasobów obliczeniowych (płaszczyzna sterowania usługi Azure Api Management) w tym samym regionie świadczenia usługi Azure. Żądanie magazynu między regionami będzie przysłane do publicznego adresu IP.
-   [Współużytkowanie zasobów między źródłami (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) nie **powinno** być włączone w usłudze Blob Service na koncie usługi Azure Storage.
-   **SKU usługi** przywracanej do  musi odpowiadać sku przywracanej usługi kopii zapasowej.

## <a name="what-is-not-backed-up"></a>Co nie jest kopii zapasowej
-   **Dane użycia** używane do tworzenia raportów analitycznych **nie są uwzględniane w** kopii zapasowej. Użyj [interfejsu API REST usługi Azure API Management,][azure api management rest api] aby okresowo pobierać raporty analityczne w celu bezpiecznego bezpiecznego użytkowania.
-   [Certyfikaty protokołu TLS/SSL](configure-custom-domain.md) domeny niestandardowej.
-   [Niestandardowy certyfikat urzędu certyfikacji](api-management-howto-ca-certificates.md), który obejmuje certyfikaty pośrednie lub główne przekazane przez klienta.
-   [Ustawienia integracji sieci](api-management-using-with-vnet.md) wirtualnej.
-   [Konfiguracja tożsamości zarządzanej.](api-management-howto-use-managed-service-identity.md)
-   [Azure Monitor diagnostyki](api-management-howto-use-azure-monitor.md) Konfiguracji.
-   [Protokoły i ustawienia szyfrowania.](api-management-howto-manage-protocols-ciphers.md)
-   [Zawartość portalu dla](developer-portal-faq.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) deweloperów.

Częstotliwość wykonywania kopii zapasowych usługi wpływa na cel punktu odzyskiwania. Aby je zminimalizować, zalecamy implementowanie regularnych kopii zapasowych i wykonywanie kopii zapasowych na żądanie po w API Management usługi.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi zasobami, aby uzyskać różne wskazówki dotyczące procesu tworzenia/przywracania kopii zapasowych.

-   [Replikowanie kont usługi Azure API Management](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatyzowanie tworzenia kopii zapasowej i przywracania w usłudze API Management za pomocą usługi Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: Backing Up and Restoring Configuration (Usługa Azure API Management: kopii zapasowej i przywracania konfiguracji)](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration) 
     _Podejście szczegółowo opisane przez Stuarta nie jest zgodne z oficjalnymi wskazówkami, ale jest interesujące._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
