---
title: Przekazywanie danych użycia do Azure Monitor
description: Przekazywanie danych użycia usługi Azure ARC z obsługą danych do Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 0c72eda59f375c70274b17796ca53614ef95505b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669512"
---
# <a name="upload-usage-data-to-azure-monitor"></a>Przekazywanie danych użycia do Azure Monitor

Okresowo można eksportować informacje o użyciu. Eksport i przekazanie tych informacji polega na tworzeniu i aktualizowaniu zasobów serwera SQL, wystąpienia zarządzanego i PostgreSQL na platformie Azure.

> [!NOTE] 
> W okresie zapoznawczym nie ma kosztu korzystania z usług danych z obsługą usługi Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> Przed przekazaniem danych użycia odczekaj co najmniej 24 godziny od utworzenia kontrolera danych usługi Azure Arc.

## <a name="create-service-principal-and-assign-roles"></a>Tworzenie nazwy głównej usługi i przypisywanie ról

Przed kontynuowaniem upewnij się, że została utworzona wymagana jednostka usługi i przypisano ją do odpowiedniej roli. Aby uzyskać szczegółowe informacje, zobacz:
* [Utwórz nazwę główną usługi](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Przypisywanie ról do jednostki usługi](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Przekazywanie danych dotyczących użycia

Informacje dotyczące użycia, takie jak spis i użycie zasobów, można przekazać do platformy Azure w następujący dwuetapowy sposób:

1. Zaloguj się do kontrolera danych. Wprowadź wartości w wierszu polecenia. 

   ```console
   azdata login
   ```

1. Wyeksportuj dane użycia za pomocą `azdata arc dc export` polecenia w następujący sposób:

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   To polecenie tworzy `usage.json` plik ze wszystkimi zasobami danych z obsługą usługi Azure ARC, takimi jak wystąpienia zarządzane SQL i wystąpieniami PostgreSQL, które są tworzone na kontrolerze danych.

2. Przekazywanie danych użycia za pomocą ```azdata upload``` polecenia

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>Automatyzacja przekazywania (opcjonalnie)

Jeśli chcesz przekazać metryki i dzienniki zgodnie z harmonogramem, możesz utworzyć skrypt i uruchomić go w czasomierzu co kilka minut. Poniżej znajduje się przykład automatyzacji przekazywania przy użyciu skryptu powłoki systemu Linux.

W ulubionym edytorze tekstu/kodu Dodaj następujący skrypt do pliku i Zapisz jako plik wykonywalny skryptu, taki jak `.sh` (Linux/Mac) lub `.cmd` , `.bat` lub `.ps1` .

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Utwórz plik skryptu wykonywalnego

```console
chmod +x myuploadscript.sh
```

Uruchom skrypt co 20 minut:

```console
watch -n 1200 ./myuploadscript.sh
```

Można również użyć harmonogramu zadań, takiego jak CRONUS lub Windows Harmonogram zadań lub do programu Orchestrator, takiego jak rozwiązania ansible, Puppet lub Chef.

## <a name="next-steps"></a>Następne kroki

[Przekazywanie metryk i dzienników do Azure Monitor](upload-metrics.md)

[Przekaż dzienniki do Azure Monitor](upload-logs.md)

[Przekaż dane dotyczące rozliczeń na platformę Azure i Wyświetl je w Azure Portal](view-billing-data-in-azure.md)

[Wyświetlanie zasobu usługi Azure Arc Data Controller w Azure Portal](view-data-controller-in-azure-portal.md)
