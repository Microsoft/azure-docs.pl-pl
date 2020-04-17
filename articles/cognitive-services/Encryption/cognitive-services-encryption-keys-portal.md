---
title: Konfigurowanie kluczy zarządzanych przez klienta za pomocą portalu Azure
titleSuffix: Cognitive Services
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta za pomocą usługi Azure Key Vault. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455261"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault przy użyciu witryny Azure portal

Do przechowywania kluczy zarządzanych przez klienta należy używać usługi Azure Key Vault. Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy. Zasób usług Cognitive Services i magazyn kluczy muszą znajdować się w tym samym regionie i w tej samej dzierżawie usługi Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

W tym artykule pokazano, jak skonfigurować usługę Azure Key Vault za pomocą kluczy zarządzanych przez klienta za pomocą [witryny Azure portal](https://portal.azure.com/). Aby dowiedzieć się, jak utworzyć magazyn kluczy za pomocą witryny Azure portal, zobacz [Szybki start: Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu witryny Azure portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Konfigurowanie usługi Azure Key Vault

Korzystanie z kluczy zarządzanych przez klienta wymaga, aby dwie właściwości były ustawiane w magazynie kluczy, **Usuwanie nietrwałe** i **Nie czyścić**. Te właściwości nie są domyślnie włączone, ale można włączyć za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure w nowym lub istniejącym magazynie kluczy.

> [!IMPORTANT]
> Jeśli nie masz włączone właściwości **Usuwanie nietrwale** i **Nie czyścić** i usuniesz klucz, nie będzie można odzyskać danych w zasobie usługi Cognitive Service.

Aby dowiedzieć się, jak włączyć te właściwości w istniejącym magazynie kluczy, zobacz sekcje **"Włączanie usuwania nietrwale** i **włączanie ochrony przed przeczyszczaniem"** w jednym z następujących artykułów:

- [Jak używać soft-delete z programem PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Jak używać soft-delete z CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Tylko klucze RSA o rozmiarze 2048 są obsługiwane za pomocą szyfrowania usługi Azure Storage. Aby uzyskać więcej informacji o kluczach, zobacz **Klucze magazynu kluczy,** wpisy tajne i certyfikaty usługi Azure Key Vault — informacje o [kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)usługi Azure Key Vault .

## <a name="enable-customer-managed-keys"></a>Włączanie kluczy zarządzanych przez klienta

Aby włączyć klucze zarządzane przez klienta w witrynie Azure portal, wykonaj następujące kroki:

1. Przejdź do zasobu usług Cognitive Services.
1. W bloku **Ustawienia** zasobu usług Cognitive Services kliknij pozycję **Szyfrowanie**. Wybierz opcję **Klucze zarządzane przez klienta,** jak pokazano na poniższym rysunku.

    ![Zrzut ekranu przedstawiający sposób wybierania kluczy zarządzanych przez klienta](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Określanie klucza

Po włączeniu kluczy zarządzanych przez klienta, będziesz mieć możliwość określenia klucza do skojarzenia z zasobem usług Cognitive Services.

### <a name="specify-a-key-as-a-uri"></a>Określanie klucza jako identyfikatora URI

Aby określić klucz jako identyfikator URI, wykonaj następujące kroki:

1. Aby zlokalizować identyfikator URI klucza w witrynie Azure portal, przejdź do magazynu kluczy i wybierz ustawienie **Klucze.** Wybierz żądany klawisz, a następnie kliknij klawisz, aby wyświetlić jego wersje. Wybierz wersję klucza, aby wyświetlić ustawienia dla tej wersji.
1. Skopiuj wartość pola **Identyfikator klucza,** które zapewnia identyfikator URI.

    ![Zrzut ekranu przedstawiający identyfikator URI klucza przechowalni kluczy](../media/cognitive-services-encryption/key-uri-portal.png)

1. W **ustawieniach szyfrowania** konta magazynu wybierz opcję **Wprowadź klucz URI.**
1. Wklej identyfikator URI skopiowany do pola **Identyfikator URI kluczy.**

   ![Zrzut ekranu przedstawiający sposób wprowadzania klucza URI](../media/cognitive-services-encryption/ssecmk2.png)

1. Określ subskrypcję zawierającą magazyn kluczy.
1. Zapisz zmiany.

### <a name="specify-a-key-from-a-key-vault"></a>Określanie klucza z magazynu kluczy

Aby określić klucz z magazynu kluczy, najpierw upewnij się, że masz magazyn kluczy zawierający klucz. Aby określić klucz z magazynu kluczy, wykonaj następujące czynności:

1. Wybierz opcję **Wybierz z przechowalni kluczy.**
1. Wybierz przechowalnię kluczy zawierającą klucz, którego chcesz użyć.
1. Wybierz klucz z magazynu kluczy.

   ![Zrzut ekranu przedstawiający opcję klucza zarządzanego przez klienta](../media/cognitive-services-encryption/ssecmk3.png)

1. Zapisz zmiany.

## <a name="update-the-key-version"></a>Aktualizowanie wersji klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować zasób usług Cognitive Services, aby użyć nowej wersji. Wykonaj następujące kroki:

1. Przejdź do zasobu usług Cognitive Services i wyświetl ustawienia **szyfrowania.**
1. Wprowadź identyfikator URI dla nowej wersji klucza. Alternatywnie można ponownie wybrać magazyn kluczy i klucz, aby zaktualizować wersję.
1. Zapisz zmiany.

## <a name="use-a-different-key"></a>Używanie innego klucza

Aby zmienić klucz używany do szyfrowania, wykonaj następujące czynności:

1. Przejdź do zasobu usług Cognitive Services i wyświetl ustawienia **szyfrowania.**
1. Wprowadź identyfikator URI dla nowego klucza. Alternatywnie można wybrać przechowalnię kluczy i wybrać nowy klucz.
1. Zapisz zmiany.

## <a name="disable-customer-managed-keys"></a>Wyłączanie kluczy zarządzanych przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta zasób usług Cognitive Services jest następnie szyfrowany za pomocą kluczy zarządzanych przez firmę Microsoft. Aby wyłączyć klucze zarządzane przez klienta, wykonaj następujące kroki:

1. Przejdź do zasobu usług Cognitive Services i wyświetl ustawienia **szyfrowania.**
1. Usuń zaznaczenie pola wyboru obok ustawienia **Użyj własnego klucza.**

## <a name="next-steps"></a>Następne kroki

* [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formularz żądania klucza zarządzanego przez klienta usług Cognitive Services](https://aka.ms/cogsvc-cmk)
* [Szyfrowanie danych w spoczynku usługi face services](../Face/face-encryption-of-data-at-rest.md)
* [Szyfrowanie danych QnA Maker w spoczynku](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Language Understanding service encryption of data at rest](../LUIS/luis-encryption-of-data-at-rest.md)
* [Szyfrowanie danych w trybie spoczynku moderatora zawartości](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Szyfrowanie danych w stanie spoczynku](../translator/translator-encryption-of-data-at-rest.md)
* [Personalizator szyfrowania danych w spoczynku](../personalizer/personalizer-encryption-of-data-at-rest.md)
