---
title: Serwer konfiguracji dostępu i rejestr usługi
titleSuffix: Azure Spring Cloud
description: Jak uzyskać dostęp do punktów końcowych rejestru serwera konfiguracji i usługi za pomocą kontroli dostępu opartej na rolach Azure Active Directory.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 90ee39039b0734bf434e7339a973358989c08175
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311366"
---
# <a name="access-config-server-and-service-registry"></a>Serwer konfiguracji dostępu i rejestr usługi

W tym artykule wyjaśniono, jak uzyskać dostęp do rejestru wiosennej konfiguracji chmury i sprężynowej usługi w chmurze zarządzanej przez chmurę z systemem Azure wiosną przy użyciu funkcji kontroli dostępu opartej na rolach (RBAC) w usłudze Azure Active Directory (AD).

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Przypisywanie roli do użytkownika/grupy usługi Azure AD, pliku MSI lub nazwy głównej usługi

Aby korzystać z usługi Azure AD i RBAC, należy przypisać rolę *czytnika danych chmurowych w chmurze platformy Azure* do użytkownika, grupy lub jednostki usługi za pomocą następującej procedury:

1. Przejdź do strony Przegląd usługi w wystąpieniu usługi.

2. Kliknij pozycję **Access Control (IAM)** , aby otworzyć blok kontrola dostępu.

3. Kliknij przycisk **Dodaj** i **Dodaj przypisania ról** (autoryzacja może być wymagana do dodania).

4. Znajdź i wybierz *czytnika danych w chmurze Azure wiosną* w ramach **roli**.
5. Przypisz dostęp do `User, group, or service principal` lub `User assigned managed identity` zgodnie z typem użytkownika. Wyszukaj i wybierz użytkownika.  
6. Kliknij pozycję `Save`.

   ![Przypisz rolę](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-config-server-and-service-registry-endpoints"></a>Punkty końcowe serwera konfiguracji dostępu i rejestru usługi

Po przypisaniu roli czytnika danych w chmurze Azure wiosny klienci mogą uzyskać dostęp do serwera konfiguracji chmury oraz punktów końcowych rejestru usługi w chmurze sprężyny. Wykonaj następujące procedury:

1. Uzyskaj token dostępu. Po przypisaniu użytkownika usługi Azure AD do roli czytnika danych w chmurze Azure wiosny klienci mogą używać następujących poleceń do logowania się do interfejsu wiersza polecenia platformy Azure przy użyciu użytkownika, nazwy głównej usługi lub tożsamości zarządzanej w celu uzyskania tokenu dostępu. Aby uzyskać szczegółowe informacje, zobacz [uwierzytelnianie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). 

    ```azurecli
    az login
    az account get-access-token
    ```
2. Utwórz punkt końcowy. Obsługiwane są domyślne punkty końcowe serwera konfiguracji chmury wiosennej i rejestru wiosennej usługi w chmurze zarządzanej przez chmurę Azure wiosną. Aby uzyskać więcej informacji, zobacz [punkty końcowe gotowe do produkcji](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints). Klienci mogą również uzyskać pełną listę obsługiwanych punktów końcowych serwera konfiguracji chmury sieci i sprężyny rejestru usługi w chmurze, zarządzane przez chmurę sieciową platformy Azure, uzyskując dostęp do punktów końcowych:

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/'* 

>[!NOTE]
> Jeśli używasz Chin platformy Azure, Zastąp ciąg `*.azuremicroservices.io` opcją `*.microservices.azure.cn` , [Dowiedz się więcej](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure).

3. Dostęp do składowego punktu końcowego z tokenem dostępu. Umieść token dostępu w nagłówku, aby zapewnić autoryzację.  Obsługiwana jest tylko Metoda "GET".

    Na przykład można uzyskać dostęp do punktu końcowego, takiego jak *" https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health "* , aby wyświetlić stan kondycji Eureka.

    Jeśli odpowiedź jest *401 nieautoryzowana*, sprawdź, czy rola została pomyślnie przypisana.  Zastosowanie roli może potrwać kilka minut lub sprawdzić, czy token dostępu nie wygasł.

## <a name="next-steps"></a>Następne kroki
* [Uwierzytelnianie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Punkty końcowe gotowe do produkcji](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Zobacz też
* [Tworzenie ról i uprawnień](how-to-permissions.md)
