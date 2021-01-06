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
ms.openlocfilehash: 904c00c26171854a32234d7b6ba5cac81ac43e5c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936672"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Key Vault przy użyciu witryny Azure Portal

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. W tym przewodniku Szybki start utworzysz magazyn kluczy, a następnie użyjesz go do przechowywania wpisu tajnego. 

Aby uzyskać więcej informacji na temat, zobacz 
- [Przegląd Key Vault](../general/overview.md)
- Wpisy [tajne](about-secrets.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do Azure Key Vault, musisz mieć subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Cały dostęp do wpisów tajnych odbywa się za Azure Key Vault. Aby skorzystać z tego przewodnika Szybki Start, Utwórz magazyn kluczy przy użyciu [Azure Portal](../general/quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](../general/quick-create-cli.md)lub [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać wpis tajny do magazynu, wykonaj następujące czynności:

1. Przejdź do nowego magazynu kluczy w Azure Portal
1. Na stronie Ustawienia Key Vault wybierz pozycję wpisy **tajne**.
1. Kliknij pozycję **Wygeneruj/zaimportuj**.
1. Na ekranie **Tworzenie wpisu tajnego** wybierz następujące wartości:
    - **Opcje przekazywania**: Ręcznie.
    - **Nazwa**: wpisz nazwę wpisu tajnego. Nazwa wpisu tajnego musi być unikatowa w obrębie Key Vault. Nazwa musi być ciągiem znaków 1-127, rozpoczynając od litery i zawierającą tylko 0-9, a-z, A-Z, i-. Aby uzyskać więcej informacji na temat nazewnictwa, zobacz [Key Vault obiektów, identyfikatorów i przechowywania wersji](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) .
    - **Wartość**: wpisz wartość wpisu tajnego. Key Vault interfejsy API akceptują i zwracają wartości tajne jako ciągi. 
    - Dla pozostałych opcji zostaw wartości domyślne. Kliknij przycisk **Utwórz**.

Gdy otrzymasz komunikat o pomyślnym utworzeniu wpisu tajnego, możesz kliknąć go na liście. 

Aby uzyskać więcej informacji o atrybutach tajnych, zobacz [Informacje o Azure Key Vault](./about-secrets.md) Secret

## <a name="retrieve-a-secret-from-key-vault"></a>Pobierz klucz tajny z Key Vault

Jeśli klikniesz bieżącą wersję, zostanie wyświetlona wartość określona w poprzednim kroku.

![Właściwości wpisu tajnego](../media/quick-create-portal/current-version-hidden.png)

Klikając przycisk "Pokaż wartość wpisu tajnego" w prawym okienku, zobaczysz wartość Hidden. 

![Pojawiła się wartość wpisu tajnego](../media/quick-create-portal/current-version-shown.png)

Możesz również użyć [interfejsu wiersza polecenia platformy Azure]()lub [Azure PowerShell]() do pobrania wcześniej utworzonego klucza tajnego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

> [!NOTE]
> Należy pamiętać, że po usunięciu wpisu tajnego, klucza, certyfikatu lub magazynu kluczy pozostanie on odzyskany przez konfigurowalny okres od 7 do 90 dni kalendarzowych. Jeśli konfiguracja nie zostanie określona, domyślny okres odzyskiwania zostanie ustawiony na 90 dni. Zapewnia to użytkownikom wystarczającą ilość czasu na powiadomienie o przypadkowym usunięciu tajnych wpisów i udzieleniu odpowiedzi. Aby uzyskać więcej informacji na temat usuwania i odzyskiwania magazynów kluczy oraz obiektów magazynu kluczy, zobacz [Azure Key Vault Omówienie usuwania nietrwałego](../general/soft-delete-overview.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault i Zapisano w nim wpis tajny. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Odczytaj [bezpieczny dostęp do Key Vault](../general/secure-your-key-vault.md)
- Zobacz [używanie Key Vault z aplikacją App Service Web App](../general/tutorial-net-create-vault-azure-web-app.md)
- Zobacz [używanie Key Vault z aplikacją wdrożoną na maszynie wirtualnej](../general/tutorial-net-virtual-machine.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Zapoznaj się z [omówieniem zabezpieczeń Key Vault](../general/security-overview.md)