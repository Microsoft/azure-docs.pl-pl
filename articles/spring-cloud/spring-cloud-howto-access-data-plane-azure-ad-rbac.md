---
title: Dostęp do płaszczyzny danych za pomocą usługi Azure AD, RBAC
description: Jak uzyskać dostęp do płaszczyzny danych za pomocą kontroli dostępu opartej na rolach Azure Active Directory.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220136"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Dostęp do płaszczyzny danych przy użyciu Azure Active Directory i opartej na rolach Access Control

W tym artykule wyjaśniono, jak uzyskać dostęp do punktów Azure Active Directory końcowych rejestru i usługi Azure wiosny Cloud

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>Przypisywanie roli do użytkownika/grupy usługi AAD, MSI lub nazwy głównej usług

Aby korzystać z usługi AAD i RBAC, należy przypisać rolę *czytnika danych chmurowych w chmurze platformy Azure* do użytkownika, grupy lub jednostki usługi przez następującą procedurę:

1. Przejdź do strony Przegląd usługi w wystąpieniu usługi.

2. Kliknij pozycję **Access Control (IAM)** , aby otworzyć blok kontrola dostępu.

3. Kliknij przycisk **Dodaj** i **Dodaj przypisania ról** (autoryzacja może być wymagana do dodania).

4. Znajdź i wybierz *czytnika danych w chmurze Azure wiosną* w ramach **roli**.
5. Przypisz dostęp do `User, group, or service principal` lub `User assigned managed identity` zgodnie z typem użytkownika. Wyszukaj i wybierz użytkownika.  
6. Kliknij pozycję `Save`.

   ![Przypisz rolę](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>Dostęp do płaszczyzny danych

Po przypisaniu użytkownika usługi AAD do roli *czytnika danych w chmurze Azure wiosny* klienci mogą logować się do interfejsu wiersza polecenia platformy Azure przy użyciu użytkownika, nazwy głównej usługi lub tożsamości zarządzanej.  Zobacz [uwierzytelnianie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) , aby uzyskać token dostępu.  Następnie użyj następujących poleceń.

```azurecli
az login
az account get-access-token
```

Obecnie interfejs wiersza polecenia obsługuje domyślne punkty końcowe serwera konfiguracji i rejestru usługi. Aby uzyskać więcej informacji, zobacz [punkty końcowe gotowe do produkcji](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints). 

Klienci mogą również uzyskać pełną listę obsługiwanych punktów końcowych serwera konfiguracji i rejestru usług, uzyskując dostęp do punktów końcowych:
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

Token dostępu w nagłówku zawiera autoryzację. Obsługiwana jest tylko Metoda "GET".

Na przykład można uzyskać dostęp do punktu końcowego, takiego jak *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* Aby zobaczyć stan kondycji Eureka.

Poniżej przedstawiono różne główne punkty końcowe, zgodnie z różnymi typami chmur.

| Chmura          | Główny punkt końcowy              |
| -------------- | -------------------------- |
| Publiczne         | svc.azuremicroservices.io  |
| Mooncake/Chiny | svc.microservices.azure.cn |

Jeśli odpowiedź jest *401 nieautoryzowana*, sprawdź, czy rola została pomyślnie przypisana.  Zastosowanie roli może potrwać kilka minut lub sprawdzić, czy token dostępu nie wygasł.

## <a name="next-steps"></a>Następne kroki
* [Uwierzytelnianie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Punkty końcowe gotowe do produkcji](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Zobacz też
* [Tworzenie ról i uprawnień](spring-cloud-howto-permissions.md)