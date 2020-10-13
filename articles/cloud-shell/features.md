---
title: Funkcje Azure Cloud Shell | Microsoft Docs
description: Omówienie funkcji w Azure Cloud Shell
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: a052364b06ac1b9b30cef76db10a79e8ed85b9a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89470157"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funkcje & narzędzia dla Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell uruchomiony `Ubuntu 16.04 LTS` .

## <a name="features"></a>Funkcje

### <a name="secure-automatic-authentication"></a>Bezpieczne uwierzytelnianie automatyczne

Cloud Shell bezpiecznie i automatycznie uwierzytelnia dostęp do konta dla interfejsu wiersza polecenia platformy Azure i Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME trwałość między sesjami

Aby utrwalać pliki między sesjami, Cloud Shell przeprowadzi Cię przez dołączenie udziału plików platformy Azure przy pierwszym uruchomieniu.
Po zakończeniu Cloud Shell automatycznie dołączy magazyn (zainstalowany jako `$HOME\clouddrive` ) dla wszystkich przyszłych sesji.
Ponadto `$HOME` katalog jest utrwalony jako. img w udziale plików platformy Azure.
Pliki poza programem `$HOME` i stan komputera nie są utrwalane między sesjami. W przypadku przechowywania wpisów tajnych, takich jak klucze SSH, należy stosować najlepsze rozwiązania. Usługi, takie jak [Azure Key Vault, mają samouczki dla Instalatora](../key-vault/general/manage-with-cli2.md#prerequisites).

[Dowiedz się więcej o utrwalaniu plików w Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Dysk platformy Azure (Azure:)

Program PowerShell w Cloud Shell udostępnia dysk platformy Azure ( `Azure:` ). Możesz przełączyć się na dysk platformy Azure w usłudze `cd Azure:` i z powrotem do katalogu macierzystego przy użyciu programu `cd  ~` .
Dysk platformy Azure umożliwia łatwe odnajdowanie zasobów platformy Azure, takich jak obliczenia, Sieć, magazyn itp., podobnie jak nawigowanie po systemie plików.
Aby zarządzać tymi zasobami niezależnie od używanego dysku, można nadal używać znanych [poleceń cmdlet Azure PowerShell](/powershell/azure) .
Wszystkie zmiany wprowadzone w zasobach platformy Azure, które zostały wprowadzone bezpośrednio w Azure Portal lub za pomocą poleceń cmdlet Azure PowerShell, są odzwierciedlone na dysku platformy Azure.  `dir -Force`Aby odświeżyć zasoby, można uruchomić.

![Zrzut ekranu przedstawiający Azure Cloud Shell zainicjowany i listę zasobów katalogu.](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Zarządzanie usługą Exchange Online

Program PowerShell w Cloud Shell zawiera prywatną kompilację modułu usługi Exchange Online.  Uruchom `Connect-EXOPSSession` polecenie, aby uzyskać polecenia cmdlet programu Exchange.

![Zrzut ekranu przedstawiający Azure Cloud Shell uruchamiania poleceń Connect-EXOPSSession i Get-User.](media/features-powershell/exchangeonline.png)

 Uruchom polecenie `Get-Command -Module tmp_*`
> [!NOTE]
> Nazwa modułu powinna rozpoczynać się od `tmp_` , jeśli zainstalowano moduły z tym samym prefiksem, zostaną również wyświetlone te polecenia cmdlet. 

![Zrzut ekranu przedstawiający Azure Cloud Shell, na którym działa polecenie Get-Command-module tmp_ *.](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Ścisła integracja z narzędziami Open Source

Cloud Shell obejmuje wstępnie skonfigurowane uwierzytelnianie dla narzędzi typu "open source", takich jak Terraform, rozwiązania ansible i Chef. Wypróbuj ją z przykładowych przewodników.

## <a name="tools"></a>Narzędzia

|Kategoria   |Nazwa   |
|---|---|
|Narzędzia systemu Linux            |bash<br> zsh<br> pok<br> tmux<br> Dig<br>               |
|Narzędzia platformy Azure            |[Interfejs wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli) i [klasyczny interfejs wiersza polecenia platformy Azure](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](../storage/common/storage-use-azcopy-v10.md)<br> [Interfejs wiersza polecenia usługi Azure Functions](https://github.com/Azure/azure-functions-core-tools)<br> [Interfejs wiersza polecenia usługi Service Fabric](../service-fabric/service-fabric-cli.md)<br> [Usługa Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Edytory tekstu           |kod (Edytor Cloud Shell)<br> vim<br> nano<br> emacs    |
|Kontrola źródła         |git                    |
|Narzędzia kompilacji            |make<br> maven<br> npm<br> graczy         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [INTERFEJS WIERSZA POLECENIA DC/OS](https://github.com/dcos/dcos-cli)         |
|Bazy danych              |Klient MySQL<br> Klient PostgreSql<br> [Narzędzie sqlcmd](/sql/tools/sqlcmd-utility)<br> [MSSQL-Scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Inne                  |Klient iPython<br> [Interfejs wiersza polecenia Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef — Specyfikacja](https://www.chef.io/inspec/)<br> [Puppet](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)<br> [Interfejs wiersza polecenia pakietu Office 365](https://pnp.github.io/office365-cli/)|

## <a name="language-support"></a>Obsługa języków

|Język   |Wersja   |
|---|---|
|.NET Core  |[3.1.302](https://github.com/dotnet/core/blob/master/release-notes/3.1/3.1.6/3.1.302-download.md)       |
|Przejdź         |1,9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|Program PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 i 3,5 (wartość domyślna)|

## <a name="next-steps"></a>Następne kroki
[Bash w Cloud Shell Szybki Start](quickstart.md) <br>
[Program PowerShell w Cloud Shell Szybki Start](quickstart-powershell.md) <br>
[Informacje o interfejsie wiersza polecenia platformy Azure](/cli/azure/) <br>
[Dowiedz się więcej o Azure PowerShell](/powershell/azure/) <br>