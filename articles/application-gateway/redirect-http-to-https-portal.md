---
title: Przekierowywanie protokołu HTTP do protokołu HTTPS w portalu — Application Gateway platformy Azure
description: Dowiedz się, jak utworzyć bramę aplikacji z przekierowanym ruchem z protokołu HTTP do HTTPS przy użyciu Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 67153fa750fee765dcaa1072eec87a2f6169b918
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397284"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Tworzenie bramy aplikacji przy użyciu protokołu HTTP w celu przekierowania HTTPS za pomocą Azure Portal

Za pomocą Azure Portal można utworzyć [bramę aplikacji](overview.md) z certyfikatem na potrzeby zakończenia protokołu TLS. Reguła routingu służy do przekierowywania ruchu HTTP do portu HTTPS w bramie aplikacji. W tym przykładzie utworzysz również [zestaw skalowania maszyn wirtualnych](../virtual-machine-scale-sets/overview.md) dla puli zaplecza bramy aplikacji, która zawiera dwa wystąpienia maszyn wirtualnych.

W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie certyfikatu z podpisem własnym
* Konfigurowanie sieci
* Tworzenie bramy aplikacji z certyfikatem
* Dodaj odbiornik i regułę przekierowania
* Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu domyślnej puli zaplecza

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej, aby można było utworzyć certyfikat i zainstalować usługi IIS. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Aby uruchomić polecenia z tego samouczka, należy również uruchomić polecenie, `Login-AzAccount` Aby utworzyć połączenie z platformą Azure.

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę. W tym samouczku utworzysz certyfikat z podpisem własnym przy użyciu polecenia [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Korzystając z polecenia [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) i zwróconego odcisku palca, możesz wyeksportować plik pfx z certyfikatu.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Wynik powinien wyglądać podobnie do następującego:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Użyj odcisku palca w celu utworzenia pliku pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Sieć wirtualna jest potrzebna do komunikacji między utworzonymi zasobami. W tym przykładzie są tworzone dwie podsieci: jedna dla bramy aplikacji i druga dla serwerów zaplecza. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
3. Wybierz pozycję **Sieć** , a następnie z listy Polecane wybierz pozycję **Application Gateway**.
4. Wprowadź następujące wartości dla bramy aplikacji:

   - *myAppGateway* — jako nazwę bramy aplikacji.
   - *myResourceGroupAG* — jako nową grupę zasobów.

     ![Tworzenie nowej bramy aplikacji](./media/create-url-route-portal/application-gateway-create.png)

5. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
6. Kliknij kolejno pozycje **Wybierz sieć wirtualną** , **Utwórz nową** , a następnie wprowadź następujące wartości dla sieci wirtualnej:

   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.

     ![Tworzenie sieci wirtualnej](./media/create-url-route-portal/application-gateway-vnet.png)

7. Kliknij przycisk **OK** , aby utworzyć sieć wirtualną i podsieć.
8. W obszarze **Konfiguracja adresu IP frontonu** upewnij się, że **Typ adresu IP** jest **publiczny** , i wybierz pozycję **Utwórz nowy** . Wprowadź *myAGPublicIPAddress* dla nazwy. Zaakceptuj wartości domyślne dla innych ustawień, a następnie kliknij przycisk **OK**.
9. W obszarze **Konfiguracja odbiornika** wybierz **pozycję https** , a następnie wybierz pozycję **Wybierz plik** i przejdź do pliku *c:\appgwcert.pfx* , a następnie wybierz pozycję **Otwórz**.
10. Wpisz *appgwcert* dla nazwy certyfikatu i *Azure123456!* jako hasło.
11. Pozostaw wyłączoną zaporę aplikacji sieci Web, a następnie wybierz przycisk **OK**.
12. Przejrzyj ustawienia na stronie Podsumowanie, a następnie wybierz przycisk **OK** , aby utworzyć zasoby sieciowe i bramę aplikacji. Utworzenie bramy aplikacji może potrwać kilka minut, przed przejściem do następnej sekcji poczekaj na zakończenie wdrożenia.

### <a name="add-a-subnet"></a>Dodawanie podsieci

1. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **myVNet** z listy zasoby.
2. Wybierz pozycję **podsieci** , a następnie kliknij pozycję **podsieć**.

    ![Tworzenie podsieci](./media/create-url-route-portal/application-gateway-subnet.png)

3. Wpisz *myBackendSubnet* jako nazwę podsieci.
4. W polu Zakres adresów wpisz *10.0.2.0/24* , a następnie wybierz przycisk **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Dodaj odbiornik i regułę przekierowania

### <a name="add-the-listener"></a>Dodaj odbiornik

Najpierw Dodaj odbiornik o nazwie *Listen* dla portu 80.

1. Otwórz grupę zasobów **myResourceGroupAG** i wybierz pozycję **myAppGateway**.
2. Wybierz pozycję **detektory** , a następnie pozycję **+ podstawowa**.
3. Wpisz *nazwę* .
4. Wpisz *httpPort* dla nowej nazwy portu frontonu i *80* dla portu.
5. Upewnij się, że protokół jest ustawiony na **http** , a następnie wybierz przycisk **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Dodawanie reguły routingu z konfiguracją przekierowania

1. W obszarze **myAppGateway** wybierz pozycję **reguły** , a następnie wybierz pozycję **+ reguła routingu żądania**.
2. W polu **Nazwa reguły** wpisz *ograniczeniem zakresu wystąpień*.
3. Upewnij **się, że** wybrano dla odbiornika odbiornik.
4. Kliknij kartę **cele zaplecza** i wybierz **Typ docelowy** jako *przekierowania*.
5. W obszarze **Typ przekierowania** wybierz pozycję **trwały**.
6. W obszarze **cel przekierowania** wybierz pozycję **odbiornik**.
7. Upewnij się, że **docelowy odbiornik** jest ustawiony na **appGatewayHttpListener**.
8. W polu **Uwzględnij ciąg zapytania** i **Dołącz ścieżkę** wybierz opcję *tak*.
9. Wybierz pozycję **Dodaj**.

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skalowania maszyn wirtualnych, aby zapewnić serwery dla puli zaplecza w bramie aplikacji.

1. W lewym górnym rogu portalu wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Compute**.
3. W polu wyszukiwania wpisz *skalowanie* i naciśnij klawisz ENTER.
4. Wybierz pozycję **zestaw skalowania maszyn wirtualnych** , a następnie wybierz pozycję **Utwórz**.
5. Dla **opcji Nazwa zestawu skalowania maszyn wirtualnych** wpisz *myvmss*.
6. W przypadku obrazu dysku systemu operacyjnego * * upewnij się, że wybrano pozycję **Windows Server 2016 Datacenter** .
7. W obszarze **Grupa zasobów** wybierz pozycję **myResourceGroupAG**.
8. W obszarze **Nazwa użytkownika** wpisz *azureuser*.
9. W obszarze **hasło** wpisz *Azure123456!* i Potwierdź hasło.
10. W polu **Liczba wystąpień** upewnij się, że wartość jest równa **2**.
11. W obszarze **rozmiar wystąpienia** wybierz pozycję **D2s_v3**.
12. W obszarze **Sieć** upewnij się, że opcja **Opcje równoważenia obciążenia** jest ustawiona na **Application Gateway**.
13. Upewnij się, że **Brama aplikacji** jest ustawiona na **myAppGateway**.
14. Upewnij się, że **podsieć** jest ustawiona na **myBackendSubnet**.
15. Wybierz przycisk **Utwórz**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Kojarzenie zestawu skalowania z odpowiednią pulą zaplecza

Interfejs użytkownika portalu zestawu skalowania maszyn wirtualnych tworzy nową pulę zaplecza dla zestawu skalowania, ale chcesz skojarzyć ją z istniejącym appGatewayBackendPool.

1. Otwórz grupę zasobów **myResourceGroupAg** .
2. Wybierz pozycję **myAppGateway**.
3. Wybierz pozycję **Pule zaplecza**.
4. Wybierz pozycję **myAppGatewaymyvmss**.
5. Wybierz pozycję **Usuń wszystkie elementy docelowe z puli zaplecza**.
6. Wybierz pozycję **Zapisz**.
7. Po zakończeniu tego procesu wybierz pulę zaplecza **myAppGatewaymyvmss** , wybierz pozycję **Usuń** , a następnie kliknij przycisk **OK** , aby potwierdzić.
8. Wybierz pozycję **appGatewayBackendPool**.
9. W obszarze **obiekty docelowe** wybierz pozycję **VMSS**.
10. W obszarze **VMSS** wybierz opcję **myvmss**.
11. W obszarze **konfiguracje interfejsu sieciowego** wybierz pozycję **myvmssNic**.
12. Wybierz pozycję **Zapisz**.

### <a name="upgrade-the-scale-set"></a>Uaktualnianie zestawu skalowania

Na koniec należy uaktualnić zestaw skalowania przy użyciu tych zmian.

1. Wybierz zestaw skalowania **myvmss** .
2. W obszarze **Ustawienia** wybierz pozycję **Wystąpienia**.
3. Wybierz oba wystąpienia, a następnie wybierz pozycję **Uaktualnij**.
4. Wybierz pozycję **Tak** , aby potwierdzić.
5. Po zakończeniu tego procesu Wróć do **myAppGateway** i wybierz pozycję **Pule zaplecza**. Należy teraz zobaczyć, że **appGatewayBackendPool** ma dwa elementy docelowe, a  **myAppGatewaymyvmss** nie ma żadnych elementów docelowych.
6. Wybierz pozycję **myAppGatewaymyvmss** , a następnie wybierz pozycję **Usuń**.
7. Wybierz przycisk **OK** , aby potwierdzić.

### <a name="install-iis"></a>Instalowanie usług IIS

Łatwym sposobem instalowania usług IIS na zestawie skalowania jest użycie programu PowerShell. W portalu kliknij ikonę Cloud Shell i upewnij się, że wybrano opcję **PowerShell** .

Wklej następujący kod do okna programu PowerShell i naciśnij klawisz ENTER.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Uaktualnianie zestawu skalowania

Po zmianie wystąpień z usługami IIS należy ponownie uaktualnić zestaw skalowania przy użyciu tej zmiany.

1. Wybierz zestaw skalowania **myvmss** .
2. W obszarze **Ustawienia** wybierz pozycję **Wystąpienia**.
3. Wybierz oba wystąpienia, a następnie wybierz pozycję **Uaktualnij**.
4. Wybierz pozycję **Tak** , aby potwierdzić.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Publiczny adres IP aplikacji można uzyskać na stronie Przegląd usługi Application Gateway.

1. Wybierz pozycję **myAppGateway**.
2. Na stronie **Przegląd** Zwróć uwagę na adres IP w obszarze **publiczny adres IP frontonu**.

3. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład http://52.170.203.149

   ![Ostrzeżenie o zabezpieczeniach](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz pozycję **Szczegóły** , a następnie pozycję **Przejdź do strony internetowej**. Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

   ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć bramę aplikacji przy użyciu wewnętrznego przekierowania](redirect-internal-site-powershell.md).