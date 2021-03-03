---
title: Użyj Azure AD Domain Services, aby autoryzować dostęp do danych plików za pośrednictwem protokołu SMB
description: Dowiedz się, jak włączyć uwierzytelnianie oparte na tożsamościach za pośrednictwem bloku komunikatów serwera (SMB) dla Azure Files za pośrednictwem Azure Active Directory Domain Services. Przyłączone do domeny maszyny wirtualne z systemem Windows mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: d75b60c715a758684e6f3a4b331c0b12d17eaf21
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705113"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Włącz uwierzytelnianie Azure Active Directory Domain Services na Azure Files

[Azure Files](storage-files-introduction.md)   obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) przez dwa typy usług domenowych: lokalne Active Directory Domain Services (AD DS) i Azure Active Directory Domain Services (AD DS platformy Azure). Zdecydowanie zalecamy zapoznanie się z [sekcją jak to działa](./storage-files-active-directory-overview.md#how-it-works) w celu wybrania odpowiedniej usługi domeny do uwierzytelnienia. Konfiguracja różni się w zależności od wybranej usługi domeny. Ten artykuł koncentruje się na włączaniu i konfigurowaniu usługi Azure AD DS na potrzeby uwierzytelniania przy użyciu udziałów plików platformy Azure.

Jeśli dopiero zaczynasz korzystanie z udziałów plików platformy Azure, zalecamy zapoznanie się z naszym [przewodnikiem planowania](storage-files-planning.md) przed przeczytaniem następującej serii artykułów.

> [!NOTE]
> Azure Files obsługuje uwierzytelnianie Kerberos za pomocą usługi Azure AD DS tylko z atrybutem RC4-HMAC. Szyfrowanie AES Kerberos nie jest jeszcze obsługiwane.
> Azure Files obsługuje uwierzytelnianie dla AD DS platformy Azure z pełną synchronizacją z usługą Azure AD. Jeśli włączono synchronizację z zakresem w usłudze Azure AD DS, która synchronizuje tylko ograniczony zestaw tożsamości z usługi Azure AD, uwierzytelnianie i autoryzacja nie są obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed włączeniem usługi Azure AD za pośrednictwem protokołu SMB dla udziałów plików platformy Azure upewnij się, że zostały spełnione następujące wymagania wstępne:

1.  **Wybierz lub Utwórz dzierżawę usługi Azure AD.**

    Możesz użyć nowej lub istniejącej dzierżawy do uwierzytelniania w usłudze Azure AD za pośrednictwem protokołu SMB. Dzierżawa i udział plików, do których chcesz uzyskać dostęp, muszą być skojarzone z tą samą subskrypcją.

    Aby utworzyć nową dzierżawę usługi Azure AD, możesz [dodać dzierżawę usługi Azure AD i subskrypcję usługi Azure AD](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Jeśli masz istniejącą dzierżawę usługi Azure AD, ale chcesz utworzyć nową dzierżawę do użycia z udziałami plików platformy Azure, zobacz [Tworzenie dzierżawy Azure Active Directory](/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Włącz Azure AD Domain Services w dzierżawie usługi Azure AD.**

    Aby zapewnić obsługę uwierzytelniania przy użyciu poświadczeń usługi Azure AD, musisz włączyć Azure AD Domain Services dla dzierżawy usługi Azure AD. Jeśli nie jesteś administratorem dzierżawy usługi Azure AD, skontaktuj się z administratorem i postępuj zgodnie ze wskazówkami krok po kroku, aby [włączyć Azure Active Directory Domain Services przy użyciu Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Ukończenie wdrożenia usługi Azure AD DS zwykle trwa około 15 minut. Przed przejściem do następnego kroku Sprawdź, czy stan kondycji usługi Azure AD DS jest **uruchomiony** z włączoną funkcją synchronizacji skrótów haseł.

1.  **Przyłączanie domeny do maszyny wirtualnej platformy Azure przy użyciu usługi Azure AD DS.**

    Aby uzyskać dostęp do udziału plików przy użyciu poświadczeń usługi Azure AD z maszyny wirtualnej, maszyna wirtualna musi być przyłączona do domeny do usługi Azure AD DS. Aby uzyskać więcej informacji na temat dołączania do maszyny wirtualnej, zobacz [dołączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Uwierzytelnianie za pomocą usługi Azure AD DS za pośrednictwem protokołu SMB z udziałami plików platformy Azure jest obsługiwane tylko na maszynach wirtualnych platformy Azure działających w systemach operacyjnych starszych niż Windows 7 lub Windows Server 2008 R2.

1.  **Wybierz lub Utwórz udział plików platformy Azure.**

    Wybierz nowy lub istniejący udział plików skojarzony z tą samą subskrypcją co dzierżawy usługi Azure AD. Aby uzyskać informacje na temat tworzenia nowego udziału plików, zobacz [Tworzenie udziału plików w Azure Files](storage-how-to-create-file-share.md).
    W celu uzyskania optymalnej wydajności zalecamy, aby udział plików znajdował się w tym samym regionie co maszyna wirtualna, z której planujesz uzyskać dostęp do udziału.

1.  **Sprawdź łączność Azure Files, instalując udziały plików platformy Azure przy użyciu klucza konta magazynu.**

    Aby sprawdzić, czy maszyna wirtualna i udział plików są prawidłowo skonfigurowane, spróbuj zainstalować udział plików przy użyciu klucza konta magazynu. Aby uzyskać więcej informacji, zobacz [Instalowanie udziału plików platformy Azure i uzyskiwanie dostępu do udziału w systemie Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Dostępność regionalna

Uwierzytelnianie Azure Files za pomocą usługi Azure AD DS jest dostępne we [wszystkich regionach usług Azure Public, gov i Chiny](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview-of-the-workflow"></a>Przegląd przepływu pracy

Przed włączeniem uwierzytelniania AD DS platformy Azure za pośrednictwem protokołu SMB dla udziałów plików platformy Azure Sprawdź, czy środowiska usługi Azure AD i Azure Storage są prawidłowo skonfigurowane. Zalecamy zapoznanie się z wymaganiami [wstępnymi](#prerequisites) , aby upewnić się, że zostały wykonane wszystkie wymagane kroki.

Następnie wykonaj następujące czynności, aby przyznać dostęp do Azure Files zasobów przy użyciu poświadczeń usługi Azure AD:

1. Włącz uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB dla konta magazynu, aby zarejestrować konto magazynu przy użyciu skojarzonego wdrożenia usługi Azure AD DS.
2. Przypisywanie uprawnień dostępu dla udziału do tożsamości usługi Azure AD (użytkownika, grupy lub nazwy głównej usługi).
3. Skonfiguruj uprawnienia systemu plików NTFS za pośrednictwem protokołu SMB dla katalogów i plików.
4. Zainstaluj udział plików platformy Azure z maszyny wirtualnej przyłączonej do domeny.

Na poniższym diagramie przedstawiono kompleksowy przepływ pracy służący do włączania uwierzytelniania AD DS platformy Azure za pośrednictwem protokołu SMB dla Azure Files.

![Diagram przedstawiający przepływ pracy usługi Azure AD za pośrednictwem protokołu SMB dla Azure Files](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Włącz uwierzytelnianie AD DS platformy Azure dla Twojego konta

Aby włączyć uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB dla Azure Files, można ustawić właściwość na kontach magazynu za pomocą Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Ustawienie tej właściwości niejawnie "Przyłączanie do domeny" do konta magazynu ze skojarzonym wdrożeniem usługi Azure AD DS. Uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB jest następnie włączone dla wszystkich nowych i istniejących udziałów plików na koncie magazynu.

Pamiętaj, że możesz włączyć uwierzytelnianie na platformie Azure AD DS za pośrednictwem protokołu SMB dopiero po pomyślnym wdrożeniu usługi Azure AD DS w dzierżawie usługi Azure AD. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby włączyć uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB przy użyciu [Azure Portal](https://portal.azure.com), wykonaj następujące czynności:

1. W Azure Portal przejdź do istniejącego konta magazynu lub [Utwórz konto magazynu](../common/storage-account-create.md).
1. W sekcji **Ustawienia** wybierz pozycję **Konfiguracja**.
1. W obszarze **dostęp oparty na tożsamościach dla udziałów plików** Przełącz przełącznik dla **Azure Active Directory Domain Service (AAD DS)** na wartość **włączone**.
1. Wybierz pozycję **Zapisz**.

Na poniższej ilustracji przedstawiono sposób włączania uwierzytelniania AD DS platformy Azure za pośrednictwem protokołu SMB dla konta magazynu.

![Włącz uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB w Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby włączyć uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB z Azure PowerShell, zainstaluj najnowszą wersję AZ module (2,4 lub nowszą) lub moduł AZ. Storage (1,5 lub nowszy). Aby uzyskać więcej informacji na temat instalowania programu PowerShell, zobacz [Install Azure PowerShell in Windows with PowerShellGet](/powershell/azure/install-Az-ps).

Aby utworzyć nowe konto magazynu, wywołaj polecenie [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0), a następnie ustaw  dla parametru EnableAzureActiveDirectoryDomainServicesForFile **wartość true**. W poniższym przykładzie Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami. (Jeśli używasz poprzedniego modułu w wersji zapoznawczej, parametr służący do włączania funkcji to **EnableAzureFilesAadIntegrationForSMB**).

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

Aby włączyć uwierzytelnianie usługi Azure AD za pośrednictwem protokołu SMB przy użyciu interfejsu wiersza polecenia platformy Azure, zainstaluj najnowszą wersję interfejsu wiersza polecenia (w wersji 2.0.70 lub nowszej). Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Aby utworzyć nowe konto magazynu, wywołaj polecenie [AZ Storage account Create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)i ustaw `--enable-files-aadds` Właściwość na **wartość true**. W poniższym przykładzie Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami. (Jeśli korzystasz z poprzedniego modułu w wersji zapoznawczej, parametr do włączenia funkcji to **File-AAD**).

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

Pomyślnie włączono uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB i przypisano rolę niestandardową, która zapewnia dostęp do udziału plików platformy Azure za pomocą tożsamości usługi Azure AD. Aby udzielić dodatkowych użytkownikom dostępu do udziału plików, postępuj zgodnie z instrukcjami podanymi w sekcji [przypisywanie uprawnień dostępu](#assign-access-permissions-to-an-identity) do używania tożsamości i [konfigurowania uprawnień NTFS dla protokołu SMB](#configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Files i sposobu korzystania z usługi Azure AD za pośrednictwem protokołu SMB, zobacz następujące zasoby:

- [Omówienie obsługi opartego na tożsamości uwierzytelniania w usłudze Azure Files na potrzeby dostępu do protokołu SMB](storage-files-active-directory-overview.md)
- [Często zadawane pytania](storage-files-faq.md)
