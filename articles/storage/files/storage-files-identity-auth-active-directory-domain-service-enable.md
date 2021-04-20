---
title: Używanie Azure AD Domain Services do autoryzowania dostępu do danych plików za pośrednictwem SMB
description: Dowiedz się, jak włączyć uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) na Azure Files pośrednictwem Azure Active Directory Domain Services. Maszyny wirtualne z systemem Windows przyłączone do domeny mogą następnie uzyskać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: df2bd1c12c86de43e2a5057813a743d822dbda33
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718355"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Włączanie Azure Active Directory Domain Services uwierzytelniania na Azure Files

[Azure Files](storage-files-introduction.md)   obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem dwóch typów usług domenowych: lokalna usługa Active Directory Domain Services (AD DS) i Azure Active Directory Domain Services (Azure AD DS). Zdecydowanie zalecamy zapoznanie się z sekcją [Jak to działa,](./storage-files-active-directory-overview.md#how-it-works) aby wybrać odpowiednią usługę domeny do uwierzytelniania. Konfiguracja różni się w zależności od wybranej usługi domeny. Ten artykuł koncentruje się na włączaniu i konfigurowaniu Azure AD DS uwierzytelniania za pomocą udziałów plików platformy Azure.

Jeśli jesteś nowym użytkownikiem udziałów plików platformy Azure, zalecamy przeczytanie naszego przewodnika planowania [przed](storage-files-planning.md) przeczytaniem poniższej serii artykułów.

> [!NOTE]
> Azure Files obsługuje uwierzytelnianie Kerberos przy Azure AD DS tylko rc4-HMAC. Szyfrowanie AES Kerberos nie jest jeszcze obsługiwane.
> Azure Files obsługuje uwierzytelnianie na Azure AD DS z pełną synchronizacją z usługą Azure AD. Jeśli w programie włączono synchronizację w zakresie Azure AD DS która synchronizuje tylko ograniczony zestaw tożsamości z usługi Azure AD, uwierzytelnianie i autoryzacja nie są obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed włączeniem usługi Azure AD za pośrednictwem SMB dla udziałów plików platformy Azure upewnij się, że zostały spełnione następujące wymagania wstępne:

1.  **Wybierz lub utwórz dzierżawę usługi Azure AD.**

    Do uwierzytelniania usługi Azure AD za pośrednictwem protokołu SMB możesz użyć nowej lub istniejącej dzierżawy. Dzierżawa i udział plików, do którego chcesz uzyskać dostęp, muszą być skojarzone z tą samą subskrypcją.

    Aby utworzyć nową dzierżawę usługi Azure AD, możesz dodać dzierżawę usługi [Azure AD i subskrypcję usługi Azure AD.](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription) Jeśli masz istniejącą dzierżawę usługi Azure AD, ale chcesz utworzyć nową dzierżawę do użycia z udziałami plików platformy Azure, zobacz Tworzenie dzierżawy [Azure Active Directory dzierżawy.](/rest/api/datacatalog/create-an-azure-active-directory-tenant)

1.  **Włącz Azure AD Domain Services dzierżawie usługi Azure AD.**

    Aby obsługiwać uwierzytelnianie przy użyciu poświadczeń usługi Azure AD, należy włączyć Azure AD Domain Services dzierżawie usługi Azure AD. Jeśli nie jesteś administratorem dzierżawy usługi Azure AD, skontaktuj się z administratorem i postępuj zgodnie ze wskazówkami krok po kroku, aby włączyć usługę [Azure Active Directory Domain Services przy](../../active-directory-domain-services/tutorial-create-instance.md)użyciu Azure Portal .

    Zazwyczaj ukończenie wdrożenia Azure AD DS trwa około 15 minut. Przed przystąpieniem do następnego kroku sprawdź, czy stan kondycji aplikacji jest Azure AD DS uruchomiony, z włączoną synchronizacją skrótów haseł.

1.  **Przyłącz maszynę wirtualną platformy Azure do domeny za pomocą Azure AD DS.**

    Aby uzyskać dostęp do udziału plików przy użyciu poświadczeń usługi Azure AD z maszyny wirtualnej, maszyna wirtualna musi być przysłana do Azure AD DS. Aby uzyskać więcej informacji na temat dołączania maszyny wirtualnej do domeny, zobacz Dołączanie maszyny wirtualnej z systemem [Windows Server do domeny zarządzanej.](../../active-directory-domain-services/join-windows-vm.md)

    > [!NOTE]
    > Azure AD DS uwierzytelnianie za pośrednictwem protokołu SMB z udziałami plików platformy Azure jest obsługiwane tylko na maszynach wirtualnych platformy Azure działających w wersjach systemu operacyjnego starszych niż Windows 7 lub Windows Server 2008 R2.

1.  **Wybierz lub utwórz udział plików platformy Azure.**

    Wybierz nowy lub istniejący udział plików skojarzony z tą samą subskrypcją co dzierżawa usługi Azure AD. Aby uzyskać informacje na temat tworzenia nowego udziału plików, zobacz [Tworzenie udziału plików w](storage-how-to-create-file-share.md)Azure Files .
    Aby uzyskać optymalną wydajność, zalecamy, aby udział plików był w tym samym regionie co maszyna wirtualna, z której planujesz uzyskać dostęp do udziału.

1.  **Zweryfikuj Azure Files przez instalowanie udziałów plików platformy Azure przy użyciu klucza konta magazynu.**

    Aby sprawdzić, czy maszyna wirtualna i udział plików są prawidłowo skonfigurowane, spróbuj konfigurować udział plików przy użyciu klucza konta magazynu. Aby uzyskać więcej informacji, zobacz Temat Mount an Azure file share and access the share in Windows (Zainstaluj udział [plików platformy Azure i uzyskaj dostęp do udziału w systemie Windows).](storage-how-to-use-files-windows.md)

## <a name="regional-availability"></a>Dostępność w regionach

Azure Files uwierzytelnianie za pomocą Azure AD DS jest dostępne we wszystkich regionach [świadczenia usługi Azure Public, Gov i Chiny.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="overview-of-the-workflow"></a>Omówienie przepływu pracy

Przed włączeniem uwierzytelniania Azure AD DS SMB dla udziałów plików platformy Azure sprawdź, czy środowiska usług Azure AD i Azure Storage są prawidłowo skonfigurowane. Zalecamy zapoznanie się z wymaganiami [wstępnymi,](#prerequisites) aby upewnić się, że zostały wykonane wszystkie wymagane kroki.

Następnie wykonaj następujące czynności, aby udzielić dostępu do Azure Files zasobów przy użyciu poświadczeń usługi Azure AD:

1. Włącz Azure AD DS za pośrednictwem protokołu SMB dla konta magazynu, aby zarejestrować konto magazynu ze skojarzonym Azure AD DS wdrożenia.
2. Przypisywanie uprawnień dostępu do udziału do tożsamości usługi Azure AD (użytkownika, grupy lub jednostki usługi).
3. Skonfiguruj uprawnienia systemu plików NTFS za pośrednictwem SMB dla katalogów i plików.
4. Zainstaluj udział plików platformy Azure z maszyny wirtualnej przyłączone do domeny.

Na poniższym diagramie przedstawiono przepływ pracy end-to-end umożliwiający uwierzytelnianie Azure AD DS za pośrednictwem protokołu SMB dla Azure Files.

![Diagram przedstawiający usługę Azure AD za pośrednictwem SMB na Azure Files przepływu pracy](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Włączanie Azure AD DS uwierzytelniania dla konta

Aby włączyć Azure AD DS za pośrednictwem protokołu SMB dla usługi Azure Files, możesz ustawić właściwość na kontach magazynu przy użyciu interfejsu Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Ustawienie tej właściwości niejawnie "przyłącza" konto magazynu do skojarzonego Azure AD DS wdrożenia. Azure AD DS uwierzytelnianie za pośrednictwem protokołu SMB jest następnie włączone dla wszystkich nowych i istniejących udziałów plików na koncie magazynu.

Należy pamiętać, że możesz włączyć uwierzytelnianie Azure AD DS za pośrednictwem protokołu SMB dopiero po pomyślnym wdrożeniu Azure AD DS dzierżawie usługi Azure AD. Aby uzyskać więcej informacji, zobacz [wymagania wstępne.](#prerequisites)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby włączyć Azure AD DS za pośrednictwem protokołu SMB za pomocą [Azure Portal](https://portal.azure.com), wykonaj następujące kroki:

1. W Azure Portal przejdź do istniejącego konta magazynu lub [utwórz konto magazynu.](../common/storage-account-create.md)
1. W sekcji **Ustawienia** wybierz pozycję **Konfiguracja.**
1. W **obszarze Dostęp oparty na tożsamości dla udziałów** plików przełącz przełącznik usługi Azure Active Directory Domain Service **(AAD DS)** na pozycję **Włączone.**
1. Wybierz pozycję **Zapisz**.

Na poniższej ilustracji przedstawiono sposób włączania uwierzytelniania Azure AD DS za pośrednictwem protokołu SMB dla konta magazynu.

:::image type="content" source="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png" alt-text="Zrzut ekranu przedstawiający blok konfiguracji na koncie magazynu z włączonymi usługami zarządzania usługi Azure Active Directory." lightbox="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby włączyć Azure AD DS za pośrednictwem protokołu SMB za pomocą usługi Azure PowerShell, zainstaluj najnowszy moduł Az (2.4 lub nowsze) lub moduł Az.Storage (1.5 lub nowsze). Aby uzyskać więcej informacji na temat instalowania programu PowerShell, zobacz Install Azure PowerShell on Windows with PowerShellGet (Instalowanie programu PowerShellGet w systemie Windows przy [użyciu polecenia PowerShellGet).](/powershell/azure/install-Az-ps)

Aby utworzyć nowe konto magazynu, wywołaj pozycję [New-AzStorageAccount,](/powershell/module/az.storage/New-azStorageAccount)a następnie ustaw parametr **EnableAzureActiveDirectoryDomainServicesForFile** na **wartość true**. W poniższym przykładzie pamiętaj, aby zastąpić wartości symboli zastępczych własnymi wartościami. (Jeśli używasz poprzedniego modułu w wersji zapoznawczej, parametr umożliwiający włączenie funkcji to **EnableAzureFilesAadIntegrationForSMB).**

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Aby włączyć tę funkcję na istniejących kontach magazynu, użyj następującego polecenia:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby włączyć uwierzytelnianie usługi Azure AD za pośrednictwem protokołu SMB za pomocą interfejsu wiersza polecenia platformy Azure, zainstaluj najnowszą wersję interfejsu wiersza polecenia (w wersji 2.0.70 lub nowszej). Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, [zobacz Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Aby utworzyć nowe konto magazynu, wywołaj [az storage account create](/cli/azure/storage/account#az-storage-account-create)i ustaw właściwość na wartość `--enable-files-aadds` **true**. W poniższym przykładzie pamiętaj, aby zastąpić wartości symboli zastępczych własnymi wartościami. (Jeśli korzystasz z poprzedniego modułu w wersji zapoznawczej, parametrem włączania funkcji jest **file-aad).**

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Aby włączyć tę funkcję na istniejących kontach magazynu, użyj następującego polecenia:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Teraz pomyślnie włączono uwierzytelnianie Azure AD DS za pośrednictwem protokołu SMB i przypisano rolę niestandardową, która zapewnia dostęp do udziału plików platformy Azure przy użyciu tożsamości usługi Azure AD. Aby przyznać dodatkowym użytkownikom dostęp do udziału [](#assign-access-permissions-to-an-identity) plików, postępuj zgodnie z instrukcjami w sekcjach Przypisywanie uprawnień dostępu w celu używania tożsamości i Konfigurowanie uprawnień systemu plików NTFS za [pośrednictwem funkcji SMB.](#configure-ntfs-permissions-over-smb)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Files i sposobu korzystania z usługi Azure AD za pośrednictwem SMB, zobacz następujące zasoby:

- [Omówienie obsługi opartego na tożsamości uwierzytelniania w usłudze Azure Files na potrzeby dostępu do protokołu SMB](storage-files-active-directory-overview.md)
- [Często zadawane pytania](storage-files-faq.md)
