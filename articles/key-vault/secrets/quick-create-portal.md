---
title: 'Przewodnik Szybki start platformy Azure: konfigurowanie i pobieranie wpisów tajnych z usługi Key Vault przy użyciu witryny Azure Portal | Microsoft Docs'
description: W tym przewodniku Szybki start pokazano, w jaki sposób skonfigurować i pobrać wpis tajny z usługi Azure Key Vault przy użyciu witryny Azure Portal.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 0c1a3362c4f1dfd3396ccd4704933d06d2611414
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814122"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Szybki start: konfigurowanie i pobieranie wpisów tajnych z usługi Key Vault przy użyciu witryny Azure Portal

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. W tym przewodniku Szybki start utworzysz magazyn kluczy, a następnie użyjesz go do przechowywania wpisu tajnego. 

Aby uzyskać więcej informacji na temat, zobacz 
- [Key Vault omówienie](../general/overview.md)
- [Omówienie wpisów tajnych.](about-secrets.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać Azure Key Vault, musisz mieć subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) rozpoczęciem utwórz bezpłatne konto.

Cały dostęp do wpisów tajnych odbywa się za pośrednictwem Azure Key Vault. W tym przewodniku Szybki start utworzysz magazyn kluczy przy [użyciu](../general/quick-create-portal.md)Azure Portal , [interfejsu wiersza polecenia platformy Azure](../general/quick-create-cli.md)lub [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="add-a-secret-to-key-vault"></a>Dodawanie wpisu tajnego do usługi Key Vault

Aby dodać klucz tajny do magazynu, wykonaj następujące kroki:

1. Przejdź do nowego magazynu kluczy w Azure Portal
1. Na stronie Key Vault wybierz pozycję Wpisy **tajne.**
1. Kliknij pozycję **Wygeneruj/zaimportuj**.
1. Na ekranie **Tworzenie wpisu tajnego** wybierz następujące wartości:
    - **Opcje przekazywania**: Ręcznie.
    - **Nazwa:** wpisz nazwę wpisów tajnych. Nazwa tajnego musi być unikatowa w obrębie Key Vault. Nazwa musi być ciągiem znaków od 1 do 127, rozpoczynającym się od litery i zawierającym tylko wartości od 0 do 9, a–z, A–Z i -. Aby uzyskać więcej informacji na temat nazewnictwa, [zobacz Key Vault obiektów, identyfikatorów i wersji](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)
    - **Wartość:** wpisz wartość dla tajnego. Key Vault API akceptują i zwracają wartości tajnych jako ciągi. 
    - Dla pozostałych opcji zostaw wartości domyślne. Kliknij pozycję **Utwórz**.

Gdy otrzymasz komunikat o pomyślnym utworzeniu wpisu tajnego, możesz kliknąć go na liście. 

Aby uzyskać więcej informacji na temat atrybutów wpisów tajnych, zobacz [About Azure Key Vault secrets (Informacje o wpisach tajnych)](./about-secrets.md)

## <a name="retrieve-a-secret-from-key-vault"></a>Pobieranie tajnego Key Vault

Jeśli klikniesz bieżącą wersję, zostanie wyświetlona wartość określona w poprzednim kroku.

![Właściwości wpisu tajnego](../media/quick-create-portal/current-version-hidden.png)

Klikając przycisk "Pokaż wartość tajne" w okienku po prawej stronie, możesz zobaczyć ukrytą wartość. 

![Pojawiła się wartość tajnego](../media/quick-create-portal/current-version-shown.png)

Możesz również użyć interfejsu [wiersza polecenia]()platformy Azure [lub Azure PowerShell,]() aby pobrać wcześniej utworzony klucz tajny.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

> [!NOTE]
> Należy zauważyć, że usunięcie klucza tajnego, klucza, certyfikatu lub magazynu kluczy będzie możliwe do odzyskania przez konfigurowalny okres od 7 do 90 dni kalendarzowych. Jeśli konfiguracja nie zostanie określona, domyślny okres odzyskiwania zostanie ustawiony na 90 dni. Dzięki temu użytkownicy mają wystarczająco dużo czasu, aby zauważyć przypadkowe usunięcie tajnego i zareagować. Aby uzyskać więcej informacji na temat usuwania i odzyskiwania magazynów kluczy i obiektów magazynu kluczy, zobacz Azure Key Vault omówienie usuwania [nie soft-delete](../general/soft-delete-overview.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Key Vault przechowywany w nim klucz tajny. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Odczyt [Bezpieczny dostęp do Key Vault](../general/security-features.md)
- Zobacz Use Key Vault with App Service Web App (Używanie Key Vault [z App Service web app)](../general/tutorial-net-create-vault-azure-web-app.md)
- Zobacz [Używanie Key Vault z aplikacją wdrożoną na maszynie wirtualnej](../general/tutorial-net-virtual-machine.md)
- Zobacz Azure Key Vault [dewelopera](../general/developers-guide.md)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)