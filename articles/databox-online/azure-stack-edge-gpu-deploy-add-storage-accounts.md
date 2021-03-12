---
title: Samouczek dotyczący transferowania danych do konta magazynu przy użyciu programu Azure Stack EDGE Pro GPU | Microsoft Docs
description: Dowiedz się, jak dodawać lokalne i brzegowe konta magazynu na urządzeniu z systemem Azure Stack Edge w ramach procesora GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 4d235136190845ba7531592fdeecd9cd05b0347c
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200946"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>Samouczek: przesyłanie danych za pomocą kont magazynu za pomocą Azure Stack Edge — procesor GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym samouczku opisano, jak dodawać konta magazynu i łączyć się z nimi na urządzeniu Azure Stack EDGE Pro. Po dodaniu kont magazynu Azure Stack EDGE Pro będzie mogła przesyłać dane na platformę Azure.

Wykonanie tej procedury może potrwać około 30 minut.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Dodawanie konta magazynu
> * Nawiązywanie połączenia z kontem magazynu

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed dodaniem kont magazynu do Azure Stack EDGE Pro upewnij się, że:

- Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w artykule [instalowanie Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-install.md).

- Urządzenie fizyczne zostało aktywowane zgodnie z opisem w artykule [aktywowanie Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-activate.md).


## <a name="add-an-edge-storage-account"></a>Dodawanie konta magazynu brzegowego

Aby utworzyć konto magazynu brzegowego, wykonaj następującą procedurę:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Nawiązywanie połączenia z kontem magazynu brzegowego

Teraz można połączyć się z interfejsami API REST magazynu Edge za pośrednictwem *protokołu HTTP* lub *https*.

- *Protokół https* jest bezpiecznym i zalecanym sposobem.
- *Protokół http* jest używany podczas łączenia się za pośrednictwem zaufanych sieci.

## <a name="connect-via-http"></a>Nawiązywanie połączenia za pośrednictwem protokołu HTTP

Połączenia z interfejsami API REST magazynu brzegowego za pośrednictwem protokołu HTTP wymagają następujących kroków:

- Dodawanie wirtualnego adresu IP usługi Azure spójnej i usługi BLOB Service do hosta zdalnego
- Weryfikowanie połączenia 

Wszystkie wymienione kroki zostały opisane poniżej.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>Dodaj adres IP urządzenia i punkt końcowy usługi BLOB Service do zdalnego klienta

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Sprawdź połączenie

Aby zweryfikować połączenie, zazwyczaj potrzebne są następujące informacje (mogą się różnić) w poprzednim kroku:

- Nazwa konta magazynu.
- Klucz dostępu do konta magazynu.
- Blob service punkt końcowy.

Masz już nazwę konta magazynu i punkt końcowy usługi BLOB Service. Klucz dostępu konta magazynu można uzyskać, łącząc się z urządzeniem za pośrednictwem Azure Resource Manager przy użyciu klienta Azure PowerShell.

Wykonaj kroki opisane w temacie [łączenie z urządzeniem za pośrednictwem Azure Resource Manager](azure-stack-edge-gpu-connect-resource-manager.md). Po zalogowaniu się do lokalnych interfejsów API urządzeń za pomocą Azure Resource Manager Pobierz listę kont magazynu na urządzeniu. Uruchom następujące polecenie cmdlet:

`Get-AzureRMStorageAccount`

Z listy kont magazynu na urządzeniu Zidentyfikuj konto magazynu, dla którego jest potrzebny klucz dostępu. Zanotuj nazwę konta magazynu i grupę zasobów.

Poniżej przedstawiono przykładowe dane wyjściowe:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

Aby uzyskać klucz dostępu, uruchom następujące polecenie cmdlet:

`Get-AzureRmStorageAccountAccessKey`

Poniżej przedstawiono przykładowe dane wyjściowe:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

Skopiuj i Zapisz ten klucz. Ten klucz zostanie użyty do zweryfikowania połączenia przy użyciu Eksplorator usługi Azure Storage.

Aby sprawdzić, czy połączenie zostało pomyślnie nawiązane, użyj Eksploratora usługi Storage w celu dołączenia do zewnętrznego konta magazynu. Jeśli nie masz Eksplorator usługi Storage, [pobierz Eksplorator usługi Storage](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409).

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>Nawiązywanie połączenia za pośrednictwem protokołu HTTPS

