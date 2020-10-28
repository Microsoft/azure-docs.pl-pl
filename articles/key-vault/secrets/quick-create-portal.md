---
title: 'Przewodnik Szybki start platformy Azure: konfigurowanie i pobieranie wpisów tajnych z usługi Key Vault przy użyciu witryny Azure Portal | Microsoft Docs'
description: W tym przewodniku Szybki start pokazano, w jaki sposób skonfigurować i pobrać wpis tajny z usługi Azure Key Vault przy użyciu witryny Azure Portal.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 080e2daf5065c0762fb039a84e62580e5c915ddb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735165"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Key Vault przy użyciu witryny Azure Portal

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. W tym przewodniku Szybki start utworzysz magazyn kluczy, a następnie użyjesz go do przechowywania wpisu tajnego. Aby uzyskać więcej informacji na temat usługi Key Vault, możesz zapoznać się z [omówieniem](../general/overview.md).

Aby uzyskać więcej informacji na temat wpisów tajnych, zobacz [Informacje o wpisach tajnych](about-secrets.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vault"></a>Tworzenie magazynu

1. W menu Azure Portal lub na stronie **głównej** wybierz pozycję **Utwórz zasób** .
2. W polu wyszukiwania wpisz **Key Vault** .
3. Na liście wyników wybierz pozycję **Key Vault** .
4. W sekcji Key Vault, wybierz przycisk **Utwórz** .
5. W sekcji **Tworzenie magazynu kluczy** podaj następujące informacje:
    - **Nazwa** : wymagana jest unikatowa nazwa. W tym przewodniku szybki start użyjemy **contoso-vault2** . 
    - **Subskrypcja** : wybierz subskrypcję.
    - W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę grupy zasobów.
    - W menu rozwijanym **Lokalizacja** wybierz lokalizację.
    - Dla pozostałych opcji zostaw wartości domyślne.
6. Po podaniu powyższych informacje wybierz przycisk **Utwórz** .

Zanotuj dwie poniższe właściwości:

* **Nazwa magazynu** : w tym przykładzie jest to **Contoso-Vault2** . Użyjesz tej nazwy w innych krokach.
* **Identyfikator URI magazynu** : w tym przykładzie jest to https://contoso-vault2.vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Możesz również utworzyć Key Vault za pomocą interfejsu wiersza polecenia platformy Azure i programu PowerShell:
- [Tworzenie Key Vault przy użyciu programu PowerShell](../general/quick-create-powershell.md)
- [Tworzenie Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](../general/quick-create-cli.md)

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania operacji na tym nowym magazynie.

![Dane wyjściowe po ukończeniu tworzenia usługi Key Vault](../media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać wpis tajny do magazynu, wystarczy tylko wykonać kilka dodatkowych czynności. W tym przypadku dodamy hasło, którego będzie mogła używać aplikacja. Hasło ma nazwę **ExamplePassword** i przechowujemy w nim wartość **hVFkk965BuUv** .

1. Na stronie właściwości Key Vault wybierz pozycję wpisy **tajne** .
2. Kliknij pozycję **Wygeneruj/zaimportuj** .
3. Na ekranie **Tworzenie wpisu tajnego** wybierz następujące wartości:
    - **Opcje przekazywania** : Ręcznie.
    - **Nazwa** : ExamplePassword.
    - **Wartość** : hVFkk965BuUv
    - Dla pozostałych opcji zostaw wartości domyślne. Kliknij pozycję **Utwórz** .

Gdy otrzymasz komunikat o pomyślnym utworzeniu wpisu tajnego, możesz kliknąć go na liście. 

## <a name="retrieve-a-secret-from-key-vault"></a>Pobierz klucz tajny z Key Vault

Jeśli klikniesz bieżącą wersję, zostanie wyświetlona wartość określona w poprzednim kroku.

![Właściwości wpisu tajnego](../media/quick-create-portal/current-version-hidden.png)

Klikając przycisk "Pokaż wartość wpisu tajnego" w prawym okienku, zobaczysz wartość Hidden. 

![Pojawiła się wartość wpisu tajnego](../media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów** .
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń** .


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault i Zapisano w nim wpis tajny. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Odczytaj [bezpieczny dostęp do Key Vault](../general/secure-your-key-vault.md)
- Zobacz [używanie Key Vault z aplikacją App Service Web App](../general/tutorial-net-create-vault-azure-web-app.md)
- Zobacz [używanie Key Vault z aplikacją wdrożoną na maszynie wirtualnej](../general/tutorial-net-virtual-machine.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Przegląd [Azure Key Vault najlepszych](../general/best-practices.md) rozwiązań
