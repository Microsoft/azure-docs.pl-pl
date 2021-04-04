---
title: Tworzenie wystąpienia zarządzanego Azure SQL przy użyciu Azure Data Studio
description: Tworzenie wystąpienia zarządzanego Azure SQL przy użyciu Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 651811384a8e831c41c48ca8c4849e5ee3852054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280481"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Tworzenie wystąpienia zarządzanego SQL — Azure ARC przy użyciu Azure Data Studio

Ten dokument zawiera szczegółowe instrukcje dotyczące instalowania wystąpienia zarządzanego Azure SQL — Azure ARC przy użyciu Azure Data Studio

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Logowanie się do kontrolera danych usługi Azure Arc

Aby można było utworzyć wystąpienie, zaloguj się do kontrolera danych usługi Azure ARC, jeśli nie jest jeszcze zalogowany.

```console
azdata login
```

Następnie zostanie wyświetlony monit o określenie przestrzeni nazw, w której utworzono kontroler danych, nazwy użytkownika i hasła w celu zalogowania się do kontrolera.  

> Jeśli musisz sprawdzić poprawność przestrzeni nazw, możesz uruchomić polecenie, ```kubectl get pods -A``` Aby uzyskać listę wszystkich przestrzeni nazw w klastrze.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Tworzenie wystąpienia zarządzanego Azure SQL w usłudze Azure Arc

- Azure Data Studio uruchamiania
- Na karcie połączenia kliknij trzy kropki w lewym górnym rogu i wybierz pozycję "nowe wdrożenie".
- Z opcji wdrożenia wybierz pozycję **wystąpienie zarządzane Azure SQL — Azure Arc** 
  > [!NOTE]
  > Może zostać wyświetlony monit o zainstalowanie tego programu, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Jeśli nie jest on aktualnie zainstalowany.
- Zaakceptuj postanowienia dotyczące prywatności i licencji, a następnie kliknij pozycję **Wybierz** u dołu



- W bloku Wdrażanie wystąpienia zarządzanego usługi Azure SQL — Azure Arc wprowadź następujące informacje:
  - Wprowadź nazwę dla wystąpienia SQL Server
  - Wprowadź i Potwierdź hasło dla wystąpienia SQL Server
  - Wybierz klasę magazynu odpowiednią dla danych
  - Wybierz klasę magazynu odpowiednią dla dzienników

- Kliknij przycisk **Wdróż**

- Powinno to inicjować Tworzenie wystąpienia zarządzanego usługi Azure SQL na kontrolerze danych.

- Po kilku minutach tworzenie powinno zakończyć się pomyślnie

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Nawiązywanie połączenia z wystąpieniem zarządzanym usługi Azure SQL — łuk platformy Azure z Azure Data Studio

- Zaloguj się do kontrolera danych usługi Azure ARC, podając przestrzeń nazw, nazwę użytkownika i hasło dla kontrolera danych: 
```console
azdata login
```

- Wyświetl wszystkie obsługiwane wystąpienia zarządzane przez usługę Azure SQL, korzystając z następujących poleceń:

```console
azdata arc sql mi list
```

Dane wyjściowe powinny wyglądać następująco, skopiuj ServerEndpoint (łącznie z numerem portu) w tym miejscu.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- W Azure Data Studio na karcie **połączenia** kliknij **nowe połączenie** w widoku **serwery**
- W bloku **połączenie** wklej ServerEndpoint do pola tekstowego serwera
- Wybierz **nazwę logowania SQL** jako typ uwierzytelniania
- Wprowadź *sa* jako nazwę użytkownika
- Wprowadź hasło dla `sa` konta
- Opcjonalnie wprowadź nazwę konkretnej bazy danych, z którą chcesz nawiązać połączenie
- Opcjonalnie wybierz/Dodaj nową grupę serwerów zgodnie z potrzebami
- Wybierz pozycję **Połącz** , aby nawiązać połączenie z wystąpieniem zarządzanym usługi Azure SQL — Azure Arc




## <a name="next-steps"></a>Następne kroki

Teraz spróbuj [monitorować wystąpienie bazy danych SQL](monitor-grafana-kibana.md)