Aby nawiązać połączenie z interfejsami API REST usługi Azure Blob Storage za pośrednictwem protokołu HTTPS, należy wykonać następujące czynności:

- Pobierz certyfikat punktu końcowego obiektu BLOB
- Zaimportuj certyfikat na kliencie lub hoście zdalnym
- Dodawanie adresu IP urządzenia i punktu końcowego usługi BLOB do klienta lub hosta zdalnego
- Konfigurowanie i weryfikowanie połączenia

Wszystkie wymienione kroki zostały opisane poniżej.

### <a name="get-certificate"></a>Pobierz certyfikat

Dostęp do usługi BLOB Storage za pośrednictwem protokołu HTTPS wymaga certyfikatu SSL dla urządzenia. Ten certyfikat zostanie również przekazany do urządzenia z systemem Azure Stack brzeg Pro jako plik *PFX* z dołączonym kluczem prywatnym. Aby uzyskać więcej informacji na temat sposobu tworzenia (tylko do celów testowych i deweloperskich) i przekazywania tych certyfikatów do urządzenia Azure Stack EDGE Pro, przejdź do:

- [Utwórz certyfikat punktu końcowego obiektu BLOB](azure-stack-edge-gpu-manage-certificates.md#create-certificates-optional).
- [Przekaż certyfikat punktu końcowego obiektu BLOB](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).
- [Zaimportuj certyfikaty na kliencie, uzyskując dostęp do urządzenia](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

### <a name="import-certificate"></a>Importowanie certyfikatu

W przypadku używania Eksplorator usługi Azure Storage do nawiązywania połączenia z kontami magazynu na urządzeniu należy również zaimportować certyfikat do Eksplorator usługi Storage w formacie PEM. W środowisku systemu Windows z kodowaniem Base-64 zakodowane *. cer* jest taka sama jak format PEM.

Wykonaj następujące kroki, aby zaimportować certyfikaty na Eksplorator usługi Azure Storage:

1. Upewnij się, że Eksplorator usługi Azure Storage są przeznaczone dla Azure Stack interfejsów API. Przejdź do obszaru **edytowanie > celu Azure Stack interfejsów API**. Po wyświetleniu monitu uruchom ponownie Eksplorator usługi Storage, aby zmiany zaczęły obowiązywać.

2. Aby zaimportować certyfikaty SSL, przejdź do **edytuj > certyfikaty ssl > zaimportować certyfikaty**.

  
   ![Importuj certyfikat do Eksplorator usługi Storage](./media/azure-stack-edge-gpu-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. Przejdź do i podaj łańcuch podpisywania oraz certyfikaty obiektów BLOB. Zarówno łańcuch podpisywania, jak i certyfikat obiektu BLOB powinny mieć format PEM, który jest taki sam jak format szyfrowany algorytmem Base64 w systemie Windows. Zostanie wyświetlone powiadomienie, że certyfikaty zostały pomyślnie zaimportowane.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Dodaj adres IP urządzenia i punkt końcowy usługi BLOB Service

Wykonaj te same kroki, aby [dodać adres IP urządzenia i punkt końcowy usługi BLOB Service podczas nawiązywania połączenia za pośrednictwem *protokołu HTTP*](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client).

### <a name="configure-and-verify-connection"></a>Konfigurowanie i weryfikowanie połączenia

Postępuj zgodnie z instrukcjami, aby [skonfigurować i zweryfikować połączenie, które było używane podczas nawiązywania połączenia za pośrednictwem *protokołu HTTP*](#verify-connection). Jedyną różnicą jest to, że należy pozostawić pole *Użyj protokołu HTTP* niezaznaczone.

## <a name="next-steps"></a>Następne kroki

W tym samouczku omówiono następujące tematy Azure Stack EDGE Pro:

> [!div class="checklist"]
> * Dodawanie konta magazynu
> * Łączenie się z kontem magazynu

Aby dowiedzieć się, jak przekształcić dane przy użyciu Azure Stack EDGE Pro, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Przekształcanie danych za pomocą Azure Stack EDGE Pro](./azure-stack-edge-j-series-deploy-configure-compute.md)
