---
title: Wymagania wstępne interfejsu wiersza polecenia platformy Azure dla usługi Azure HPC cache
description: Kroki konfiguracji przed rozpoczęciem korzystania z interfejsu wiersza polecenia platformy Azure w celu utworzenia lub zmodyfikowania pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87099249"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure dla usługi Azure HPC Cache

Wykonaj następujące kroki, aby przygotować środowisko przed użyciem interfejsu wiersza polecenia platformy Azure w celu utworzenia pamięci podręcznej platformy Azure HPC lub zarządzania nią.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Pamięć podręczna platformy Azure HPC wymaga wersji 2,7 lub nowszej interfejsu wiersza polecenia platformy Azure. Uruchom polecenie `az --version`, aby znaleźć zainstalowaną wersję i biblioteki zależne. Aby uzyskać informacje o instalowaniu lub uaktualnianiu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Zainstaluj rozszerzenie pamięci podręcznej platformy Azure HPC

Funkcje pamięci podręcznej platformy Azure HPC nie są częścią głównego kodu bazowego, dlatego należy również zainstalować odwołanie do rozszerzenia. Postępuj zgodnie z poniższymi instrukcjami.

1. Zaloguj

   Zaloguj się przy użyciu polecenia [AZ login](/cli/azure/reference-index#az-login) , jeśli korzystasz z zainstalowanej lokalnie interfejsu CLI.

    ```azurecli
    az login
    ```

    Wykonaj kroki wyświetlane w terminalu, aby ukończyć proces uwierzytelniania.

   > [!TIP]
   > Jeśli masz wiele subskrypcji, musisz ją wybrać. Wybierz ją po rozpoczęciu sesji Cloud Shell w witrynie Azure Portal lub postępuj zgodnie z instrukcjami w temacie [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli#sign-in) , aby ustawić subskrypcję z wiersza poleceń.

2. Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

   Funkcje pamięci podręcznej platformy Azure HPC są dostępne jako rozszerzenie interfejsu wiersza polecenia platformy Azure — nie jest jeszcze częścią podstawowego pakietu interfejsu wiersza polecenia. Musisz zainstalować odwołanie do rozszerzenia, aby można było z niego korzystać.

   Rozszerzenia interfejsu wiersza polecenia platformy Azure umożliwiają dostęp do poleceń eksperymentalnych i w wersji wstępnej. Aby dowiedzieć się więcej na temat rozszerzeń, w tym aktualizacji i odinstalowywania, zobacz [Korzystanie z rozszerzeń przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).

   Zainstaluj rozszerzenie dla pamięci podręcznej platformy Azure HPC, uruchamiając następujące polecenie:

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>Ustaw domyślną grupę zasobów (opcjonalnie)

Większość poleceń pamięci podręcznej HPC wymaga przekazania grupy zasobów pamięci podręcznej. Domyślną grupę zasobów można ustawić za pomocą polecenia [AZ Configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu rozszerzenia interfejsu wiersza polecenia platformy Azure i zalogowaniu się za pomocą interfejsu wiersza polecenia platformy Azure można tworzyć systemy pamięci podręcznej platformy Azure HPC i zarządzać nimi.

* [Tworzenie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md)
* [Interfejs wiersza polecenia platformy Azure HPC — dokumentacja pamięci podręcznej](/cli/azure/ext/hpc-cache/hpc-cache)
